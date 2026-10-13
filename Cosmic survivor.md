Code Autopsy Report: Cosmic Survivor 2D
1. The Canvas
 * Where is it in the HTML?
   <div id="gameContainer">
  <canvas id="gameCanvas" width="800" height="600"></canvas>
</div>

 * What size is it? 800 pixels wide by 600 pixels high.
 * Effect of changing dimensions: Increasing width and height expands the play area, giving the player more space to maneuver away from enemies. Decreasing dimensions shrinks the tactical space, increasing the difficulty and frequency of collisions.
2. The State Variables
The game tracks player stats, active entities, and skill cooldowns through central state variables:
| Variable | Type | Purpose |
|---|---|---|
| score, wave, credits | number | Tracks player progress, difficulty scaling, and currency |
| player | object | Stores player position (x, y), size (radius), speed, and health (hp) |
| bullets, enemies, particles | array | Dynamic lists holding all active projectiles, enemies, and visual explosion fragments |
| skills | array | Stores cooldown timers and active durations for all 5 special skills |
| gameOver | boolean | Flag controlling whether gameplay logic updates or stops |
// State Variables Declaration
let score = 0, wave = 1, credits = 0, gameOver = false;
let player = {
  x: canvas.width / 2,
  y: canvas.height - 80,
  radius: 18,
  speed: 5,
  hp: 100,
  maxHp: 100
};

let bullets = [], enemies = [], particles = [];

3. The Game Loop
 * Function: loop() calling update() and draw()
 * Execution Frequency: Approximately 60 frames per second using requestAnimationFrame(loop).
 * Operations Per Frame (update()):
   * Manage skill cooldowns and active durations.
   * Handle keyboard input for 8-directional or axis movement.
   * Process auto-firing mechanics and active skill modifiers.
   * Update positions of bullets, enemies, and particle physics.
   * Check collision detection and wave progression conditions.
   * Call draw() to render background graphics, ships, particles, and UI overlays.
function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

4. Input Handling & Special Skills
 * Controls: Arrow keys or WASD for movement, number keys 1 through 5 for special skills, and R to restart.
 * Event Listeners:
   const keys = {};
window.addEventListener('keydown', e => {
  initAudio();
  keys[e.code] = true;
  if (e.code === 'Digit1') activateSkill(0);
  if (e.code === 'Digit2') activateSkill(1);
  if (e.code === 'Digit3') activateSkill(2);
  if (e.code === 'Digit4') activateSkill(3);
  if (e.code === 'Digit5') activateSkill(4);
  if (e.code === 'KeyR' && gameOver) resetGame();
});
window.addEventListener('keyup', e => keys[e.code] = false);

 * Mechanism: Pressing movement keys sets key flags to true, altering player coordinates smoothly each frame. Pressing number keys triggers activateSkill(index), which checks cooldowns and activates abilities like shields, time warp, or weapon surges.
5. Collision Detection
The game uses geometric distance checks (Pythagorean theorem via Math.hypot) to detect circular overlaps between entities:
// Player vs Enemy Collision
let dist = Math.hypot(player.x - e.x, player.y - e.y);
if (dist < player.radius + e.radius) {
  e.hp = 0;
  if (skills[0].dur <= 0) {
    player.hp -= 20;
  }
}

// Bullet vs Enemy Collision
if (Math.hypot(b.x - e.x, b.y - e.y) < b.radius + e.radius) {
  b.dead = true;
  e.hp -= 10;
}

 * Explanation: Math.hypot(dx, dy) calculates the exact distance between the center points of two circular objects. If that distance is less than the sum of their radii (player.radius + e.radius), an intersection (collision) has occurred.
6. Scoring & Wave Progression
 * Score Variable: let score = 0;
 * Trigger Line: Incremented when an enemy's health drops to zero:
   score += e.type === 'tank' ? 30 : 10;

 * Wave Progression: Automatically checks if the score threshold has been met to escalate the wave tier:
   if (score >= wave * 100) {
  wave++;
}

