# 🕹️ Neon Breakout Arcade

Um jogo clássico de **Breakout (Quebra-blocos)** desenvolvido com visual neon arcade retro, efeitos de partículas, trilha sonora sintetizada via Web Audio API e controles adaptados para mouse e teclado.

## 🚀 Demonstração Online
Jogue agora direto no navegador:
👉 [https://diegosoares-dev.github.io/Neon-Breakout-Arcade/](https://diegosoares-dev.github.io/Neon-Breakout-Arcade/)

---

## 🛠️ Tecnologias Utilizadas
* **HTML5 Canvas** (Renderização gráfica em 2D)
* **JavaScript Moderno (ES6+)** (Física de colisão, animação e lógica de jogo)
* **CSS3** (Estilização futurista / Cyberpunk HUD)
* **Web Audio API** (Efeitos sonoros gerados por código em tempo real)

---

## 🎮 Como Jogar
* **Com o Mouse:** Mova o cursor horizontalmente para controlar a barra de luz.
* **Com o Teclado:** Utilize as **Setas Direcionais (Esquerda / Direita)** para movimentar a barra.
* **Objetivo:** Destrua todos os blocos coloridos sem deixar a esfera cair!

---

## ⚙️ Como Executar Localmente
1. Clone o repositório:
   ```bash
   git clone [https://github.com/DIEGOSOARES-DEV/Neon-Breakout-Arcade.git](https://github.com/DIEGOSOARES-DEV/Neon-Breakout-Arcade.git)

   Abra o arquivo index.html em qualquer navegador web moderno.

   ---

### 💻 3. Código Completo do Jogo (`index.html`)
Salve o código abaixo na sua máquina com o nome exato de **`index.html`** e faça o upload para o repositório (`Add file` > `Upload files`):

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>NEON_BREAKOUT // Arcade HUD</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700;900&family=Rajdhani:wght@500;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #07090f;
      --panel: #0e121d;
      --cyan: #00f0ff;
      --cyan-glow: rgba(0, 240, 255, 0.4);
      --pink: #ff0055;
      --pink-glow: rgba(255, 0, 85, 0.4);
      --purple: #9d00ff;
      --text: #d8e2ec;
      --muted: #536275;
      --border: #1a2333;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
    body {
      background-color: var(--bg);
      color: var(--text);
      font-family: 'Rajdhani', sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 15px;
      overflow: hidden;
    }
    body::before {
      content: "";
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background: linear-gradient(rgba(0, 240, 255, 0.03) 1px, transparent 1px),
                  linear-gradient(90deg, rgba(0, 240, 255, 0.03) 1px, transparent 1px);
      background-size: 30px 30px;
      pointer-events: none;
      z-index: 0;
    }
    .arcade-container {
      position: relative;
      z-index: 2;
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 20px;
      box-shadow: 0 0 35px rgba(0,0,0,0.8), 0 0 20px rgba(0, 240, 255, 0.1);
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    .hud-header {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 15px;
      border-bottom: 1px dashed var(--border);
      padding-bottom: 10px;
    }
    h1 {
      font-family: 'Orbitron', monospace;
      font-size: 1.1rem;
      letter-spacing: 2px;
      color: var(--cyan);
      text-shadow: 0 0 8px var(--cyan-glow);
    }
    .stats-group {
      display: flex;
      gap: 20px;
      font-family: 'Orbitron', monospace;
      font-size: 0.85rem;
    }
    .stat-item span { color: var(--cyan); }
    canvas {
      background: #040508;
      border: 1px solid var(--border);
      border-radius: 6px;
      box-shadow: inset 0 0 20px rgba(0,0,0,0.9);
      cursor: crosshair;
      display: block;
    }
    .instructions {
      margin-top: 12px;
      font-size: 0.85rem;
      color: var(--muted);
      text-align: center;
      font-family: 'Orbitron', monospace;
      letter-spacing: 1px;
    }
    .instructions span { color: var(--text); }
  </style>
</head>
<body>
  <div class="arcade-container">
    <div class="hud-header">
      <h1>NEON_BREAKOUT</h1>
      <div class="stats-group">
        <div class="stat-item">SCORE: <span id="scoreVal">0</span></div>
        <div class="stat-item">LIVES: <span id="livesVal">3</span></div>
      </div>
    </div>
    <canvas id="gameCanvas" width="480" height="360"></canvas>
    <div class="instructions">Use as <span>Setas</span> ou o <span>Mouse</span> para mover a barra</div>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const scoreVal = document.getElementById('scoreVal');
    const livesVal = document.getElementById('livesVal');

    let score = 0;
    let lives = 3;

    let x = canvas.width / 2;
    let y = canvas.height - 30;
    let dx = 3;
    let dy = -3;
    const ballRadius = 6;

    const paddleHeight = 10;
    const paddleWidth = 75;
    let paddleX = (canvas.width - paddleWidth) / 2;

    let rightPressed = false;
    let leftPressed = false;

    const brickRowCount = 5;
    const brickColumnCount = 7;
    const brickWidth = 58;
    const brickHeight = 16;
    const brickPadding = 8;
    const brickOffsetTop = 25;
    const brickOffsetLeft = 22;

    const bricks = [];
    const colors = ['#ff0055', '#ffaa00', '#00f0ff', '#9d00ff', '#00ff66'];

    function initBricks() {
      for (let c = 0; c < brickColumnCount; c++) {
        bricks[c] = [];
        for (let r = 0; r < brickRowCount; r++) {
          bricks[c][r] = { x: 0, y: 0, status: 1, color: colors[r % colors.length] };
        }
      }
    }
    initBricks();

    let particles = [];
    function createParticles(px, py, color) {
      for (let i = 0; i < 8; i++) {
        particles.push({
          x: px,
          y: py,
          dx: (Math.random() - 0.5) * 4,
          dy: (Math.random() - 0.5) * 4,
          life: 20,
          color: color
        });
      }
    }

    let audioCtx = null;
    function playSound(freq, duration) {
      try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === 'suspended') audioCtx.resume();
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
        gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);
        osc.start();
        osc.stop(audioCtx.currentTime + duration);
      } catch(e) {}
    }

    document.addEventListener('keydown', e => {
      if (e.key === 'Right' || e.key === 'ArrowRight') rightPressed = true;
      else if (e.key === 'Left' || e.key === 'ArrowLeft') leftPressed = true;
    });
    document.addEventListener('keyup', e => {
      if (e.key === 'Right' || e.key === 'ArrowRight') rightPressed = false;
      else if (e.key === 'Left' || e.key === 'ArrowLeft') leftPressed = false;
    });
    canvas.addEventListener('mousemove', e => {
      const rect = canvas.getBoundingClientRect();
      const relativeX = e.clientX - rect.left;
      if (relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth / 2;
      }
    });

    function collisionDetection() {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          let b = bricks[c][r];
          if (b.status === 1) {
            if (x > b.x && x < b.x + brickWidth && y > b.y && y < b.y + brickHeight) {
              dy = -dy;
              b.status = 0;
              score += 10;
              scoreVal.textContent = score;
              createParticles(b.x + brickWidth/2, b.y + brickHeight/2, b.color);
              playSound(580, 0.08);

              if (score === brickRowCount * brickColumnCount * 10) {
                alert('FASE CONCLUÍDA! REINICIANDO...');
                document.location.reload();
              }
            }
          }
        }
      }
    }

    function drawBall() {
      ctx.beginPath();
      ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
      ctx.fillStyle = '#00f0ff';
      ctx.shadowBlur = 12;
      ctx.shadowColor = '#00f0ff';
      ctx.fill();
      ctx.closePath();
      ctx.shadowBlur = 0;
    }

    function drawPaddle() {
      ctx.beginPath();
      ctx.rect(paddleX, canvas.height - paddleHeight - 6, paddleWidth, paddleHeight);
      ctx.fillStyle = '#ff0055';
      ctx.shadowBlur = 12;
      ctx.shadowColor = '#ff0055';
      ctx.fill();
      ctx.closePath();
      ctx.shadowBlur = 0;
    }

    function drawBricks() {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          if (bricks[c][r].status === 1) {
            let brickX = (c * (brickWidth + brickPadding)) + brickOffsetLeft;
            let brickY = (r * (brickHeight + brickPadding)) + brickOffsetTop;
            bricks[c][r].x = brickX;
            bricks[c][r].y = brickY;
            ctx.beginPath();
            ctx.rect(brickX, brickY, brickWidth, brickHeight);
            ctx.fillStyle = bricks[c][r].color;
            ctx.shadowBlur = 8;
            ctx.shadowColor = bricks[c][r].color;
            ctx.fill();
            ctx.closePath();
            ctx.shadowBlur = 0;
          }
        }
      }
    }

    function drawParticles() {
      particles.forEach((p, index) => {
        p.x += p.dx;
        p.y += p.dy;
        p.life--;
        ctx.fillStyle = p.color;
        ctx.fillRect(p.x, p.y, 2, 2);
        if (p.life <= 0) particles.splice(index, 1);
      });
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawBricks();
      drawBall();
      drawPaddle();
      drawParticles();
      collisionDetection();

      if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) {
        dx = -dx;
        playSound(300, 0.05);
      }
      if (y + dy < ballRadius) {
        dy = -dy;
        playSound(300, 0.05);
      } else if (y + dy > canvas.height - ballRadius - 6) {
        if (x > paddleX && x < paddleX + paddleWidth) {
          dy = -dy;
          playSound(440, 0.06);
          let hitPoint = x - (paddleX + paddleWidth / 2);
          dx = hitPoint * 0.15;
        } else {
          lives--;
          livesVal.textContent = lives;
          playSound(150, 0.2);
          if (!lives) {
            alert('GAME OVER');
            document.location.reload();
          } else {
            x = canvas.width / 2;
            y = canvas.height - 30;
            dx = 3;
            dy = -3;
            paddleX = (canvas.width - paddleWidth) / 2;
          }
        }
      }

      x += dx;
      y += dy;

      if (rightPressed && paddleX < canvas.width - paddleWidth) {
        paddleX += 5;
      } else if (leftPressed && paddleX > 0) {
        paddleX -= 5;
      }

      requestAnimationFrame(draw);
    }

    draw();
  </script>
</body>
</html>
