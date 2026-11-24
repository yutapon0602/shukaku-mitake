# Harvest Sort Patrol — しゅうかく見分け隊

A tiny harvest-sorting game built for **Kiroween 2026 (Costume Contest)** with Kiro.

Tomatoes fall from the sky. Move the basket and catch **only the ripe red ones**.
Let the green (unripe) and damaged ones fall — dodging them is the right call.
Every catch teaches a real ripeness sign used on the farm: deep red color, gloss,
and red all the way to the stem.

This is the first prototype of the agriculture module in Ap Robo's training-game
series for foreign technical trainees and new farm workers: learn the job in a
game first, then practice it in the field.

## Play

- Mouse / touch / arrow keys move the basket
- Ripe red tomato: catch it (+points, combos build bonus points)
- Green or damaged tomato: dodge it. Catching one — or dropping a ripe one — costs a heart
- 3 misses and the harvest ends
- Japanese / English toggle in the top-right corner

## Tech

- Single-file HTML5 Canvas game (`index.html`), no dependencies, no build step
- Works on desktop and mobile browsers

## Run locally

Open `index.html` in a browser, or serve the folder:

```sh
python3 -m http.server 8000
```

## License

MIT — see `LICENSE`.

## About

Ap Robo Educational LLC (Okayama, Japan) — educational training games and an
AI learning-and-communication platform for foreign technical trainees.
https://ap-robo.com
