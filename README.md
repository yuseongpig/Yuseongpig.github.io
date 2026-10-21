<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=no" />
  <title>Snake Black</title>
  <style>
    :root {
      --bg-1: #05070b;
      --bg-2: #0d1117;
      --panel: rgba(14, 17, 22, 0.96);
      --panel-2: rgba(18, 21, 28, 0.98);
      --line: rgba(148, 163, 184, 0.15);
      --text: #edf6ff;
      --muted: #a7b7c8;
      --green-1: #9def9d;
      --green-2: #56d364;
      --green-3: #27b85e;
      --red: #ff5a5f;
      --board-1: #0a1017;
      --board-2: #101923;
      --grid: rgba(255,255,255,0.04);
      --shadow: rgba(0, 0, 0, 0.45);
    }

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
      -webkit-touch-callout: none;
      user-select: none;
      -webkit-user-select: none;
      touch-action: manipulation;
    }

    html, body {
      margin: 0;
      width: 100%;
      height: 100%;
      background:
        radial-gradient(circle at 15% 10%, rgba(86, 211, 100, 0.08), transparent 25%),
        linear-gradient(180deg, var(--bg-1), var(--bg-2));
      color: var(--text);
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", sans-serif;
      overscroll-behavior: none;
    }

    body {
      display: flex;
      align-items: center;
      justify-content: center;
      padding: max(16px, env(safe-area-inset-top)) 16px max(16px, env(safe-area-inset-bottom));
    }

    .app {
      width: min(92vw, 430px);
      border-radius: 26px;
      background: linear-gradient(180deg, rgba(12,16,22,0.97), rgba(9,12,17,0.99));
      border: 1px solid var(--line);
      box-shadow:
        0 25px 60px rgba(0,0,0,0.5),
        inset 0 1px 0 rgba(255,255,255,0.04);
      overflow: hidden;
    }

    .topbar {
      padding: 18px 18px 12px;
      border-bottom: 1px solid rgba(148,163,184,0.08);
      background: rgba(255,255,255,0.01);
    }

    .title-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 14px;
    }

    .brand {
      display: inline-flex;
      align-items: center;
      gap: 10px;
      font-size: clamp(1.25rem, 2vw, 1.75rem);
      font-weight: 800;
      letter-spacing: 0.08em;
    }

    .brand-mark {
      width: 18px;
      height: 18px;
      border-radius: 50%;
      background: linear-gradient(135deg, var(--green-1), var(--green-2));
      box-shadow: 0 0 18px rgba(86, 211, 100, 0.4);
      position: relative;
    }

    .brand-mark::before {
      content: "";
      position: absolute;
      inset: 5px;
      border-radius: 50%;
      background: rgba(2, 6, 12, 0.75);
    }

    .hud {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }

    .score-box {
      background: linear-gradient(180deg, rgba(20, 24, 30, 0.95), rgba(13, 17, 23, 0.97));
      border: 1px solid var(--line);
      border-radius: 16px;
      min-height: 72px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.02);
    }

    .label {
      font-size: 0.68rem;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--muted);
      font-weight: 700;
    }

    .value {
      margin-top: 6px;
      font-size: clamp(1.6rem, 4vw, 2.1rem);
      font-weight: 900;
      line-height: 1;
      letter-spacing: -0.04em;
      color: var(--text);
    }

    .action-row {
      display: flex;
      gap: 10px;
      margin-top: 14px;
    }

    button {
      appearance: none;
      -webkit-appearance: none;
      border: none;
      outline: none;
      background: none;
      color: var(--text);
      font: inherit;
      cursor: pointer;
      border-radius: 14px;
      transition: transform 0.08s ease, filter 0.08s ease, background 0.08s ease;
      touch-action: manipulation;
      -webkit-user-select: none;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    button:active {
      transform: scale(0.98);
      filter: brightness(1.08);
    }

    .primary-btn {
      flex: 1;
      height: 52px;
      font-weight: 800;
      letter-spacing: 0.03em;
      background: linear-gradient(180deg, rgba(97, 114, 133, 0.25), rgba(53, 62, 74, 0.25));
      border: 1px solid rgba(148,163,184,0.2);
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.04);
    }

    .secondary-btn {
      flex: 1;
      height: 52px;
      font-weight: 800;
      letter-spacing: 0.03em;
      background: rgba(255,255,255,0.02);
      border: 1px solid rgba(148,163,184,0.14);
    }

    .game-wrap {
      position: relative;
      padding: 16px 18px 18px;
      background: rgba(255,255,255,0.01);
    }

    .game-frame {
      position: relative;
      width: min(84vw, 360px);
      height: min(84vw, 360px);
      margin: 0 auto;
      border-radius: 22px;
      background: linear-gradient(180deg, var(--board-1), var(--board-2));
      border: 1px solid var(--line);
      box-shadow:
        inset 0 0 0 1px rgba(255,255,255,0.02),
        0 12px 24px rgba(0,0,0,0.28);
      overflow: hidden;
    }

    canvas {
      display: block;
      width: 100%;
      height: 100%;
      background: linear-gradient(180deg, var(--board-1), var(--board-2));
    }

    .overlay {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      background: rgba(5, 7, 11, 0.52);
      backdrop-filter: blur(3px);
      -webkit-backdrop-filter: blur(3px);
      z-index: 5;
      opacity: 1;
      visibility: visible;
      transition: opacity 0.2s ease, visibility 0.2s ease;
    }

    .overlay.hidden {
      opacity: 0;
      visibility: hidden;
      pointer-events: none;
    }

    .overlay-card {
      width: min(74vw, 250px);
      background: rgba(17, 21, 28, 0.96);
      border: 1px solid var(--line);
      border-radius: 18px;
      padding: 18px 16px 16px;
      text-align: center;
      box-shadow: 0 18px 34px rgba(0,0,0,0.35);
    }

    .overlay-card h2 {
      margin: 0 0 8px;
      font-size: clamp(1.3rem, 4vw, 1.75rem);
      letter-spacing: 0.02em;
    }

    .overlay-card p {
      margin: 0 0 16px;
      color: var(--muted);
      font-size: 0.94rem;
      line-height: 1.4;
    }

    .overlay-card .primary-btn {
      width: 100%;
      height: 48px;
    }

    .controls {
      width: min(84vw, 360px);
      margin: 16px auto 0;
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 12px;
    }

    .empty {
      visibility: hidden;
    }

    .dir-btn {
      width: 100%;
      height: 64px;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 18px;
      background: linear-gradient(180deg, rgba(20,24,30,0.98), rgba(13,16,22,0.98));
      border: 1px solid rgba(148,163,184,0.16);
      color: var(--text);
      font-size: 1.5rem;
      font-weight: 900;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.03);
    }

    .dir-btn[data-dir="up"]::before { content: "▲"; }
    .dir-btn[data-dir="left"]::before { content: "◀"; }
    .dir-btn[data-dir="down"]::before { content: "▼"; }
    .dir-btn[data-dir="right"]::before { content: "▶"; }

    .dir-btn:active {
      background: linear-gradient(180deg, rgba(86, 211, 100, 0.12), rgba(12, 16, 22, 0.98));
      border-color: rgba(86,211,100,0.32);
    }

    @media (max-width: 360px) {
      .dir-btn {
        height: 58px;
      }
    }
  </style>
</head>
<body>
  <div class="app">
    <div class="topbar">
      <div class="title-row">
        <div class="brand">
          <span class="brand-mark"></span>
          <span>Snake</span>
        </div>
      </div>

      <div class="hud">
        <div class="score-box">
          <span class="label">Score</span>
          <span id="score" class="value">0</span>
        </div>

        <div class="score-box">
          <span class="label">Best</span>
          <span id="bestScore" class="value">0</span>
        </div>
      </div>

      <div class="action-row">
        <button id="startBtn" class="primary-btn" type="button">시작</button>
        <button id="pauseBtn" class="secondary-btn" type="button">일시정지</button>
      </div>
    </div>

    <div class="game-wrap">
      <div class="game-frame" id="gameFrame">
        <canvas id="gameCanvas" width="360" height="360"></canvas>

        <div id="overlay" class="overlay hidden">
          <div class="overlay-card">
            <h2 id="overlayTitle">게임 종료</h2>
            <p id="overlayMessage">다시 도전해보세요.</p>
            <button id="overlayRestartBtn" class="primary-btn" type="button">다시 시작</button>
          </div>
        </div>
      </div>

      <div class="controls" aria-label="direction pad">
        <div class="empty"></div>
        <button class="dir-btn" data-dir="up" type="button" aria-label="위로"></button>
        <div class="empty"></div>

        <button class="dir-btn" data-dir="left" type="button" aria-label="왼쪽"></button>
        <button class="dir-btn" data-dir="down" type="button" aria-label="아래로"></button>
        <button class="dir-btn" data-dir="right" type="button" aria-label="오른쪽"></button>
      </div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const scoreEl = document.getElementById("score");
    const bestScoreEl = document.getElementById("bestScore");
    const startBtn = document.getElementById("startBtn");
    const pauseBtn = document.getElementById("pauseBtn");
    const overlay = document.getElementById("overlay");
    const overlayTitle = document.getElementById("overlayTitle");
    const overlayMessage = document.getElementById("overlayMessage");
    const overlayRestartBtn = document.getElementById("overlayRestartBtn");
    const dirButtons = document.querySelectorAll(".dir-btn");

    const gridSize = 18;
    const cellSize = canvas.width / gridSize;

    let snake = [];
    let direction = { x: 1, y: 0 };
    let nextDirection = { x: 1, y: 0 };
    let food = { x: 0, y: 0 };

    let score = 0;
    let bestScore = Number(localStorage.getItem("snake-black-best-score") || 0);

    let started = false;
    let paused = false;
    let gameOver = false;
    let loopId = null;
    let swipeStart = null;

    function updateHud() {
      scoreEl.textContent = String(score);
      bestScoreEl.textContent = String(bestScore);
    }

    function saveBestScore() {
      localStorage.setItem("snake-black-best-score", String(bestScore));
    }

    function setOverlay(show, title, message) {
      if (show) {
        overlayTitle.textContent = title;
        overlayMessage.textContent = message;
        overlay.classList.remove("hidden");
      } else {
        overlay.classList.add("hidden");
      }
    }

    function resetGameState() {
      snake = [
        { x: 6, y: 9 },
        { x: 5, y: 9 },
        { x: 4, y: 9 }
      ];
      direction = { x: 1, y: 0 };
      nextDirection = { x: 1, y: 0 };
      score = 0;
      updateHud();
      spawnFood();
    }

    function spawnFood() {
      let x, y;
      do {
        x = Math.floor(Math.random() * gridSize);
        y = Math.floor(Math.random() * gridSize);
      } while (snake.some(seg => seg.x === x && seg.y === y));

      food = { x, y };
    }

    function drawBoard() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      const bg = ctx.createLinearGradient(0, 0, 0, canvas.height);
      bg.addColorStop(0, "#0b1017");
      bg.addColorStop(1, "#101821");
      ctx.fillStyle = bg;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.strokeStyle = "rgba(255,255,255,0.04)";
      ctx.lineWidth = 1;

      for (let i = 0; i <= gridSize; i++) {
        const p = i * cellSize;
        ctx.beginPath();
        ctx.moveTo(p, 0);
        ctx.lineTo(p, canvas.height);
        ctx.stroke();

        ctx.beginPath();
        ctx.moveTo(0, p);
        ctx.lineTo(canvas.width, p);
        ctx.stroke();
      }
    }

    function drawFood() {
      const cx = food.x * cellSize + cellSize / 2;
      const cy = food.y * cellSize + cellSize / 2;
      const r = cellSize * 0.33;

      ctx.save();
      ctx.shadowColor = "rgba(255, 90, 95, 0.55)";
      ctx.shadowBlur = 18;

      ctx.fillStyle = "#ff5a5f";
      ctx.beginPath();
      ctx.arc(cx, cy, r, 0, Math.PI * 2);
      ctx.fill();

      ctx.fillStyle = "rgba(255,255,255,0.32)";
      ctx.beginPath();
      ctx.arc(cx - r * 0.18, cy - r * 0.18, r * 0.22, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    function drawSnake() {
      snake.forEach((segment, index) => {
        const x = segment.x * cellSize + 1;
        const y = segment.y * cellSize + 1;
        const size = cellSize - 2;

        if (index === 0) {
          const headGrad = ctx.createLinearGradient(x, y, x + size, y + size);
          headGrad.addColorStop(0, "#c8f9c4");
          headGrad.addColorStop(0.5, "#9def9d");
          headGrad.addColorStop(1, "#56d364");

          ctx.fillStyle = headGrad;
          ctx.fillRect(x, y, size, size);

          ctx.fillStyle = "#0d1117";
          ctx.fillRect(x + size * 0.25, y + size * 0.28, 2.5, 2.5);
          ctx.fillRect(x + size * 0.68, y + size * 0.28, 2.5, 2.5);

          ctx.fillStyle = "#0d1117";
          ctx.fillRect(x + size * 0.46, y + size * 0.62, 3, 3);
        } else {
          const bodyGrad = ctx.createLinearGradient(x, y, x + size, y + size);
          bodyGrad.addColorStop(0, "#9def9d");
          bodyGrad.addColorStop(1, "#2dbd6b");

          ctx.fillStyle = bodyGrad;
          ctx.fillRect(x, y, size, size);
        }
      });
    }

    function render() {
      drawBoard();
      drawFood();
      drawSnake();
    }

    function clearLoop() {
      if (loopId) {
        clearInterval(loopId);
        loopId = null;
      }
    }

    function getSpeed() {
      return Math.max(80, 175 - score * 5);
    }

    function startLoop() {
      clearLoop();
      loopId = setInterval(stepGame, getSpeed());
    }

    function canChangeDirection(nx, ny) {
      if (snake.length <= 1) return true;
      return !(direction.x === -nx && direction.y === -ny);
    }

    function setDirection(nx, ny) {
      if (!started || gameOver) return;
      if (!canChangeDirection(nx, ny)) return;
      nextDirection = { x: nx, y: ny };
    }

    function updateDirection() {
      direction = { x: nextDirection.x, y: nextDirection.y };
    }

    function endGame() {
      clearLoop();
      gameOver = true;
      started = false;
      paused = false;
      setOverlay(true, "게임 종료", "점수: " + score + "점");
    }

    function stepGame() {
      if (!started || paused || gameOver) return;

      updateDirection();

      const head = {
        x: snake[0].x + direction.x,
        y: snake[0].y + direction.y
      };

      const hitWall =
        head.x < 0 ||
        head.x >= gridSize ||
        head.y < 0 ||
        head.y >= gridSize;

      const eatFood = head.x === food.x && head.y === food.y;
      const collisionCheck = eatFood ? snake : snake.slice(0, -1);

      for (let i = 0; i < collisionCheck.length; i++) {
        if (head.x === collisionCheck[i].x && head.y === collisionCheck[i].y) {
          endGame();
          render();
          return;
        }
      }

      if (hitWall) {
        endGame();
        render();
        return;
      }

      snake.unshift(head);

      if (eatFood) {
        score += 1;
        if (score > bestScore) {
          bestScore = score;
          saveBestScore();
        }
        updateHud();
        spawnFood();
        startLoop();
      } else {
        snake.pop();
      }

      render();
    }

    function startGame() {
      clearLoop();
      resetGameState();
      started = true;
      paused = false;
      gameOver = false;
      setOverlay(false);
      startLoop();
    }

    function togglePause() {
      if (!started || gameOver) return;

      paused = !paused;

      if (paused) {
        clearLoop();
        setOverlay(true, "일시정지", "계속하려면 다시 눌러주세요.");
      } else {
        setOverlay(false);
        startLoop();
      }
    }

    function handleKeyboard(event) {
      const key = event.key.toLowerCase();
      const mapping = {
        arrowup: [0, -1],
        w: [0, -1],
        arrowdown: [0, 1],
        s: [0, 1],
        arrowleft: [-1, 0],
        a: [-1, 0],
        arrowright: [1, 0],
        d: [1, 0]
      };

      if (!mapping[key]) return;

      event.preventDefault();
      const [x, y] = mapping[key];
      setDirection(x, y);

      if (!started && !gameOver) {
        startGame();
      }
    }

    function bindDirectionButton(button) {
      const map = {
        up: [0, -1],
        down: [0, 1],
        left: [-1, 0],
        right: [1, 0]
      };

      const handle = (event) => {
        event.preventDefault();
        event.stopPropagation();
        const [x, y] = map[button.dataset.dir];
        setDirection(x, y);

        if (!started && !gameOver) {
          startGame();
        }
      };

      button.addEventListener("pointerdown", handle, { passive: false });
      button.addEventListener("touchstart", handle, { passive: false });
    }

    dirButtons.forEach(bindDirectionButton);

    startBtn.addEventListener("click", function (event) {
      event.preventDefault();
      if (!started || gameOver) {
        startGame();
      } else if (paused) {
        togglePause();
      }
    });

    pauseBtn.addEventListener("click", function (event) {
      event.preventDefault();
      togglePause();
    });

    overlayRestartBtn.addEventListener("click", function (event) {
      event.preventDefault();
      startGame();
    });

    document.addEventListener("keydown", handleKeyboard);

    const gameFrame = document.getElementById("gameFrame");

    gameFrame.addEventListener("touchstart", function (event) {
      if (event.touches && event.touches.length > 0) {
        const touch = event.touches[0];
        swipeStart = { x: touch.clientX, y: touch.clientY };
      }
    }, { passive: false });

    gameFrame.addEventListener("touchend", function (event) {
      if (!swipeStart || !event.changedTouches || event.changedTouches.length === 0) return;

      const touch = event.changedTouches[0];
      const dx = touch.clientX - swipeStart.x;
      const dy = touch.clientY - swipeStart.y;
      const absX = Math.abs(dx);
      const absY = Math.abs(dy);

      swipeStart = null;

      if (Math.max(absX, absY) < 18) return;

      event.preventDefault();

      if (absX > absY) {
        setDirection(dx > 0 ? 1 : -1, 0);
      } else {
        setDirection(0, dy > 0 ? 1 : -1);
      }

      if (!started && !gameOver) {
        startGame();
      }
    }, { passive: false });

    bestScoreEl.textContent = String(bestScore);
    resetGameState();
    render();
    setOverlay(false);
  </script>
</body>
</html>
