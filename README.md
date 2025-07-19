# ak-battle-royal
AK Battle Royale is an action-packed survival game where 100 players fight to be the last one standing. Drop in, loot powerful weapons like the AK-47, survive the shrinking zone, and outlast your enemies in solo or squad mode.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Mini Shooter Game</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <canvas id="gameCanvas" width="480" height="640"></canvas>
  <script src="game.js"></script>
</body>
</html>
body {
  margin: 0;
  background: black;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
canvas {
  background: #111;
  border: 2px solid white;
}
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let player = { x: 220, y: 580, width: 40, height: 20 };
let bullets = [];
let enemies = [];
let score = 0;
let gameOver = false;

document.addEventListener("keydown", (e) => {
  if (e.key === "ArrowLeft") player.x -= 20;
  if (e.key === "ArrowRight") player.x += 20;
  if (e.key === " ") bullets.push({ x: player.x + 15, y: player.y });
});

function drawPlayer() {
  ctx.fillStyle = "lime";
  ctx.fillRect(player.x, player.y, player.width, player.height);
}

function drawBullets() {
  ctx.fillStyle = "yellow";
  bullets.forEach((b, i) => {
    b.y -= 5;
    ctx.fillRect(b.x, b.y, 5, 10);
    if (b.y < 0) bullets.splice(i, 1);
  });
}

function drawEnemies() {
  ctx.fillStyle = "red";
  enemies.forEach((e, i) => {
    e.y += 2;
    ctx.fillRect(e.x, e.y, 30, 30);

    // Collision with player
    if (
      e.x < player.x + player.width &&
      e.x + 30 > player.x &&
      e.y < player.y + player.height &&
      e.y + 30 > player.y
    ) {
      gameOver = true;
    }

    // Collision with bullet
    bullets.forEach((b, j) => {
      if (
        b.x < e.x + 30 &&
        b.x + 5 > e.x &&
        b.y < e.y + 30 &&
        b.y + 10 > e.y
      ) {
        enemies.splice(i, 1);
        bullets.splice(j, 1);
        score += 1;
      }
    });

    if (e.y > canvas.height) enemies.splice(i, 1);
  });
}

function drawScore() {
  ctx.fillStyle = "white";
  ctx.font = "20px Arial";
  ctx.fillText("Score: " + score, 10, 30);
}

function loop() {
  if (gameOver) {
    ctx.fillStyle = "white";
    ctx.font = "40px Arial";
    ctx.fillText("Game Over!", 120, 320);
    return;
  }

  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawPlayer();
  drawBullets();
  drawEnemies();
  drawScore();

  if (Math.random() < 0.02) {
    enemies.push({ x: Math.random() * 450, y: 0 });
  }

  requestAnimationFrame(loop);
}

loop();
