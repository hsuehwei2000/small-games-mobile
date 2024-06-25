
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: Arial, sans-serif;
        }
        canvas {
            border: 1px solid black;
        }
        #score {
            margin-top: 10px;
            font-size: 20px;
        }
        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            display: none;
        }
        #restartButton {
            margin-top: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="300" height="300"></canvas>
    <div id="score">Score: 0</div>
    <div id="gameOver">
        <h2>Game Over!</h2>
        <p>Your score: <span id="finalScore"></span></p>
        <button id="restartButton">Restart</button>
    </div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const gameOverElement = document.getElementById('gameOver');
        const finalScoreElement = document.getElementById('finalScore');
        const restartButton = document.getElementById('restartButton');

        const gridSize = 10;
        const tileCount = canvas.width / gridSize;

        let snake = [{x: 10, y: 10}];
        let food = {x: 15, y: 15};
        let dx = 0;
        let dy = 0;
        let score = 0;
        let gameRunning = true;

        function drawGame() {
            if (!gameRunning) return;

            clearCanvas();
            moveSnake();
            drawSnake();
            drawFood();
            checkCollision();
            setTimeout(drawGame, 100);
        }

        function clearCanvas() {
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        function moveSnake() {
            const head = {x: snake[0].x + dx, y: snake[0].y + dy};
            snake.unshift(head);
            if (head.x === food.x && head.y === food.y) {
                placeFood();
                score += 10;
                updateScore();
            } else {
                snake.pop();
            }
        }

        function drawSnake() {
            ctx.fillStyle = 'green';
            snake.forEach(segment => {
                ctx.fillRect(segment.x * gridSize, segment.y * gridSize, gridSize - 1, gridSize - 1);
            });
        }

        function drawFood() {
            ctx.fillStyle = 'red';
            ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize - 1, gridSize - 1);
        }

        function placeFood() {
            food = {
                x: Math.floor(Math.random() * tileCount),
                y: Math.floor(Math.random() * tileCount)
            };
        }

        function checkCollision() {
            const head = snake[0];
            if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
                gameOver();
            }
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    gameOver();
                }
            }
        }

        function gameOver() {
            gameRunning = false;
            finalScoreElement.textContent = score;
            gameOverElement.style.display = 'block';
        }

        function resetGame() {
            snake = [{x: 10, y: 10}];
            food = {x: 15, y: 15};
            dx = 0;
            dy = 0;
            score = 0;
            updateScore();
            gameRunning = true;
            gameOverElement.style.display = 'none';
            drawGame();
        }

        function updateScore() {
            scoreElement.textContent = `Score: ${score}`;
        }

        canvas.addEventListener('touchstart', handleTouchStart, false);
        canvas.addEventListener('touchmove', handleTouchMove, false);

        let xDown = null;
        let yDown = null;

        function handleTouchStart(evt) {
            const firstTouch = evt.touches[0];
            xDown = firstTouch.clientX;
            yDown = firstTouch.clientY;
        }

        function handleTouchMove(evt) {
            if (!xDown || !yDown) {
                return;
            }

            const xUp = evt.touches[0].clientX;
            const yUp = evt.touches[0].clientY;

            const xDiff = xDown - xUp;
            const yDiff = yDown - yUp;

            if (Math.abs(xDiff) > Math.abs(yDiff)) {
                if (xDiff > 0) {
                    dx = -1; dy = 0;
                } else {
                    dx = 1; dy = 0;
                }
            } else {
                if (yDiff > 0) {
                    dx = 0; dy = -1;
                } else {
                    dx = 0; dy = 1;
                }
            }

            xDown = null;
            yDown = null;
        }

        restartButton.addEventListener('click', resetGame);

        drawGame();
    </script>
</body>
</html>
