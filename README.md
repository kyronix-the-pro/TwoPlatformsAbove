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

    /* Screen Base & Animated Gradient Background */
    body {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;
      background: linear-gradient(-45deg, #0f2027, #203a43, #2c5364, #111111);
      background-size: 400% 400%;
      animation: gradientBG 12s ease infinite;
      color: #ffffff;
      transition: background 0.3s ease;
    }

    @keyframes gradientBG {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    /* Main Menu Frame */
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

    /* Buttons */
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

    /* Slot Selection Modal Box */
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
      position: relative;
    }

    .modal-title {
      font-size: 1.3rem;
      text-align: center;
      margin-bottom: 5px;
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
      transition: all 0.2s ease;
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
      margin-top: 5px;
      background: transparent;
      border: none;
      color: #888888;
      cursor: pointer;
      font-size: 0.9rem;
      text-decoration: underline;
    }

    /* Loading Screen Container */
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
      transition: width 0.1s linear;
    }

    /* States */
    body.state-black {
      background: #000000 !important;
      animation: none !important;
    }

    body.state-white {
      background: #ffffff !important;
      animation: none !important;
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

  <script>
    let currentMode = '';

    const menuContainer = document.getElementById('menuContainer');
    const slotModal = document.getElementById('slotModal');
    const modalTitle = document.getElementById('modalTitle');
    const loadingContainer = document.getElementById('loadingContainer');
    const progressFill = document.getElementById('progressFill');

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
          if (hasSavedGame) {
            slotButton.disabled = false;
            slotButton.innerText = `Slot ${i}: Game Saved`;
          } else {
            slotButton.disabled = true;
            slotButton.innerText = `Slot ${i}: Empty`;
          }
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
      document.body.classList.add('state-black');
      loadingContainer.style.display = 'flex';

      let progress = 0;
      const interval = setInterval(() => {
        progress += 2;
        progressFill.style.width = progress + '%';

        if (progress >= 100) {
          clearInterval(interval);
          finishLoading();
        }
      }, 40);
    }

    function finishLoading() {
      loadingContainer.style.display = 'none';
      document.body.classList.remove('state-black');
      document.body.classList.add('state-white');
    }
  </script>

</body>
</html>
