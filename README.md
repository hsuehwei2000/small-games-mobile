# Volley fun
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Volleyball Game</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background-color: #87CEEB;
            font-family: Arial, sans-serif;
            touch-action: none;
        }
        #gameContainer {
            position: relative;
            width: 100%;
            height: 100%;
        }
        canvas {
            width: 100%;
            height: 100%;
            touch-action: none;
        }
        #startButton, #restartButton {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 10px 20px;
            font-size: 20px;
            cursor: pointer;
        }
        #gameOverScreen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            color: white;
            font-size: 24px;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <canvas id="gameCanvas"></canvas>
        <button id="startButton">Start Game</button>
        <div id="gameOverScreen">
            <p id="winnerText"></p>
            <button id="restartButton">Restart Game</button>
        </div>
    </div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const startButton = document.getElementById('startButton');
        const restartButton = document.getElementById('restartButton');
        const gameOverScreen = document.getElementById('gameOverScreen');
        const winnerText = document.getElementById('winnerText');

        let gameRunning = false;
        let canvasWidth, canvasHeight;

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            canvasWidth = canvas.width;
            canvasHeight = canvas.height;
            initializeGameObjects();
        }

        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        const ball = {};
        const player1 = {};
        const player2 = {};
        const net = {};

        function initializeGameObjects() {
            const paddleWidth = canvasWidth * 0.05;
            const paddleHeight = canvasHeight * 0.2;
            const ballRadius = Math.min(canvasWidth, canvasHeight) * 0.02;

            Object.assign(ball, {
                x: canvasWidth / 2,
                y: canvasHeight / 2,
                radius: ballRadius,
                dx: 3,
                dy: -3,
                speed: 5
            });

            Object.assign(player1, {
                x: paddleWidth,
                y: canvasHeight / 2 - paddleHeight / 2,
                width: paddleWidth,
                height: paddleHeight,
                dy: 0,
                score: 0
            });

            Object.assign(player2, {
                x: canvasWidth - paddleWidth * 2,
                y: canvasHeight / 2 - paddleHeight / 2,
                width: paddleWidth,
                height: paddleHeight,
                dy: 0,
                score: 0
            });

            Object.assign(net, {
                x: canvasWidth / 2 - 2,
                y: canvasHeight - canvasHeight * 0.25,
                width: 4,
                height: canvasHeight * 0.25
            });
        }

        function drawRect(x, y, width, height, color) {
            ctx.fillStyle = color;
            ctx.fillRect(x, y, width, height);
        }

        function drawCircle(x, y, radius, color) {
            ctx.fillStyle = color;
            ctx.beginPath();
            ctx.arc(x, y, radius, 0, Math.PI * 2, false);
            ctx.closePath();
            ctx.fill();
        }

        function drawText(text, x, y, color) {
            ctx.fillStyle = color;
            ctx.font = `${Math.floor(canvasHeight * 0.05)}px Arial`;
            ctx.fillText(text, x, y);
        }

        function drawGame() {
            drawRect(0, 0, canvasWidth, canvasHeight, "#87CEEB");
            drawRect(0, canvasHeight - 10, canvasWidth, 10, "#8B4513");
            drawRect(net.x, net.y, net.width, net.height, "white");
            drawRect(player1.x, player1.y, player1.width, player1.height, "yellow");
            drawRect(player2.x, player2.y, player2.width, player2.height, "red");
            drawCircle(ball.x, ball.y, ball.radius, "white");
            drawText(player1.score, canvasWidth / 4, 50, "yellow");
            drawText(player2.score, 3 * canvasWidth / 4, 50, "red");
        }

        function movePlayers() {
            // Touch controls
            canvas.addEventListener('touchmove', (e) => {
                const touch = e.touches[0];
                const rect = canvas.getBoundingClientRect();
                const touchX = touch.clientX - rect.left;
                const touchY = touch.clientY - rect.top;

                if (touchX < canvasWidth / 2) {
                    player1.y = touchY - player1.height / 2;
                } else {
                    player2.y = touchY - player2.height / 2;
                }
            });

            // Keyboard controls (for testing on desktop)
            if (keys['KeyW'] && player1.y > 0) player1.y -= 5;
            if (keys['KeyS'] && player1.y < canvasHeight - player1.height) player1.y += 5;
            if (keys['ArrowUp'] && player2.y > 0) player2.y -= 5;
            if (keys['ArrowDown'] && player2.y < canvasHeight - player2.height) player2.y += 5;
        }

        function moveBall() {
            ball.x += ball.dx;
            ball.y += ball.dy;

            if (ball.y - ball.radius < 0 || ball.y + ball.radius > canvasHeight - 10) {
                ball.dy = -ball.dy;
            }

            let player = (ball.x < canvasWidth / 2) ? player1 : player2;
            if (
                ball.x - ball.radius < player.x + player.width &&
                ball.x + ball.radius > player.x &&
                ball.y - ball.radius < player.y + player.height &&
                ball.y + ball.radius > player.y
            ) {
                let collidePoint = (ball.y - (player.y + player.height / 2)) / (player.height / 2);
                let angleRad = (Math.PI / 4) * collidePoint;
                let direction = (ball.x < canvasWidth / 2) ? 1 : -1;
                
                ball.dx = direction * Math.cos(angleRad) * ball.speed;
                ball.dy = Math.sin(angleRad) * ball.speed;

                ball.speed += 0.1;
            }

            if (ball.x - ball.radius < 0) {
                player2.score++;
                checkGameOver();
                resetBall();
            } else if (ball.x + ball.radius > canvasWidth) {
                player1.score++;
                checkGameOver();
                resetBall();
            }
        }

        function resetBall() {
            ball.x = canvasWidth / 2;
            ball.y = canvasHeight / 2;
            ball.dx = (Math.random() > 0.5 ? 1 : -1) * 3;
            ball.dy = -3;
            ball.speed = 5;
        }

        function checkGameOver() {
            if (player1.score >= 5 || player2.score >= 5) {
                gameRunning = false;
                let winner = player1.score >= 5 ? "Player 1" : "Player 2";
                winnerText.textContent = `${winner} wins!`;
                gameOverScreen.style.display = 'flex';
            }
        }

        function gameLoop() {
            if (gameRunning) {
                movePlayers();
                moveBall();
                drawGame();
                requestAnimationFrame(gameLoop);
            }
        }

        function startGame() {
            gameRunning = true;
            startButton.style.display = 'none';
            gameLoop();
        }

        function restartGame() {
            player1.score = 0;
            player2.score = 0;
            resetBall();
            gameOverScreen.style.display = 'none';
            startGame();
        }

        const keys = {};

        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
        });

        document.addEventListener('keyup', (e) => {
            keys[e.code] = false;
        });

        startButton.addEventListener('click', startGame);
        restartButton.addEventListener('click', restartGame);

        drawGame();
    </script>
</body>
</html>
