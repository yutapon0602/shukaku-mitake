# Requirements — Harvest Sort Patrol （しゅうかく見分け隊）

Kiroween 2026 / Costume Contest entry. First prototype of the agriculture
module in Ap Robo's training-game series.

## Gameplay requirements

1. Tomatoes fall from the top of the screen in three types:
   ripe (red), unripe (green), damaged (brown/spotted).
2. The player moves a basket horizontally (mouse, touch, arrow keys).
3. Catching a ripe tomato scores points; consecutive catches build a combo
   that adds bonus points.
4. Catching an unripe or damaged tomato, or letting a ripe tomato hit the
   ground, counts as a miss. Three misses end the game.
5. Letting an unripe or damaged tomato fall is a correct dodge.
6. Every catch or miss shows a one-line, real-world sorting tip
   (ripeness signs: color, gloss, red to the stem).
7. Difficulty ramps: fall speed and spawn rate increase over time.
8. Title screen with a visual legend, game-over screen with score,
   per-type counts, best score, and a closing sorting tip.
9. Japanese / English UI toggle.
10. Single-file HTML5 Canvas, no dependencies, works on mobile and desktop.

## Non-goals for the skeleton milestone (2026-11-25)

- Sound effects / BGM
- Online leaderboard
- Multiple stages or crops (tomato only for the contest build)
