# 📓 Innovator Journal: Code Autopsy
**Project:** HTML5 Canvas Game Autopsy  
**Game Name:** *Flappy Webby*  
**File:** `flappy_webby.html`  


## 1. Project Overview & Setup

For this activity, I generated a single-file HTML5/JavaScript game called **Flappy Webby** using the HTML5 `<canvas>` element. The game features a simple physics-based bird maneuvering through obstacles set against a sunset backdrop.


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Flappy Webby</title>
  <style>
    body {
      background-color: #2b1f3d;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>
</body>
</html>
Markdown

## 2. Code Autopsy Worksheet

### The Canvas
> **Where is it in the HTML?**  
> Line 16 inside the `<body>` tag:  
> ```html
> <canvas id="gameCanvas" width="400" height="600"></canvas>
> ```

* **Dimensions:** `400px` wide by `600px` high.
* **Effect of Changing Dimensions:** Increasing the width to `600px` gives the player more time to react to approaching obstacles. Decreasing the height to `400px` compresses the vertical movement range, making timing noticeably more unforgiving.

---

### The State Variables
The game keeps track of the following values throughout gameplay:

| Variable | Type | Purpose |
| :--- | :--- | :--- |
| `birdX`, `birdY` | `number` | Precise pixel coordinates of the bird center |
| `velocity` | `number` | Vertical speed of the bird (pixels moved per frame) |
| `gravity` | `number` | Downward acceleration added to velocity every frame |
| `pipeX` | `number` | Horizontal position of the moving obstacle wall |
| `topPipeHeight` | `number` | Randomly generated height of the upper barrier |
| `score` | `number` | Points accumulated by successfully passing pipes |
| `gameOver` | `boolean` | Flag controlling active gameplay vs. game-over state |

```javascript
// State Variables Declaration
let birdX = 80;
let birdY = 250;
let birdRadius = 16;
let velocity = 0;
let gravity = 0.45;
let jumpStrength = -8;

let pipeX = canvas.width;
let pipeWidth = 60;
let pipeGap = 160;
let topPipeHeight = 180;
let pipeSpeed = 3;

let score = 0;
let gameOver = false;
### The Game Loop
* **Function:** `update()`
* **Execution Frequency:** Approximately 60 times per second via `requestAnimationFrame(update)`.
* **Operations Per Frame:**
  1. Apply acceleration: `velocity += gravity`
  2. Move bird position: `birdY += velocity`
  3. Slide pipe leftward: `pipeX -= pipeSpeed`
  4. Recycle pipe & increment score when pipe moves off-screen
  5. Check collision conditions
  6. Re-render visual frame via `draw()`

```javascript
function update() {
  if (!gameOver) {
    velocity += gravity;
    birdY += velocity;
    pipeX -= pipeSpeed;

    // Recycle Pipe & Score Point
    if (pipeX + pipeWidth < 0) {
      pipeX = canvas.width;
      topPipeHeight = Math.floor(Math.random() * (canvas.height - pipeGap - 100)) + 50;
      score += 1;
    }
  }

  draw();
  requestAnimationFrame(update);
}
### Input Handling
* **Control Key:** `Spacebar`
* **Event Listener Code:**
  ```javascript
  document.addEventListener("keydown", function(event) {
    if (event.code === "Space") {
      if (gameOver) {
        resetGame();
      } else {
        velocity = jumpStrength;
      }
    }
  });
# Mechanism
Pressing Space while active sets `velocity = -8` (launching the bird upward). Pressing Space during a `gameOver` state calls `resetGame()`, resetting positions and scores.

---

### Collision & Win/Lose Logic
The game checks for two failure states during every frame:

1. **Boundary Collision:** Checks if the top or bottom of the bird circle contacts screen edges (`0` or `canvas.height`).
2. **Obstacle Collision:** Checks if the bird's horizontal footprint overlaps with `pipeX`, and whether its vertical position touches either the top or bottom pipe.

```javascript
// 1. Floor & Ceiling Check
if (birdY + birdRadius >= canvas.height || birdY - birdRadius <= 0) {
  gameOver = true;
}

// 2. Pipe Overlap Check
if (birdX + birdRadius > pipeX && birdX - birdRadius < pipeX + pipeWidth) {
  if (birdY - birdRadius < topPipeHeight || birdY + birdRadius > topPipeHeight + pipeGap) {
    gameOver = true;
  }
}
### Scoring
* **Score Variable:** `let score = 0;`
* **Trigger Line:** Incremented inside `update()` when the trailing edge of a pipe clears the left edge of the screen:

```javascript
score += 1;
