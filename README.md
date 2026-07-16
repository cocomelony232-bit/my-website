<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>A Special Letter</title>
  <style>
    body, html {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      background-color: #12131a;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      overflow: hidden;
      perspective: 1000px;
    }

    /* SCREEN 1: ENVELOPE WRAPPER */
    .envelope-wrapper {
      position: relative;
      width: 100%;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #12131a;
      z-index: 10;
      transition: opacity 0.8s ease, transform 0.8s ease;
    }

    /* THE ENVELOPE BASE */
    .envelope {
      position: relative;
      width: 90%;
      max-width: 440px;
      height: 280px;
      background-color: #1b1c24;
      border-bottom-left-radius: 16px;
      border-bottom-right-radius: 16px;
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6);
    }

    /* FRONT DIAGONAL POCKET PANS */
    .envelope::before {
      content: '';
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      z-index: 3;
      background: 
        linear-gradient(to top right, #1b1c24 49%, rgba(255,255,255,0.02) 50%, #15161c 51%) no-repeat bottom left / 50% 100%,
        linear-gradient(to top left, #1b1c24 49%, rgba(255,255,255,0.02) 50%, #15161c 51%) no-repeat bottom right / 50% 100%;
      border-bottom-left-radius: 16px;
      border-bottom-right-radius: 16px;
    }

    /* TOP FLAP CRITICAL TRIANGLE */
    .top-flap {
      position: absolute;
      top: 0; left: 0; width: 0; height: 0;
      border-left: 220px solid transparent;
      border-right: 220px solid transparent;
      border-top: 140px solid #22232d;
      transform-origin: top;
      transition: transform 0.5s ease-in-out;
      z-index: 4;
    }
    @media(max-width: 480px) {
      .top-flap {
        border-left-width: calc(45vw);
        border-right-width: calc(45vw);
      }
    }

    /* LETTER CARD (With dynamic expansion states) */
    .letter-card {
      position: absolute;
      top: 10px;
      left: 5%;
      width: 90%;
      height: 260px;
      background: #1f202b;
      border: 1px solid rgba(255, 255, 255, 0.05);
      border-radius: 12px;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      box-sizing: border-box;
      padding: 30px;
      transition: transform 0.8s cubic-bezier(0.25, 1, 0.5, 1), 
                  width 0.6s ease, height 0.6s ease, left 0.6s ease, top 0.6s ease;
      z-index: 2;
    }

    /* INITIAL ENVELOPE CONTENT STYLES */
    .card-text {
      color: rgba(255, 255, 255, 0.4);
      font-size: 14px;
      margin: 6px 0;
      letter-spacing: 1px;
      transition: opacity 0.3s;
    }
    .card-text span {
      color: rgba(255, 255, 255, 0.8);
      font-family: 'Georgia', serif;
      font-style: italic;
      margin-left: 5px;
    }

    .open-link {
      margin-top: 15px;
      color: #ff8da1;
      text-decoration: none;
      font-size: 13px;
      letter-spacing: 2px;
      text-transform: uppercase;
      font-weight: bold;
      cursor: pointer;
      border-bottom: 1px dashed #ff8da1;
      padding-bottom: 2px;
      transition: opacity 0.3s;
    }

    /* WAX SEAL BUTTON CONTAINER */
    .heart-seal {
      position: absolute;
      top: 115px;
      left: calc(50% - 25px);
      width: 50px;
      height: 50px;
      background: radial-gradient(circle, #ffcbd6 0%, #ff8da1 100%);
      border-radius: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      box-shadow: 0 5px 15px rgba(255, 141, 161, 0.4);
      cursor: pointer;
      z-index: 5;
      transition: transform 0.4s ease, opacity 0.4s ease;
    }
    .heart-seal::after { content: '♥'; color: white; font-size: 22px; }

    /* HIDDEN FULL MESSAGE PARAGRAPH */
    .full-message {
      opacity: 0;
      max-height: 0;
      color: rgba(255, 255, 255, 0.9);
      font-size: 16px;
      line-height: 1.6;
      text-align: center;
      font-family: 'Georgia', serif;
      transition: opacity 0.5s ease 0.6s;
      overflow: hidden;
    }

    /* CHIC BUTTON TO ENTER FEED */
    .next-feed-btn {
      opacity: 0;
      pointer-events: none;
      margin-top: 25px;
      background: transparent;
      border: 1px solid #ff8da1;
      color: #ff8da1;
      padding: 10px 24px;
      border-radius: 20px;
      cursor: pointer;
      font-weight: bold;
      text-transform: uppercase;
      font-size: 12px;
      letter-spacing: 1px;
      transition: opacity 0.5s ease 0.8s, background 0.3s, color 0.3s;
    }
    .next-feed-btn:hover {
      background: #ff8da1;
      color: #12131a;
    }

    /* STATE 1: FLAP FOLDS OPEN */
    .envelope.open-flap .top-flap {
      transform: rotateX(180deg);
      z-index: 1;
    }
    .envelope.open-flap .heart-seal {
      transform: scale(0);
      opacity: 0;
      pointer-events: none;
    }
    .envelope.open-flap .letter-card {
      transform: translateY(-130px); /* Slides clear out of the pouch pocket */
    }

    /* STATE 2: FULLY UNFOLDED VIEWING POSITION */
    .envelope.fully-open .letter-card {
      position: fixed;
      top: 10vh;
      left: 5vw;
      width: 90vw;
      height: 80vh;
      max-width: 440px;
      transform: translateY(0);
      z-index: 100; /* Floats over everything */
      justify-content: center;
    }
    .envelope.fully-open .card-text, 
    .envelope.fully-open .open-link {
      opacity: 0;
      display: none;
    }
    .envelope.fully-open .full-message {
      opacity: 1;
      max-height: 400px;
    }
    .envelope.fully-open .next-feed-btn {
      opacity: 1;
      pointer-events: auto;
    }

    /* EXIT TRANSITIONS FOR ENVELOPE FRAME */
    .envelope-wrapper.fade-out {
      opacity: 0;
      transform: scale(0.95);
      pointer-events: none;
    }

    /* SCREEN 2: FINAL TIKTOK STYLE VIDEO FEED PANEL */
    .feed-container {
      position: absolute;
      top: 0; left: 0; width: 100%; height: 100vh;
      background: #000;
      display: flex;
      justify-content: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.8s ease;
      z-index: 1;
    }
    .feed-container.active {
      opacity: 1;
      pointer-events: auto;
    }
    .tiktok-frame {
      width: 100%;
      max-width: 420px;
      height: 100%;
      background: #111;
    }
    iframe { width: 100%; height: 100%; border: none; }
  </style>
</head>
<body>

  <!-- STEP 1 VIEWPORT: ENVELOPE EXPERIENCE -->
  <div class="envelope-wrapper" id="envelopeWrapper">
    <div class="envelope" id="envelopeElement">
      
      <!-- Foldable Top Triangular Tab -->
      <div class="top-flap"></div>
      
      <!-- Expandable Letter Paper Sheet -->
      <div class="letter-card" id="cardElement">
        <div class="card-text">To: <span>You</span></div>
        <div class="card-text">From: <span>Me</span></div>
        <a class="open-link" onclick="openSequence()">Open Letter</a>
        
        <!-- CUSTOM MESSAGE TARGET AREA -->
        <div class="full-message">
          Dearest,<br><br>
          This is your fully opened message note! You can type whatever text you want to here. Share a poem, say happy birthday, or explain the video stream coming up next.<br><br>
          With love,
        </div>
        
        <!-- ACTION ACTION BUTTON TRIGGER -->
        <button class="next-feed-btn" onclick="transitionToVideos()">View Feed →</button>
      </div>
      
      <!-- Physical Center Seal Pin -->
      <div class="heart-seal" onclick="openSequence()"></div>
      
    </div>
  </div>

  <!-- STEP 2 VIEWPORT: ACTUAL TIKTOK CONTAINER CLONE -->
  <div class="feed-container" id="videoFeed">
    <div class="tiktok-frame">
      <iframe src="https://youtube.com" allow="autoplay; encrypted-media" allowfullscreen></iframe>
    </div>
  </div>

  <script>
    function openSequence() {
      const envelope = document.getElementById('envelopeElement');
      
      // Phase 1: Open the flap and pop up the card slightly
      envelope.classList.add('open-flap');
      
      // Phase 2: Fully pop out and scale up into full viewing mode
      setTimeout(() => {
        envelope.classList.add('fully-open');
      }, 1000);
    }

    function transitionToVideos() {
      // Fade out envelope interface
      document.getElementById('envelopeWrapper').classList.add('fade-out');
      
      // Bring up and activate the background media container
      setTimeout(() => {
        document.getElementById('videoFeed').classList.add('active');
      }, 500);
    }
  </script>
</body>
</html>
