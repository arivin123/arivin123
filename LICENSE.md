<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Zombie Shooter Game</title>
    <style>
        /* CSS */
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            font-family: Arial, sans-serif;
            margin: 0;
            background-color: #333;
            color: #fff;
        }

        canvas {
            border: 2px solid #fff;
            background-color: #222;
        }

        #score {
            font-size: 1.2em;
            margin-top: 10px;
        }

        .hidden { display: none; }
    </style>
</head>
<body>
    <h1>Zombie Shooter Game</h1>
    <canvas id="gameCanvas"></canvas>
    <p id="score">Score: 0</p>

    <script>
        // JavaScript
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        canvas.width = 800;
        canvas.height = 600;

        let score = 0;
        let zombies = [];
        let bullets = [];
        const player = { x: canvas.width / 2, y: canvas.height / 2, size: 20 };

        function spawnZombie() {
            const size = 30;
            const x = Math.random() > 0.5 ? 0 : canvas.width;
            const y = Math.random() * canvas.height;
            const speed = Math.random() * 1.5 + 0.5;
            zombies.push({ x, y, size, speed });
        }

        function drawPlayer() {
            ctx.fillStyle = "blue";
            ctx.fillRect(player.x - player.size / 2, player.y - player.size / 2, player.size, player.size);
        }

        function drawZombie(zombie) {
            ctx.fillStyle = "green";
            ctx.fillRect(zombie.x - zombie.size / 2, zombie.y - zombie.size / 2, zombie.size, zombie.size);
        }

        function drawBullet(bullet) {
            ctx.fillStyle = "red";
            ctx.fillRect(bullet.x - 5, bullet.y - 5, 10, 10);
        }

        function updateZombies() {
            zombies.forEach((zombie, index) => {
                const angle = Math.atan2(player.y - zombie.y, player.x - zombie.x);
                zombie.x += Math.cos(angle) * zombie.speed;
                zombie.y += Math.sin(angle) * zombie.speed;

                if (Math.hypot(player.x - zombie.x, player.y - zombie.y) < player.size) {
                    alert("Game Over! Your score: " + score);
                    resetGame();
                }

                drawZombie(zombie);
            });
        }

        function updateBullets() {
            bullets.forEach((bullet, bIndex) => {
                bullet.x += Math.cos(bullet.angle) * 5;
                bullet.y += Math.sin(bullet.angle) * 5;

                if (bullet.x < 0 || bullet.x > canvas.width || bullet.y < 0 || bullet.y > canvas.height) {
                    bullets.splice(bIndex, 1);
                }

                zombies.forEach((zombie, zIndex) => {
                    if (Math.hypot(zombie.x - bullet.x, zombie.y - bullet.y) < zombie.size) {
                        zombies.splice(zIndex, 1);
                        bullets.splice(bIndex, 1);
                        score += 10;
                        document.getElementById("score").textContent = "Score: " + score;
                    }
                });

                drawBullet(bullet);
            });
        }

        function resetGame() {
            score = 0;
            zombies = [];
            bullets = [];
            document.getElementById("score").textContent = "Score: " + score;
            spawnZombie();
        }

        function gameLoop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            drawPlayer();
            updateZombies();
            updateBullets();

            requestAnimationFrame(gameLoop);
        }

        window.addEventListener("click", (event) => {
            const angle = Math.atan2(event.clientY - player.y, event.clientX - player.x);
            bullets.push({ x: player.x, y: player.y, angle });
        });

        setInterval(spawnZombie, 1500);
        resetGame();
        gameLoop();
    </script>
</body>
</html>
