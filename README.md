# wizardry-hub
TechWizard872’s digital spellbook: a playground for experimentation, tinkering, and software sorcery. From prototypes to full-fledged tools, all crafted with passion and curiosity.
HOw code a Simple Ping Pong Game-------->
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<TechWizard872'Simple Ping Pong game.html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>TechWizard872'S I'LLPONG</title>
  <style>
    body {
      background: #343663;
      color: #fff;
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
    }
    #pongCanvas {
      background:#31c1e5;
      display: block;
      margin: 40px auto 10px auto;
      border: 4px solid #fff;
      border-radius: 10px;
    }
    #scoreBoard {
      font-size: 2em;
      margin-bottom: 10px;
      letter-spacing: 1em;
      user-select: none;
    }
    #instructions {
      color: #cb8198;
      font-size: 1em;
    }
  </style>
</head>
<body>
  <div id="scoreBoard">0 : 0</div>
  <canvas id="pongCanvas" width="700" height="400"></canvas>
  <div id="instructions">
    Move: Mouse or Up/Down arrows ;>
  </div>
  <script>
    const canvas = document.getElementById('pongCanvas');
    const ctx = canvas.getContext('2d');
    const width = canvas.width;
    const height = canvas.height;

    // Paddles
    const paddleHeight = 80;
    const paddleWidth = 12;
    let paddleSpeed = 6;
    let leftPaddleY = (height - paddleHeight) / 2;
    let rightPaddleY = (height - paddleHeight) / 2;
    let upPressed = false, downPressed = false;

    // Ball
    const ballSize = 14;
    let ballX = width/2 - ballSize/2;
    let ballY = height/2 - ballSize/2;
    let ballSpeedX = 6 * (Math.random() < 0.5 ? 1 : -1);
    let ballSpeedY = 4 * (Math.random()*2-1);

    // Score
    let playerScore = 0, aiScore = 0;

    // Mouse control
    canvas.addEventListener('mousemove', function(e) {
      const rect = canvas.getBoundingClientRect();
      let mouseY = e.clientY - rect.top;
      leftPaddleY = mouseY - paddleHeight/2;

      if(leftPaddleY < 0) leftPaddleY = 0;
      if(leftPaddleY > height-paddleHeight) leftPaddleY = height-paddleHeight;
    });

    // Keyboard control
    document.addEventListener('keydown', function(e) {
      if(e.code==='ArrowUp') upPressed = true;
      if(e.code==='ArrowDown') downPressed = true;
    });
    document.addEventListener('keyup', function(e) {
      if(e.code==='ArrowUp') upPressed = false;
      if(e.code==='ArrowDown') downPressed = false;
    });

    // Draw everything
    function draw() {
      ctx.clearRect(0, 0, width, height);

      // Draw center line
      ctx.strokeStyle = "#444";
      ctx.setLineDash([6, 8]);
      ctx.beginPath();
      ctx.moveTo(width/2, 0);
      ctx.lineTo(width/2, height);
      ctx.stroke();
      ctx.setLineDash([]);

      // Draw paddles
      ctx.fillStyle = "#fff";
      ctx.fillRect(8, leftPaddleY, paddleWidth, paddleHeight); // left
      ctx.fillRect(width-8-paddleWidth, rightPaddleY, paddleWidth, paddleHeight); // right

      // Draw ball
      ctx.beginPath();
      ctx.arc(ballX+ballSize/2, ballY+ballSize/2, ballSize/2, 0, Math.PI*2);
      ctx.fill();

      // Update score
      document.getElementById('scoreBoard').innerText = `${playerScore} : ${aiScore}`;
    }

    // Ball and paddle collision detection
    function updateBall() {
      ballX += ballSpeedX;
      ballY += ballSpeedY;

      // Top and bottom wall collision
      if(ballY < 0) {
        ballY = 0;
        ballSpeedY = -ballSpeedY;
      }
      if(ballY + ballSize > height) {
        ballY = height - ballSize;
        ballSpeedY = -ballSpeedY;
      }

      // Left paddle
      if (ballX <= 8+paddleWidth && ballY+ballSize > leftPaddleY && ballY < leftPaddleY+paddleHeight) {
        ballX = 8 + paddleWidth;
        ballSpeedX = -ballSpeedX * 1.05;
        // "english": add impact based on where it hit paddle
        let impact = (ballY + ballSize/2) - (leftPaddleY + paddleHeight/2);
        ballSpeedY += impact * 0.13;
      }

      // Right paddle
      if (ballX + ballSize >= width-8-paddleWidth && ballY+ballSize > rightPaddleY && ballY < rightPaddleY+paddleHeight) {
        ballX = width-8-paddleWidth-ballSize;
        ballSpeedX = -ballSpeedX * 1.05;
        let impact = (ballY + ballSize/2) - (rightPaddleY + paddleHeight/2);
        ballSpeedY += impact * 0.13;
      }

      // Left wall: AI scores
      if (ballX < 0) {
        aiScore++;
        resetBall();
      }
      // Right wall: player scores
      if (ballX + ballSize > width) {
        playerScore++;
        resetBall();
      }
    }

    function resetBall() {
      ballX = width/2 - ballSize/2;
      ballY = height/2 - ballSize/2;
      // Alternate ball direction
      ballSpeedX = 6 * (Math.random() < 0.5 ? 1 : -1);
      ballSpeedY = 4 * (Math.random()*2-1);
    }

    // Player paddle update (for keyboard)
    function updatePlayerPaddle() {
      if(upPressed) leftPaddleY -= paddleSpeed;
      if(downPressed) leftPaddleY += paddleSpeed;
      // Boundaries
      if(leftPaddleY < 0) leftPaddleY = 0;
      if(leftPaddleY > height-paddleHeight) leftPaddleY = height-paddleHeight;
    }

    // Simple AI for right paddle
    function updateAIPaddle() {
      let aiCenter = rightPaddleY + paddleHeight/2;
      let target = ballY + ballSize/2;
      let diff = target - aiCenter;
      rightPaddleY += Math.sign(diff) * Math.min(Math.abs(diff), 4.3); // Moderate paddle speed
      // Boundaries
      if(rightPaddleY < 0) rightPaddleY = 0;
      if(rightPaddleY > height-paddleHeight) rightPaddleY = height-paddleHeight;
    }

    function gameLoop() {
      updatePlayerPaddle();
      updateAIPaddle();
      updateBall();
      draw();
      requestAnimationFrame(gameLoop);
    }

    draw();
    gameLoop();
  </script>
</body>
</html>
