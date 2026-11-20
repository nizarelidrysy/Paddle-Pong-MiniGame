A single-player 2D simple Android game built with Kotlin in a single `MainActivity` as a Mobile Development module project.

## Features

- Player name setup with empty-input validation (`Enter your name.`).
- Exclusive difficulty selection: Easy (`65 ms`), Medium (`45 ms`), and Hard (`30 ms`).
- Real-time score tracking and responsive Left/Right paddle controls.
- Light and dark mode toggle (`Switch`).
- Game Over `AlertDialog` with **Play Again** and **Back to Setup** options.

## Math & Game Logic

- **Arena:** $280 \times 300\text{ dp}$ playfield, $24 \times 24\text{ dp}$ ball, and $72 \times 12\text{ dp}$ paddle.
- **Trajectory:** Ball moves by $(dx, dy) = (\pm 4\text{ dp}, \pm 4\text{ dp})$ at a $45^\circ$ angle, inverting signs on wall or paddle bounces.
- **Bounds:** Paddle moves in $16\text{ dp}$ steps clamped to $[0, 208]\text{ dp}$; right wall rebounds at $x = 256\text{ dp}$.
- **Collision:** Checks if the ball's bottom edge reaches the paddle top ($y = 268\text{ dp}$) while overlapping horizontally.

## Setup

1. Open the project in Android Studio.
2. Sync Gradle and run on an emulator or Android device.

---

<div align="center">
  <p>Engineered with dedication by Nizar EL IDRYSY ❤️</p>
</div>