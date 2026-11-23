<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Scrabble Neon Arcade</title>
  <script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
  <style>
    :root {
      --bg-dark: #090b18;
      --panel-bg: rgba(16, 20, 42, 0.92);
      --neon-cyan: #00f5ff;
      --neon-pink: #ff2a85;
      --neon-lime: #7dff2e;
      --neon-yellow: #ffe600;
      --neon-purple: #b84dff;
      --neon-orange: #ff7b00;
    }

    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: "Comic Sans MS", "Chalkboard SE", "Comic Neue", cursive, sans-serif;
      background:
        radial-gradient(circle at 15% 15%, rgba(0, 245, 255, 0.14), transparent 35%),
        radial-gradient(circle at 85% 20%, rgba(255, 42, 133, 0.16), transparent 35%),
        radial-gradient(circle at 50% 90%, rgba(125, 255, 46, 0.12), transparent 40%),
        var(--bg-dark);
      color: var(--neon-cyan);
      overflow-x: hidden;
    }

    /* Neon + Crisp Black Outline Helper */
    .neon-outline {
      paint-order: stroke fill;
      -webkit-text-stroke: 2px #000000;
      text-shadow:
        2px 2px 0px #000000,
        -1px -1px 0px #000000,
        1px -1px 0px #000000,
        -1px 1px 0px #000000,
        0 0 12px currentColor;
    }

    .neon-outline-sm {
      paint-order: stroke fill;
      -webkit-text-stroke: 1.3px #000000;
      text-shadow:
        1.5px 1.5px 0px #000000,
        -1px -1px 0px #000000,
        1px -1px 0px #000000,
        -1px 1px 0px #000000,
        0 0 8px currentColor;
    }

    .app-shell {
      max-width: 1320px;
      margin: 0 auto;
      padding: 16px;
    }

    .hero-header {
      text-align: center;
      margin-bottom: 18px;
    }

    .badge-strip {
      display: inline-flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
      padding: 8px 18px;
      border-radius: 999px;
      background: rgba(34, 18, 66, 0.9);
      border: 2px solid var(--neon-purple);
      box-shadow: 0 0 16px rgba(184, 77, 255, 0.45);
      color: var(--neon-pink);
      font-size: 0.9rem;
      font-weight: bold;
      margin-bottom: 10px;
    }

    .game-title {
      margin: 4px 0 6px;
      font-size: clamp(2.1rem, 5vw, 3.6rem);
      color: var(--neon-yellow);
      letter-spacing: 1px;
    }

    .subtitle {
      margin: 0;
      font-size: 1.05rem;
      color: var(--neon-cyan);
    }

    /* Setup / Lobby Card */
    .panel {
      background: var(--panel-bg);
      border: 2px solid rgba(0, 245, 255, 0.45);
      border-radius: 22px;
      padding: 20px;
      box-shadow: 0 0 26px rgba(0, 245, 255, 0.14);
    }

    .mode-bar {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      justify-content: center;
      margin-bottom: 18px;
    }

    .btn {
      font-family: inherit;
      font-weight: bold;
      cursor: pointer;
      border-radius: 16px;
      border: 2px solid #000000;
      padding: 12px 20px;
      font-size: 1rem;
      transition: transform 0.14s ease, filter 0.14s ease, box-shadow 0.14s ease;
    }

    .btn:hover:not(:disabled) {
      transform: translateY(-2px) scale(1.02);
      filter: brightness(1.08);
    }

    .btn:active:not(:disabled) {
      transform: translateY(1px) scale(0.98);
    }

    .btn:disabled {
      opacity: 0.45;
      cursor: not-allowed;
    }

    .btn-cyan {
      background: linear-gradient(135deg, #00f5ff, #008cff);
      color: var(--neon-yellow);
      box-shadow: 0 0 16px rgba(0, 245, 255, 0.5);
    }

    .btn-pink {
      background: linear-gradient(135deg, #ff2a85, #b84dff);
      color: var(--neon-yellow);
      box-shadow: 0 0 16px rgba(255, 42, 133, 0.5);
    }

    .btn-lime {
      background: linear-gradient(135deg, #7dff2e, #00d084);
      color: var(--neon-yellow);
      box-shadow: 0 0 16px rgba(125, 255, 46, 0.45);
    }

    .btn-yellow {
      background: linear-gradient(135deg, #ffe600, #ff7b00);
      color: var(--neon-cyan);
      box-shadow: 0 0 16px rgba(255, 230, 0, 0.45);
    }

    .btn-dark {
      background: #171c38;
      border-color: var(--neon-cyan);
      color: var(--neon-cyan);
    }

    .setup-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 14px;
      margin: 16px 0;
    }

    .player-setup-card {
      background: rgba(11, 14, 31, 0.92);
      border: 2px solid var(--neon-purple);
      border-radius: 18px;
      padding: 14px;
      display: flex;
      flex-direction: column;
      gap: 10px;
      align-items: center;
    }

    .avatar-circle {
      width: 72px;
      height: 72px;
      border-radius: 50%;
      border: 3px solid var(--neon-yellow);
      box-shadow: 0 0 14px rgba(255, 230, 0, 0.55);
      object-fit: cover;
      background: #1d2246;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.8rem;
      overflow: hidden;
    }

    .avatar-circle img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    input[type="text"] {
      width: 100%;
      padding: 9px 12px;
      border-radius: 12px;
      border: 2px solid var(--neon-cyan);
      background: #090d20;
      color: var(--neon-yellow);
      font-family: inherit;
      font-size: 0.95rem;
      font-weight: bold;
      outline: none;
    }

    .online-box {
      display: none;
      margin-top: 14px;
      padding: 16px;
      border-radius: 18px;
      background: rgba(13, 17, 38, 0.95);
      border: 2px dashed var(--neon-lime);
    }

    .online-box.active {
      display: block;
    }

    .online-controls {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      align-items: center;
      margin-top: 10px;
    }

    .room-code-pill {
      padding: 8px 14px;
      border-radius: 12px;
      background: #151b3d;
      border: 2px solid var(--neon-pink);
      color: var(--neon-lime);
      font-size: 1.05rem;
      font-weight: bold;
    }

    /* Main Game Workspace */
    .game-workspace {
      display: none;
      grid-template-columns: 310px minmax(320px, 1fr) 290px;
      gap: 18px;
      align-items: start;
    }

    .game-workspace.active {
      display: grid;
    }

    @media (max-width: 1080px) {
      .game-workspace.active {
        grid-template-columns: 1fr;
      }
    }

    /* Animated Moving Vibrant Border around Board Stage */
    .animated-board-frame {
      position: relative;
      padding: 12px;
      border-radius: 28px;
      background: #070914;
      overflow: hidden;
      box-shadow: 0 0 30px rgba(0, 245, 255, 0.25);
    }

    .animated-board-frame::before {
      content: "";
      position: absolute;
      inset: -65%;
      background: conic-gradient(
        from 0deg,
        #ff2a85,
        #ffe600,
        #7dff2e,
        #00f5ff,
        #b84dff,
        #ff7b00,
        #ff2a85
      );
      animation: spinBorder 6s linear infinite;
      z-index: 0;
    }

    .animated-board-frame::after {
      content: "";
      position: absolute;
      inset: 6px;
      border-radius: 23px;
      background: #0a0d1f;
      z-index: 1;
    }

    @keyframes spinBorder {
      to {
        transform: rotate(360deg);
      }
    }

    .marquee-dots {
      position: relative;
      z-index: 2;
      display: flex;
      justify-content: space-between;
      padding: 0 8px 6px;
    }

    .marquee-dots.bottom {
      padding: 6px 8px 0;
    }

    .bulb {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      border: 1.5px solid #000;
      animation: blinkBulb 1.2s infinite alternate;
    }

    .bulb:nth-child(odd) {
      background: var(--neon-yellow);
      box-shadow: 0 0 8px var(--neon-yellow);
    }

    .bulb:nth-child(even) {
      background: var(--neon-pink);
      box-shadow: 0 0 8px var(--neon-pink);
      animation-delay: 0.6s;
    }

    @keyframes blinkBulb {
      0% { transform: scale(0.85); filter: brightness(0.8); }
      100% { transform: scale(1.2); filter: brightness(1.35); }
    }

    .board-inner {
      position: relative;
      z-index: 2;
    }

    /* 15x15 Scrabble Grid */
    .scrabble-board {
      display: grid;
      grid-template-columns: repeat(15, 1fr);
      gap: 3px;
      background: #050712;
      padding: 6px;
      border-radius: 16px;
      aspect-ratio: 1 / 1;
      width: 100%;
    }

    .cell {
      position: relative;
      border-radius: 6px;
      border: 1.5px solid rgba(255, 255, 255, 0.16);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: clamp(0.45rem, 1.05vw, 0.78rem);
      font-weight: bold;
      cursor: pointer;
      transition: transform 0.1s ease, box-shadow 0.15s ease;
      overflow: hidden;
    }

    .cell:hover {
      border-color: var(--neon-yellow);
      z-index: 2;
    }

    .cell-normal { background: #131936; color: var(--neon-cyan); }
    .cell-dl { background: #0d3b66; color: var(--neon-cyan); }
    .cell-tl { background: #14452f; color: var(--neon-lime); }
    .cell-dw { background: #5c1638; color: var(--neon-pink); }
    .cell-tw { background: #5e2208; color: var(--neon-yellow); }
    .cell-star { background: #4b1869; color: var(--neon-yellow); }

    .cell-label {
      pointer-events: none;
      text-align: center;
      line-height: 1;
    }

    /* Cute Cartoon Neon Letter Tiles */
    .letter-tile {
      width: 100%;
      height: 100%;
      border-radius: 8px;
      border: 2px solid #000000;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      font-weight: 900;
      cursor: grab;
      box-shadow: 0 3px 0 #000000, 0 0 10px rgba(0, 245, 255, 0.35);
      transition: transform 0.12s ease;
    }

    .letter-tile:active {
      cursor: grabbing;
      transform: scale(1.06);
    }

    .letter-tile.selected {
      outline: 3px solid var(--neon-yellow);
      transform: translateY(-4px) scale(1.06);
      z-index: 5;
    }

    .tile-char {
      font-size: clamp(0.85rem, 1.8vw, 1.35rem);
      line-height: 1;
    }

    .tile-pts {
      position: absolute;
      right: 3px;
      bottom: 2px;
      font-size: clamp(0.45rem, 0.75vw, 0.65rem);
      line-height: 1;
      color: var(--neon-yellow);
    }

    .rack-area {
      margin-top: 14px;
      padding: 14px;
      border-radius: 20px;
      background: rgba(14, 18, 38, 0.95);
      border: 2px solid var(--neon-pink);
    }

    .rack-tiles {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 8px;
      min-height: 62px;
      margin-bottom: 12px;
    }

    .rack-tile-slot {
      width: 54px;
      height: 58px;
    }

    .action-row {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      justify-content: center;
    }

    /* Players & Sand Timer Column */
    .players-list {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .player-card {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px 12px;
      border-radius: 16px;
      background: rgba(11, 14, 30, 0.9);
      border: 2px solid rgba(255, 255, 255, 0.15);
    }

    .player-card.active-turn {
      border-color: var(--neon-lime);
      box-shadow: 0 0 18px rgba(125, 255, 46, 0.45);
      background: rgba(20, 34, 48, 0.95);
    }

    .mini-avatar {
      width: 44px;
      height: 44px;
      border-radius: 50%;
      border: 2px solid var(--neon-cyan);
      object-fit: cover;
      background: #1b2248;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-shrink: 0;
      overflow: hidden;
      font-size: 1.2rem;
    }

    .mini-avatar img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .player-meta {
      flex: 1;
      min-width: 0;
    }

    .player-name-text {
      font-size: 1rem;
      font-weight: bold;
      color: var(--neon-yellow);
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .player-score-text {
      font-size: 0.95rem;
      font-weight: bold;
      color: var(--neon-lime);
    }

    /* Sand Hourglass Timer */
    .timer-box {
      margin-top: 14px;
      padding: 14px;
      border-radius: 18px;
      background: rgba(11, 14, 30, 0.92);
      border: 2px solid var(--neon-yellow);
      text-align: center;
    }

    .hourglass-wrap {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 12px;
      margin: 8px 0;
    }

    .sand-stream {
      stroke: var(--neon-yellow);
      stroke-width: 3;
      stroke-dasharray: 4 3;
      animation: sandFlow 0.35s linear infinite;
    }

    @keyframes sandFlow {
      to { stroke-dashoffset: -7; }
    }

    .timer-readout {
      font-size: 1.5rem;
      font-weight: 900;
      color: var(--neon-pink);
    }

    /* Optional Live Camera Grid in Online Mode */
    .cam-panel {
      margin-top: 14px;
      padding: 14px;
      border-radius: 18px;
      background: rgba(11, 14, 30, 0.92);
      border: 2px solid var(--neon-cyan);
    }

    .cam-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(110px, 1fr));
      gap: 8px;
      margin-top: 10px;
    }

    .cam-tile {
      position: relative;
      border-radius: 12px;
      overflow: hidden;
      border: 2px solid var(--neon-purple);
      background: #070914;
      aspect-ratio: 4 / 3;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .cam-tile video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transform: scaleX(-1);
    }

    .cam-tag {
      position: absolute;
      bottom: 4px;
      left: 4px;
      font-size: 0.72rem;
      font-weight: bold;
      color: var(--neon-lime);
      background: rgba(0, 0, 0, 0.65);
      padding: 2px 6px;
      border-radius: 6px;
    }

    /* Flying Tile Animation Overlay */
    .flying-tile {
      position: fixed;
      z-index: 9999;
      pointer-events: none;
      border-radius: 8px;
      border: 2px solid #000;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 900;
      transition: left 0.42s cubic-bezier(0.22, 1, 0.36, 1),
                  top 0.42s cubic-bezier(0.22, 1, 0.36, 1),
                  transform 0.42s ease;
    }

    .status-banner {
      margin-bottom: 12px;
      padding: 10px 14px;
      border-radius: 14px;
      background: rgba(20, 26, 56, 0.95);
      border: 2px solid var(--neon-cyan);
      color: var(--neon-yellow);
      font-weight: bold;
      text-align: center;
    }

    .log-box {
      max-height: 180px;
      overflow-y: auto;
      font-size: 0.85rem;
      display: flex;
      flex-direction: column;
      gap: 6px;
      color: var(--neon-cyan);
    }

    .log-item {
      padding: 6px 8px;
      border-radius: 10px;
      background: rgba(18, 24, 52, 0.85);
      border-left: 3px solid var(--neon-pink);
    }

    select {
      font-family: inherit;
      font-weight: bold;
      background: #111633;
      color: var(--neon-yellow);
      border: 2px solid var(--neon-cyan);
      border-radius: 10px;
      padding: 6px 10px;
    }
  </style>
</head>
<body>
  <div class="app-shell">
    <header class="hero-header">
      <div class="badge-strip neon-outline-sm">
        <span>Cartoon Neon Tiles</span>
        <span>•</span>
        <span>Live Sand Timer</span>
        <span>•</span>
        <span>2–4 Players + Camera</span>
      </div>
      <h1 class="game-title neon-outline">Scrabble Neon Arcade</h1>
      <p class="subtitle neon-outline-sm">Colorful neon tiles with black outlines, gallery avatars & optional live cam!</p>
    </header>

    <!-- SETUP PANEL -->
    <section id="setupPanel" class="panel">
      <div class="mode-bar">
        <button id="btnModeLocal" class="btn btn-cyan neon-outline-sm" onclick="setGameMode('local')">Local Pass & Play</button>
        <button id="btnModeOnline" class="btn btn-pink neon-outline-sm" onclick="setGameMode('online')">Play Online (2–4P)</button>
        <label class="neon-outline-sm" style="color: var(--neon-lime); display: inline-flex; align-items: center; gap: 6px;">
          Players:
          <select id="playerCountSelect" onchange="renderPlayerSetupCards()">
            <option value="2" selected>2 Players</option>
            <option value="3">3 Players</option>
            <option value="4">4 Players</option>
          </select>
        </label>
        <label class="neon-outline-sm" style="color: var(--neon-yellow); display: inline-flex; align-items: center; gap: 6px;">
          Sand Timer:
          <select id="timerSelect">
            <option value="60">60s</option>
            <option value="90" selected>90s</option>
            <option value="120">120s</option>
            <option value="0">Unlimited</option>
          </select>
        </label>
      </div>

      <div id="playerSetupCards" class="setup-grid"></div>

      <!-- ONLINE ROOM CONTROLS -->
      <div id="onlineBox" class="online-box">
        <div class="neon-outline-sm" style="color: var(--neon-lime); font-size: 1.05rem;">
          Online Room (PeerJS) — Share your Room Code with friends!
        </div>
        <div class="online-controls">
          <button class="btn btn-lime neon-outline-sm" onclick="createOnlineRoom()">Create Room</button>
          <input id="joinCodeInput" type="text" placeholder="Enter Room Code..." style="max-width: 210px;" />
          <button class="btn btn-cyan neon-outline-sm" onclick="joinOnlineRoom()">Join Room</button>
          <span id="roomStatusPill" class="room-code-pill neon-outline-sm">Not connected</span>
        </div>
      </div>

      <div style="text-align: center; margin-top: 20px;">
        <button id="startGameBtn" class="btn btn-yellow neon-outline" style="font-size: 1.2rem; padding: 14px 30px;" onclick="startMatch()">
          Start Neon Scrabble!
        </button>
      </div>
    </section>

    <!-- GAME WORKSPACE -->
    <main id="gameWorkspace" class="game-workspace">
      <!-- LEFT: PLAYERS, SAND TIMER & LIVE CAM -->
      <aside class="panel">
        <h2 class="neon-outline-sm" style="margin: 0 0 10px; color: var(--neon-yellow);">Players</h2>
        <div id="playersList" class="players-list"></div>

        <!-- SAND HOURGLASS TIMER -->
        <div class="timer-box">
          <div class="neon-outline-sm" style="color: var(--neon-cyan); font-weight: bold;">Sand Hourglass</div>
          <div class="hourglass-wrap">
            <svg width="54" height="74" viewBox="0 0 54 74">
              <rect x="6" y="4" width="42" height="6" rx="3" fill="#ffe600" stroke="#000" stroke-width="2" />
              <rect x="6" y="64" width="42" height="6" rx="3" fill="#ffe600" stroke="#000" stroke-width="2" />
              <path d="M12 10 L42 10 L27 37 L42 64 L12 64 L27 37 Z" fill="rgba(0,245,255,0.12)" stroke="#00f5ff" stroke-width="2.5" />
              <polygon id="topSandPoly" points="16,14 38,14 27,35" fill="#ff2a85" />
              <line id="sandStreamLine" x1="27" y1="36" x2="27" y2="62" class="sand-stream" />
              <polygon id="bottomSandPoly" points="27,50 38,62 16,62" fill="#7dff2e" />
            </svg>
            <div>
              <div id="timerReadout" class="timer-readout neon-outline-sm">90s</div>
              <div id="timerLabel" style="font-size: 0.78rem; color: var(--neon-cyan);">Ticking...</div>
            </div>
          </div>
        </div>

        <!-- OPTIONAL CAMERA IN ONLINE MODE -->
        <div id="camPanel" class="cam-panel" style="display: none;">
          <div style="display: flex; justify-content: space-between; align-items: center; gap: 8px;">
            <span class="neon-outline-sm" style="color: var(--neon-pink); font-weight: bold;">Live Cam</span>
            <button id="toggleCamBtn" class="btn btn-dark neon-outline-sm" style="padding: 6px 12px; font-size: 0.85rem;" onclick="toggleCamera()">
              Cam: OFF
            </button>
          </div>
          <div id="camGrid" class="cam-grid"></div>
        </div>
      </aside>

      <!-- CENTER: ANIMATED BORDER BOARD & RACK -->
      <section>
        <div id="statusBanner" class="status-banner neon-outline-sm">Ready to play!</div>

        <div class="animated-board-frame">
          <div class="marquee-dots">
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
          </div>
          <div class="board-inner">
            <div id="scrabbleBoard" class="scrabble-board"></div>
          </div>
          <div class="marquee-dots bottom">
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
          </div>
        </div>

        <div class="rack-area">
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px;">
            <span id="rackOwnerLabel" class="neon-outline-sm" style="color: var(--neon-yellow); font-weight: bold;">Your Neon Tiles</span>
            <span id="bagCountLabel" class="neon-outline-sm" style="color: var(--neon-lime); font-size: 0.9rem;">Bag: 100</span>
          </div>
          <div id="rackTiles" class="rack-tiles"></div>
          <div class="action-row">
            <button class="btn btn-lime neon-outline-sm" onclick="submitWordMove()">Play Word</button>
            <button class="btn btn-yellow neon-outline-sm" onclick="recallPendingTiles()">Recall</button>
            <button class="btn btn-cyan neon-outline-sm" onclick="swapSelectedRackTile()">Swap Tile</button>
            <button class="btn btn-pink neon-outline-sm" onclick="passTurn()">Pass</button>
            <button class="btn btn-dark neon-outline-sm" onclick="backToSetup()">New Setup</button>
          </div>
        </div>
      </section>

      <!-- RIGHT: MOVE LOG & QUICK GUIDE -->
      <aside class="panel">
        <h2 class="neon-outline-sm" style="margin: 0 0 10px; color: var(--neon-pink);">Move Feed</h2>
        <div id="logBox" class="log-box"></div>
        <hr style="border-color: rgba(255,255,255,0.12); margin: 14px 0;" />
        <div style="font-size: 0.85rem; line-height: 1.45; color: var(--neon-cyan);">
          <div class="neon-outline-sm" style="color: var(--neon-yellow); margin-bottom: 4px;">How to Play:</div>
          <div>1. Click or drag a neon tile from your rack onto the 15×15 board.</div>
          <div>2. First word must cover the center <b>★</b> tile.</div>
          <div>3. Connected words of 2+ letters are scored automatically!</div>
          <div>4. In Online mode, tap <b>Cam: OFF/ON</b> to share your camera.</div>
        </div>
      </aside>
    </main>
  </div>

  <script>
    const LETTER_SCORES = {
      A:1, B:3, C:3, D:2, E:1, F:4, G:2, H:4, I:1, J:8, K:5, L:1, M:3,
      N:1, O:1, P:3, Q:10, R:1, S:1, T:1, U:1, V:4, W:4, X:8, Y:4, Z:10
    };

    const LETTER_COUNTS = {
      A:9, B:2, C:2, D:4, E:12, F:2, G:3, H:2, I:9, J:1, K:1, L:4, M:2,
      N:6, O:8, P:2, Q:1, R:6, S:4, T:6, U:4, V:2, W:2, X:1, Y:2, Z:1
    };

    const NEON_TILE_PALETTES = [
      { bg: "linear-gradient(145deg, #00f5ff, #0088ff)", text: "#ffe600" },
      { bg: "linear-gradient(145deg, #ff2a85, #ff6ec7)", text: "#ffe600" },
      { bg: "linear-gradient(145deg, #7dff2e, #00d47e)", text: "#ffffff" },
      { bg: "linear-gradient(145deg, #ffe600, #ff8800)", text: "#00f5ff" },
      { bg: "linear-gradient(145deg, #b84dff, #6b38fb)", text: "#7dff2e" }
    ];

    const BONUS_MAP = {
      "0,0":"TW","0,7":"TW","0,14":"TW","7,0":"TW","7,14":"TW","14,0":"TW","14,7":"TW","14,14":"TW",
      "1,1":"DW","2,2":"DW","3,3":"DW","4,4":"DW","1,13":"DW","2,12":"DW","3,11":"DW","4,10":"DW",
      "13,1":"DW","12,2":"DW","11,3":"DW","10,4":"DW","13,13":"DW","12,12":"DW","11,11":"DW","10,10":"DW",
      "1,5":"TL","1,9":"TL","5,1":"TL","5,5":"TL","5,9":"TL","5,13":"TL",
      "9,1":"TL","9,5":"TL","9,9":"TL","9,13":"TL","13,5":"TL","13,9":"TL",
      "0,3":"DL","0,11":"DL","2,6":"DL","2,8":"DL","3,0":"DL","3,7":"DL","3,14":"DL",
      "6,2":"DL","6,6":"DL","6,8":"DL","6,12":"DL","7,3":"DL","7,11":"DL",
      "8,2":"DL","8,6":"DL","8,8":"DL","8,12":"DL","11,0":"DL","11,7":"DL","11,14":"DL",
      "12,6":"DL","12,8":"DL","14,3":"DL","14,11":"DL",
      "7,7":"STAR"
    };

    let state = {
      mode: "local",
      playerCount: 2,
      players: [],
      currentTurn: 0,
      board: Array.from({ length: 15 }, () => Array(15).fill(null)),
      pendingPlacements: [], // { r, c, tile, rackIndex }
      selectedRackIdx: null,
      tileBag: [],
      firstMoveDone: false,
      timerDuration: 90,
      timeLeft: 90,
      timerInterval: null,
      // Online PeerJS
      peer: null,
      connections: [],
      mediaCalls: [],
      isHost: false,
      localSeat: 0,
      localStream: null,
      camEnabled: false
    };

    function getNeonPaletteForLetter(letter) {
      const idx = (letter.charCodeAt(0) - 65) % NEON_TILE_PALETTES.length;
      return NEON_TILE_PALETTES[Math.max(0, idx)];
    }

    function setGameMode(mode) {
      state.mode = mode;
      document.getElementById("onlineBox").classList.toggle("active", mode === "online");
      document.getElementById("camPanel").style.display = mode === "online" ? "block" : "none";
      renderPlayerSetupCards();
    }

    function renderPlayerSetupCards() {
      const count = parseInt(document.getElementById("playerCountSelect").value, 10);
      state.playerCount = count;
      const container = document.getElementById("playerSetupCards");
      container.innerHTML = "";

      for (let i = 0; i < count; i++) {
        if (!state.players[i]) {
          state.players[i] = {
            name: `Player ${i + 1}`,
            score: 0,
            rack: [],
            photo: null
          };
        }
        const p = state.players[i];
        const card = document.createElement("div");
        card.className = "player-setup-card";
        card.innerHTML = `
          <div class="avatar-circle" id="setupAvatarPreview_${i}">
            ${p.photo ? `<img src="${p.photo}" alt="Avatar" />` : "😎"}
          </div>
          <input type="text" id="playerNameInput_${i}" value="${p.name}" maxlength="16" placeholder="Player ${i + 1}" />
          <label class="btn btn-dark neon-outline-sm" style="padding: 6px 12px; font-size: 0.82rem; cursor: pointer;">
            Gallery Photo
            <input type="file" accept="image/*" style="display: none;" onchange="handlePhotoUpload(event, ${i})" />
          </label>
        `;
        container.appendChild(card);
      }
    }

    function handlePhotoUpload(event, playerIndex) {
      const file = event.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function(e) {
        const img = new Image();
        img.onload = function() {
          // Compress slightly for clean sync & fast rendering
          const canvas = document.createElement("canvas");
          const size = 140;
          canvas.width = size;
          canvas.height = size;
          const ctx = canvas.getContext("2d");
          ctx.drawImage(img, 0, 0, size, size);
          const dataUrl = canvas.toDataURL("image/jpeg", 0.75);
          state.players[playerIndex].photo = dataUrl;
          document.getElementById(`setupAvatarPreview_${playerIndex}`).innerHTML = `<img src="${dataUrl}" alt="Avatar" />`;
          broadcastStateIfOnline();
        };
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }

    function buildBag() {
      const bag = [];
      let uid = 1;
      for (const [letter, count] of Object.entries(LETTER_COUNTS)) {
        for (let i = 0; i < count; i++) {
          bag.push({
            id: `t_${uid++}_${letter}`,
            letter,
            points: LETTER_SCORES[letter]
          });
        }
      }
      for (let i = bag.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [bag[i], bag[j]] = [bag[j], bag[i]];
      }
      return bag;
    }

    function drawTiles(n) {
      const drawn = [];
      while (drawn.length < n && state.tileBag.length > 0) {
        drawn.push(state.tileBag.pop());
      }
      return drawn;
    }

    function startMatch() {
      for (let i = 0; i < state.playerCount; i++) {
        const inputEl = document.getElementById(`playerNameInput_${i}`);
        if (inputEl && inputEl.value.trim()) {
          state.players[i].name = inputEl.value.trim();
        }
        state.players[i].score = 0;
        state.players[i].rack = [];
      }
      state.players = state.players.slice(0, state.playerCount);
      state.board = Array.from({ length: 15 }, () => Array(15).fill(null));
      state.pendingPlacements = [];
      state.selectedRackIdx = null;
      state.tileBag = buildBag();
      state.firstMoveDone = false;
      state.currentTurn = 0;
      state.timerDuration = parseInt(document.getElementById("timerSelect").value, 10);

      for (let i = 0; i < state.playerCount; i++) {
        state.players[i].rack = drawTiles(7);
      }

      document.getElementById("setupPanel").style.display = "none";
      document.getElementById("gameWorkspace").classList.add("active");
      document.getElementById("camPanel").style.display = state.mode === "online" ? "block" : "none";
      document.getElementById("logBox").innerHTML = "";

      addLog("Game started! First word covers ★.");
      renderAll();
      startTurnTimer();
      broadcastStateIfOnline();
    }

    function backToSetup() {
      clearInterval(state.timerInterval);
      document.getElementById("gameWorkspace").classList.remove("active");
      document.getElementById("setupPanel").style.display = "block";
    }

    function canCurrentDeviceAct() {
      if (state.mode === "local") return true;
      return state.currentTurn === state.localSeat;
    }

    function createTileElement(tile, options = {}) {
      const pal = getNeonPaletteForLetter(tile.letter);
      const el = document.createElement("div");
      el.className = "letter-tile" + (options.selected ? " selected" : "");
      el.style.background = pal.bg;
      el.style.color = pal.text;

      const charSpan = document.createElement("span");
      charSpan.className = "tile-char neon-outline-sm";
      charSpan.textContent = tile.letter;

      const ptsSpan = document.createElement("span");
      ptsSpan.className = "tile-pts neon-outline-sm";
      ptsSpan.textContent = tile.points;

      el.appendChild(charSpan);
      el.appendChild(ptsSpan);

      if (options.draggable) {
        el.draggable = true;
        el.addEventListener("dragstart", (e) => {
          state.selectedRackIdx = options.rackIndex;
          e.dataTransfer.setData("text/plain", String(options.rackIndex));
          renderRack();
        });
      }
      return el;
    }

    function renderAll() {
      renderPlayers();
      renderBoard();
      renderRack();
      updateStatusBanner();
      document.getElementById("bagCountLabel").textContent = `Bag: ${state.tileBag.length}`;
    }

    function renderPlayers() {
      const list = document.getElementById("playersList");
      list.innerHTML = "";
      state.players.forEach((p, idx) => {
        const card = document.createElement("div");
        card.className = "player-card" + (idx === state.currentTurn ? " active-turn" : "");
        const seatTag = state.mode === "online" && idx === state.localSeat ? " (You)" : "";
        card.innerHTML = `
          <div class="mini-avatar">
            ${p.photo ? `<img src="${p.photo}" alt="${p.name}" />` : "😎"}
          </div>
          <div class="player-meta">
            <div class="player-name-text neon-outline-sm">${escapeHtml(p.name)}${seatTag}</div>
            <div class="player-score-text neon-outline-sm">Score: ${p.score}</div>
          </div>
        `;
        list.appendChild(card);
      });
    }

    function renderBoard() {
      const boardEl = document.getElementById("scrabbleBoard");
      boardEl.innerHTML = "";

      for (let r = 0; r < 15; r++) {
        for (let c = 0; c < 15; c++) {
          const bonus = BONUS_MAP[`${r},${c}`] || "";
          const cell = document.createElement("div");
          cell.className = "cell " + (
            bonus === "TW" ? "cell-tw" :
            bonus === "DW" ? "cell-dw" :
            bonus === "TL" ? "cell-tl" :
            bonus === "DL" ? "cell-dl" :
            bonus === "STAR" ? "cell-star" : "cell-normal"
          );

          const pending = state.pendingPlacements.find(item => item.r === r && item.c === c);
          const locked = state.board[r][c];

          if (locked) {
            cell.appendChild(createTileElement(locked));
          } else if (pending) {
            const pTile = createTileElement(pending.tile, { selected: true });
            pTile.onclick = (ev) => {
              ev.stopPropagation();
              returnPendingTileAt(r, c);
            };
            cell.appendChild(pTile);
          } else {
            const lbl = document.createElement("span");
            lbl.className = "cell-label neon-outline-sm";
            lbl.textContent = bonus === "STAR" ? "★" : bonus;
            cell.appendChild(lbl);
          }

          cell.addEventListener("dragover", (e) => e.preventDefault());
          cell.addEventListener("drop", (e) => {
            e.preventDefault();
            const rackIdx = parseInt(e.dataTransfer.getData("text/plain"), 10);
            if (!Number.isNaN(rackIdx)) {
              placeRackTileOnCell(rackIdx, r, c);
            }
          });
          cell.addEventListener("click", () => {
            if (!locked && !pending && state.selectedRackIdx !== null) {
              placeRackTileOnCell(state.selectedRackIdx, r, c);
            }
          });

          boardEl.appendChild(cell);
        }
      }
    }

    function renderRack() {
      const rackEl = document.getElementById("rackTiles");
      rackEl.innerHTML = "";
      const activePlayer = state.players[state.currentTurn];
      const viewPlayer = state.mode === "online" ? state.players[state.localSeat] : activePlayer;
      if (!viewPlayer) return;

      document.getElementById("rackOwnerLabel").textContent =
        `${viewPlayer.name}'s Neon Tiles` + (canCurrentDeviceAct() ? " — Your Turn!" : "");

      const usedRackIndices = new Set(state.pendingPlacements.map(p => p.rackIndex));

      for (let i = 0; i < 7; i++) {
        const slot = document.createElement("div");
        slot.className = "rack-tile-slot";
        const tile = viewPlayer.rack[i];
        if (tile && !usedRackIndices.has(i)) {
          const tileEl = createTileElement(tile, {
            draggable: canCurrentDeviceAct(),
            rackIndex: i,
            selected: state.selectedRackIdx === i
          });
          tileEl.onclick = () => {
            if (!canCurrentDeviceAct()) return;
            state.selectedRackIdx = state.selectedRackIdx === i ? null : i;
            renderRack();
          };
          slot.appendChild(tileEl);
        }
        rackEl.appendChild(slot);
      }
    }

    function placeRackTileOnCell(rackIndex, r, c) {
      if (!canCurrentDeviceAct()) return;
      const player = state.players[state.currentTurn];
      const tile = player.rack[rackIndex];
      if (!tile || state.board[r][c]) return;
      if (state.pendingPlacements.some(p => p.r === r && p.c === c)) return;

      // Flying visual cue from rack area to board cell
      const rackEl = document.getElementById("rackTiles");
      const boardCells = document.querySelectorAll("#scrabbleBoard .cell");
      const targetCell = boardCells[r * 15 + c];
      if (rackEl && targetCell) {
        animateFlyingTile(tile, rackEl.getBoundingClientRect(), targetCell.getBoundingClientRect());
      }

      state.pendingPlacements.push({ r, c, tile, rackIndex });
      state.selectedRackIdx = null;
      renderBoard();
      renderRack();
    }

    function returnPendingTileAt(r, c) {
      if (!canCurrentDeviceAct()) return;
      state.pendingPlacements = state.pendingPlacements.filter(p => !(p.r === r && p.c === c));
      renderBoard();
      renderRack();
    }

    function recallPendingTiles() {
      if (!canCurrentDeviceAct()) return;
      state.pendingPlacements = [];
      state.selectedRackIdx = null;
      renderBoard();
      renderRack();
    }

    function animateFlyingTile(tile, fromRect, toRect) {
      const pal = getNeonPaletteForLetter(tile.letter);
      const ghost = document.createElement("div");
      ghost.className = "flying-tile";
      ghost.style.background = pal.bg;
      ghost.style.color = pal.text;
      ghost.style.width = `${Math.max(36, toRect.width)}px`;
      ghost.style.height = `${Math.max(36, toRect.height)}px`;
      ghost.style.left = `${fromRect.left + fromRect.width / 2 - 20}px`;
      ghost.style.top = `${fromRect.top}px`;
      ghost.innerHTML = `<span class="neon-outline-sm">${tile.letter}</span>`;
      document.body.appendChild(ghost);

      requestAnimationFrame(() => {
        ghost.style.left = `${toRect.left}px`;
        ghost.style.top = `${toRect.top}px`;
        ghost.style.transform = "scale(1.08) rotate(8deg)";
      });

      setTimeout(() => ghost.remove(), 460);
    }

    function submitWordMove() {
      if (!canCurrentDeviceAct()) return;
      if (state.pendingPlacements.length === 0) {
        updateStatusBanner("Place at least 1 tile on the board first!");
        return;
      }

      // Check alignment (all same row or all same col)
      const rows = state.pendingPlacements.map(p => p.r);
      const cols = state.pendingPlacements.map(p => p.c);
      const sameRow = rows.every(r => r === rows[0]);
      const sameCol = cols.every(c => c === cols[0]);
      if (!sameRow && !sameCol) {
        updateStatusBanner("Tiles must be placed in a straight row or column!");
        return;
      }

      // First move must touch center (7,7)
      if (!state.firstMoveDone) {
        const coversStar = state.pendingPlacements.some(p => p.r === 7 && p.c === 7);
        if (!coversStar) {
          updateStatusBanner("First word must cover the center ★ tile!");
          return;
        }
      }

      // Build temporary grid to extract words and score
      const tempGrid = state.board.map(row => row.slice());
      const pendingSet = new Set(state.pendingPlacements.map(p => `${p.r},${p.c}`));
      for (const p of state.pendingPlacements) {
        tempGrid[p.r][p.c] = p.tile;
      }

      // Must connect to existing tile after first move
      if (state.firstMoveDone) {
        const touchesExisting = state.pendingPlacements.some(({ r, c }) => {
          const dirs = [[-1,0],[1,0],[0,-1],[0,1]];
          return dirs.some(([dr, dc]) => {
            const nr = r + dr, nc = c + dc;
            return nr >= 0 && nr < 15 && nc >= 0 && nc < 15 && state.board[nr][nc] !== null;
          });
        });
        if (!touchesExisting) {
          updateStatusBanner("New tiles must connect to an existing word!");
          return;
        }
      }

      const wordsFound = extractFormedWords(tempGrid, pendingSet);
      if (wordsFound.length === 0) {
        updateStatusBanner("Form a word of at least 2 letters!");
        return;
      }

      let totalPoints = 0;
      const wordLabels = [];
      for (const w of wordsFound) {
        let wordScore = 0;
        let wordMult = 1;
        for (const cell of w.cells) {
          let letterScore = cell.tile.points;
          if (pendingSet.has(`${cell.r},${cell.c}`)) {
            const bonus = BONUS_MAP[`${cell.r},${cell.c}`];
            if (bonus === "DL") letterScore *= 2;
            else if (bonus === "TL") letterScore *= 3;
            else if (bonus === "DW" || bonus === "STAR") wordMult *= 2;
            else if (bonus === "TW") wordMult *= 3;
          }
          wordScore += letterScore;
        }
        wordScore *= wordMult;
        totalPoints += wordScore;
        wordLabels.push(`${w.word} (+${wordScore})`);
      }

      if (state.pendingPlacements.length === 7) {
        totalPoints += 50; // Bingo bonus
        wordLabels.push("7-Tile Bonus (+50)");
      }

      // Commit tiles to board and remove from player rack
      const player = state.players[state.currentTurn];
      const usedIndices = new Set(state.pendingPlacements.map(p => p.rackIndex));
      for (const p of state.pendingPlacements) {
        state.board[p.r][p.c] = p.tile;
      }
      player.rack = player.rack.filter((_, idx) => !usedIndices.has(idx));
      player.rack.push(...drawTiles(7 - player.rack.length));
      player.score += totalPoints;
      state.pendingPlacements = [];
      state.selectedRackIdx = null;
      state.firstMoveDone = true;

      addLog(`${player.name} played ${wordLabels.join(", ")} = +${totalPoints} pts`);
      advanceTurn();
    }

    function extractFormedWords(grid, pendingSet) {
      const results = [];
      const seen = new Set();

      function collectLine(r, c, dr, dc) {
        let sr = r, sc = c;
        while (sr - dr >= 0 && sr - dr < 15 && sc - dc >= 0 && sc - dc < 15 && grid[sr - dr][sc - dc]) {
          sr -= dr;
          sc -= dc;
        }
        const cells = [];
        let cr = sr, cc = sc;
        while (cr >= 0 && cr < 15 && cc >= 0 && cc < 15 && grid[cr][cc]) {
          cells.push({ r: cr, c: cc, tile: grid[cr][cc] });
          cr += dr;
          cc += dc;
        }
        if (cells.length >= 2 && cells.some(x => pendingSet.has(`${x.r},${x.c}`))) {
          const key = `${dr},${dc}:${cells[0].r},${cells[0].c}->${cells[cells.length-1].r},${cells[cells.length-1].c}`;
          if (!seen.has(key)) {
            seen.add(key);
            results.push({
              word: cells.map(x => x.tile.letter).join(""),
              cells
            });
          }
        }
      }

      for (const p of state.pendingPlacements) {
        collectLine(p.r, p.c, 0, 1);
        collectLine(p.r, p.c, 1, 0);
      }
      return results;
    }

    function swapSelectedRackTile() {
      if (!canCurrentDeviceAct()) return;
      if (state.tileBag.length === 0) {
        updateStatusBanner("Tile bag is empty!");
        return;
      }
      if (state.selectedRackIdx === null) {
        updateStatusBanner("Select a tile in your rack first to swap!");
        return;
      }
      const player = state.players[state.currentTurn];
      const oldTile = player.rack[state.selectedRackIdx];
      if (!oldTile) return;

      state.pendingPlacements = [];
      state.tileBag.push(oldTile);
      state.tileBag.sort(() => Math.random() - 0.5);
      player.rack[state.selectedRackIdx] = drawTiles(1)[0] || null;
      player.rack = player.rack.filter(Boolean);
      state.selectedRackIdx = null;

      addLog(`${player.name} swapped a tile.`);
      advanceTurn();
    }

    function passTurn() {
      if (!canCurrentDeviceAct()) return;
      const player = state.players[state.currentTurn];
      state.pendingPlacements = [];
      state.selectedRackIdx = null;
      addLog(`${player.name} passed their turn.`);
      advanceTurn();
    }

    function advanceTurn() {
      state.currentTurn = (state.currentTurn + 1) % state.playerCount;
      renderAll();
      startTurnTimer();
      broadcastStateIfOnline();
    }

    function updateStatusBanner(customMsg) {
      const banner = document.getElementById("statusBanner");
      if (customMsg) {
        banner.textContent = customMsg;
        return;
      }
      const p = state.players[state.currentTurn];
      banner.textContent = `Current Turn: ${p.name}`;
    }

    /* SAND TIMER LOGIC */
    function startTurnTimer() {
      clearInterval(state.timerInterval);
      if (state.timerDuration <= 0) {
        document.getElementById("timerReadout").textContent = "∞";
        document.getElementById("timerLabel").textContent = "Unlimited";
        return;
      }
      state.timeLeft = state.timerDuration;
      updateHourglassVisual();

      state.timerInterval = setInterval(() => {
        state.timeLeft--;
        updateHourglassVisual();
        if (state.timeLeft <= 0) {
          clearInterval(state.timerInterval);
          if (canCurrentDeviceAct()) {
            addLog(`Time's up for ${state.players[state.currentTurn].name}!`);
            advanceTurn();
          }
        }
      }, 1000);
    }

    function updateHourglassVisual() {
      document.getElementById("timerReadout").textContent = `${state.timeLeft}s`;
      const ratio = Math.max(0, Math.min(1, state.timeLeft / Math.max(1, state.timerDuration)));
      const topHeight = 21 * ratio;
      const topY = 35 - topHeight;
      const topHalfW = Math.max(1, 11 * ratio);
      document.getElementById("topSandPoly").setAttribute(
        "points",
        `${27 - topHalfW},${topY} ${27 + topHalfW},${topY} 27,35`
      );
      document.getElementById("sandStreamLine").style.opacity = state.timeLeft > 0 ? "1" : "0";
    }

    function addLog(msg) {
      const box = document.getElementById("logBox");
      const div = document.createElement("div");
      div.className = "log-item neon-outline-sm";
      div.textContent = msg;
      box.prepend(div);
    }

    function escapeHtml(str) {
      return str.replace(/[&<>"']/g, m => ({ "&":"&","<":"<",">":">",'"':""","'":"'" }[m]));
    }

    /* ONLINE MULTIPLAYER (PEERJS) + OPTIONAL CAMERA */
    function generateRoomCode() {
      const chars = "ABCDEFGHJKLMNPQRSTUVWXYZ23456789";
      let code = "SCRAB-";
      for (let i = 0; i < 5; i++) {
        code += chars[Math.floor(Math.random() * chars.length)];
      }
      return code;
    }

    function createOnlineRoom() {
      const code = generateRoomCode();
      state.isHost = true;
      state.localSeat = 0;
      setupPeer(code, true);
    }

    function joinOnlineRoom() {
      const code = document.getElementById("joinCodeInput").value.trim().toUpperCase();
      if (!code) return;
      state.isHost = false;
      setupPeer(null, false, code);
    }

    function setupPeer(roomId, isCreator, targetHostId) {
      if (state.peer) state.peer.destroy();
      const statusPill = document.getElementById("roomStatusPill");
      statusPill.textContent = "Connecting...";

      const peer = isCreator ? new Peer(roomId) : new Peer();
      state.peer = peer;

      peer.on("open", (id) => {
        if (isCreator) {
          statusPill.textContent = `Room: ${id}`;
        } else {
          const conn = peer.connect(targetHostId);
          registerDataConnection(conn);
          statusPill.textContent = `Joined: ${targetHostId}`;
          if (state.camEnabled && state.localStream) {
            peer.call(targetHostId, state.localStream);
          }
        }
      });

      peer.on("connection", (conn) => {
        registerDataConnection(conn);
        const seatIndex = state.connections.length;
        state.playerCount = Math.min(4, Math.max(state.playerCount, seatIndex + 1));
        document.getElementById("playerCountSelect").value = String(state.playerCount);
        renderPlayerSetupCards();
        conn.send({ type: "ASSIGN_SEAT", seat: seatIndex });
        broadcastStateIfOnline();
      });

      peer.on("call", (call) => {
        if (state.localStream) {
          call.answer(state.localStream);
        } else {
          call.answer();
        }
        call.on("stream", (remoteStream) => {
          attachRemoteVideoStream(call.peer, remoteStream);
        });
      });

      peer.on("error", () => {
        statusPill.textContent = "Room error — retry";
      });
    }

    function registerDataConnection(conn) {
      state.connections.push(conn);
      conn.on("data", (data) => {
        if (!data || !data.type) return;
        if (data.type === "ASSIGN_SEAT") {
          state.localSeat = data.seat;
          addLog(`You joined as Player ${data.seat + 1}!`);
        } else if (data.type === "SYNC_STATE") {
          const savedSeat = state.localSeat;
          const savedHost = state.isHost;
          state.board = data.board;
          state.players = data.players;
          state.playerCount = data.players.length;
          state.currentTurn = data.currentTurn;
          state.tileBag = data.tileBag;
          state.firstMoveDone = data.firstMoveDone;
          state.pendingPlacements = [];
          state.localSeat = savedSeat;
          state.isHost = savedHost;

          document.getElementById("setupPanel").style.display = "none";
          document.getElementById("gameWorkspace").classList.add("active");
          document.getElementById("camPanel").style.display = "block";
          renderAll();
          startTurnTimer();
        } else if (data.type === "LOG") {
          addLog(data.text);
        }
      });
    }

    function broadcastStateIfOnline() {
      if (state.mode !== "online" || !state.connections.length) return;
      const payload = {
        type: "SYNC_STATE",
        board: state.board,
        players: state.players,
        currentTurn: state.currentTurn,
        tileBag: state.tileBag,
        firstMoveDone: state.firstMoveDone
      };
      state.connections.forEach(conn => {
        if (conn.open) conn.send(payload);
      });
    }

    async function toggleCamera() {
      const btn = document.getElementById("toggleCamBtn");
      if (!state.camEnabled) {
        try {
          const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          state.localStream = stream;
          state.camEnabled = true;
          btn.textContent = "Cam: ON";
          attachRemoteVideoStream("local", stream, "You");

          if (state.peer) {
            state.connections.forEach(conn => {
              const call = state.peer.call(conn.peer, stream);
              if (call) {
                call.on("stream", (remoteStream) => attachRemoteVideoStream(conn.peer, remoteStream));
              }
            });
          }
        } catch (err) {
          updateStatusBanner("Camera permission denied or unavailable.");
        }
      } else {
        if (state.localStream) {
          state.localStream.getTracks().forEach(t => t.stop());
          state.localStream = null;
        }
        state.camEnabled = false;
        btn.textContent = "Cam: OFF";
        const localTile = document.getElementById("cam_local");
        if (localTile) localTile.remove();
      }
    }

    function attachRemoteVideoStream(peerId, stream, label = "Friend") {
      const grid = document.getElementById("camGrid");
      let tile = document.getElementById(`cam_${peerId}`);
      if (!tile) {
        tile = document.createElement("div");
        tile.id = `cam_${peerId}`;
        tile.className = "cam-tile";
        const vid = document.createElement("video");
        vid.autoplay = true;
        vid.playsInline = true;
        vid.muted = peerId === "local";
        const tag = document.createElement("span");
        tag.className = "cam-tag neon-outline-sm";
        tag.textContent = label;
        tile.appendChild(vid);
        tile.appendChild(tag);
        grid.appendChild(tile);
      }
      const vid = tile.querySelector("video");
      vid.srcObject = stream;
    }

    // Initialize setup screen
    renderPlayerSetupCards();
  </script>
</body>
</html>
