# Harvest Sort Patrol — しゅうかく見分け隊

A tomato-growing, picking and breeding game built for **Kiroween 2026 (Costume Contest)** with Kiro.

You are a farm trainee. Your grandfather has entrusted you with his field:
grow tomatoes good enough to win a prize at the **autumn harvest fair**.

Tomatoes do **not** fall from the sky — they grow on the plants, in a
**real-time 3D** tomato field. Each fruit ripens on the vine:
small and green → unripe → coloring → **ripe** → overripe → it drops off
the vine and squashes on the ground. You and your rival are little 3D
farmers who **carry their baskets through the rows** — legs pumping as
they run, faces reacting to every pick. **Tap the ripe ones to pick
them** — leaves flutter off the plant and the fruit arcs through the air
into your basket. Pick too early and the fruit cannot be shipped; pick in
the short **"best" window** right after ripening for bonus points and
combos. A **countdown ring** around every ripe fruit shows how long it
will stay good — green, then yellow, then red and blinking — and a gold
ring marks the best window. Every pick teaches a real ripeness sign used
on the farm: deep red color, gloss, and red all the way to the stem.

Between harvests, work the **field**: plant seeds, water and fertilize your
plants, and **cross two varieties** to breed new ones — sweeter, bigger,
hardier. A season lasts **7 days**; on the last day, the harvest fair is decided
in a **final picking match against your rival farmer** in the same field,
and your finest variety is judged.

This is the first prototype of the agriculture module in Ap Robo's training-game
series for foreign technical trainees and new farm workers: learn the job in a
game first, then practice it in the field.

## What's new in v12 — bones, and 3D faces in the speech bubbles

Two things the v11 cast could not do are fixed:

- **The characters have skeletons now.** Each human model was rigged in
  Blender with a 21-bone humanoid armature and hand-tuned weights, and
  carries five bone-animated clips baked into the same GLB: **idle**
  (breathing sway), **walk** (legs and arms swing, sped up when running),
  **harvest** (one arm reaches out to pick), **happy** (both arms up) and
  **sad** (head bowed, arms hanging). The game drives them with
  AnimationMixer: walk while moving, harvest the moment a pick lands,
  happy/sad on the result screens. The old whole-body acting is switched
  off for rigged characters, so nothing double-moves. Models are
  0.60–0.72 MB each including the animations.
- **The little character icons next to speech bubbles are 3D too.** The
  flat straw-hat faces on the title screen, the story, the farm screen
  and the result screens are replaced by a small dedicated 3D view that
  shows the actual speaker's model (head and shoulders, gently swaying,
  idle clip playing) and switches between Grandpa and the trainee as the
  conversation turns. If WebGL or the models are unavailable, the old 2D
  faces remain as the fallback.

Rules, judging, saves and the language toggle are unchanged.

## What's new in v11 — a human cast

The little farmers built from cylinders and spheres are gone. The cast is
now played by **real human character models** (from the MegaJam starter-pack
cast, decimated from ~2M triangles to ~49k each and packed at ~0.5 MB per
model in `models/chars/`):

- **You, the trainee** — a young man in a varsity jacket.
- **Grandpa** — a grey-haired old hand with glasses and an apron.
- **Old Gon, the rival** — a burly working man in blue coveralls and a cap.

The models have no bones, so they act with their whole body: a bobbing
stride and a forward lean as they run, a happy hop, a sad droop, a panic
shake when the crow dives — and they still carry the same baskets, in the
same places, on every screen (title, story, field, results, fair). If a
character model fails to load, that role quietly falls back to the old
primitive farmer. Rules, judging, saves and the language toggle are
unchanged, as is the 2D fallback.

## What's new in v10 — 3D everywhere

Until v9 only the harvest itself was 3D; every other screen was a flat
panel. v10 brings the same 3D farm and the same little farmers to **every
screen**, so the whole game looks like one place:

- **Title**: the 3D tomato field behind the title, with you and Old Gon
  standing in it — waving, miming a pick — while the camera sways gently.
- **Story**: Grandpa and the trainee appear in the field as 3D characters;
  whoever is speaking nods along as the lines advance.
- **Farm management**: the abstract plot cards are gone. You now look down
  at your actual 3D field — tap a section to select it (it lights up gold),
  planted sections grow that variety's plants, and watering shows: a dry
  plot's plants wilt, a cared-for plot stands tall. Orders, the daily
  challenge, the shop and your money stay as readable panels at the side.
- **Field guide**: pick any entry and its fruit slowly rotates on a 3D
  turntable above the list — every ripeness state, every variety.
- **Results**: at day's end the field turns to evening, and the tomatoes
  you picked drop one by one onto the shipping crate and mound up — the
  pile is exactly as big as your catch. After a match, both farmers stand
  by with winner's and loser's faces.
- **Harvest fair**: evening field behind the results, both farmers on
  stage, and a **golden tomato trophy** turning inside the panel itself.
- Nothing about the rules changed — same judging, same saves, same
  Japanese/English toggle — and if WebGL is unavailable, every screen
  falls back to the v9 2D presentation, cards and all.

## What's new in v9 — a wide field, and reasons to come back

- **The field is now 3.5 screens wide.** The camera follows you as you run
  between **4 sections** (36 plants in all), each with its own soil tint and
  landmarks — numbered signs, trees, a hut, a pond — so you always know where
  you are. The field sections are the farm screen's 4 plots: plant a plot and
  its variety grows in that section; leave it empty and it stays weedy land.
- **You can't get lost**: arrows at the screen edge point to ripe and
  best-window fruit off-screen (gold = best, red = ripe, blinking when time
  is short), and to your rival during a match.
- **The ripening rush picks a section now** — the warning names it
  ("Ripening rush in Grandpa's rows!"), so the play is to sprint over there
  before the wave lands. The crow swoops in across the sky to its target
  section, and Old Gon roams the whole field, even off-camera.
- **Market orders every morning**: ship 8 of a named variety, or any 12 ripe
  tomatoes, for a cash bonus (sometimes care points too). Orders only ever
  ask for varieties actually growing in your field that day, and their
  progress shows on the farm screen and in the harvest HUD.
- **A little tool shop**: spend what you earn on fast shoes, a bigger
  basket, a scarecrow, or better fertilizer — three levels each, kept
  forever, across seasons.
- **A daily challenge** (clear the quota without a single squash, take 10
  in a rush, never lose one to the crow…) pays a small reward, and after
  the harvest fair the **next season** carries your varieties, guide,
  tools and money over — with higher quotas and a sharper, faster Gon.

## Play

- Drag (mouse / touch) or use the arrow keys to walk the basket along the rows;
  tap / click a fruit to pick it — tapping only works near a fruit, so moving
  and picking never clash on a touchscreen
- Fruit grows on the plants through 6 visible stages. **Ripe red** is the pick:
  +points, combos build bonus points; the short **best window** right after
  ripening pays 1.5× and charges FEVER (double points, and the field grows faster)
- **Pick gently!** A fruit picked from far away, or while you are moving fast,
  can *squash* on the basket rim and counts as a miss — stand still, get close,
  and it never squashes. Hardy varieties resist squashing
- Unripe / half-colored / damaged fruit cannot be shipped: picking one costs a
  heart. Overripe fruit ships cheap — and left on the vine too long it falls
  and squashes (also a miss)
- Golden fruit (rare, grows on the vine): pick it for a big bonus and +1 heart
- **Pest bugs** (from day 2): one settles on a plant, stops it growing and
  damages its fruit — tap the bug to shoo it away
- **Crows** (from day 2): a shadow, a caw and a warning come first, then the
  crow dives for a ripe fruit on the vine and carries it off — tap it in time
  to scare it away
- **Ripening rush**: once or twice a day, a telegraphed wave sweeps the field
  and most unripe fruit surges toward ripe for a few seconds — a picking
  frenzy (on match days, you and Gon fight over the same wave)
- **Rival matches**: on the field screen you can race Old Gon, the rival farmer,
  in the *same* field — he picks from the same plants, so ripe fruit is first
  come, first served. 60 seconds, highest shipped value wins. Both shipped
  values are always shown as bars at the top, Gon talks back in speech
  bubbles (gloating, groaning, taunting), and the last 10 seconds get a big
  countdown. Day 7 is the fair final against him
- Weather changes daily (check the forecast!): rain makes fruit grow faster,
  but slippery hands squash a little more easily; wind only sways the plants
- 3 misses and the harvest ends; clear day 7 to reach the harvest fair

## Grow & Breed

- **Field screen** between days: 4 plots. Plant seeds you earn from ripe picks (38% drop)
- Plants grow seed → sprout → young → **fruiting**. You get 3 care points a day:
  watering speeds growth, fertilizer raises fruit quality (★). Rain waters everything for free
- A fruiting plant makes its variety appear more often in the field, and care
  given on the field screen speeds up the whole field's growth and fruit count.
  After a harvest day the plant rests back to young; after two harvests it withers
- **Crossing**: spend 2 seeds to breed a new variety — parameters (sweetness /
  size / hardiness, 1–5) average the parents' with a little luck, and mutations
  happen. Names are auto-generated from the parents' strongest traits
- Variety parameters play directly in the picking game: **size** = visibly
  bigger fruit worth more points; **sweetness** = a wider best window and a
  faster FEVER charge; **hardiness** = fruit stays ripe longer on the vine and
  resists rim squashes. The guide's variety pages list each variety's exact effects
- The best variety you own is your entry at the fair — breed a champion!
- Difficulty select (Easy / Normal / Hard), a two-part field guide (ripeness +
  varieties), and 14 trophies to unlock
- Japanese / English toggle in the top-right corner

## Tech

- `index.html` holds the whole game (logic, 2D UI, and the 3D layer); no build step
- The picking scene is real-time 3D: Three.js (vendored in `vendor/`) renders
  tomato, basket and plant models generated with Tripo and decimated from
  ~62 MB to ~0.5 MB each (`models/`, ~3.7 MB total). Twelve field plants carry
  up to six growing fruits each; the plant model's own baked fruit was
  recolored green so only the game's real, pickable fruit stands out
- Physics-flavored motion: fruit arcs into the basket, squashes deform with
  juice particles, baskets tilt, sway and sink under the catch, weather
  changes the light and rain falls in 3D
- The cast are human character models (`models/chars/`, ~0.5 MB each,
  ~49k triangles): whole-body acting — a bobbing stride, leaning into a
  run, a hop of joy on a good pick, a droop on a miss, a shake of panic
  when the crow dives. Picking feels alive — leaves burst off
  the plant, best picks sparkle in flight and burst stars in the basket,
  and the pick jingle climbs a pentatonic ladder as your combo grows
- If WebGL or the models are unavailable, the game falls back to a 2D Canvas
  renderer with identical rules
- Sound effects and BGM are synthesized with WebAudio — no audio files
- Field, varieties, guide progress, trophies, match record and your in-progress season persist in `localStorage`
- Works on desktop and mobile browsers

## Run locally

Serve the folder (the 3D layer loads models over HTTP):

```sh
python3 -m http.server 8000
```

## License

MIT — see `LICENSE`.

## About

Ap Robo Educational LLC (Okayama, Japan) — educational training games and an
AI learning-and-communication platform for foreign technical trainees.
https://ap-robo.com
