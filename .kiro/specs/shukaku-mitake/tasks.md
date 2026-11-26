# Tasks — Harvest Sort Patrol

- [x] Skeleton: playable core loop (spawn / catch / dodge / miss / game over)
- [x] Title + game-over screens, JP/EN toggle, best score
- [x] Public repository + GitHub Pages URL (milestone 2026-11-25)

## v2
- [x] Day stages + quota, crosswind, shipping rank (S/A/B/C)
- [x] Fever mode (10 combo, double points, effects)
- [x] Five tomato types with distinct art, farm background, sky changes by day
- [x] WebAudio SFX + mute, richer title / result screens

## v3
- [x] Season system: 7 days per season, ending screen, next-season continuation with rising difficulty
- [x] Tomato guide (zukan): 6 entries with real ripeness knowledge, "done" stamps, localStorage
- [x] Trophies: 8 achievements, toast notifications, list screen, localStorage
- [x] Special items: golden tomato (+score/+heart), pest bug (-heart), rain event (warning + faster falls)
- [x] Shipping crate visualization stacking harvested tomatoes
- [x] BGM: WebAudio loop, shares the mute toggle
- [x] Farmer mascot with per-day speech-bubble tips on title / day-clear / result / ending screens
- [x] Difficulty select (easy / normal / hard)
- [x] Records saved (best score, best reach, seasons cleared) and shown on title
- [x] Extra effects: squash-pop on catch, confetti on day clear, big celebration on season clear
- [x] Specs + README updated to v3
- [x] Playwright verification of all screens and events

## v5 (story / growth / breeding)
- [x] Story: trainee protagonist + grandfather, opening dialogue, goal = win at the autumn harvest fair
- [x] Grandfather's daily farm-screen lines (hints + story), event reaction lines (rain / first fever / first cross / golden tomato)
- [x] Harvest fair ending: best variety entered, judged by variety total + season performance, win/prize/participant results with matching dialogue
- [x] Field screen between days: 4 plots, plant seeds, growth stages (seed → sprout → young → fruiting)
- [x] Care: 3 care points/day, watering (+2 growth) and fertilizer (+1 growth, +1 quality); rain waters everything
- [x] Fruiting plants boost their variety's drop rate, ripe rate and score (quality); revert to young after a harvest, wither after two
- [x] Weather system: per-day weather with next-day forecast; rainy days rain all through the harvest, cloudy days keep the shower event
- [x] Varieties: parameters (sweetness / size / hardiness 1–5), 3 starter varieties, seeds drop from ripe catches (38%)
- [x] Crossing: consume 2 seeds, child = parent average ± variation with occasional mutation, auto-generated names, generations tracked
- [x] Variety visuals: fruit size and ripe hue follow the variety; ripeness sorting rules unchanged
- [x] Guide split into two tabs: ripeness guide + variety pages (parameters, generation, parents, harvested stamp)
- [x] Trophies expanded to 14 (added planting / seed / fruiting / crossing / fair prize / fair champion)
- [x] Run state persisted (season, day, score, weather, care points) with Continue on the title screen
- [x] Specs + README updated to v5
- [x] Playwright verification: full loop (intro → plant/care → cross → fruiting → rainy day → 7 days → fair → season 2 carry-over → game over → reload restore), JA + EN, screenshots in files/v5-verify/

## v6 (real-time 3D / versus / crow)
- [x] Tripo GLB models decimated (weld → simplify → 512px textures → meshopt): 8 models, ~62MB → 0.4–0.6MB each (3.7MB total), visual check of every model
- [x] Three.js vendored locally (module + importmap); gold material fixed with a room environment map
- [x] Harvest scene in real-time 3D: plant rows as a tomato farm, soil/grass ground, sky gradient + fog, weather lighting, 3D rain streaks, real shadows
- [x] Physics motion: gravity acceleration (bigger varieties fall faster), spin, ground bounce & roll, catch squash-and-recover, basket tilt/sway/sink
- [x] Rim rule: ripe fruit hitting the basket rim squashes (flatten + juice particles + SFX + reason text, counts as a miss); hardy varieties can slide in instead
- [x] Breeding wired into play: size = fall speed & value, sweetness = fever charge rate, hardiness = rim survival; effects printed on the guide's variety pages
- [x] Crow enemy: shadow + caw warning, dive, steal (combo break) or knock-down; pest bug as a 3D model
- [x] Versus mode: rival farmer AI in the same field, 60-second shipped-value match, both totals + clock on the HUD, win/lose on the day result, match record saved
- [x] Harvest fair = final match vs the rival; fair judging includes season match wins and the final result
- [x] Fixed a latent v5 bug: guide tabs shared the difficulty-button class and corrupted the difficulty value (crash on the next harvest)
- [x] 2D fallback kept: if the 3D layer fails to load, the v5 renderer runs with identical rules
- [x] Specs + README updated to v6
- [x] Playwright verification: models, title/story/farm/breeding, normal + rain harvests, rim squash & hardy save, versus scoring & result, crow warning/dive, day-7 final → fair ending, game over (screenshots in files/v6-verify/)

## Later
- [ ] Upload to GitHub and refresh the public page
- [ ] Re-make the demo video

## v7 (pick from the plants — harvest core rebuilt)
- [x] Backup v6 as index-v6-backup.html
- [x] Field system: 3 rows x 4 plants, up to 6 fruit slots per plant, fruit grows on the vine (sprout -> unripe -> half -> ripe -> overripe -> drops and squashes)
- [x] Picking: tap a fruit (near-only hit test, no move/pick conflicts on touch), fruit arcs into the basket; basket moves in 2D (drag / click / arrow keys)
- [x] Squash rule rebuilt: decided at pick time from distance + basket speed + rain slip, resisted by hardiness; standing still and close never squashes
- [x] Best window (1.5x + combo), overripe ships cheap, quota / day results / rank wired as before
- [x] Fever reworked: best/ripe picks charge it; active fever doubles gains and speeds field growth
- [x] Golden fruit grows on the vine (heart recovery, never overripens)
- [x] Pest bugs settle on a plant: growth paused, fruit damaged every few seconds; tap to shoo
- [x] Crow: warning shadow at the target plant + caw + text, dives for a ripe fruit on the vine, carries it off; tappable to scare away
- [x] Versus rebuilt as a real scramble: rival picks from the same plants in the same field (2D AI, own squash chance); day 7 fair final reconnected
- [x] Breeding reconnected: size = bigger, pricier fruit; sweetness = wider best window + faster fever; hardiness = longer ripe window + squash resistance; guide variety pages show the numbers
- [x] Weather reconnected: rain = faster growth + slippery picks; wind = swaying plants only
- [x] Plant model recolored (baked red fruit -> green) so only the game's fruit shows ripeness
- [x] 3D layer rebuilt for the field snapshot; fruits/plants cast no shadows, 512px shadow map, pixel ratio capped at 1.5 (keeps the scene light)
- [x] 2D fallback rebuilt to the same pick-from-plants rules
- [x] Specs + README updated to v7
- [x] Playwright verification of the full flow (files/v7-verify/): growth stages, best pick, squash + reason text, overripe falls, crow warn/steal, pest damage, rain, versus + result, day-7 fair ending, 2D fallback

## v8 (polish — looks fun at a glance, feels good to pick)
- [x] Backup v7 as index-v7-backup.html
- [x] Characters: player + Gon built from 3D primitives carrying their baskets (run cycle, lean, bob), happy hop on catches, droop on misses, panic sweat/shake at the crow; smug Gon with beard + raised brow; matching 2D-fallback characters
- [x] Rival speech bubbles (start / scoring / player's best / squash / lead changes) with cooldown, drawn on the overlay canvas
- [x] Ripeness countdown rings on ripe/best/gold fruit (green -> yellow -> red, blinking under 25%, gold + sparkles for best), in 3D (arc textures) and 2D (canvas arc)
- [x] Ripening rush: telegraphed wave, up to twice a day — most unripe fruit surges toward ripe for ~6s; pill indicator + gold glow; versus days share the wave
- [x] Pick feel: leaves burst off the plant on every pick (2D particles + 3D point pool), best picks sparkle in flight and burst stars in the basket, combo jingle climbs a pentatonic ladder, combo counter bounces and grows
- [x] Field layout: wider plant spacing and slightly smaller rows for readability; day-start banner replaced by a slim top pill
- [x] Versus presentation: both shipped-value bars always on top, leader glow on lead changes, big countdown for the last 10 seconds
- [x] Fixed: basket scale was overwritten with setScalar and vanished — now multiplyScalar relative to the normalized proto scale
- [x] Specs + README updated to v8
- [x] Playwright verification (files/v8-verify/, 31 screenshots): run/happy/sad/panic faces, Gon bubble + reactions, rings (normal/blinking/gold), rush warn/wave, versus bars + countdown, fair ending, 2D fallback — JS errors 0 (favicon 404s only). Fixed during verification: warning text legibility (backing pill added)

## v9 (wide scrolling field + replayability)
- [x] Backup v8 as index-v8-backup.html
- [x] Wide field: world width 1680 (3.5 screens), 4 sections x 9 plants (36 total), paths between sections, smooth camera with velocity look-ahead
- [x] Farm-screen link: field section i grows farm plot i's variety; unplanted sections are empty weedy land (grandpa's volunteer rows keep a fresh farm playable)
- [x] Landmarks per section (numbered signs, trees, hut, pond, scarecrow prop) with per-section soil tint, in 3D and in the 2D fallback
- [x] Off-screen indicators: edge arrows for ripe/best fruit (gold = best, red = ripe, blinking when time is short), rival direction in versus, rush-section direction
- [x] Section rush: the warning names the section ("...で完熟ラッシュ予告！"), the wave boosts only that section
- [x] Crow flies in across the sky to the target section; Gon roams the whole field off-camera; bugs stay per-plant
- [x] One minimal balance change, reported: an overripe drop outside the camera view costs no heart (in-view drops unchanged); quotas/time/judgements unchanged
- [x] Culling: snapshot filters off-camera plants/fruit/bugs/corpses; 3D hides culled plant meshes and backdrop rows
- [x] Market orders: 1-2 every morning, targets always grow in today's field; progress on the farm screen and the harvest HUD; completion pays money (+ sometimes care points)
- [x] Tool shop on the farm screen: fast shoes / big basket / scarecrow / good fertilizer, 3 levels each, kept across seasons
- [x] NG+: next season keeps varieties, guide, tools and money; quotas rise as before and Gon gets faster, quicker and sharper each season; season number shown
- [x] Daily challenge: one small condition per day, progress on the farm screen, result shown on the day/fair result screens, reward money + a seed
- [x] Back-compatible saves: money/tools/orders/challenge default in when loading a v8-or-older save
- [x] Specs + README updated to v9
- [x] Playwright verification (files/v9-verify/, 25 screenshots): sections L/C/R, run sequence with camera follow, edge indicators both ways, section-rush warning, versus rival indicator, empty section, day-clear + fair ending with challenge/money rows, order completion with exact reward payment (delta == reward), shop purchase, NG+ carry-over, 2D fallback (frames animate, no errors), old-save load. Fixed during verification: drawProp2D roundRect crash in 2D mode, orders could target varieties not growing that day, money HUD hidden under the EN button, props floating in the sky band, backdrop-row culling

## v10 (3D on every screen — one unified 3D look)
- [x] Backup v9 as index-v9-backup.html
- [x] Title: 3D farm backdrop, both farmers idling (wave / pick mime), slow camera sway, DOM title/buttons on top
- [x] Story: 3D farm backdrop, speaker character (Grandpa added as a third primitive character), nod on line advance, dialogue panel unchanged
- [x] Farm management: 3D top-down field view (own renderer) replaces plot cards — tap a section to select (gold wash + marker), planted sections grow that variety's plants, growth stage and watering (wilt) reflected; selection/planting/care go through the exact same code path as the cards; ridges + dirt paths so it reads as a field
- [x] Field guide: turntable (own renderer) shows the selected state's / variety's 3D fruit rotating; list and selection highlight unchanged
- [x] Result screens: evening 3D farm, caught tomatoes drop one by one and mound on the shipping crate (count-linked), crate placed clear of the DOM button, versus faces by result
- [x] Fair ending: evening scene behind + rotating gold-tomato trophy inside the panel (own renderer, own PMREM environment — a shared PMREM texture renders black across GL contexts)
- [x] 2D fallback intact everywhere (farm cards return, turntable/trophy hidden, no errors)
- [x] No new assets: load size unchanged apart from +31KB of code in index.html
- [x] Specs + README updated to v10
- [x] Playwright verification (files/v10-verify/, 34 screenshots): 3D title, story speaker swap, guide turntable (state + variety), farm view select/plant/water/grow, play, day result crate pile (count-checked against the scene state), versus result, ending trophy, season-2 farm, 2D fallback — JS errors 0 (favicon 404s only). Fixed during verification: menu-camera height so the cast clears the buttons, farm marker projected off-canvas (lowered to hover over the soil), crate pile hidden inside the basket (restack on the rim, wider slots, crate moved clear of the button), trophy gold rendering black (per-renderer PMREM). Note: menu/result scene clocks run on game time, so in the SwiftShader verification environment (~1fps) the crate pile takes ~40 real seconds to finish landing; on a real GPU it lands in about 2 seconds.

## v11 (a human cast — real character models replace the primitive farmers)
- [x] Backup v10 as index-v10-backup.html
- [x] Survey all 8 starter-pack FBX characters: ~60MB each, Tripo-generated static meshes (~2M tris), **no rigs, no animation clips** in any of them (checked via FBX node types and post-conversion GLB skin/animation inspection); rendered previews of every model and judged them on screen
- [x] Cast: trainee = yuma (young man), Grandpa = shopkeeper (elderly, glasses, apron), Old Gon = truckdriver (burly worker in blue coveralls — middle-aged, not elderly; the only rival-shaped body in the set)
- [x] Conversion pipeline: FBX2glTF → Blender (position weld + decimate to ~49k tris) → gltf-transform (512px textures + meshopt) = 623KB / 510KB / 496KB in models/chars/ (gltf-transform simplify alone stalled at ~314k tris on these split-vertex meshes, hence Blender)
- [x] Integration: separate char loading that can never take the whole 3D mode down; per-role fallback to the primitive farmers; feet-at-origin height-100 normalization matching the old cast's footprint; baskets carried as before (slightly higher/further forward for the human bodies, home-Y reset honored in menu scenes)
- [x] Whole-body acting for the boneless models (animHuman): bobbing stride + forward lean + sway + landing squash, happy hop, sad droop, panic shake, idle breathing; group transforms (basket tilt, stage directions) untouched; primitive path unchanged
- [x] Specs + README updated to v11
- [x] Playwright verification (files/v11-verify/, 15 screenshots): title cast, story speaker swap (Grandpa/trainee), farm, harvest idle + running drags + pick/rush, versus play with both characters (rival name tag + speech bubble), day result, versus result (both cast members), fair ending, plus a dedicated error probe — JS errors 0 across title/story/farm/play (incl. crow event) and across farm/versus/result/ending. Note: the first verification run was killed mid-versus by a runtime restart; the remaining screens were re-verified in a second run (errors []) and the error probe in a third. Honest limits: the models are stylized (not photoreal) and sit next to photoreal fruit; no facial-expression changes (faces are baked) and no limb animation (no bones); on the fair-ending screen the DOM panel covers the cast, exactly as in v10; real-GPU smoothness still unverified (SwiftShader only).

## v12 (bones for the cast + 3D speaker icons)

- [x] Backup: index-v11-backup.html created before any edit
- [x] Rig pipeline: decompressed the meshopt GLBs, built a 21-bone humanoid armature per model in Blender 4.2; analytic weights (geodesic membership x analytic hats x radial gate), 3 passes of surface-graph smoothing, stray-vertex guard. Root cause of early deformation artifacts found: the imported mesh object carried a (0, -0.0001, 0.49) location offset that left the armature half a body below the mesh — fixed by zeroing the mesh location after transform apply. Blender bone-heat auto-weights rejected (0 weighted verts on these dense meshes)
- [x] Five clips per model baked into the GLB: idle / walk / harvest / happy / sad; posed renders eyeballed per model (walk leg+arm swing, harvest reach, sad bow all read clearly; happy arm raise reduced to ~-105 deg to limit sleeve artifacts)
- [x] Re-compressed with meshopt: yuma 720 KB / shopkeeper 614 KB / truckdriver 599 KB (target <=1.5 MB each), each verified to keep 1 skin + all 5 animations
- [x] Game integration: clips stored per role, cloneSkinned() for per-instance skeletons, AnimationMixer playback with state priority (harvest > emotion > walk > idle), harvest triggered from the leaf-particle fx, whole-body animHuman disabled for rigged characters (no double motion); basket position/judging untouched
- [x] normalizeChar fix: skeleton.update() before Box3 measurement (skinned meshes otherwise measure a collapsed box); in-game logs confirmed identical normalization to v11 for all three models
- [x] 3D speaker icons: one shared 192px offscreen renderer + per-role busts (idle clip + gentle sway), wired to title/story/farm/dayclear/over/ending icon canvases, story speaker follows the dialogue (grandpa/trainee); 2D faces remain as fallback when WebGL/models are unavailable
- [x] Specs + README updated to v12
- [x] Playwright verification (files/v12-verify/, 26 screenshots): title + 3D icon, story with both speakers + trainee icon, farm + icon, play walk/harvest, day-clear (happy) + icon, versus (both rigged characters), day-7 final battle, harvest-fair ending + ending icon, plus isolated rig renders of walk/harvest/happy/sad for all three models — JS errors 0 in every run (verify1-6). Honest limits: in the happy pose the sleeve lining and the shopkeeper's apron hem can show as cloth-like flaps beside the raised arms (inherent to the source Tripo mesh layering; mitigated by the reduced raise angle) and fingers stretch slightly; small dark spikes can appear at shoulders/shoes in extreme poses; real-GPU smoothness still unverified (SwiftShader ~1-4 fps in this environment)
