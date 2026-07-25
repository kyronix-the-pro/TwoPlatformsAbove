<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Enter</title>
  
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Space Grotesk', sans-serif;
    }

    body {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;
      transition: background 0.5s ease;
      
      /* Cool Black & White Grid/Dot Pattern Background */
      background-color: #000000;
      background-image: 
        radial-gradient(rgba(255, 255, 255, 0.25) 1px, transparent 0),
        linear-gradient(to right, rgba(255, 255, 255, 0.05) 1px, transparent 1px),
        linear-gradient(to bottom, rgba(255, 255, 255, 0.05) 1px, transparent 1px);
      background-size: 24px 24px, 40px 40px, 40px 40px;
      background-position: 0 0;
    }

    /* Cool Glowing Button Style */
    .enter-btn {
      padding: 18px 48px;
      font-size: 1.5rem;
      font-weight: 700;
      letter-spacing: 2px;
      color: #000000;
      background-color: #ffffff;
      border: 2px solid #ffffff;
      border-radius: 50px;
      cursor: pointer;
      box-shadow: 0 0 20px rgba(255, 255, 255, 0.4);
      transition: all 0.3s ease;
    }

    .enter-btn:hover {
      transform: scale(1.08);
      box-shadow: 0 0 35px rgba(255, 255, 255, 0.8);
      background-color: #ffffff;
      color: #000000;
    }

    .enter-btn:active {
      transform: scale(0.98);
    }

    /* Hidden State for Work in Progress Screen */
    .wip-text {
      display: none;
      font-size: 3rem;
      font-weight: 700;
      color: #000000;
      letter-spacing: 4px;
      text-transform: uppercase;
      margin-bottom: 12vh; /* Positions it a little above the middle */
    }

    /* Active White Screen State */
    body.active-white {
      background: #ffffff !important;
    }

    body.active-white .enter-btn {
      display: none;
    }

    body.active-white .wip-text {
      display: block;
    }
  </style>
</head>
<body>

  <button class="enter-btn" id="enterBtn">Enter.</button>

  <div class="wip-text" id="wipText">WORK IN PROGRESS</div>

  <script>
    const enterBtn = document.getElementById('enterBtn');
    
    enterBtn.addEventListener('click', () => {
      // Toggle background to white and reveal the WIP text
      document.body.classList.add('active-white');
    });
  </script>

</body>
</html>
