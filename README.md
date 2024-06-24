# small-games-mobile
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Volleyball Game</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #87CEEB;
            font-family: Arial, sans-serif;
            touch-action: none;
        }
        #gameContainer {
            position: relative;
            width: 100%;
            max-width: 800px;
        }
        canvas {
            border: 2px solid black;
            width: 100%;
            height: auto;
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
        #controls {
            display: flex;
            justify-content: space-between;
            width: 100%;
            position: absolute;
            bottom: 20px;
        }
        .control-button {
            width: 80px;
            height: 80px;
            font-size: 24px;
            background-color: rgba(255, 255, 255, 0.5);
            border: none;
            border-radius: 50%;
            touch-action: none;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <canvas id="gameCanvas" width="800" height="400"></canvas>
        <button id="startButton">Start Game</button>
        <div id="gameOverScreen">
            <p id="winnerText"></p>
            <button id="restartButton">Restart Game</button>
        </div>
        <div id="controls">
            <div>
                <button class="control-button" id="player1Up">↑</button>
                <button class="control-button" id="player1Down">↓</button>
            </div>
            <div>
                <button class="control-button" id="player2Up">↑</button>
                <button class="control-button" id="player2Down">↓</button>
            </div>
        </div>
    </div>
    <script>
        // ... (keep the existing JavaScript code)

        // Add touch controls
        const player1Up = document.getElementById('player1Up');
        const player1Down = document.getElementById('player1Down');
        const player2Up = document.getElementById('player2Up');
        const player2Down = document.getElementById('player2Down');

        player1Up.addEventListener('touchstart', () => keys['KeyW'] = true);
        player1Up.addEventListener('touchend', () => keys['KeyW'] = false);
        player1Down.addEventListener('touchstart', () => keys['KeyS'] = true);
        player1Down.addEventListener('touchend', () => keys['KeyS'] = false);

        player2Up.addEventListener('touchstart', () => keys['ArrowUp'] = true);
        player2Up.addEventListener('touchend', () => keys['ArrowUp'] = false);
        player2Down.addEventListener('touchstart', () => keys['ArrowDown'] = true);
        player2Down.addEventListener('touchend', () => keys['ArrowDown'] = false);

        // Resize canvas
        function resizeCanvas() {
            const container = document.getElementById('gameContainer');
            const aspectRatio = canvas.width / canvas.height;
            const newWidth = container.clientWidth;
            const newHeight = newWidth / aspectRatio;
            canvas.style.width = `${newWidth}px`;
            canvas.style.height = `${newHeight}px`;
        }

        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // ... (keep the rest of the JavaScript code)
    </script>
</body>
</html>
