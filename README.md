# TheChosenone-ezio.github.io
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Happy Birthday Dory — F1 Lights Out!</title>
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
  <style>
    :root{
      --bg:#080f24;
      --f1-red:#221b4b;
      --f1-blue:#1463a0;
      --f1-yellow:#ffe600;
      --donkey-brown:#8d7435;
      --donkey-ear:#725e34;
      --track:#2b2b2b;
      --grass:#1f5d34;
      --panel:rgba(255,255,255,0.03);
      --txt:#fff;
    }
    html,body{margin:0;height:100%;background:var(--bg);font-family:Inter,system-ui,Arial;color:var(--txt)}
    .root{min-height:100vh;display:flex;align-items:center;justify-content:center}
    .game{width:100%;max-width:900px;display:flex;flex-direction:column;align-items:center;gap:12px}
    h1{font-family:Orbitron,system-ui;font-size:24px;margin:8px 0;text-align:center}
    .panel{width:100%;background:var(--panel);border-radius:12px;padding:12px 16px;}
    .muted{color:rgba(255,255,255,0.82);font-size:16px;text-align:center}
    .controls{display:flex;justify-content:center;margin-top:8px}
    .btn{background:var(--f1-yellow);border:0;padding:10px 18px;border-radius:10px;font-size:15px;font-weight:700;color:#222;cursor:pointer;}
    .lights{display:flex;gap:10px;justify-content:center;margin-top:12px;}
    .light{width:44px;height:44px;border-radius:50%;background:#222;border:3px solid rgba(255,255,255,0.04);display:flex;align-items:center;justify-content:center;box-shadow:0 6px 16px rgba(0,0,0,0.6);}
    .light.red{background:radial-gradient(circle at 35% 35%,#ff6b6b,#c93030);}
    .light.green{background:radial-gradient(circle at 35% 35%,#6bff6b,#24a024);}
    .status{margin-top:6px;text-align:center;font-weight:700;background:rgba(255,255,255,0.02);padding:8px 10px;border-radius:10px;}
    .svg-wrap{width:100%;max-width:750px;height:calc(min(70vh,700px));display:flex;align-items:center;justify-content:center;}
    svg{width:100%;height:100%;}
    .confetti{position:absolute;left:0;right:0;top:0;bottom:0;pointer-events:none;z-index:30}
    .confetti .piece{position:absolute;width:12px;height:20px;border-radius:6px;opacity:0;transform:translateY(-15vh);animation:confetti 1300ms linear forwards}
    @keyframes confetti{to{transform:translateY(110vh) rotate(720deg);opacity:1}}
    .boom{position:absolute;width:170px;height:170px;border-radius:50%;left:50%;top:50%;transform:translate(-50%,-50%) scale(0);opacity:0;pointer-events:none;z-index:50}
    .boom.show{animation:boom 700ms ease-out forwards}
    @keyframes boom{0%{transform:translate(-50%,-50%) scale(0);opacity:1}60%{transform:translate(-50%,-50%) scale(1.06);opacity:1}100%{transform:translate(-50%,-50%) scale(1.6);opacity:0}}
    .modal{position:fixed;inset:0;background:rgba(0,0,0,0.7);display:flex;align-items:center;justify-content:center;z-index:100}
    .card{background:#080f24;padding:18px 18px 10px 18px;border-radius:12px;text-align:center;max-width:92%}
    .big{font-family:Orbitron;font-size:22px;margin-bottom:10px;}
    .retry,.close{background:#fff;border:0;padding:10px 16px;border-radius:10px;font-weight:800;cursor:pointer;margin-top:6px;}
    .close{background:var(--f1-yellow);}
    @media (max-width:430px){ .light{width:36px;height:36px} .btn{padding:8px 12px} .big{font-size:18px}}
  </style>
</head>
<body>
<div class="root">
  <div class="game" role="application" aria-label="Dory Lights Out F1 Game">
    <h1>🏁 Dory’s F1 Lights-Out Birthday Challenge!</h1>
    <div class="panel">
      <div class="muted">Tap <strong>START RACE</strong>. When <strong>ALL</strong> lights turn green, tap the track <strong>within 600ms</strong> for a perfect start! Slow tap (over 3s) and... boom!</div>
      <div class="controls"><button id="startBtn" class="btn">START RACE</button></div>
      <div class="lights" id="lightsContainer" style="margin-top:10px">
        <div class="light" id="L1"></div>
        <div class="light" id="L2"></div>
        <div class="light" id="L3"></div>
        <div class="light" id="L4"></div>
        <div class="light" id="L5"></div>
      </div>
      <div class="status" id="status">Waiting to start</div>
    </div>

    <div class="svg-wrap" id="svgWrap">
      <svg id="svg" viewBox="0 0 1200 800" aria-label="F1 Racetrack">
        <defs>
          <filter id="shadow" x="-60%" y="-60%" width="220%" height="220%">
            <feDropShadow dx="0" dy="7" stdDeviation="14" flood-color="#000" flood-opacity="0.62"/>
          </filter>
        </defs>
        <!-- Outer Track -->
        <rect x="60" y="60" width="1080" height="680" rx="54" fill="#222"/>
        <!-- Grass Interior -->
        <rect x="210" y="170" width="780" height="460" rx="36" fill="#1f5d34"/>

        <!-- Race Path / Circuit -->
        <path id="centerPath" d="
          M 320 300
          C 300 180, 960 180, 900 320
          S 960 620, 350 600
          Q 230 580, 320 300
        " fill="none" stroke="transparent" stroke-width="4"/>

        <!-- Dashed Midline -->
        <path d="
          M 370 340
          C 330 220, 910 220, 860 360
          S 930 570, 400 565
          Q 260 545, 370 340
        " stroke="#fff" stroke-width="12" stroke-dasharray="28 18" fill="none" opacity="0.88"/>

        <!-- Start Line -->
        <rect x="570" y="560" width="70" height="10" fill="#fff" rx="4" />

        <!-- Dory’s F1 Car -->
        <g id="playerGroup" transform="translate(600,560)">
          <g id="playerCar" filter="url(#shadow)">
            <!-- F1 Car Shape -->
            <rect x="-34" y="-22" width="68" height="44" rx="13" fill="#1463a0" stroke="#ffd400" stroke-width="5"/>
            <rect x="-6" y="-18" width="12" height="36" rx="4" fill="#ffd400"/>
            <rect x="-30" y="-3" width="8" height="9" rx="2" fill="#ffd400"/>
            <rect x="22" y="-3" width="8" height="9" rx="2" fill="#ffd400"/>
            <!-- Wheels -->
            <rect x="-41" y="-16" width="8" height="32" rx="4" fill="#383838"/>
            <rect x="34" y="-16" width="8" height="32" rx="4" fill="#383838"/>
            <!-- DORY Label -->
            <text x="0" y="6" font-family="Orbitron,system-ui" font-size="20" fill="#fff" font-weight="800" text-anchor="middle">DORY</text>
          </g>
        </g>

        <!-- Dora’s Donkey Car -->
        <g id="oppGroup" transform="translate(560,560)">
          <g id="oppCar" filter="url(#shadow)">
            <!-- Donkey face/body (car-like) -->
            <rect x="-32" y="-14" width="64" height="32" rx="12" fill="#8d7435" stroke="#725e34" stroke-width="4"/>
            <!-- Ears -->
            <rect x="-27" y="-28" width="8" height="18" rx="3" fill="#725e34" transform="rotate(-18 -30 -23)"/>
            <rect x="19" y="-28" width="8" height="18" rx="3" fill="#725e34" transform="rotate(18 23 -23)"/>
            <!-- Snout -->
            <rect x="-10" y="-8" width="20" height="16" rx="4" fill="#be9c68"/>
            <text x="0" y="10" font-family="system-ui" font-size="16" fill="#fff" font-weight="700" text-anchor="middle">DORA</text>
          </g>
        </g>
      </svg>
    </div>

    <div id="confetti" class="confetti" aria-hidden="true"></div>
    <div id="boom" class="boom" style="background:radial-gradient(circle,#fff69c 0%, #ff6644 40%, rgba(255,0,0,0) 60%);" aria-hidden="true"></div>
  </div>
</div>

<!-- Sounds: F1 effects -->
<audio id="snd_engine" preload="auto" src="https://cdn.pixabay.com/audio/2022/03/15/audio_10bb8d43ad.mp3"></audio>
<audio id="snd_siren" preload="auto" src="https://www.soundjay.com/misc/sounds/air-raid-siren-01.mp3"></audio>
<audio id="snd_zoom" preload="auto" src="https://cdn.pixabay.com/audio/2022/03/15/audio_10bb8d43ad.mp3"></audio>
<audio id="snd_cheer" preload="auto" src="https://www.soundjay.com/human/sounds/applause-8.mp3"></audio>
<audio id="snd_boom" preload="auto" src="https://www.soundjay.com/explosion/sounds/explosion-01.mp3"></audio>

<!-- Win Modal -->
<div id="finishModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">AND TAKING THE WIN... IT'S DORY 🏆</div>
    <div style="font-weight:800;font-size:18px;margin-bottom:8px;color:#ffe600">Happy Birthday Dory! 🎂</div>
    <div style="font-weight:700;margin-bottom:10px;">From your world’s fastest donkey, Dora 💫</div>
    <button id="closeFinish" class="close">Close</button>
  </div>
</div>
<!-- Fail Modal -->
<div id="failModal" class="modal" style="display:none">
  <div class="card">
    <div class="big">Oh no! Too slow 😭</div>
    <div style="margin:8px 0">Your F1 car exploded and Dora wins!</div>
    <button id="retryBtn" class="retry">Retry Race</button>
  </div>
</div>

<script>
(function(){
  const startBtn = document.getElementById('startBtn');
  const status = document.getElementById('status');
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
  let pathLen = 0, startPos = 0, playerPos = 0, oppPos = 0, waitingForTap = false, reactionStart = 0, tapped = false, timers = [], animFrame = null;
  const laneOffset = 40, lightDelay = 700;

  function clearTimers(){ timers.forEach(t=>clearTimeout(t)); timers=[]; }
  function cancelAnim(){ if(animFrame) cancelAnimationFrame(animFrame); animFrame=null; }

  function initPath(){
    pathLen = centerPath.getTotalLength();
    let best = 0, bestDiff = Infinity;
    for(let i=0;i<=100;i++){
      const p = centerPath.getPointAtLength((i/100)*pathLen);
      const diff = Math.abs(p.y - 560); if(diff < bestDiff){ bestDiff = diff; best = (i/100)*pathLen; }
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
    const nxn = nx / nlen, nyn = ny / nlen;
    const x = p.x + nxn * lateral;
    const y = p.y + nyn * lateral;
    groupEl.setAttribute('transform', `translate(${x},${y}) rotate(${ang})`);
  }

  function startLights(){
    clearTimers();
    status.textContent = 'Lights coming...';
    lights.forEach(l=>{ l.classList.remove('red'); l.classList.remove('green'); });
    lights.forEach((l,i)=>{ timers.push(setTimeout(()=> l.classList.add('red'), i * lightDelay)); });
    const afterRed = lights.length * lightDelay + 500 + Math.random()*400;
    timers.push(setTimeout(()=>{
      status.textContent = 'Ready...';
      lights.forEach((l,i)=>{
        timers.push(setTimeout(()=>{
          l.classList.remove('red');
          l.classList.add('green');
          if(i === lights.length - 1) {
            onAllGreen();
          }
        }, i * lightDelay));
      });
      try{ s_siren.currentTime = 0; s_siren.play(); }catch(e){}
    }, afterRed));
  }

  function onAllGreen(){
    reactionStart = performance.now();
    waitingForTap = true;
    tapped = false;
    status.textContent = '⚡ TAP the track NOW!';
    timers.push(setTimeout(()=>{ if(!tapped) explosionFail(); }, 3000));
  }

  function handleTap(e){
    if(!waitingForTap) return;
    if(tapped) return;
    tapped = true; waitingForTap = false;
    const now = performance.now(); const dt = now - reactionStart;
    if(dt <= 600){
      playerStart(true);
    } else if(dt <= 3000){
      playerStart(false);
    }
  }

  function explosionFail(){
    waitingForTap = false; tapped = true;
    status.textContent = "Too slow! BOOM!";
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
    driveOpponentWin();
    timers.push(setTimeout(()=> { failModal.style.display = 'flex'; }, 900));
  }

  function driveOpponentWin(){
    const start = oppPos, end = oppPos + pathLen * 1.12, dur = 1500, t0 = performance.now();
    function step(t){
      const p = Math.min(1, (t - t0)/dur);
      oppPos = start + (end - start) * easeOutCubic(p);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(p < 1) requestAnimationFrame(step);
    }
    requestAnimationFrame(step);
  }

  function playerStart(isPerfect){
    status.textContent = isPerfect ? 'PERFECT START! Dory rockets away!' : 'Nice, Dory takes the lead!';
    try{ s_engine.currentTime = 0; s_engine.play(); }catch(e){}
    timers.push(setTimeout(()=>{ try{ s_zoom.currentTime = 0; s_zoom.play(); }catch(e){} }, 320));
    timers.push(setTimeout(()=>{ try{ s_cheer.currentTime = 0; s_cheer.play(); }catch(e){} }, 1000));
    const pStart = playerPos, oStart = oppPos, pEnd = pStart + pathLen * 1.13, oEnd = oStart + pathLen * 0.7, pDur = isPerfect ? 1800 : 2500, oDur = isPerfect ? 2700 : 3600, t0 = performance.now();
    cancelAnim();
    function step(t){
      const tp = Math.min(1, (t - t0) / pDur), to = Math.min(1, (t - t0) / oDur);
      playerPos = pStart + (pEnd - pStart) * easeOutCubic(tp);
      oppPos = oStart + (oEnd - oStart) * easeInCubic(to);
      placeAt(playerGroup, playerPos, laneOffset);
      placeAt(oppGroup, oppPos, -laneOffset);
      if(tp < 1 || to < 1){
        animFrame = requestAnimationFrame(step);
      } else {
        launchConfetti();
        timers.push(setTimeout(()=>{ finishModal.style.display = 'flex'; }, 430));
      }
    }
    animFrame = requestAnimationFrame(step);
  }

  function launchConfetti(){
    confetti.innerHTML = '';
    const colors = ['#ffd400','#1463a0','#ff3b3b','#7aef54','#ff7bd4'];
    for(let i=0;i<60;i++){
      const el = document.createElement('div');
      el.className = 'piece';
      el.style.left = (Math.random()*100)+'%';
      el.style.top = (Math.random()*10 - 5)+'%';
      el.style.background = colors[Math.floor(Math.random()*colors.length)];
      el.style.animationDuration = (900 + Math.random()*700)+'ms';
      confetti.appendChild(el);
    }
  }
  function easeOutCubic(t){ return 1 - Math.pow(1 - t, 3); }
  function easeInCubic(t){ return t*t*t; }
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
    initPath();
  }

  window.addEventListener('load', ()=>{
    initPath();
    // unlock audio on first touch/click (needed for mobiles)
    function unlockAudio(){
      try{ s_engine.play().then(()=>{ s_engine.pause(); s_engine.currentTime = 0; }).catch(()=>{}); }catch(e){}
      document.removeEventListener('touchstart', unlockAudio);
      document.removeEventListener('click', unlockAudio);
    }
    document.addEventListener('touchstart', unlockAudio, {passive:true});
    document.addEventListener('click', unlockAudio);
  });
  startBtn.addEventListener('click', ()=>{
    resetAll();
    startBtn.style.display = 'none';
    setTimeout(()=> startLights(), 180);
  });
  const svgWrap = document.getElementById('svgWrap');
  svgWrap.addEventListener('touchstart', (e)=>{ e.preventDefault(); handleTap(e); }, {passive:false});
  svgWrap.addEventListener('mousedown', ()=>{ handleTap(); });
  retryBtn.addEventListener('click', ()=>{ resetAll(); startBtn.style.display = 'inline-block'; failModal.style.display = 'none'; });
  closeFinish.addEventListener('click', ()=>{ finishModal.style.display = 'none'; resetAll(); startBtn.style.display = 'inline-block'; });
  window.addEventListener('resize', ()=>{ initPath(); });
  window.doryReset = resetAll;
})();
</script>
</body>
</html>
