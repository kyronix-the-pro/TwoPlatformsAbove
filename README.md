<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Two Platforms Above</title>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    }

    body {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;
      background: #000000;
      color: #ffffff;
    }

    /* Main Menu */
    .menu-container {
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
      z-index: 2;
    }

    h1.title {
      font-size: 3rem;
      letter-spacing: 3px;
      text-transform: uppercase;
      text-shadow: 0 0 15px rgba(255, 255, 255, 0.4);
      margin-bottom: 10px;
      font-weight: 800;
    }

    .menu-btn {
      width: 220px;
      padding: 14px;
      font-size: 1.2rem;
      font-weight: 700;
      color: #ffffff;
      background-color: rgba(255, 255, 255, 0.1);
      border: 2px solid #ffffff;
      border-radius: 8px;
      cursor: pointer;
      backdrop-filter: blur(5px);
      transition: all 0.3s ease;
    }

    .menu-btn:hover {
      background-color: #ffffff;
      color: #000000;
      box-shadow: 0 0 20px rgba(255, 255, 255, 0.6);
      transform: translateY(-2px);
    }

    /* Modal Box */
    .modal-overlay {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.7);
      backdrop-filter: blur(4px);
      justify-content: center;
      align-items: center;
      z-index: 10;
    }

    .modal-box {
      width: 320px;
      background: #111111;
      border: 2px solid #ffffff;
      border-radius: 12px;
      padding: 24px;
      display: flex;
      flex-direction: column;
      gap: 15px;
      box-shadow: 0 0 25px rgba(0, 0, 0, 0.8);
    }

    .modal-title {
      font-size: 1.3rem;
      text-align: center;
      font-weight: 700;
    }

    .slot-btn {
      width: 100%;
      padding: 12px;
      background: #1a1a1a;
      border: 1px solid rgba(255, 255, 255, 0.3);
      color: #ffffff;
      border-radius: 6px;
      font-size: 1rem;
      font-weight: 600;
      cursor: pointer;
    }

    .slot-btn:hover:not(:disabled) {
      border-color: #ffffff;
      background: #2a2a2a;
    }

    .slot-btn:disabled {
      opacity: 0.4;
      cursor: not-allowed;
    }

    .close-btn {
      background: transparent;
      border: none;
      color: #888888;
      cursor: pointer;
      font-size: 0.9rem;
      text-decoration: underline;
    }

    /* Loading Screen */
    .loading-container {
      display: none;
      flex-direction: column;
      align-items: center;
      gap: 20px;
    }

    .loading-text {
      font-size: 2rem;
      letter-spacing: 2px;
      font-weight: 700;
    }

    .progress-bar-bg {
      width: 300px;
      height: 16px;
      background: #222222;
      border-radius: 10px;
      overflow: hidden;
      border: 1px solid #444444;
    }

    .progress-bar-fill {
      width: 0%;
      height: 100%;
      background: #00ff66;
      box-shadow: 0 0 10px #00ff66;
    }

    /* Game Canvas */
    #gameCanvas {
      display: none;
      width: 100vw;
      height: 100vh;
    }
  </style>
</head>
<body>

  <div class="menu-container" id="menuContainer">
    <h1 class="title">Two platforms Above</h1>
    <button class="menu-btn" id="newGameBtn">New game</button>
    <button class="menu-btn" id="loadGameBtn">Load game</button>
  </div>

  <div class="modal-overlay" id="slotModal">
    <div class="modal-box">
      <div class="modal-title" id="modalTitle">Select Slot</div>
      <button class="slot-btn" id="slot1" onclick="selectSlot(1)">Slot 1</button>
      <button class="slot-btn" id="slot2" onclick="selectSlot(2)">Slot 2</button>
      <button class="slot-btn" id="slot3" onclick="selectSlot(3)">Slot 3</button>
      <button class="close-btn" onclick="closeModal()">Back</button>
    </div>
  </div>

  <div class="loading-container" id="loadingContainer">
    <div class="loading-text">Loading...</div>
    <div class="progress-bar-bg">
      <div class="progress-bar-fill" id="progressFill"></div>
    </div>
  </div>

  <canvas id="gameCanvas"></canvas>

  <script>
    let currentMode = '';

    const menuContainer = document.getElementById('menuContainer');
    const slotModal = document.getElementById('slotModal');
    const modalTitle = document.getElementById('modalTitle');
    const loadingContainer = document.getElementById('loadingContainer');
    const progressFill = document.getElementById('progressFill');
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    document.getElementById('newGameBtn').addEventListener('click', () => openModal('new'));
    document.getElementById('loadGameBtn').addEventListener('click', () => openModal('load'));

    function openModal(mode) {
      currentMode = mode;
      modalTitle.innerText = mode === 'new' ? 'New Game - Choose Slot' : 'Load Game';
      
      for (let i = 1; i <= 3; i++) {
        const slotButton = document.getElementById(`slot${i}`);
        const hasSavedGame = localStorage.getItem(`save_slot_${i}`);

        if (mode === 'new') {
          slotButton.disabled = false;
          slotButton.innerText = hasSavedGame ? `Slot ${i} (Overwrite)` : `Slot ${i} (Empty)`;
        } else if (mode === 'load') {
          slotButton.disabled = !hasSavedGame;
          slotButton.innerText = hasSavedGame ? `Slot ${i}: Game Saved` : `Slot ${i}: Empty`;
        }
      }
      slotModal.style.display = 'flex';
    }

    function closeModal() {
      slotModal.style.display = 'none';
    }

    function selectSlot(slotNumber) {
      if (currentMode === 'new') {
        localStorage.setItem(`save_slot_${slotNumber}`, 'active');
      }
      closeModal();
      startLoadingSequence();
    }

    function startLoadingSequence() {
      menuContainer.style.display = 'none';
      loadingContainer.style.display = 'flex';

      let progress = 0;
      const interval = setInterval(() => {
        progress += 2;
        progressFill.style.width = progress + '%';

        if (progress >= 100) {
          clearInterval(interval);
          finishLoading();
        }
      }, 30);
    }

    function finishLoading() {
      loadingContainer.style.display = 'none';
      canvas.style.display = 'block';
      initGame();
    }

    /* --- GAME CODE --- */
    let keys = {};
    let cameraX = 0;
    let bgAnimOffset = 0;

    const player = {
      x: 100,
      y: 0,
      width: 40,
      height: 70,
      vx: 0,
      vy: 0,
      speed: 6,
      jumpForce: -14,
      gravity: 0.7,
      grounded: false
    };

    function resizeCanvas() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    function initGame() {
      resizeCanvas();
      window.addEventListener('resize', resizeCanvas);

      window.addEventListener('keydown', (e) => keys[e.code] = true);
      window.addEventListener('keyup', (e) => keys[e.code] = false);

      player.y = canvas.height - 100 - player.height;
      requestAnimationFrame(gameLoop);
    }

    function update() {
      // Horizontal Movement
      if (keys['ArrowRight'] || keys['KeyD']) {
        player.vx = player.speed;
      } else if (keys['ArrowLeft'] || keys['KeyA']) {
        player.vx = -player.speed;
      } else {
        player.vx = 0;
      }

      // Jump
      if ((keys['ArrowUp'] || keys['KeyW'] || keys['Space']) && player.grounded) {
        player.vy = player.jumpForce;
        player.grounded = false;
      }

      // Apply Gravity
      player.vy += player.gravity;

      // Update position
      player.x += player.vx;
      player.y += player.vy;

      // Camera centers smoothly around player (Infinite left & right walking)
      cameraX = player.x - canvas.width / 2 + player.width / 2;

      // Ground Collision
      const groundY = canvas.height - 100;
      if (player.y + player.height >= groundY) {
        player.y = groundY - player.height;
        player.vy = 0;
        player.grounded = true;
      }

      // Moving Background Animation Offset
      bgAnimOffset += 0.5;
    }

    function drawCylinder(x, y, w, h) {
      const rx = w / 2;
      const ry = 10;

      // Cylinder Body
      ctx.fillStyle = '#1e90ff';
      ctx.fillRect(x, y + ry, w, h - (ry * 2));

      // Top Ellipse
      ctx.beginPath();
      ctx.ellipse(x + rx, y + ry, rx, ry, 0, 0, Math.PI * 2);
      ctx.fillStyle = '#63b3ff';
      ctx.fill();
      ctx.strokeStyle = '#005bb5';
      ctx.lineWidth = 2;
      ctx.stroke();

      // Bottom Ellipse
      ctx.beginPath();
      ctx.ellipse(x + rx, y + h - ry, rx, ry, 0, 0, Math.PI * 2);
      ctx.fillStyle = '#1e90ff';
      ctx.fill();
      ctx.strokeStyle = '#005bb5';
      ctx.lineWidth = 2;
      ctx.stroke();

      // Outline Side Edges
      ctx.beginPath();
      ctx.moveTo(x, y + ry);
      ctx.lineTo(x, y + h - ry);
      ctx.moveTo(x + w, y + ry);
      ctx.lineTo(x + w, y + h - ry);
      ctx.strokeStyle = '#005bb5';
      ctx.stroke();
    }

    function drawMovingBackground() {
      // Dark Animated Base Gradient
      const gradient = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
      gradient.addColorStop(0, '#0f2027');
      gradient.addColorStop(0.5, '#203a43');
      gradient.addColorStop(1, '#111111');
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Moving Parallax Grid Effect
      const gridSize = 60;
      const offsetX = (-cameraX * 0.3 + bgAnimOffset) % gridSize;
      
      ctx.strokeStyle = 'rgba(255, 255, 255, 0.07)';
      ctx.lineWidth = 1;

      // Vertical Grid Lines
      for (let x = offsetX - gridSize; x < canvas.width + gridSize; x += gridSize) {
        ctx.beginPath();
        ctx.moveTo(x, 0);
        ctx.lineTo(x, canvas.height);
        ctx.stroke();
      }

      // Horizontal Grid Lines
      for (let y = 0; y < canvas.height; y += gridSize) {
        ctx.beginPath();
        ctx.moveTo(0, y);
        ctx.lineTo(canvas.width, y);
        ctx.stroke();
      }
    }

    function render() {
      // 1. Draw Moving Animated Background
      drawMovingBackground();

      // 2. Draw World Objects with Camera Offset
      ctx.save();
      ctx.translate(-cameraX, 0);

      // Draw Infinite Ground
      const groundY = canvas.height - 100;
      ctx.fillStyle = '#181d28';
      ctx.fillRect(cameraX - 100, groundY, canvas.width + 200, 100);

      // Neon Green Ground Surface
      ctx.strokeStyle = '#00ff66';
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.moveTo(cameraX - 100, groundY);
      ctx.lineTo(cameraX + canvas.width + 100, groundY);
      ctx.stroke();

      // Draw Blue Cylinder Player
      drawCylinder(player.x, player.y, player.width, player.height);

      ctx.restore();
    }

    function gameLoop() {
      update();
      render();
      requestAnimationFrame(gameLoop);
    }
  </script>

</body>
</html>
