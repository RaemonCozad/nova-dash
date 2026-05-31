# 🚀 NOVA DASH

A handcrafted 2D sci-fi platformer built entirely in vanilla HTML, CSS, and JavaScript — no engines, no libraries, no build tools. Every system was written from scratch: physics, collision, camera, AI, particles, and audio.

🔗 **[Play it live →](https://raemoncozad.github.io/nova-dash)**

> _Replace the link above with your GitHub Pages URL after deploying._

---

## 📸 Preview

<!-- Record a GIF with ScreenToGif (Windows) or Kap (Mac) and drop it here -->

![Nova Dash Screenshot](screenshot.png)

---

## 🎮 How to Play

| Input | Action |
|---|---|
| `←` / `A` | Move left |
| `→` / `D` | Move right |
| `↑` / `W` / `Space` | Jump |
| Jump again (mid-air) | Double jump |
| Jump while touching a wall | Wall jump |
| Land on top of an enemy | Stomp — kills it and bounces you |

**Goal:** Collect coins, stomp enemies, and reach the glowing green portal to clear the level. Survive 5 levels with your health intact.

---

## ✨ Features

### Gameplay
- 🗺 **5 handcrafted levels** with increasing difficulty, enemy density, and platforming complexity
- ❤️ **Health system** — 5 HP, with invincibility frames after taking damage
- 💀 **Enemy AI** — enemies patrol, reverse at ledge edges, and respond to stomp vs collision differently
- 🌀 **Portal** — animated exit at the end of each level

### Movement & Physics
- ⚙️ **Custom physics engine** — gravity, acceleration, friction, terminal velocity, all written from scratch
- 🦘 **Double jump** — two jumps before landing, resets on grounding
- 🧱 **Wall jump** — push off any wall surface mid-air
- 🎯 **Jump buffering** — jump input registered slightly before landing for responsive feel
- 📷 **Smooth lerp camera** — follows the player with clamped level bounds

### Visual Polish
- ✨ **Particle system** — burst effects on coin collection, enemy kills, damage, and jumps
- 💫 **Player trail** — motion blur effect on the player sprite
- 🔵 **Glowing coins** — radial gradient + sine-wave bobbing animation
- 🌀 **Animated portal** — pulsing concentric rings with sine-wave variation
- 🏃 **Enemy leg animation** — frame-based walk cycle
- 👾 **Player blink** — sprite flashes during invincibility frames
- 🌌 **Parallax star background** — stars shift at a different rate to the foreground
- 🔢 **Floating score popups** — `+50`, `+100` text rises and fades on pickup/kill

### Audio
- 🔊 **Synthesised sound effects** via Web Audio API — zero external audio files
- Distinct tones for: jump, double jump, coin, hurt, enemy kill, level win, portal entry

### UI & Persistence
- 📊 **Live HUD** — score, coins, health bar, level, hi-score
- 🏆 **Hi-score persistence** via `localStorage` — survives page refresh
- 📱 **Mobile controls** — on-screen D-pad buttons on narrow screens

---

## 🛠️ Tech Stack

| Technology | Usage |
|---|---|
| HTML5 Canvas | Game rendering — every frame drawn in JS |
| CSS3 | UI, HUD, screen overlays, animations |
| Vanilla JavaScript | All game logic — physics, AI, collision, camera |
| Web Audio API | Procedurally synthesised sound effects |
| localStorage | Hi-score persistence |

**Zero dependencies. Zero frameworks. Zero build step.** Just open `nova-dash.html`.

---

## 🚀 Run Locally

```bash
git clone https://github.com/yourusername/nova-dash.git
open nova-dash.html
```

No install. No server required.

---

## 🧠 Technical Deep Dives

### Physics & Collision

The physics engine uses separate X and Y resolution passes — a technique that prevents corner-clipping and makes slopes/edges feel clean:

```js
// 1. Move horizontally, resolve horizontal tile collisions
p.x += p.vx;
resolveX(p, tiles);

// 2. Move vertically, resolve vertical tile collisions
p.y += p.vy;
resolveY(p, tiles);
```

Each resolution checks the player's leading edge tiles, snaps position to the tile boundary, and zeroes out velocity in that axis. Vertical resolution also sets `onGround = true` and resets the jump counter.

---

### Double Jump & Wall Jump

Jump state is tracked with a `jumpsLeft` counter (starts at 2) and resets when the player lands:

```js
if (jumpPressed && p.jumpsLeft > 0) {
  p.vy = -11;
  p.jumpsLeft--;
}
// Wall jump — no jumpsLeft required
if (jumpPressed && p.onWall !== 0) {
  p.vy = -10;
  p.vx = -p.onWall * 5; // launch away from the wall
  p.jumpsLeft = 1;
}
```

Jump input is buffered via a `jumpQueued` flag so pressing jump a frame before landing still registers — a subtle detail that makes the controls feel responsive.

---

### Enemy AI

Each enemy stores a velocity vector and runs through the same physics pipeline as the player. Ledge detection reads the tile one step ahead and below — if that tile is not solid, the enemy reverses direction:

```js
const ahead = Math.floor((e.x + (e.vx > 0 ? e.w + 2 : -2)) / TILE);
const floor = Math.floor((e.y + e.h + 2) / TILE);
if (!isSolid(tiles, ahead, floor) && e.onGround) e.vx *= -1;
```

Player-enemy collision checks whether the player is descending and above the enemy's midpoint to distinguish a stomp from a side collision:

```js
if (p.vy > 0 && p.y + p.h < e.y + e.h * 0.6) {
  // stomp — kill enemy, bounce player
} else {
  // side hit — damage player
}
```

---

### Particle System

Particles are plain objects in an array, updated each frame and spliced out when dead:

```js
{ x, y, vx, vy, life: 1, decay: 0.03, size: 5, color: '#ffd700' }
```

Each frame: position integrates from velocity, velocity accumulates a gravity constant, `life` decrements by `decay`. When `life <= 0` the particle is removed. No classes, no allocation pools — simple and fast enough for hundreds of particles at 60fps.

---

### Smooth Camera

The camera uses lerp (linear interpolation) to smoothly chase the player rather than snapping:

```js
cam.x += (targetX - cam.x) * 0.1;
cam.y += (targetY - cam.y) * 0.1;
```

The `0.1` factor means the camera covers 10% of the remaining distance each frame, creating a natural lag that feels cinematic. The result is clamped to level bounds so it never shows outside the map.

---

## 📁 Project Structure

```
nova-dash/
├── nova-dash.html    # Entire game — HTML + CSS + JS in one file
└── README.md
```

---

## 🔮 Future Improvements

- [ ] More level types — moving platforms, water, one-way platforms
- [ ] Power-ups — speed boost, shield, double damage
- [ ] Boss encounter on level 5
- [ ] Animated tile spritesheet
- [ ] Local leaderboard
- [ ] Level editor

---

## 📄 License

MIT — free to use, modify, and learn from.
