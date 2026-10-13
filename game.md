1. Project Overview & Setup
For this activity, I generated a single-file HTML5/JavaScript game called Flappy Webby using the HTML canvas element. The game features a simple physics-based bird maneuvering through obstacles set against a sunset backdrop.
HTML
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
2. Code Autopsy Worksheet
The Canvas
Where is it in the HTML?
Line 16 inside the <body> tag:
HTML
<canvas id="gameCanvas" width="400" height="600"></canvas>
What size is it?
400 pixels wide by 600 pixels high.
Effect of changing dimensions:
Increasing the width to 600px gives the player more time to react to approaching obstacles. Decreasing the height to 400px compresses the vertical movement range, making timing noticeably more unforgiving.
The State Variables
Values tracked by the game:
birdX, birdY: Precise pixel coordinates of the bird center.
velocity: Vertical speed of the bird (pixels moved per frame).
gravity: Acceleration added to velocity every frame.
pipeX: Horizontal position of the moving obstacle wall.
topPipeHeight: Randomly generated height of the upper barrier.
score: Points accumulated by passing pipes.
gameOver: Boolean flag (true/false) controlling gameplay vs. end state.
Code section:
JavaScript
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
The Game Loop
Function: update()
Frequency: Approximately 60 times per second via requestAnimationFrame(update).
Execution steps per frame:
Add gravity to velocity.
Update birdY by adding velocity.
Move the pipe leftward (pipeX -= pipeSpeed).
Check for screen boundaries and pipe collisions.
Reset the pipe and increment score when a pipe exits screen left.
Call draw() to re-render all elements.
JavaScript
function update() {
  if (!gameOver) {
    velocity += gravity;
    birdY += velocity;
    pipeX -= pipeSpeed;

    if (pipeX + pipeWidth < 0) {
      pipeX = canvas.width;
      topPipeHeight = Math.floor(Math.random() * (canvas.height - pipeGap - 100)) + 50;
      score += 1;
    }
    // [Collision logic omitted for brevity]
  }

  draw();
  requestAnimationFrame(update);
}
Input Handling
Controls: Spacebar.
Event Listener:
JavaScript
document.addEventListener("keydown", function(event) {
  if (event.code === "Space") {
    if (gameOver) {
      resetGame();
    } else {
      velocity = jumpStrength;
    }
  }
});
Mechanism: Pressing Space while active sets velocity = -8 (moving the bird up instantly). Pressing Space during a gameOver state re-initializes position, score, and obstacle variables.
Collision / Win-Lose Logic
Detection mechanisms:
Ground/Ceiling: Checks if the bird's outer edge crosses screen limits (0 or canvas.height).
Pipes: Checks bounding box overlaps between the bird's X/Y range and pipe coordinates.
JavaScript
// Floor & Ceiling check
if (birdY + birdRadius >= canvas.height || birdY - birdRadius <= 0) {
  gameOver = true;
}

// Pipe X overlap check
if (birdX + birdRadius > pipeX && birdX - birdRadius < pipeX + pipeWidth) {
  // Pipe Y gap check
  if (birdY - birdRadius < topPipeHeight || birdY + birdRadius > topPipeHeight + pipeGap) {
    gameOver = true;
  }
}
Scoring
Variable: let score = 0;
Trigger line: Runs inside update() when a pipe passes X = -pipeWidth:
JavaScript
score += 1;
