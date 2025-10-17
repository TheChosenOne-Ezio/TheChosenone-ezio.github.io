<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1" />
<title>Happy Birthday Dori — Lights Out (Final)</title>
<style>
  :root{
    --bg:#071026; --panel:rgba(255,255,255,0.03); --txt:#fff;
    --track:#2b2b2b; --grass:#0a5b2a; --accent:#ffd400;
  }
  html,body{height:100%;margin:0;background:var(--bg);font-family:Inter,system-ui,Roboto,Arial;color:var(--txt)}
  .root{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:12px;box-sizing:border-box}
  .game{width:100%;max-width:920px;display:flex;flex-direction:column;align-items:center;gap:10px}
  h1{font-family:Orbitron,system-ui;font-size:20px;margin:6px 0;text-align:center}
  .panel{width:100%;background:var(--panel);border-radius:12px;padding:12px;border:1px solid rgba(255,255,255,0.03)}
  .muted{font-size:14px;text-align:center;margin-bottom:6px}
  .controls{display:flex;gap:10px;align-items:center;justify-content:center}
  .btn{background:var(--accent);color:#111;border:0;padding:10px 14px;border-radius:10px;font-weight:800;cursor:pointer}
  .row{display:flex;gap:8px;align-items:center}
  .lights{display:flex;gap:8px;justify-content:center;margin-top:8px}
  .light{width:44px;height:44px;border-radius:50%;background:#222;border:3px solid rgba(255,255,255,0.04);display:inline-flex;align-items:center;justify-content:center}
  .light.red{background:radial-gradient(circle at 35% 35%,#ff6b6b,#c93030)}
  .light.green{background:radial-gradient(circle at 35% 35%,#8cff8c,#2eb82e)}
  .status{margin-top:6px;text-align:center;font-weight:700;background:rgba(255,255,255,0.02);padding:8px 10px;border-radius:10px}
  .svg-wrap{width:100%;max-width:760px;height:calc(min(74vh,760px));display:flex;align-items:center;justify-content:center;position:relative}
  svg{width:100%;height:100%;display:block}
  .scoreboard{display:flex;gap:12px;align-items:center;justify-content:center;margin-top:6px;font-weight:800}
  .score{background:rgba(255,255,255,0.03);padding:6px 10px;border-radius:8px}
  .confetti{position:absolute;left:0;right:0;top:0;bottom:0;pointer-events:none;z-index:40}
  .confetti .piece{position:absolute;width:10px;height:18px;border-radius:2px;opacity:0;transform:translateY(-15vh);animation:confetti 1200ms linear forwards}
  @keyframes confetti{to{transform:translateY(120vh) rotate(720deg);opacity:1}}
  .boom{position:absolute;width:160px;height:160px;border-radius:50%;left:50%;top:50%;transform:translate(-50%,-50%) scale(0);opacity:0;pointer-events:none;z-index:50}
  .boom.show{animation:boom 700ms ease-out forwards}
  @keyframes boom{0%{transform:translate(-50%,-50%) scale(0);opacity:1}60%{transform:translate(-50%,-50%) scale(1.06);opacity:1}100%{transform:translate(-50%,-50%) scale(1.6);opacity:0}}
  .modal{position:fixed;inset:0;background:rgba(0,0,0,0.6);display:flex;align-items:center;justify-content:center;z-index:120}
  .card{background:#071026;padding:18px;border-radius:12px;border:1px solid rgba(255,255,255,0.04);text-align:center;max-width:92%}
  .big{font-family:Orbitron;font-size:20px;margin-bottom:8px}
  .retry{background:#fff;border:0;padding:10px 14px;border-radius:10px;font-weight:800;cursor:pointer}
  .close{background:var(--accent);border:0;padding:10px 14px;border-radius:10px;font-weight:800;cursor:pointer}
  @media (max-width:420px){ .light{width:36px;height:36px} .btn{padding:10px 12px} .big{font-size:18px} }
</style>
</head>
<body>
<div class="root">
  <div class="game" role="application" aria-label="Dori Lights Out F1 Game">
    <h1>🏁 Dori’s F1 Lights-Out Birthday Challenge!</h1>

    <div class="panel">
      <div class="muted">Press <strong>START RACE</strong>. When <strong>ALL</strong> the lights turn green, tap the track within <strong>400 ms</strong> to secure the lap. There are 5 rounds — good luck!</div>
      <div class="controls">
        <button id="startBtn" class="btn">START RACE</button>
      </div>

      <div class="lights" id="lightsContainer" style="margin-top:10px">
        <div class="light" id="L1"></div>
        <div class="light" id="L2"></div>
        <div class="light" id="L3"></div>
        <div class="light" id="L4"></div>
        <div class="light" id="L5"></div>
      </div>

      <div class="status" id="status">Waiting to start</div>
      <div class="scoreboard" style="margin-top:8px">
        <div class="score">Round: <span id="roundNum">0</span>/5</div>
        <div class="score">Dori: <span id="scoreDori">0</span></div>
        <div class="score">Dora: <span id="scoreDora">0</span></div>
      </div>
    </div>

    <div class="svg-wrap" id="svgWrap">
      <svg id="svg" viewBox="0 0 1200 800" aria-label="F1 Racetrack">
        <defs>
          <filter id="shadow" x="-60%" y="-60%" width="220%" height="220%">
            <feDropShadow dx="0" dy="7" stdDeviation="14" flood-color="#000" flood-opacity="0.62"/>
          </filter>
        </defs>

        <!-- Track background -->
        <rect x="24" y="24" width="1152" height="752" rx="40" fill="#071022"/>
        <rect x="80" y="60" width="1040" height="680" rx="50" fill="#2b2b2b"/>
        <rect x="220" y="200" width="760" height="400" rx="30" fill="#0a5b2a"/>

        <!-- Center path -->
        <path id="centerPath" d="
          M 320 300
          C 300 180, 960 180, 900 320
          S 960 620, 350 600
          Q 230 580, 320 300
        " fill="none" stroke="transparent" stroke-width="4"/>

        <!-- Visual dashed line -->
        <path d="
          M 370 340
          C 330 220, 910 220, 860 360
          S 930 570, 400 565
          Q 260 545, 370 340
        " stroke="#fff" stroke-width="12" stroke-dasharray="28 18" fill="none" opacity="0.88"/>

        <!-- Start line -->
        <rect id="startLine" x="570" y="560" width="70" height="10" fill="#fff" rx="4" />

        <!-- DORY (player) - Red Bull style top-view -->
        <g id="playerGroup" transform="translate(600,560)">
          <g id="playerCar" filter="url(#shadow)">
            <rect x="-34" y="-22" width="68" height="44" rx="13" fill="#1463a0" stroke="#ffd400" stroke-width="5"/>
            <rect x="-6" y="-18" width="12" height="36" rx="4" fill="#ffd400"/>
            <rect x="-30" y="-3" width="8" height="9" rx="2" fill="#ffd400"/>
            <rect x="22" y="-3" width="8" height="9" rx="2" fill="#ffd400"/>
            <rect x="-41" y="-16" width="8" height="32" rx="4" fill="#383838"/>
            <rect x="34" y="-16" width="8" height="32" rx="4" fill="#383838"/>
            <text id="playerLabel" x="0" y="6" font-family="Orbitron,system-ui" font-size="18" fill="#fff" font-weight="800" text-anchor="middle">DORI</text>
          </g>
        </g>

        <!-- DORA (opponent) - donkey top-view -->
        <g id="oppGroup" transform="translate(560,560)">
          <g id="oppCar" filter="url(#shadow)">
            <rect x="-32" y="-14" width="64" height="32" rx="12" fill="#8d7435" stroke="#725e34" stroke-width="4"/>
            <rect x="-27" y="-28" width="8" height="18" rx="3" fill="#725e34" transform="rotate(-18 -30 -23)"/>
            <rect x="19" y="-28" width="8" height="18" rx="3" fill="#725e34" transform="rotate(18 23 -23)"/>
            <rect x="-10" y="-8" width="20" height="16" rx="4" fill="#be9c68"/>
            <text id="oppLabel" x="0" y="10" font-family="system-ui" font-size="14" fill="#fff" font-weight="800" text-anchor="middle">DORA</text>
          </g>
        </g>
      </svg>

      <div id="confetti" class="confetti" aria-hidden="true"></div>
      <div id="boom" class="boom" style="background:radial-gradient(circle,#fff69c 0%, #ff6644 40%, rgba(255,0,0,0) 60%);" aria-hidden="true"></div>
    </div>
  </div>
</div>

<!-- sounds -->
<audio id="snd_engine" preload="auto" src="https://www.soundjay.com/transportation/sounds/car-engine-1.mp3"></audio>
<audio id="snd_siren" preload="auto" src="https://www.soundjay.com/misc/sounds/air-raid-siren-01.mp3"></audio>
<audio id="snd_zoom" preload="auto" src="https://www.soundjay.com/mechanical/sounds/car-screeching-01.mp3"></audio>
<audio id="snd_cheer" preload="auto" src="https://www.soundjay.com/human/sounds/applause-8.mp3"></audio>
<audio id="snd_boom" preload="auto" src="https://www.soundjay.com/explosion/sounds/explosion-01.mp3"></audio>

<!-- finish modal -->
<div id="finishModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">AND TAKING THE WIN... IT'S DORI!! 🏆</div>
    <div style="font-weight:800;font-size:18px;margin-bottom:8px;color:#ffd400">HAPPY BIRTHDAY Babygirl❤️🔥</div>
    <div style="font-weight:700;margin-bottom:12px">From Dora 💫</div>
    <button id="closeFinish" class="close">Close</button>
  </div>
</div>

<!-- fail modal -->
<div id="failModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">Oh no! Too slow 😭</div>
    <div style="margin:8px 0">Your car blew up and Dora wins this lap.</div>
    <button id="retryBtn" class="retry">Continue</button>
  </div>
</div>

<script>
/* Finalized game logic:
   - 5 rounds with predetermined winners: [Dori, Dora, Dora, Dori, Dori]
   - Reaction window after last green: 400 ms
   - If round outcome forced, override reaction result accordingly
   - Scoreboard updates, cars reset after each lap
   - Top-down track, path-following movement
*/

(function(){
  // Elements
  const startBtn = document.getElementById('startBtn');
  const status = document.getElementById('status');
  const roundNumEl = document.getElementById('roundNum');
  const scoreDoriEl = document.getElementById('scoreDori');
  const scoreDoraEl = document.getElementById('scoreDora');
  const lights = [document.getElementById('L1'),document.getElementById('L2'),document.getElementById('L3'),document.getElementById('L4'),document.getElementById('L5')];
  const svg = document.getElementById('svg');
  const centerPath = document.getElementById('centerPath');
  const playerGroup = document.getElementById('playerGroup');
  const oppGroup = document.getElementById('oppGroup');
  const confetti = document.getElementById('confetti');
  const boom = document.getElementById('boom');
  const finishModal = document.getElementById('finishModal');
  const failModal = document.getElementById('failModal');
  const retryBtn = document.getElementById('retryBtn');
  const closeFinish = document.getElementById('closeFinish');

  const s_engine = document.getElementById('snd_engine');
  const s_siren = document.getElementById('snd_siren');
  const s_zoom = document.getElementById('snd_zoom');
  const s_cheer = document.getElementById('snd_cheer');
  const s_boom = document.getElementById('snd_boom');

  // Game settings / state
  const TOTAL_ROUNDS = 5;
  const winners = ['Dori','Dora','Dora','Dori','Dori']; // predetermined outcomes by round index 0..4
  const reactionWindow = 400; // ms (player must tap within this after last green to register a tap)
  const explosionTimeout = 400; // same (player fails if not clicked within this in rounds where failure causes explosion)
  const lightDelay = 700; // ms between each light step
  const laneOffset = 45;
  let pathLen = 0, playerPos = 0, oppPos = 0, startPos = 0;
  let currentRound = 0;
  let score = { Dori:0, Dora:0 };
  let waitingForTap = false, reactionStart = 0, playerTapped = false;
  let timers = [], animFrame = null;

  // Utilities
  function clearTimers(){ timers.forEach(t=>clearTimeout(t)); timers=[]; }
  function cancelAnim(){ if(animFrame) cancelAnimationFrame(animFrame); animFrame = null; }

  function initPath(){
    pathLen = centerPath.getTotalLength();
    // find path point near y~560 to use as start
    let best = 0, bestDiff = Infinity;
    for(let i=0;i<=200;i++){
      const pos = (i/200) * pathLen;
      const p = centerPath.getPointAtLength(pos);
      const d = Math.abs(p.y - 560);
      if(d < bestDiff){ bestDiff = d; best = pos; }
    }
    startPos = best;
    playerPos = startPos;
    oppPos = startPos;
    placeAt(playerGroup, playerPos, laneOffset);
    placeAt(oppGroup, oppPos, -laneOffset);
  }

  function placeAt(groupEl, pos, lateral){
    const p = centerPath.getPointAtLength(pos % pathLen);
    const ahead = centerPath.getPointAtLength((pos + 1) % pathLen);
    const dx = ahead.x - p.x, dy = ahead.y - p.y;
    const ang = Math.atan2(dy, dx) * 180 / Math.PI;
    const nx = -dy, ny = dx;
    const nlen = Math.sqrt(nx*nx + ny*ny) || 1;
    const x = p.x + (nx / nlen) * lateral;
    const y = p.y + (ny / nlen) * lateral;
    groupEl.setAttribute('transform', `translate(${x},${y}) rotate(${ang})`);
  }

  // lights sequence for a round
  function startLightsForRound(){
    clearTimers();
    playerTapped = false;
    waitingForTap = false;
    status.textContent = 'Lights coming...';
    lights.forEach(l=>{ l.classList.remove('red','green'); });
    // red on
    lights.forEach((l,i)=> timers.push(setTimeout(()=> l.classList.add('red'), i * lightDelay)));
    const afterRed = lights.length * lightDelay + 500;
    timers.push(setTimeout(()=> {
      status.textContent = 'Ready...';
      // green on one by one
      lights.forEach((l,i)=> timers.push(setTimeout(()=> {
        l.classList.remove('red'); l.classList.add('green');
        // when last goes green, start reaction window
        if(i === lights.length - 1) onAllGreen();
      }, i * lightDelay)));
      try{ s_siren.currentTime = 0; s_siren.play(); }catch(e){}
    }, afterRed));
  }

  function onAllGreen(){
    reactionStart = performance.now();
    waitingForTap = true;
    playerTapped = false;
    status.textContent = '⚡ TAP the track NOW!';
    // explosion/fail check after explosionTimeout (400ms)
    timers.push(setTimeout(()=> {
      if(!playerTapped){
        // player didn't tap within reaction window
        // determine outcome according to predetermined winners:
        const desiredWinner = winners[currentRound];
        if(desiredWinner === 'Dori'){
          // even if player didn't tap, Dori still wins this round (per your fixed plan) -> no explosion
          // proceed to show win animation for Dori
          simulateWin('Dori', false);
        } else {
          // desiredWinner is Dora and player didn't tap -> explosion (fail) then Dora wins
          explosionFail();
        }
      } else {
        // player did tap within reaction window; handle outcome (may be overridden by predetermined winners)
        evaluateTapWin();
      }
    }, explosionTimeout)); // 400ms
  }

  // handle player tap event (tap anywhere inside svg area)
  function handleTap(){
    if(!waitingForTap) return;
    if(playerTapped) return;
    playerTapped = true;
    waitingForTap = false;
    const dt = performance.now() - reactionStart;
    // dt should be <= reactionWindow (400ms) logically since we only accept this click during waiting
    // decide winner according to predetermined winners
    evaluateTapWin();
  }

  // Evaluate tap vs predetermined result
  function evaluateTapWin(){
    const desiredWinner = winners[currentRound];
    if(desiredWinner === 'Dori'){
      // Dori must win this round regardless (so treat as win)
      simulateWin('Dori', true);
    } else if(desiredWinner === 'Dora'){
      // Dora must win regardless — even if the player tapped in time, we still make Dora win
      // But if player tapped, don't show explosion; just let Dora win
      simulateWin('Dora', true);
    } else {
      // fallback: if player tapped, Dori wins
      simulateWin('Dori', true);
    }
  }

  function explosionFail(){
    // Show explosion at player's position and then award lap to Dora
    status.textContent = 'Too slow! BOOM!';
    const pt = centerPath.getPointAtLength(playerPos % pathLen);
    const svgRect = svg.getBoundingClientRect();
    const svgWidth = svg.viewBox.baseVal.width, svgHeight = svg.viewBox.baseVal.height;
    const screenX = svgRect.left + (pt.x / svgWidth) * svgRect.width;
    const screenY = svgRect.top + (pt.y / svgHeight) * svgRect.height;
    boom.style.left = (screenX) + 'px';
    boom.style.top = (screenY) + 'px';
    boom.classList.add('show');
    try{ s_boom.currentTime = 0; s_boom.play(); }catch(e){}
    playerGroup.style.opacity = 0;
    // Opponent drives to finish quickly
    driveOpponentWin(()=> {
      // award Dora point and continue next round after short delay
      awardPointAndNext('Dora');
      timers.push(setTimeout(()=> { failModal.style.display = 'flex'; }, 700));
    });
  }

  function driveOpponentWin(cb){
    const start = oppPos;
    const end = oppPos + pathLen * 1.1;
    const dur = 1400;
    const t0 = performance.now();
    function step(t){
      const p = Math.min(1, (t - t0)/dur);
      oppPos = start + (end - start) * easeOutCubic(p);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(p < 1) requestAnimationFrame(step); else if(cb) cb();
    }
    requestAnimationFrame(step);
  }

  // Simulate win animation for the named winner
  // tappedFlag indicates whether player actually tapped (true) or result forced/auto (false)
  function simulateWin(winner, tappedFlag){
    status.textContent = (winner === 'Dori') ? 'Dori takes the lap!' : 'Dora takes the lap!';
    // play sounds: engine then zoom then cheer
    try{ s_engine.currentTime = 0; s_engine.play(); }catch(e){}
    timers.push(setTimeout(()=>{ try{ s_zoom.currentTime = 0; s_zoom.play(); }catch(e){} }, 220));
    timers.push(setTimeout(()=>{ try{ s_cheer.currentTime = 0; s_cheer.play(); }catch(e){} }, 1000));

    // animate cars: winner does a faster lap, loser slower
    const pStart = playerPos, oStart = oppPos;
    // distances based on who wins -> winner gets longer travel to finish earlier
    const pEnd = (winner === 'Dori') ? pStart + pathLen * 1.15 : pStart + pathLen * 0.65;
    const oEnd = (winner === 'Dora') ? oStart + pathLen * 1.15 : oStart + pathLen * 0.75;
    const pDur = (winner === 'Dori') ? 2200 : 3200;
    const oDur = (winner === 'Dora') ? 2200 : 3200;
    const t0 = performance.now();
    cancelAnim();
    function step(t){
      const tp = Math.min(1, (t - t0) / pDur);
      const to = Math.min(1, (t - t0) / oDur);
      playerPos = pStart + (pEnd - pStart) * easeOutCubic(tp);
      oppPos   = oStart + (oEnd - oStart) * easeInCubic(to);
      placeAt(playerGroup, playerPos, laneOffset);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(tp < 1 || to < 1){
        animFrame = requestAnimationFrame(step);
      } else {
        // lap finished
        launchConfetti();
        awardPointAndNext(winner);
        timers.push(setTimeout(()=> {
          // if this was last round, show final modal; else continue
          if(currentRound >= TOTAL_ROUNDS){
            // show final modal after tiny delay
            timers.push(setTimeout(()=> { finishModal.style.display = 'flex'; }, 500));
          }
        }, 450));
      }
    }
    animFrame = requestAnimationFrame(step);
  }

  function awardPointAndNext(winner){
    if(winner === 'Dori') score.Dori++;
    else score.Dora++;
    scoreDoriEl.textContent = score.Dori;
    scoreDoraEl.textContent = score.Dora;
    // proceed to next round after small pause (reset cars and lights)
    timers.push(setTimeout(()=> {
      currentRound++;
      roundNumEl.textContent = Math.min(currentRound+1, TOTAL_ROUNDS);
      // If rounds remain, reset positions and start next after short cue
      if(currentRound < TOTAL_ROUNDS){
        resetCarsForNextRound();
        // small delay then start next lights automatically
        timers.push(setTimeout(()=> startLightsForRound(), 900));
      } else {
        // All rounds over -> show finish after a beat (handled above)
      }
    }, 800));
  }

  function resetCarsForNextRound(){
    // reset visual positions to start line
    playerGroup.style.opacity = 1;
    initPath();
    lights.forEach(l=> l.classList.remove('red','green'));
    boom.className = 'boom';
    confetti.innerHTML = '';
    status.textContent = 'Preparing next round...';
  }

  // confetti
  function launchConfetti(){
    confetti.innerHTML = '';
    const colors = ['#ffd400','#1463a0','#ff3b3b','#7aef54','#ff7bd4'];
    for(let i=0;i<45;i++){
      const el = document.createElement('div');
      el.className = 'piece';
      el.style.left = (Math.random()*100)+'%';
      el.style.top = (Math.random()*10 - 5)+'%';
      el.style.background = colors[Math.floor(Math.random()*colors.length)];
      el.style.animationDuration = (900 + Math.random()*900)+'ms';
      confetti.appendChild(el);
    }
  }

  function easeOutCubic(t){ return 1 - Math.pow(1 - t, 3); }
  function easeInCubic(t){ return t*t*t; }

  // Reset game to initial
  function resetGame(){
    clearTimers(); cancelAnim();
    currentRound = 0;
    score = { Dori:0, Dora:0 };
    scoreDoriEl.textContent = 0;
    scoreDoraEl.textContent = 0;
    roundNumEl.textContent = 0;
    finishModal.style.display = 'none';
    failModal.style.display = 'none';
    boom.className = 'boom';
    confetti.innerHTML = '';
    playerGroup.style.opacity = 1;
    lights.forEach(l=> l.classList.remove('red','green'));
    initPath();
    status.textContent = 'Waiting to start';
    startBtn.style.display = 'inline-block';
  }

  // unlock audio on first gesture (mobile)
  function unlockAudio(){
    try{
      s_engine.play().then(()=>{ s_engine.pause(); s_engine.currentTime = 0; }).catch(()=>{});
    }catch(e){}
    document.removeEventListener('touchstart', unlockAudio);
    document.removeEventListener('click', unlockAudio);
  }

  // Start round flow
  function startGame(){
    resetRoundVisuals();
    currentRound = 0;
    score = { Dori:0, Dora:0 };
    scoreDoriEl.textContent = 0;
    scoreDoraEl.textContent = 0;
    roundNumEl.textContent = 1;
    startBtn.style.display = 'none';
    // small delay then start lights for round 1
    timers.push(setTimeout(()=> startLightsForRound(), 350));
  }

  function resetRoundVisuals(){
    clearTimers(); cancelAnim();
    boom.className = 'boom';
    confetti.innerHTML = '';
    playerGroup.style.opacity = 1;
    lights.forEach(l=> l.classList.remove('red','green'));
    initPath();
    status.textContent = 'Get ready...';
  }

  // attach events
  window.addEventListener('load', ()=>{
    initPath();
    document.addEventListener('touchstart', unlockAudio, {passive:true});
    document.addEventListener('click', unlockAudio);
    roundNumEl.textContent = 0;
  });

  startBtn.addEventListener('click', ()=>{
    resetGame();
    startGame();
  });

  // tapping on the svgWrap counts as the reaction tap
  const svgWrap = document.getElementById('svgWrap');
  svgWrap.addEventListener('touchstart', (e)=>{ e.preventDefault(); handleTap(); }, {passive:false});
  svgWrap.addEventListener('mousedown', ()=>{ handleTap(); });

  // failure modal continue
  retryBtn.addEventListener('click', ()=> {
    failModal.style.display = 'none';
    // after fail modal, award has already been handled; just continue flow
  });

  closeFinish.addEventListener('click', ()=> {
    finishModal.style.display = 'none';
    // game ends; show reset button
    startBtn.style.display = 'inline-block';
    resetGame();
  });

  // helper to expose reset in console if you want
  window.doriReset = resetGame;

})();
</script>
</body>
</html>
