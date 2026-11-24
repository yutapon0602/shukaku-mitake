# Design — Harvest Sort Patrol

## Structure

- `index.html` — the whole game: CSS-styled overlay screens (title / game over)
  on top of a 480x720 Canvas. All sprites are drawn with Canvas paths and
  gradients (no image assets), so the file stays self-contained and crisp
  at any size.

## Visual direction (Costume Contest = UI polish is judged)

- Warm farm scene: sky gradient, sun, green field rows, soil strip.
- Wooden signboard title treatment; cream panels; red CTA buttons.
- Tomatoes are visually distinct at a glance: glossy red (ripe), bright
  green (unripe), brown with spots and a crack (damaged).

## Core loop

`spawn -> fall (with sway) -> catch check at basket rim / ground check ->
score, combo, miss, feedback tip -> difficulty ramp`

Feedback text is the teaching layer: each event surfaces one short,
real sorting rule so players learn the job while playing.

## Controls

- Pointer (mouse/touch): basket follows with easing.
- Arrow keys: nudge + hold-to-move.
