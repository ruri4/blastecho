# BLAST ECHO

A game made by Gemini 4 Argon (one-shot prompt)

## Prompt

```
Build a complete, playable game as ONE self-contained HTML file (HTML + CSS + JS, <canvas>, no libraries, no external assets, no network requests). Output only the file in a single code block, with no commentary before or after.

GAME CONCEPT
A 2D side-view game. You pilot a small rocket that must reach the end of a narrow, randomly generated tunnel. The tunnel is too tight to fly through at first. When the rocket hits a wall it explodes, and the explosion carves a crater into the rock. On the next attempt your past selves (ghosts) replay their previous runs and explode at the exact spots where they originally died, re-carving the path ahead of you. Each death is an investment: you are digging the tunnel open one explosion at a time, and you must time your flight to arrive after your ghosts have blasted through.

CONTROLS
- One input only: tap/hold anywhere on the canvas (mouse, touch) or hold Space. Holding fires the thruster, releasing cuts it.
- It must feel smooth and rocket-like, not like Flappy Bird's instant flap: thrust ramps up and down over roughly 100-150 ms, thrust is applied along the rocket's nose direction, gravity is constant, velocity has light damping and a max speed clamp, and the rocket's rotation eases toward its velocity direction. Constant slow auto-scroll is not used; forward motion comes from the rocket's own physics, with the camera smoothly following the rocket horizontally.
- Draw a flame and exhaust particles whose size and intensity scale with current thrust.

TUNNEL AND TERRAIN
- Terrain is a destructible grid (e.g. Uint8Array with ~4px cells) rendered to an offscreen canvas, so circular craters can be carved cheaply and redrawn.
- Generate the tunnel from a seeded PRNG (mulberry32 or similar): a smooth winding centerline (layered noise) with varying width, rough cave-like walls, a clear start zone and a finish line at a fixed length (roughly 6-10 screen widths).
- Include several pinch points where the gap is narrower than the rocket, so the player cannot pass without carving. Place them so the first pinch is reachable in the first attempt.
- Collision: sample terrain cells at several points around the rocket's hull. Any hit means death.
- Crater radius is fixed (tune so 3-6 explosions open a pinch point), with a slightly jagged edge for a natural look.

DEATH, GHOSTS, AND RESET
- On death: explosion at the impact point (particles, flash, brief screen shake), carve the crater, record the run, then auto-restart after ~0.7 s.
- On restart: terrain resets to its pristine seeded state. All previous ghosts start replaying from t=0.
- Use a fixed-timestep simulation (e.g. 120 Hz accumulator) so runs are deterministic. Record the player's position and rotation every tick plus the death tick.
- Ghosts are intangible: no collision with the player and they ignore walls. Each ghost follows its recorded path, and at its recorded death tick it explodes and carves its crater at the recorded position, in sync with every other ghost.
- Ghost visuals: translucent rocket silhouettes with a faint trail, older ghosts more faded. Cap rendered ghosts at ~25 (older ones still simulate their craters but may be drawn fainter or hidden).
- Craters from ghosts must appear at the correct time, so the player can watch the path open and learn to time arrival.

PROGRESSION
- Reaching the finish line clears the tunnel: show a short success screen with attempts used and best (fewest attempts), then generate a new random tunnel with a new seed and wipe all ghosts.
- Keep the best attempt count in memory for the session (do not use localStorage).

UI AND FEEL
- Minimal HUD: attempt number, number of ghosts, a thin progress bar showing distance to the finish, and a marker on it for where the furthest ghost died.
- Start screen with the title and "Tap to launch". The first input begins the run.
- Responsive full-window canvas, handle devicePixelRatio, prevent scrolling and zoom on touch (touch-action: none), pause when the tab is hidden.
- Cohesive stylized look using flat shapes and gradients: dark rock walls with a lighter edge highlight, a glowing rocket, warm explosion colors. No emojis anywhere.
- Optional WebAudio synthesized sounds (thrust hiss, explosion boom) with a mute toggle, starting only after the first user input.

QUALITY BAR
- Runs at 60 fps on a mid-range phone. Avoid per-frame allocations in hot loops and redraw only the visible terrain region.
- Clean, commented, well-organized code with named constants at the top for gravity, thrust, damping, crater radius, tunnel length, and ghost cap so they are easy to tune.
- The game must be beatable: verify that pinch points can be opened by a reasonable number of deaths, and that the first attempt makes visible progress.
```
