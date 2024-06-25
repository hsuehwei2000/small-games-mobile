<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }
        canvas {
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="300" height="300"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const gridSize = 10;
        const tileCount = canvas.width / gridSize;

        let snake = [
            {x: 10, y: 10},
        ];
        let food = {x: 15, y: 15};
        let dx = 0;
        let dy = 0;

        function drawGame() {
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
                resetGame();
            }
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    resetGame();
                }
            }
        }

        function resetGame() {
            snake = [{x: 10, y: 10}];
            food = {x: 15, y: 15};
            dx = 0;
            dy = 0;
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

        drawGame();
    </script>
</body>
</html>
