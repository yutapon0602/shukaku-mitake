# Design — Harvest Sort Patrol v10 (v6/v7 sections kept below as history)

## 構成
- `index.html` がゲーム本体。Canvas（480×720論理座標）でゲーム画面を描画し、タイトル/物語/畑/日クリア/結果/品評会/図鑑/実績は DOM オーバーレイで表示。v6では収穫シーン専用の WebGL キャンバス（`#gl`）を2Dキャンバスの下に重ね、Three.js（`vendor/` に同梱、importmap 経由）が畑・実・カゴ・カラス・雨を描き、2D側は HUD・フィードバック・バナー・紙吹雪だけを重ねる。
- 状態遷移: `title → (初回のみ story) → farm → play → dayclear → farm … → (7日目) ending(品評会) → farm(次シーズン)`、ミス3回で `over`（ラン消去、畑は残る）。`play` には `playKind`（normal / versus）があり、7日目は常に対戦（決戦）、他日は畑画面で選択できる。

## 主要モジュール（スクリプト内）
- **i18n**: `STR`（ja/en）に全UI文字列。図鑑・実績・農家コメント・物語は専用データ（`ZUKAN` / `ACH` / `FARMER` / `FARMDAY` / `STORY_INTRO`）に ja/en を持つ。
- **品種**: `farm.varieties` の各要素が `{id, name{ja,en}, sweet, size, hardy, hue, gen, parents}`。`varTotal()` がパラメータ合計。初期品種3つは `defaultFarm()` で生成。交配 `breed()` は親の平均±1＋18%で突然変異、名前は親の最強パラメータの漢字（甘/大/丈）を最大2つ連結＋「とまと」、色相は親平均±4。
- **畑**: `farm.plots` は4要素、各 `{vid, growth, quality, watered, fert, harvests}` か null。段階は growth 閾値（0:種 / 1:芽 / 3:若株 / 5:実つき）で `stageOf()` が判定。夜の処理 `overnight()`：実つきだった株は若株へ戻し harvests+1（2回で消去）、それ以外は 水やり+2・肥料+1・雨+1 を加算し、実つき到達で実績判定。
- **世話**: 畑画面の朝に世話ポイント3を付与（`farmDayGranted` で同日再付与を防止）。水やりは雨の日不可。肥料は品質+1（上限3）。
- **天気**: `weatherToday / weatherTomorrow` をランに保持し、日送りで繰り上げ＋新規生成（晴50%/曇30%/雨20%、シーズン初日は晴れ）。空の描画は天気プリセット（雨・曇り）を優先。雨の日は落下×1.18で終日降雨、曇りは従来のにわか雨ステートマシン（3日目相当〜）。
- **スポーンと品種**: 落下物は熟度 `pickType()` で決めた後、`pickVariety()` で品種を重み付け抽選（実つき5 / 植え付けあり2 / 初期品種1 / 未植えの交配品種0）。実つき品種は未熟・色づき途中が25%で完熟に化ける。実の半径は大きさ、完熟の色は品種の色相で `drawTomatoArt(c, type, r, variety)` が描く。得点は合計パラメータと実つき株の品質で補正。完熟収穫時に38%でその品種の種を付与。
- **品評会**: `seasonClear()` 内で審査。出品は `bestVariety()`（合計最大）。審査式は requirements 参照。結果で紙吹雪量・祖父の台詞（`STR.fairEnd`）・実績（contest/champion）が変わる。
- **物語**: `showStory(lines, cb)` が会話オーバーレイを進行。話者は `who` で農家/研修生の描画を切替（`drawFarmer` / `drawTrainee`）。畑画面の一言は `farmLine()`：出来事フラグ `farm.pendingLine`（交配・初フィーバー・金のトマト）→ 雨の日 → 日別 `FARMDAY` の優先順。
- **畑画面UI**: DOMで構築（`buildFarm()`）。区画カードは小型Canvasに `drawPlant()`（段階と品種の実・品質★を描画）。選択中区画への操作（種チップ/水やり/肥料）と交配パネル（select×2＋ボタン）を再描画方式で管理。
- **描画**: 空は天気 or 日別プリセット。出荷箱・カゴ・フィーバー演出は v3 から維持。HUDに天気アイコンを追加。
- **音響**: WebAudio。効果音は `tone()` ヘルパー＋ `sfx` セット（v5で plant/breed を追加）。BGMは16ステップのペンタトニック・ループを lookahead スケジューラで再生し、マスターゲインでミュートと連動。
- **永続化**: localStorage キー — `shukakuBest` / `shukakuBestAbs` / `shukakuSeasons` / `shukakuLife` / `shukakuAch`（JSON）/ `shukakuZukan`（JSON）/ `shukakuDiff` / `shukakuMute` / **`shukakuFarmV5`（JSON）**。`shukakuFarmV5` に品種・種・区画・品種図鑑・導入済みフラグ・進行中ラン（`snapshotRun()` / `restoreRun()`）をまとめて保存。

## v6の追加モジュール
- **3Dモデル軽量化**: Tripo の GLB（各約60MB・約190万面）を gltf-transform（weld → simplify ratio 0.02 → テクスチャ512px → meshopt 圧縮）で各0.4〜0.6MB・約4万面にし、`models/` に配置（合計約3.7MB）。金のトマトは金属質のため環境マップ（RoomEnvironment）が必須で、全モデルに envMapIntensity（金1.5 / 他0.35）を設定。
- **3Dレイヤー**（module スクリプト）: 座標系は「x=ゲームx、y=−ゲームy、z=手前」。カメラは z=1000 の透視で z=0 平面が 480×720 に一致。毎フレーム、ロジック側の `h3dSnapshot()`（items/corpses/basket/rival/crow/空の色/天気）が渡され、モデル複製の配置・回転・潰れ変形・汁パーティクル・雨・雲・株畝・影を描く。ロジックと描画の分離により、モデル未読込時は `__h3dReady()` が false のままとなり2D経路にフォールバックする。
- **物理**: 落下物は `vy`（初速＝日速度×約0.5）＋重力 `g`（560×大きさ係数、雨で×1.15〜1.3、上限キャップ）。横は風への指数接近＋揺れ。回転は `spin` で蓄積。地面到達で従来判定を行い、見た目用に `corpses`（kind: bounce）へ渡して跳ね・転がりを続ける。
- **縁と潰れ**: カゴ中心 |dx|≤40 が通常キャッチ、40<|dx|≤63 が縁ヒット。完熟は丈夫さで滑り込み判定、それ以外は `corpses`（kind: squash）＋ `miss("squash")`。金は常に滑り込み、害虫は通常ミス、その他の実は跳ね返って落下継続（`rimCd` で再判定防止）。
- **フィーバー接続**: コンボとは別に `feverCharge` を持ち、完熟収穫ごとに 0.7+甘さ×0.16 ずつ溜まり `feverNext`（初期10）到達で発動。ミスでリセット。
- **カラス**: ステートマシン（warn 0.75s → dive → leave）。対象は y<300 の落下中完熟。warn 中は対象を追尾し地面に影を表示。dive は 540px/s で対象へ、接触で grab（対象を除去し運び去る、コンボ0）か hit（横下に初速、回転3.2倍）。出現間隔は15〜24s。
- **ライバルAI**: 0.22s ごとに目標選定（完熟/金の価値 − 距離×0.55 − 到達時間×12、12%でミスチョイス）。最高速は日数と難易度で変動。ライバルのキャッチは中心帯のみ、縁では完熟が潰れる（得点なし）。対戦中はノルマ終了が無効で、60s タイマーが日を終わらせる（`endVersus()` → dayClear / 決戦時は seasonClear）。成績は `farm.vsWins / farm.vsPlayed` に保存。
- **検証フック**: `window.__state()` ほかのテスト用フックを本体に同梱（v5では検証用コピーに注入していた方式から変更。ゲームプレイには影響しない読み取り・強制スポーン系のみ）。

## 検証
- Playwright で通しプレイを自動化し、全画面のスクリーンショットを目視確認（`files/v6-verify/`、モデル単体確認は `files/v6-verify/models/`）。

## v7の追加モジュール（株から取る収穫への作り替え）
- **フィールド**：`buildField()` が3畝（y=262/398/540、縮尺0.70/0.86/1.04）×4株＝12株を作る。各株は品種（`pickVariety()` の重み抽選：実つき5/植え付け2/初期1）を持ち、スロットは最大6（基本4＋実つき品種+1＋4日目以降+1）。実は `spawnFruit(slot, g0)` でスロットに付き、`f.plant` / `f.slot` を相互参照する。収穫・落果・奪取で外れたスロットは `emptyT`（2.5〜6s）後に新しい実が実る。
- **成長**：`updateField(dt)` が `f.g += dt × f.rate × growthRate() × plant.gMul / 30` で進める（1サイクル約30〜35秒）。`growthRate()` は難易度速度×(0.92+通算日×0.035)、雨×1.25、フィーバー×1.3。畑の世話は `careGrowthMul()`（水+0.05/肥料+0.04 per 区画）として株の `gMul` に効く。段階は `fruitStage(f)`：damaged優先、金は g≥0.55、芽<0.30、未熟<0.55、色づき<0.75、完熟< `ripeEndOf(f)`（0.91+丈夫さ×0.014）、以降は過熟。`fruitBest(f)` は完熟直後の窓（0.045+甘さ×0.009）。g≥1.12 で落果（corpse "squash" を株元ではなく地面 GROUND_Y に出し、通常モードでは `miss("overripe")`）。
- **収穫と潰れ**：`pickFruit(f, who)` が取る瞬間に潰れ確率を確定する（距離>120pxで加算、カゴ速度>160px/sで加算、雨+0.07、過熟+0.1、丈夫さで×(1−(丈夫さ−1)×0.17)、上限0.6。ライバルは一律0.05）。実は `flies`（放物線飛行、所要0.26〜0.55s、弧の高さは距離連動）になり、着地時に `resolveFly()` が保存済み確率で抽選して潰れ/収穫を決める。判定は「取る瞬間」で完結するので、止まって近くで取れば絶対に潰れない。
- **カゴの2次元移動**：目標へ最高980px/sで追従し、実速度 (basketVX/VY) を指数平滑で保持（潰れ判定が読む）。タッチは指でカゴが隠れないよう y −26px のオフセット。タップ判定は「最も近い実まで max(24, r+18)px 以内」のときだけ収穫し、それ以外は移動目標の更新のみ。
- **カラス**：`updateCrow()` 内蔵のステートマシン（spawn は crowNext カウントダウン、2日目相当から）。対象は株の上の完熟/金（ベスト優先）。warn 1.15s（対象を追尾、影は対象株の根元 `tgt.plant.y+6`）、dive 540px/s、接触で実を `removeFruit` して運び去る（コンボ0）。タップで `scareCrow()`。
- **害虫**：`updateBugs()` 内蔵（bugNext、2日目相当から、最大2匹）。株の上部へ飛来→着座。着座中は `plant.bugId` が立ち、その株の実は成長停止。3.2sごとに同株の実を1つ damaged にする。タップで `shooBug()`。
- **ライバルAI（v7）**：0.25sごとに目標選定（金＞ベスト＞完熟、12%で色づき途中を誤選択）。目標の実の下（y+58）に立ち、クールダウン0.5〜0.95sで `pickFruit(f, "rival")`。同じ実の奪い合いが自然に発生する。
- **株モデルの色替え**：Tripo の株モデルは実が赤く焼き込まれていたため、GLB の basecolor テクスチャ（JPEG）を PIL で色相回転（赤→緑）し、buffer 0 の bufferView オフセットと meshopt 拡張の byteOffset だけをずらして再構築した（buffer 1 は仮想デコード空間なので触らない）。これで株側の実は全て未熟な緑に見え、ゲームの実だけが色づく。
- **3Dスナップショット（v7）**：`h3dSnapshot()` は {plants, fruits（位置・段階・ベスト・金）, flies, bugs, corpses, basket{x,y,tilt,sink}, rival{x,y,...}, crow{x,y,state,shadowX,shadowY}, sky, weather, raining, fever, groundY} を返す。3D側は株を `plantMeshes`（id管理）で保持し、実は段階が変わった時だけメッシュを作り替える。落果・潰れの汁は corpse 自身の y に出す。
- **パフォーマンス**：実（最大60個×約3.8万面）と株は影を落とさない（影マップ512px、カゴと地面のみ）。ピクセル比は最大1.5。実モデルの更なる削減は表面の水滴ディテールが壊れるため見送った（検証で確認）。
- **検証フック（v7）**：`__state`（plants/fruits/flies/bugs数・全実の段階・crow状態・カゴ/ライバル座標を追加）、`__forceStage`（全実の g を段階プリセットに＋傷み解除）、`__pickFirst(stage)`、`__fruitPos(stage)`、`__corpses()`、`__freezeCorpses()`、`__fruitG()`、`__stepGame(sec)`（rAFを介さず update を直接進める。低速な検証環境での時刻駆動用）、`__crowNow` / `__bugNow`（出現カウントダウンを0にする。出現ゲート＝2日目相当はそのまま）、`__setBasket(x,y)`、`__setBasketSpeed(v)`、`__setWeather(w)`、`__setVsT(s)`。

## v8の追加モジュール（磨き込み）
- **表情ステート**：ロジック側に `pHappyT / pSadT / rHappyT / rSadT` のタイマーを持ち、`playerFace() / rivalFace()` が happy / sad / panic / normal を返す（panic はカラスが warn/dive 中のとき）。スナップショットに basket/rival の速度（vx, vy）と face を追加し、3D・2D 両描画が同じ表情を使う。
- **3Dキャラ**：`makeFarmer3D()` が脚（股関節ピボット）・胴・腕（肩ピボット）・頭（目・眉・口3種・汗・麦わら帽子）をプリミティブで組む。ライバルは色替え＋髭＋片眉上げ。`animChar()` が速度から走りサイクル（脚振り・上下動・傾き）を駆動し、表情で腕・頭・口・汗を切り替える。カゴはキャラの子要素として前方に持たせ、傾き・沈みは v7 と同じ値をカゴ側に適用する（判定は不変）。カゴの正規化スケールは `multiplyScalar` で相対変更すること（`setScalar` で上書きすると消える）。
- **リング**：ロジックの `ripeFrac()`（完熟窓の残り割合）と `ringKind()`（ripe/best/gold）をスナップショットに載せ、3Dは5%刻みの円弧テクスチャ（Canvas 生成・キャッシュ）を平面に貼って実の前面に表示。2Dは `drawFieldFruit` 内で arc を直接描く。
- **ラッシュ**：`rushNext / rushWarnT / rushT / rushCount` のスケジューラを `update()` に追加。開始時に未熟な実の約8割へ `rushBoost` を付け、`updateField` の成長を ×3.4 にする（完熟到達で解除）。予告は `say()`＋専用表示、ラッシュ中はピル表示と実の金発光（3D emissive / 2D きらめき）。
- **収穫FX**：`pickFruit` で葉パーティクル（2D `parts` の kind:"leaf"）と 3D 向け `fxQueue`（スナップショット経由で消費されるワンショット）を発行。3Dは共有ポイントプール（260個）で葉と星を描く。ベスト収穫は飛行中の実に金の火花、着地で星バースト（`corpses.best` をスナップショットに追加）。効果音は `sfx.pick(combo, best)` がコンボ数でペンタトニックを上がる。
- **ライバルの吹き出し**：`rivalSay(key)` が `STR` のセリフ群（gonStart/gonScore/gonPlayerBest/gonSquash/gonPlayerSquash/gonLeadWin/gonLeadLose/gonOvertake）から引き、クールダウン付きで `rivalBubble / rivalBubbleT` をセット。描画はオーバーレイ Canvas の `drawRivalBubble()`（3D/2D 共通）。リード交代は `versusLeadCheck()` が検出し `vsLeadFxT` でバーを発光させる。
- **HUD**：対戦の金額表示を `drawVsBars()` のバー2本（上部常時表示、リーダーは★と発光）に変更。残り10秒は中央上に大きなカウントダウン。コンボ表示は `comboPopT` でバウンスし、コンボ数で少し大きくなる。日開始バナーは中央の大文字をやめ、上部のピル（対戦時はバーの下）に変更。
- **畑の配置**：ROWS を {250, 0.66} / {390, 0.80} / {532, 0.95} に、株の横間隔を 118 に調整し、実とキャラの重なりを減らした。

## v9の追加モジュール（広い畑＋リプレイ性）
- **ワールドとカメラ**：論理幅 `WW=1680`、区画幅 `SEC_W=420`。`camX` はカゴ位置＋速度先行（±120px）へ指数追随（dt×5.5）。入力（タップ/ドラッグ）は画面座標＋camX でワールド座標に変換する。描画は2D/3Dともカメラ分だけ平行移動し、HUD・バナー・出荷箱は画面座標のまま。
- **区画の構築**：`buildField()` が区画ごとに `sections[i] = {kind: plot|wild|empty, vid}` を作り、plot は畑の区画iの品種、wild は植え付け2未満のときの自生枠（`buildField` と `fieldVarieties()` で同じ規則を共有）。株は区画あたり3畝×3株。目印小物は `fieldProps`（看板/木/小屋/池/カカシ）で、3Dは `buildProps()` がスナップショットの `fieldKey` 変化時に組み直し、2Dは `drawProp2D()` が描く。
- **カリング**：`h3dSnapshot()` は camX±(480+190) の範囲外の株・実・害虫・残骸を除外し、3D側は見えない株メッシュを `visible=false` にする。背景の装飾畝もカメラ範囲だけ表示する。地面・空の平面はワールド幅に合わせて拡張した。
- **画面外インジケータ**：`drawIndicators()`（オーバーレイCanvas、3D/2D共通）が、画面外の完熟/ベスト実を優先度（金・ベスト＞完熟、残り時間の短い順）で片側最大3つまで端に表示。ラッシュ対象区画と対戦中のライバルの方向印も同じ場所に出す。ライバルの吹き出しと名前ラベルはカメラ座標に追随し、画面外では隠す。
- **区画ラッシュ**：スケジューラが予告時点で `chooseRushSection()`（未熟な実が最も多い非空区画）を選び、`rushSection` と区画名を保持。`startRush()` はその区画の実・株だけを加速する。
- **お金と注文**：完熟/金/過熟の出荷で `farm.money` に出荷額が入る（スコアと同額）。`orderProgress(variety)` が完熟・金の出荷で注文を進め、達成時に報酬（お金、場合により `farm.careBonus`）を払う。世話ボーナスは翌朝の `enterFarm()` で世話ポイントに加算される。注文・挑戦は `ensureDailyOffers()` が朝（dayKey=season×100+day の変化時）に再生成する。
- **道具**：`farm.tools = {shoes, basket, scarecrow, fert}`（各0〜3）。効果は `basketMaxSpeed()` / `crowMul()` / `fertMul()` と潰れ確率・タップ半径への補正として一元化し、`SHOP_ITEMS` の価格表で `buyTool()` が購入を処理する（畑画面と検証フックで共用）。
- **挑戦**：当日のカウンタ（daySquash/dayBest/dayGold/dayRushPicks/dayCrowStolen/dayMaxCombo）を収穫・カラス処理で積み、`checkChallengeLive()`（即時達成）と `evalChallengeEndOfDay()`（日クリア/品評会時に判定）で `completeChallenge()` が報酬を払う。結果行は dayStats / endStats に追加される。
- **周回強化**：ライバルの速度・取り間隔・判断ミス率は `season` でスケーリングする（`updateRival` 内）。ノルマは従来の通算日スケーリングのまま。
- **検証フック（v9）**：`__camX` / `__sections` / `__money` / `__setMoney` / `__tools` / `__buyTool` / `__orders` / `__challenge` / `__setChallenge` / `__dayStats9` / `__seasonClear` / `__nextSeason` / `__enterFarm` を追加し、`__state` に camX・money・tools・orders・challenge・day9 を含めた。

## v10の追加モジュール（全画面3D化）
- **メニューシーン（プレイ用GLの再利用）**：`window.__h3dScene(name, dt, snap)` が title/story/result/ending を描く。プレイ用の畑・株・キャラ・小物をそのまま使い、プレイ物（実・リング・カラス・雨）は `setPlayVisible(false)` で隠す。メインスクリプトの `frame()` が mode からシーン名を決め、`sceneSnapshot()`（空・話者・収穫数・対戦結果・品評会結果・天気）を渡す。対象画面のベールは `.veil3d`（薄く・ぼかし無し）で3Dを見せる。キャラは `ensureChars()` で共有化し、じいちゃん（charG）を追加。タイトルはカメラ緩スウェイ＋挨拶/収穫モーション、物語は話者の頷き（セリフ番号の変化で発火）、結果は出荷箱への実の落下スタック（`CRATE_SLOTS`、収穫数と連動、箱はボタン回避のため左寄せ）、エンディングは台座＋金のトマト回転＋星屑。
- **畑ビュー（2台目のレンダラー、#farmGl 440×300）**：2×2区画を少し上から見る。土メッシュへのレイキャストで `window.__farmPick(i)`（＝カードクリックと同一処理）を呼ぶ。`window.__farmViewData()` のJSONキー変化で再構築：株クローン（段階で高さ、未水やりで傾き＝しおれ）、実の球、空き地の雑草、カカシ、畝と十字の土道、選択枠（金・点滅）とマーカー。3Dが使えない場合は従来の区画カード表示に戻る。
- **図鑑ターンテーブル（3台目のレンダラー、#zukanGl 220×220・透過）**：`__h3dGuideSet({type,hue,scale})` で選択項目の実モデルを差し替え、`__h3dGuideFrame(dt)` で回転＋上下動。状態タブは状態モデル、品種タブは品種色の完熟モデル。
- **エンディング・トロフィー（4台目のレンダラー、#endGl 190×190・透過）**：金のトマトを回転表示。金属質感には**そのレンダラー自身のPMREM環境マップ**が必須（PMREMテクスチャはGLコンテキスト間で共有できないため、`envTex` の流用では金が黒く沈む。v10検証で発見・修正）。
- **読み込み量**：新規アセット無し。index.html は 237,912 → 268,928 bytes（コード増のみ）。models/ 3.8MB・vendor/ 828KB は不変。
- **検証フック（v10）**：`__farmViewData` / `__farmPick` / `__onH3dReady` を追加（既存フックは不変）。

## v11の追加モジュール（人間キャスト）
- **変換パイプライン**：FBX → FBX2glTF でGLB化（約80MB）→ Blender 4.2 で頂点結合（remove_doubles、メッシュ空間で dist=3e-6 ※モデルは cm スケールで実寸の1/100の座標系だった）＋ Decimate（Collapse、ratio 0.025 → 約49kポリゴン）→ gltf-transform でテクスチャ512px化＋meshopt圧縮（最終 約0.5MB/体）。配置先は `models/chars/{yuma,truckdriver,shopkeeper}.glb`。なお gltf-transform の simplify はこのメッシュでは約31万ポリゴンで頭打ちになった（頂点分割が原因）ため Blender 経由にした。
- **読み込み**：`CHAR_FILES = {trainee:'yuma', rival:'truckdriver', grandpa:'shopkeeper'}` を `loadJobs` とは別系統で読み込む（失敗しても3Dモード全体は落とさない）。`Promise.all(charJobs)` の完了で `charLoadsSettled=true`。`normalizeChar(root, 100)` は足裏を原点（y=0）に置き、高さ100単位に揃える（プリミティブ農家の背丈とほぼ同じ）。
- **生成**：`ensureChars()` は全ロールの読み込み確定後に一度だけ生成する。各ロールは人間モデルがあれば `makeHuman3D(proto)`、無ければ従来の `makeFarmer3D(...)`。カゴは `carry()` が役の種類に応じて位置を変えて付ける（人間: (0,27,25)、プリミティブ: (0,24,22)）。メニューシーンのカゴ位置リセットは `basket.userData.homeY` を使うよう変更。
- **演技**：`animChar()` の冒頭で `userData.human` を見て `animHuman()` に分岐（プリミティブ側は従来処理のまま）。`animHuman` はグループ変換には触らず（カゴ傾け・舞台演出は呼び出し側の領分）、内側のモデルにだけ回転・スケールを付ける：速度に応じた phase 進行で上下動（最大5）＋前傾（最大0.13 rad）＋左右揺れ＋着地スクワッシュ、happy は跳ね（最大9）、sad は前傾0.17＋沈み、panic は x 震え。待機中も呼吸の微動（sin 1.8Hz系、振幅0.7）が入る。
- **検証フック**：追加なし（既存フックで全行程を検証）。

## v12の追加モジュール（リグ＋3Dアイコン）
- **リグパイプライン**（`hidden_files/v12work/rig/`）：meshopt圧縮GLBを展開（gltf-transform）→ Blender 4.2で21ボーンのアーマチュアを構築（`build_rig.py`）。ウェイトは「骨セグメントへの測地的所属判定×解析ハット×放射ゲート」で割り当て、メッシュ表面グラフ上で3回平滑化し、浮いた頂点を最近傍ボーンへ付け替えるガード付き。Blender標準のボーンヒート自動ウェイトはこの密メッシュで全頂点0ウェイトに失敗したため不採用。ポーズはワールド軸回転で付け、5クリップ（idle/walk/harvest/happy/sad）を書き出し、meshopt再圧縮して `models/chars/` へ（0.60〜0.72MB/体、スキン＋5クリップ保持を確認）。**重要**：インポートしたメッシュオブジェクトの位置オフセット（z=0.49）をリセットしないとアーマチュアが体からずれて全ポーズが破綻する（切り分け済み）。
- **再生**：`charAnims[role]` にクリップを保持。`cloneSkinned()` が階層複製と同時にスケルトンを複製側ボーンへ張り替える（素の clone ではプロトタイプの骨を共有してしまう）。`makeHuman3D` はクリップがあれば AnimationMixer を作り `userData.rigged=true`。`animRigged` が harvestT > 表情（happy/sad/panic）> 移動（walk、速度でtimeScale）> idle の優先度で `playClip`（0.16秒クロスフェード、harvestはLoopOnce）する。収穫トリガーは葉パーティクルの発生時に最寄りのリグ済みカゴへ `harvestT=0.62` を立てる（`__h3dFrame` 内）。リグ済みキャラには全身演技を一切重ねない。
- **正規化の注意**：スキニング済みメッシュは `Box3.setFromObject` の前に `skeleton.update()` が必須（さもないと潰れたバウンディングボックスを読み、背丈がずれる）。`normalizeChar` はその順序に修正済み（実測：3体ともバインド姿勢で高さ0.981→スケール102.0、v11と同一）。
- **3Dアイコン**：顔専用の192×192オフスクリーンレンダラー（独自キャンバス＋独自PMREM。PMREMテクスチャはGLコンテキストを跨げない）を1つだけ持ち、話者ごとのバスト（cloneSkinned＋独自ミキサーでidle再生、rotation.yの小さな揺れ）を遅延生成。`__h3dFaceFrame(dt, mode)` が画面→キャンバスの対応（title/story/farm/dayclear/over/ending）で描き、表示側の96×96キャンバスへ毎フレーム転写する。ストーリーの話者は `renderStoryLine` が `window.__faceWho` に設定。WebGL不可・モデル未着なら何もせず2D描画が残る。
