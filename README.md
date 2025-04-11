<!DOCTYPE html>
<html>
<head>
    <title>Flappy Bird Clone</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #70c5ce;
            font-family: Arial, sans-serif;
        }
        
        #game-container {
            position: relative;
            width: 400px;
            height: 600px;
            overflow: hidden;
            background-color: #70c5ce;
            border: 3px solid #000;
        }
        
        #bird {
            position: absolute;
            width: 40px;
            height: 30px;
            background-color: #ffcc00;
            border-radius: 50%;
            z-index: 10;
        }
        
        .pipe {
            position: absolute;
            width: 60px;
            background-color: #4CAF50;
            border: 2px solid #388E3C;
            right: -60px;
        }
        
        #top-pipe {
            top: 0;
        }
        
        #bottom-pipe {
            bottom: 0;
        }
        
        #score {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 24px;
            font-weight: bold;
            color: white;
            z-index: 100;
        }
        
        #game-over {
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
            z-index: 200;
        }
        
        #restart-btn {
            margin-top: 15px;
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        
        #restart-btn:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="bird"></div>
        <div id="top-pipe" class="pipe"></div>
        <div id="bottom-pipe" class="pipe"></div>
        <div id="score">0</div>
        <div id="game-over">
            <h2>Game Over!</h2>
            <p>Your score: <span id="final-score">0</span></p>
            <button id="restart-btn">Play Again</button>
        </div>
    </div>

    <script>
        // Game variables
        const bird = document.getElementById('bird');
        const topPipe = document.getElementById('top-pipe');
        const bottomPipe = document.getElementById('bottom-pipe');
        const scoreElement = document.getElementById('score');
        const gameOverElement = document.getElementById('game-over');
        const finalScoreElement = document.getElementById('final-score');
        const restartBtn = document.getElementById('restart-btn');
        
        let birdY = 300;
        let birdVelocity = 0;
        let gravity = 0.5;
        let gameSpeed = 3;
        let pipeGap = 200;
        let pipeWidth = 60;
        let topPipeHeight;
        let bottomPipeHeight;
        let pipeX = 400;
        let score = 0;
        let gameRunning = true;
        
        // Initialize game
        function init() {
            birdY = 300;
            birdVelocity = 0;
            pipeX = 400;
            score = 0;
            scoreElement.textContent = '0';
            gameRunning = true;
            gameOverElement.style.display = 'none';
            
            // Randomize initial pipe height
            randomizePipes();
            
            // Reset bird position
            bird.style.top = birdY + 'px';
            
            // Start game loop
            requestAnimationFrame(gameLoop);
        }
        
        // Randomize pipe heights
        function randomizePipes() {
            topPipeHeight = Math.floor(Math.random() * 250) + 50;
            bottomPipeHeight = 600 - topPipeHeight - pipeGap;
            
            topPipe.style.height = topPipeHeight + 'px';
            bottomPipe.style.height = bottomPipeHeight + 'px';
        }
        
        // Game loop
        function gameLoop() {
            if (!gameRunning) return;
            
            // Update bird position
            birdVelocity += gravity;
            birdY += birdVelocity;
            bird.style.top = birdY + 'px';
            
            // Update pipe position
            pipeX -= gameSpeed;
            topPipe.style.right = (400 - pipeX) + 'px';
            bottomPipe.style.right = (400 - pipeX) + 'px';
            
            // Check if bird hits the ground or ceiling
            if (birdY > 570 || birdY < 0) {
                endGame();
                return;
            }
            
            // Check for pipe collision
            if (
                pipeX < 60 && pipeX > -pipeWidth && 
                (birdY < topPipeHeight || birdY > 600 - bottomPipeHeight - 30)
            ) {
                endGame();
                return;
            }
            
            // Check if pipe passed
            if (pipeX === -pipeWidth) {
                score++;
                scoreElement.textContent = score;
                randomizePipes();
                pipeX = 400;
                
                // Increase speed slightly every 5 points
                if (score % 5 === 0) {
                    gameSpeed += 0.2;
                }
            }
            
            requestAnimationFrame(gameLoop);
        }
        
        // End game
        function endGame() {
            gameRunning = false;
            finalScoreElement.textContent = score;
            gameOverElement.style.display = 'block';
        }
        
        // Handle space key press
        document.addEventListener('keydown', function(e) {
            if (e.code === 'Space') {
                if (!gameRunning) {
                    init();
                } else {
                    birdVelocity = -10;
                }
                e.preventDefault();
            }
        });
        
        // Handle restart button click
        restartBtn.addEventListener('click', init);
        
        // Start the game
        init();
    </script>
</body>
</html>
