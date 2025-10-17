<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1" />
<title>Happy Birthday Dory — Lights Out</title>
<style>
  :root{
    --bg:#071026;
    --panel:rgba(255,255,255,0.03);
    --txt:#fff;
    --track:#2b2b2b;
    --grass:#0a5b2a;
  }
  html,body{height:100%;margin:0;background:var(--bg);font-family:Inter,system-ui,Roboto,Arial;color:var(--txt);-webkit-font-smoothing:antialiased}
  .root{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:12px;box-sizing:border-box}
  .game{width:100%;max-width:920px;display:flex;flex-direction:column;align-items:center;gap:12px}
  h1{font-family:Orbitron,system-ui;font-size:20px;margin:4px 0;text-align:center}
  .panel{width:100%;background:var(--panel);border-radius:12px;padding:10px;border:1px solid rgba(255,255,255,0.03);box-sizing:border-box}
  .muted{text-align:center;color:rgba(255,255,255,0.92);font-size:14px}
  .controls{display:flex;gap:10px;align-items:center;justify-content:center;margin-top:8px}
  .btn{background:#ffcc00;border:0;padding:10px 14px;border-radius:10px;font-weight:800;color:#111;font-size:15px;cursor:pointer}
  .lights{display:flex;gap:10px;justify-content:center;margin-top:8px}
  .light{width:44px;height:44px;border-radius:50%;background:#222;border:3px solid rgba(255,255,255,0.04);display:inline-flex;align-items:center;justify-content:center;box-shadow:0 8px 18px rgba(0,0,0,0.6)}
  .light.red{background:radial-gradient(circle at 35% 35%,#ff6b6b,#c93030)}
  .light.green{background:radial-gradient(circle at 35% 35%,#8cff8c,#2eb82e)}
  .status{margin-top:6px;text-align:center;font-weight:700;background:rgba(255,255,255,0.02);padding:8px 10px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
  .svg-wrap{width:100%;max-width:760px;height:calc(min(78vh,760px));display:flex;align-items:center;justify-content:center}
  svg{width:100%;height:100%;display:block}
  /* confetti pieces */
  .confetti{position:absolute;left:0;right:0;top:0;bottom:0;pointer-events:none;z-index:50}
  .confetti .piece{position:absolute;width:10px;height:18px;border-radius:2px;opacity:0;transform:translateY(-15vh);animation:confetti 1600ms linear forwards}
  @keyframes confetti{to{transform:translateY(120vh) rotate(720deg);opacity:1}}
  /* explosion placeholder */
  .boom{position:absolute;width:160px;height:160px;border-radius:50%;left:50%;top:50%;transform:translate(-50%,-50%) scale(0);opacity:0;pointer-events:none;z-index:60}
  .boom.show{animation:boom 700ms ease-out forwards}
  @keyframes boom{0%{transform:translate(-50%,-50%) scale(0);opacity:1}60%{transform:translate(-50%,-50%) scale(1.06);opacity:1}100%{transform:translate(-50%,-50%) scale(1.6);opacity:0}}
  /* modals */
  .modal{position:fixed;left:0;right:0;top:0;bottom:0;background:rgba(0,0,0,0.6);display:flex;align-items:center;justify-content:center;z-index:100}
  .card{background:#071026;padding:18px;border-radius:12px;border:1px solid rgba(255,255,255,0.04);text-align:center;max-width:92%}
  .big{font-family:Orbitron;font-size:20px;margin-bottom:8px}
  .retry{background:#fff;border:0;padding:10px 14px;border-radius:10px;font-weight:800;cursor:pointer}
  .close{background:#ffcc00;border:0;padding:10px 14px;border-radius:10px;font-weight:800;cursor:pointer}
  @media (max-width:420px){ .light{width:36px;height:36px} .btn{padding:10px 12px} .big{font-size:18px} }
</style>
</head>
<body>
<div class="root">
  <div class="game" role="application" aria-label="Dory Lights Out Game">
    <h1>🏁 Dory's Lights-Out Birthday Challenge</h1>

    <div class="panel">
      <div class="muted">Press <strong>START RACE</strong>. When <strong>ALL</strong> the lights turn green, tap the track within <strong>600ms</strong> to get an INSANE start. If you don't react in 3s, boom.</div>

      <div class="controls">
        <button id="startBtn" class="btn">START RACE</button>
      </div>

      <div class="lights" id="lightsContainer" aria-hidden="false" style="margin-top:10px">
        <div class="light" id="L1"></div>
        <div class="light" id="L2"></div>
        <div class="light" id="L3"></div>
        <div class="light" id="L4"></div>
        <div class="light" id="L5"></div>
      </div>

      <div class="status" id="status">Waiting to start</div>
    </div>

    <div class="svg-wrap" id="svgWrap">
      <!-- SVG track & cars -->
      <svg id="svg" viewBox="0 0 1200 800" preserveAspectRatio="xMidYMid meet" aria-label="Racetrack">
        <defs>
          <filter id="soft" x="-50%" y="-50%" width="200%" height="200%">
            <feDropShadow dx="0" dy="6" stdDeviation="12" flood-color="#000" flood-opacity="0.6"/>
          </filter>
        </defs>

        <!-- background panel -->
        <rect x="24" y="24" width="1152" height="752" rx="40" fill="#071022"/>

        <!-- outer asphalt -->
        <rect x="80" y="60" width="1040" height="680" rx="50" fill="#2b2b2b"/>

        <!-- inner grass (cutout) -->
        <rect x="220" y="200" width="760" height="400" rx="30" fill="#0a5b2a"/>

        <!-- centerline path defining route (invisible) -->
        <path id="centerPath" d="
          M 260 260
          C 260 170, 940 170, 940 260
          C 940 330, 940 470, 940 540
          C 940 630, 260 630, 260 540
          C 260 470, 260 330, 260 260
        " fill="none" stroke="transparent" stroke-width="4"/>

        <!-- dashed middle lane visual -->
        <path d="
          M 300 300
          C 300 220, 900 220, 900 300
          C 900 350, 900 450, 900 500
          C 900 580, 300 580, 300 500
          C 300 450, 300 350, 300 300
        " stroke="#fff" stroke-width="10" stroke-dasharray="28 18" fill="none" stroke-linecap="round" opacity="0.9"/>

        <!-- start line (white stripe) - center front where cars stand -->
        <rect id="startLine" x="540" y="560" width="120" height="8" fill="#fff" rx="4" />

        <!-- Player car (DORY) group -->
        <g id="playerGroup" transform="translate(600,560)">
          <g id="playerCar" filter="url(#soft)">
            <!-- stylized top-view RB-like car -->
            <rect x="-70" y="-34" width="140" height="68" rx="18" fill="#081e4a" stroke="#d92121" stroke-width="6"/>
            <rect x="-10" y="-18" width="20" height="36" rx="6" fill="#ffd400"/>
            <rect x="-58" y="-6" width="20" height="12" rx="4" fill="#d92121"/>
            <rect x="38" y="-6" width="20" height="12" rx="4" fill="#d92121"/>
            <text x="0" y="6" font-family="Orbitron,system-ui" font-size="20" fill="#fff" font-weight="800" text-anchor="middle">DORY 18</text>
          </g>
        </g>

        <!-- Opponent car (DORA - donkey) -->
        <g id="oppGroup" transform="translate(560,560)">
          <g id="oppCar" filter="url(#soft)">
            <rect x="-64" y="-30" width="128" height="60" rx="14" fill="#6b4b2b" stroke="#3e2b1f" stroke-width="4"/>
            <rect x="-12" y="-14" width="24" height="28" rx="6" fill="#cda57f"/>
            <!-- ears -->
            <rect x="-40" y="-42" width="8" height="20" rx="3" fill="#5a3f2a" transform="rotate(-18 -36 -32)"/>
            <rect x="32" y="-42" width="8" height="20" rx="3" fill="#5a3f2a" transform="rotate(18 36 -32)"/>
            <text x="0" y="18" font-family="system-ui" font-size="18" fill="#fff" font-weight="700" text-anchor="middle">DORA</text>
          </g>
        </g>

      </svg>
    </div>

    <div id="confetti" class="confetti" aria-hidden="true"></div>
    <div id="boom" class="boom" style="background:radial-gradient(circle,#ffcc66 0%, #ff6644 40%, rgba(255,0,0,0) 60%);" aria-hidden="true"></div>

  </div>
</div>

<!-- sounds (hosted links) -->
<audio id="snd_engine" preload="auto" src="https://www.soundjay.com/transportation/sounds/car-engine-1.mp3"></audio>
<audio id="snd_siren" preload="auto" src="https://www.soundjay.com/misc/sounds/air-raid-siren-01.mp3"></audio>
<audio id="snd_zoom" preload="auto" src="https://www.soundjay.com/mechanical/sounds/car-screeching-01.mp3"></audio>
<audio id="snd_cheer" preload="auto" src="https://www.soundjay.com/human/sounds/applause-8.mp3"></audio>
<audio id="snd_boom" preload="auto" src="https://www.soundjay.com/explosion/sounds/explosion-01.mp3"></audio>

<!-- finish modal -->
<div id="finishModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">AND TAKING THE WIN... IT'S DORY!! 🏆</div>
    <div style="font-weight:800;font-size:18px;margin-bottom:8px">HAPPY BIRTHDAY Babygirl ❤️🔥</div>
    <div style="font-weight:700;margin-bottom:12px">From Dora 💫</div>
    <button id="closeFinish" class="close">Close</button>
  </div>
</div>

<!-- fail modal -->
<div id="failModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">Oh no! You didn't react in time 😭</div>
    <div style="margin:8px 0">Your car blew up and Dora snatched the win.</div>
    <button id="retryBtn" class="retry">Retry Race</button>
  </div>
</div>

<script>
/* Final rebuilt Dory Lights-Out
   - Centered, slower timings, start line, cars aligned, proper audio unlock, retry, confetti, explosion.
   - Save as index.html and open on phone browser.
*/

(function(){
  // DOM refs
  const startBtn = document.getElementById('startBtn');
  const status = document.getElementById('status');
  const lights = [document.getElementById('L1'),document.getElementById('L2'),document.getElementById('L3'),document.getElementById('L4'),document.getElementById('L5')];
  const svg = document.getElementById('svg');
  const centerPath = document.getElementById('centerPath');
  const playerGroup = document.getElementById('playerGroup');
  const oppGroup = document.getElementById('oppGroup');
  const playerCar = document.getElementById('playerCar');
  const oppCar = document.getElementById('oppCar');
  const confetti = document.getElementById('confetti');
  const boom = document.getElementById('boom');
  const finishModal = document.getElementById('finishModal');
  const failModal = document.getElementById('failModal');
  const retryBtn = document.getElementById('retryBtn');
  const closeFinish = document.getElementById('closeFinish');

  // sounds
  const s_engine = document.getElementById('snd_engine');
  const s_siren = document.getElementById('snd_siren');
  const s_zoom = document.getElementById('snd_zoom');
  const s_cheer = document.getElementById('snd_cheer');
  const s_boom = document.getElementById('snd_boom');

  // state
  let pathLen = 0;
  let startPos = 0;
  let playerPos = 0;
  let oppPos = 0;
  let waitingForTap = false;
  let reactionStart = 0;
  let tapped = false;
  let timers = [];
  let animFrame = null;

  // lane lateral offset (px) to separate cars from center path
  const laneOffset = 45; // separation from centerline
  const lightDelay = 700; // ms for red->green step

  function clearTimers(){ timers.forEach(t=>clearTimeout(t)); timers=[]; }
  function cancelAnim(){ if(animFrame) cancelAnimationFrame(animFrame); animFrame=null; }

  function initPath(){
    // ensure centerPath exists
    pathLen = centerPath.getTotalLength();
    // choose a start position along path where start line visually near bottom area
    // we pick the point nearest to y ~ 560 (visual start line)
    // search along path to find a point with y near 560
    let best = 0, bestDiff = Infinity;
    for(let i=0;i<=100;i++){
      const p = centerPath.getPointAtLength((i/100)*pathLen);
      const diff = Math.abs(p.y - 560); if(diff < bestDiff){ bestDiff = diff; best = (i/100)*pathLen; }
    }
    startPos = best;
    // set both cars at the startPos (slightly offset lateral)
    playerPos = startPos;
    oppPos = startPos;
    placeAt(playerGroup, playerPos, laneOffset); // player to right of center
    placeAt(oppGroup, oppPos, -laneOffset); // opponent left of center
  }

  // place an element at a path position with lateral offset
  function placeAt(groupEl, pos, lateral){
    const p = centerPath.getPointAtLength(pos % pathLen);
    const ahead = centerPath.getPointAtLength((pos + 1) % pathLen);
    const dx = ahead.x - p.x, dy = ahead.y - p.y;
    const ang = Math.atan2(dy, dx) * 180 / Math.PI;
    // normal vector (perpendicular) normalized
    const nx = -dy, ny = dx;
    const nlen = Math.sqrt(nx*nx + ny*ny) || 1;
    const nxn = nx / nlen, nyn = ny / nlen;
    const x = p.x + nxn * lateral;
    const y = p.y + nyn * lateral;
    groupEl.setAttribute('transform', `translate(${x},${y}) rotate(${ang})`);
  }

  // lights sequence
  function startLights(){
    clearTimers();
    status.textContent = 'Lights are coming...';
    lights.forEach(l=>{ l.classList.remove('red'); l.classList.remove('green'); });
    // red on one-by-one
    lights.forEach((l,i)=>{
      timers.push(setTimeout(()=> l.classList.add('red'), i * lightDelay));
    });
    // after last red complete, pause a bit then green one-by-one
    const afterRed = lights.length * lightDelay + 500 + Math.random()*400;
    timers.push(setTimeout(()=>{
      status.textContent = 'Get ready...';
      // turn to green one-by-one
      lights.forEach((l,i)=>{
        timers.push(setTimeout(()=>{
          l.classList.remove('red');
          l.classList.add('green');
          // when the last light turns green:
          if(i === lights.length - 1) {
            onAllGreen();
          }
        }, i * lightDelay));
      });
      // a light siren when greens begin
      try{ s_siren.currentTime = 0; s_siren.play(); }catch(e){}
    }, afterRed));
  }

  function onAllGreen(){
    reactionStart = performance.now();
    waitingForTap = true;
    tapped = false;
    status.textContent = 'GO! TAP the track NOW!';
    // start 3s explosion timer
    timers.push(setTimeout(()=>{ if(!tapped) explosionFail(); }, 3000));
  }

  // handle user tap for reaction
  function handleTap(e){
    if(!waitingForTap) return;
    if(tapped) return;
    tapped = true; waitingForTap = false;
    const now = performance.now(); const dt = now - reactionStart;
    if(dt <= 600){
      playerStart(true);
    } else if(dt <= 3000){
      playerStart(false);
    } else {
      // let explosion timer handle it
    }
  }

  // explosion fail
  function explosionFail(){
    waitingForTap = false; tapped = true;
    status.textContent = "Too slow! BOOM!";
    // compute screen coordinates of player's current path point to place boom element
    const pt = centerPath.getPointAtLength(playerPos % pathLen);
    const svgRect = svg.getBoundingClientRect();
    // convert SVG point to screen px in the svg bounding box
    const svgWidth = svg.viewBox.baseVal.width, svgHeight = svg.viewBox.baseVal.height;
    const screenX = svgRect.left + (pt.x / svgWidth) * svgRect.width;
    const screenY = svgRect.top + (pt.y / svgHeight) * svgRect.height;
    // position boom element at screen coordinates
    boom.style.left = (screenX) + 'px';
    boom.style.top = (screenY) + 'px';
    boom.classList.add('show');
    try{ s_boom.currentTime = 0; s_boom.play(); }catch(e){}
    // hide player visually by reducing opacity
    playerGroup.style.opacity = 0;
    // animate opponent to speed and win
    driveOpponentWin();
    // show fail modal after small delay
    timers.push(setTimeout(()=> { failModal.style.display = 'flex'; }, 900));
  }

  // animate opponent to win (simple)
  function driveOpponentWin(){
    const start = oppPos;
    const end = oppPos + pathLen * 1.2;
    const dur = 1600;
    const t0 = performance.now();
    function step(t){
      const p = Math.min(1, (t - t0)/dur);
      oppPos = start + (end - start) * easeOutCubic(p);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(p < 1) requestAnimationFrame(step);
    }
    requestAnimationFrame(step);
  }

  // player start animation
  function playerStart(isPerfect){
    status.textContent = isPerfect ? 'INSANE START! Dory rockets away!' : 'Nice start — Dory takes the lead!';
    // sounds
    try{ s_engine.currentTime = 0; s_engine.play(); }catch(e){}
    timers.push(setTimeout(()=>{ try{ s_zoom.currentTime = 0; s_zoom.play(); }catch(e){} }, 220));
    timers.push(setTimeout(()=>{ try{ s_cheer.currentTime = 0; s_cheer.play(); }catch(e){} }, 1200));

    // animate: player moves faster (shorter duration) and opponent slower
    const pStart = playerPos;
    const oStart = oppPos;
    const pEnd = pStart + pathLen * 1.1;
    const oEnd = oStart + pathLen * 0.65;
    const pDur = isPerfect ? 2200 : 3000;  // slower than earlier
    const oDur = isPerfect ? 3200 : 4200;
    const t0 = performance.now();

    cancelAnim();
    function step(t){
      const tp = Math.min(1, (t - t0) / pDur);
      const to = Math.min(1, (t - t0) / oDur);
      playerPos = pStart + (pEnd - pStart) * easeOutCubic(tp);
      oppPos = oStart + (oEnd - oStart) * easeInCubic(to);
      placeAt(playerGroup, playerPos, laneOffset);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(tp < 1 || to < 1){
        animFrame = requestAnimationFrame(step);
      } else {
        // finished, show confetti and finish modal
        launchConfetti();
        timers.push(setTimeout(()=>{ finishModal.style.display = 'flex'; }, 450));
      }
    }
    animFrame = requestAnimationFrame(step);
  }

  // confetti
  function launchConfetti(){
    confetti.innerHTML = '';
    const colors = ['#ff3b3b','#ffd400','#00d4ff','#6bff6b','#ff7bd4'];
    for(let i=0;i<60;i++){
      const el = document.createElement('div');
      el.className = 'piece';
      el.style.left = (Math.random()*100)+'%';
      el.style.top = (Math.random()*10 - 5)+'%';
      el.style.background = colors[Math.floor(Math.random()*colors.length)];
      el.style.animationDuration = (1000 + Math.random()*1000)+'ms';
      confetti.appendChild(el);
    }
  }

  // easing
  function easeOutCubic(t){ return 1 - Math.pow(1 - t, 3); }
  function easeInCubic(t){ return t*t*t; }

  // reset / initial
  function resetAll(){
    clearTimers(); cancelAnim();
    waitingForTap = false; tapped = false;
    status.textContent = 'Waiting to start';
    finishModal.style.display = 'none';
    failModal.style.display = 'none';
    boom.className = 'boom';
    confetti.innerHTML = '';
    playerGroup.style.opacity = 1;
    lights.forEach(l=>{ l.classList.remove('red','green'); });
    // reset positions
    initPath();
  }

  // find start pos and place cars
  function initPath(){
    pathLen = centerPath.getTotalLength();
    // locate approximate path point near y ~ 560 (visual start line)
    let best = 0, bestDiff = Infinity;
    const steps = 200;
    for(let i=0;i<=steps;i++){
      const pos = (i/steps) * pathLen;
      const pt = centerPath.getPointAtLength(pos);
      const diff = Math.abs(pt.y - 560);
      if(diff < bestDiff){ bestDiff = diff; best = pos; }
    }
    startPos = best;
    playerPos = startPos;
    oppPos = startPos;
    placeAt(playerGroup, playerPos, laneOffset);
    placeAt(oppGroup, oppPos, -laneOffset);
  }

  // event wiring
  window.addEventListener('load', ()=>{
    initPath();
    // unlock audio on first touch/click (user gesture)
    function unlockAudio(){
      try{ s_engine.play().then(()=>{ s_engine.pause(); s_engine.currentTime = 0; }).catch(()=>{}); }catch(e){}
      document.removeEventListener('touchstart', unlockAudio);
      document.removeEventListener('click', unlockAudio);
    }
    document.addEventListener('touchstart', unlockAudio, {passive:true});
    document.addEventListener('click', unlockAudio);
  });

  // start button
  startBtn.addEventListener('click', ()=>{
    resetAll();
    startBtn.style.display = 'none';
    // start lights sequence after tiny delay
    setTimeout(()=> startLights(), 200);
  });

  // allow tapping anywhere inside the svg wrapper area
  const svgWrap = document.getElementById('svgWrap');
  svgWrap.addEventListener('touchstart', (e)=>{ e.preventDefault(); handleTap(e); }, {passive:false});
  svgWrap.addEventListener('mousedown', ()=>{ handleTap(); });

  // modal buttons
  retryBtn.addEventListener('click', ()=>{ resetAll(); startBtn.style.display = 'inline-block'; failModal.style.display = 'none'; });
  closeFinish.addEventListener('click', ()=>{ finishModal.style.display = 'none'; resetAll(); startBtn.style.display = 'inline-block'; });

  // resize -> re-init path & place cars
  window.addEventListener('resize', ()=>{ initPath(); });

  // expose reset in console for debugging
  window.doryReset = resetAll;
})();
</script>
</body>
</html>
