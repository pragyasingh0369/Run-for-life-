<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>RUN FOR LIFE</title>
<style>
* { margin:0; padding:0; box-sizing:border-box; }
body { background:#000; overflow:hidden; font-family:'Courier New',monospace; touch-action:none; }
canvas { display:block; }

#ui { position:fixed; top:0; left:0; width:100%; height:100%; pointer-events:none; z-index:10; }

#score  { position:fixed; top:12px; left:14px; color:#00ffcc; font-size:20px; font-weight:bold; text-shadow:0 0 10px #00ffcc; }
#coins  { position:fixed; top:12px; right:14px; color:#00e5ff; font-size:18px; font-weight:bold; text-shadow:0 0 8px #00e5ff; }
#missions { position:fixed; top:12px; left:50%; transform:translateX(-50%); color:#88ffcc; font-size:11px; white-space:nowrap; text-shadow:0 0 6px #00ff88; background:rgba(0,0,0,0.5); padding:4px 12px; border-radius:8px; }

#pups { position:fixed; bottom:80px; left:12px; display:flex; flex-direction:column; gap:4px; }
.pup { background:rgba(0,30,15,0.85); border:1px solid #00ff88; border-radius:6px; padding:3px 10px; color:#00ffcc; font-size:11px; font-weight:bold; }

#shieldHUD { position:fixed; top:40px; left:14px; color:#ffcc00; font-size:12px; font-weight:bold; opacity:0; transition:opacity .3s; text-shadow:0 0 8px #ffcc00; }
#beastHUD  { position:fixed; bottom:140px; left:50%; transform:translateX(-50%); color:#ff3300; font-size:14px; font-weight:bold; opacity:0; text-shadow:0 0 12px #ff3300; letter-spacing:2px; }

#rainHUD { position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,15,35,0); transition:background 2s; pointer-events:none; }
#rainHUD.on { background:rgba(0,15,35,0.38); }
#rainMsg { position:fixed; top:40%; left:50%; transform:translate(-50%,-50%); color:#00ccff; font-size:clamp(20px,6vw,44px); font-weight:bold; text-shadow:0 0 30px #00ccff; opacity:0; transition:opacity .4s; pointer-events:none; letter-spacing:4px; }

#flash { position:fixed; inset:0; background:rgba(255,40,0,.22); opacity:0; pointer-events:none; transition:opacity .12s; }

/* Controls */
#ctrlWrap { position:fixed; bottom:14px; left:50%; transform:translateX(-50%); display:flex; gap:12px; pointer-events:all; z-index:20; }
.cb { width:54px; height:54px; border-radius:50%; background:rgba(0,255,150,.12); border:2px solid rgba(0,255,150,.4); color:#00ffcc; font-size:20px; cursor:pointer; display:flex; align-items:center; justify-content:center; touch-action:manipulation; -webkit-tap-highlight-color:transparent; }
.cb:active { background:rgba(0,255,150,.3); }

#pauseBtn { position:fixed; top:10px; right:110px; background:rgba(0,0,0,.6); border:1px solid rgba(0,255,150,.4); border-radius:8px; padding:5px 12px; color:#00ffcc; font-size:12px; cursor:pointer; pointer-events:all; z-index:20; font-family:'Courier New',monospace; }

/* START */
#startScreen {
  position:fixed; inset:0;
  background:radial-gradient(ellipse at 50% 25%, #002d18 0%, #000d06 75%);
  display:flex; flex-direction:column; align-items:center; justify-content:center;
  z-index:100;
}
.logo { font-size:clamp(48px,12vw,90px); font-weight:900; color:#00ffcc;
  text-shadow:0 0 30px #00ffcc,0 0 70px #00ff88; letter-spacing:6px;
  font-family:Impact,sans-serif; line-height:1; }
.logo-sub { color:#33cc77; font-size:clamp(9px,2vw,13px); letter-spacing:4px; margin-bottom:30px; opacity:.8; }
.logo-icon { font-size:60px; margin-bottom:6px; }

#charRow { display:flex; gap:12px; margin-bottom:26px; flex-wrap:wrap; justify-content:center; padding:0 10px; }
.cc { background:rgba(0,255,150,.05); border:2px solid rgba(0,255,150,.25); border-radius:14px;
  padding:12px 14px; cursor:pointer; text-align:center; transition:all .22s; min-width:90px; }
.cc:hover { border-color:#00ffcc; transform:translateY(-3px); }
.cc.sel { border-color:#00ffcc; background:rgba(0,255,180,.18); box-shadow:0 0 18px rgba(0,255,170,.35); }
.cc .av { font-size:32px; margin-bottom:4px; }
.cc .cn { color:#00ffcc; font-size:13px; font-weight:bold; }
.cc .ca { color:#66ffaa; font-size:9px; margin-top:3px; }

#startBtn { background:linear-gradient(135deg,#00ffcc,#00bb88); color:#000; border:none;
  padding:14px 56px; font-size:19px; border-radius:40px; cursor:pointer; font-weight:900;
  letter-spacing:3px; box-shadow:0 0 35px rgba(0,255,180,.6); transition:all .2s;
  font-family:'Courier New',monospace; }
#startBtn:hover { transform:scale(1.06); box-shadow:0 0 55px rgba(0,255,180,.8); }
.hint { color:rgba(255,255,255,.25); font-size:10px; letter-spacing:2px; margin-top:16px; }

/* PAUSE */
#pauseScreen { position:fixed; inset:0; background:rgba(0,0,0,.82); display:none; flex-direction:column;
  align-items:center; justify-content:center; z-index:80; }
#pauseScreen h2 { color:#00ffcc; font-size:48px; text-shadow:0 0 30px #00ffcc; margin-bottom:24px; font-family:Impact,sans-serif; letter-spacing:4px; }

/* GAME OVER */
#goScreen { position:fixed; inset:0; background:rgba(0,0,0,.88); display:none;
  flex-direction:column; align-items:center; justify-content:center; z-index:90; }
#goScreen h1 { color:#ff2222; font-size:clamp(36px,10vw,72px); text-shadow:0 0 40px #ff0000;
  font-family:Impact,sans-serif; letter-spacing:4px; margin-bottom:14px;
  animation:shake .5s ease; }
@keyframes shake { 0%,100%{transform:translateX(0)} 25%{transform:translateX(-10px)} 75%{transform:translateX(10px)} }
.statGrid { display:grid; grid-template-columns:1fr 1fr; gap:8px 24px; margin:10px 0 20px; }
.stat { background:rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1); border-radius:10px; padding:10px 20px; text-align:center; }
.stat .v { color:#00ffcc; font-size:24px; font-weight:bold; }
.stat .l { color:#555; font-size:10px; letter-spacing:2px; margin-top:2px; }
.mtext { color:#44ff88; font-size:12px; margin-bottom:14px; letter-spacing:1px; text-align:center; padding:0 20px; }

button.btn { background:linear-gradient(135deg,#00ffcc,#00bb88); color:#000; border:none;
  padding:12px 34px; font-size:15px; border-radius:30px; cursor:pointer; font-weight:900;
  font-family:'Courier New',monospace; letter-spacing:2px; margin:5px; transition:all .2s; }
button.btn:hover { transform:scale(1.05); }
button.btn2 { background:rgba(255,255,255,.08); border:1px solid rgba(255,255,255,.2); color:#fff; }
</style>
</head>
<body>

<canvas id="c"></canvas>

<!-- Rain overlay -->
<div id="rainHUD"></div>
<div id="rainMsg">⚡ RAIN MODE ⚡</div>
<div id="flash"></div>

<!-- HUD -->
<div id="score">🏃 0m</div>
<div id="coins">💎 0</div>
<div id="missions"></div>
<div id="pups"></div>
<div id="shieldHUD">🛡 SHIELD ACTIVE</div>
<div id="beastHUD">👹 SHADOW BEAST INCOMING!</div>

<button id="pauseBtn">⏸ PAUSE</button>

<!-- Touch controls -->
<div id="ctrlWrap">
  <div class="cb" id="L">◀</div>
  <div class="cb" id="J">▲</div>
  <div class="cb" id="S">▼</div>
  <div class="cb" id="R">▶</div>
</div>

<!-- START SCREEN -->
<div id="startScreen">
  <div class="logo-icon">📖💎</div>
  <div class="logo">RUN FOR LIFE</div>
  <div class="logo-sub">THE FOREST NEVER LETS YOU ESCAPE</div>
  <div id="charRow">
    <div class="cc sel" data-i="0"><div class="av">🧑</div><div class="cn">ERIC</div><div class="ca">⚡ Better Reactions</div></div>
    <div class="cc"     data-i="1"><div class="av">👱‍♀️</div><div class="cn">REGINA</div><div class="ca">💎 Double Coins</div></div>
    <div class="cc"     data-i="2"><div class="av">👧</div><div class="cn">CATY</div><div class="ca">🐢 Slower Speedup</div></div>
    <div class="cc"     data-i="3"><div class="av">🧑‍🦳</div><div class="cn">NICK</div><div class="ca">🛡 One Free Hit</div></div>
  </div>
  <button id="startBtn">▶ START RUNNING</button>
  <div class="hint">ARROW KEYS · WASD · SWIPE · BUTTONS</div>
</div>

<!-- PAUSE -->
<div id="pauseScreen">
  <h2>PAUSED</h2>
  <button class="btn" onclick="resume()">▶ RESUME</button>
  <button class="btn btn2" onclick="toMenu()">🏠 MENU</button>
</div>

<!-- GAME OVER -->
<div id="goScreen">
  <h1>💀 YOU DIED</h1>
  <div class="statGrid">
    <div class="stat"><div class="v" id="goDist">0m</div><div class="l">DISTANCE</div></div>
    <div class="stat"><div class="v" id="goCoins">0</div><div class="l">COINS</div></div>
    <div class="stat"><div class="v" id="goTime">0s</div><div class="l">TIME</div></div>
    <div class="stat"><div class="v" id="goJumps">0</div><div class="l">JUMPS</div></div>
  </div>
  <div class="mtext" id="mtext"></div>
  <button class="btn" onclick="startGame()">🔄 RUN AGAIN</button>
  <button class="btn btn2" onclick="toMenu()">🏠 MENU</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
// ═══════════════════════════════════════════════
//  RUN FOR LIFE  –  Full Working Game
// ═══════════════════════════════════════════════

const canvas = document.getElementById('c');
const W = () => window.innerWidth, H = () => window.innerHeight;

// Renderer
const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
renderer.setSize(W(), H());
renderer.shadowMap.enabled = true;

// Scene / Camera
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x000d06);
scene.fog = new THREE.FogExp2(0x000e06, 0.04);

const cam = new THREE.PerspectiveCamera(68, W()/H(), 0.1, 250);
cam.position.set(0, 4.2, 8.5);
cam.lookAt(0, 1.2, 0);

window.addEventListener('resize', () => {
  renderer.setSize(W(), H());
  cam.aspect = W()/H();
  cam.updateProjectionMatrix();
});

// ── LIGHTS ──────────────────────────────────
const aLight = new THREE.AmbientLight(0x112211, 1.0);
scene.add(aLight);

const dLight = new THREE.DirectionalLight(0x44ff99, 0.8);
dLight.position.set(4, 18, 6);
dLight.castShadow = true;
scene.add(dLight);

const pLight = new THREE.PointLight(0x00ffcc, 1.5, 14);
pLight.position.set(0, 5, 6);
scene.add(pLight);

const lLight = new THREE.PointLight(0xaaccff, 0, 200); // lightning
lLight.position.set(0, 50, 0);
scene.add(lLight);

// ── GROUND ──────────────────────────────────
const LANE = [-2.4, 0, 2.4];
const gMat = new THREE.MeshLambertMaterial({ color: 0x081508 });
const tiles = [];
for (let i = 0; i < 10; i++) {
  const t = new THREE.Mesh(new THREE.PlaneGeometry(14, 28), gMat);
  t.rotation.x = -Math.PI/2;
  t.position.set(0, 0, -i * 28 + 20);
  t.receiveShadow = true;
  scene.add(t);
  tiles.push(t);
}

// Side path lines
const lineMat = new THREE.MeshLambertMaterial({ color: 0x1a3a1a });
[-3.6, -1.2, 1.2, 3.6].forEach(x => {
  const line = new THREE.Mesh(new THREE.PlaneGeometry(0.06, 500), lineMat);
  line.rotation.x = -Math.PI/2;
  line.position.set(x, 0.01, -200);
  scene.add(line);
});

// ── FOREST TREES ────────────────────────────
function makeTree(x, z) {
  const h = 8 + Math.random() * 10;
  const trunkM = new THREE.MeshLambertMaterial({ color: 0x150900 });
  const leafM  = new THREE.MeshLambertMaterial({ color: new THREE.Color().setHSL(0.33, 0.55, 0.04 + Math.random()*0.06) });
  const trunk = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.28, h, 7), trunkM);
  trunk.position.set(x, h/2, z);
  scene.add(trunk);
  const fh = 3.5 + Math.random()*3;
  const top = new THREE.Mesh(new THREE.ConeGeometry(1.6+Math.random()*0.8, fh, 7), leafM);
  top.position.set(x, h + fh/2 - 0.5, z);
  scene.add(top);
}
for (let z = 20; z > -350; z -= 7) {
  makeTree(-7.5 + (Math.random()-0.5)*2, z);
  makeTree( 7.5 + (Math.random()-0.5)*2, z);
}

// ── CHARACTERS ──────────────────────────────
const CHARS = [
  { name:'Eric',   col:0x1a3a6a, skin:0xffe0bd, hair:0x4a2800, ability:'reaction'  },
  { name:'Regina', col:0xcc3377, skin:0xffe0bd, hair:0xddcc00, ability:'coins'      },
  { name:'Caty',   col:0x3366cc, skin:0xffd5b8, hair:0x884400, ability:'slowspeed' },
  { name:'Nick',   col:0x2a3a44, skin:0x3d2010, hair:0xdddddd, ability:'shield'    },
];
let selChar = 0, player = null;

function buildPlayer(ci) {
  if (player) scene.remove(player);
  const g = new THREE.Group();
  const c = CHARS[ci];
  const bM = new THREE.MeshLambertMaterial({ color: c.col });
  const hM = new THREE.MeshLambertMaterial({ color: c.skin });
  const lM = new THREE.MeshLambertMaterial({ color: 0x111111 });
  const hairM = new THREE.MeshLambertMaterial({ color: c.hair });

  // Torso
  const torso = new THREE.Mesh(new THREE.BoxGeometry(0.56, 0.82, 0.38), bM);
  torso.position.y = 0.91; torso.castShadow = true; g.add(torso);

  // Head
  const head = new THREE.Mesh(new THREE.SphereGeometry(0.26, 10, 8), hM);
  head.position.y = 1.60; g.add(head);

  // Hair (flattened sphere on top)
  const hair = new THREE.Mesh(new THREE.SphereGeometry(0.27, 8, 6), hairM);
  hair.scale.y = 0.45; hair.position.set(0, 1.77, -0.03); g.add(hair);

  // Eyes
  const eyeM = new THREE.MeshLambertMaterial({ color: 0x001100 });
  const eG = new THREE.SphereGeometry(0.052, 5, 5);
  const eL = new THREE.Mesh(eG, eyeM); eL.position.set(-0.10, 1.63, 0.25); g.add(eL);
  const eR = eL.clone(); eR.position.x = 0.10; g.add(eR);

  // Arms (boxes)
  [-0.37, 0.37].forEach(x => {
    const arm = new THREE.Mesh(new THREE.BoxGeometry(0.16, 0.62, 0.16), bM);
    arm.position.set(x, 0.88, 0); g.add(arm);
  });

  // Legs
  [-0.14, 0.14].forEach(x => {
    const leg = new THREE.Mesh(new THREE.BoxGeometry(0.2, 0.58, 0.2), lM);
    leg.position.set(x, 0.26, 0); g.add(leg);
    const shoe = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.1, 0.30), new THREE.MeshLambertMaterial({ color: 0x222222 }));
    shoe.position.set(x, -0.05, 0.06); g.add(shoe);
  });

  g.position.set(0, 0, 4);
  g.castShadow = true;
  scene.add(g);
  return g;
}

// ── MATERIALS (reused) ──────────────────────
const M = {
  vine:   new THREE.MeshLambertMaterial({ color: 0x1a5500 }),
  mud:    new THREE.MeshLambertMaterial({ color: 0x2a1000 }),
  bolt:   new THREE.MeshLambertMaterial({ color: 0xffff00, emissive: 0xffaa00 }),
  trunk:  new THREE.MeshLambertMaterial({ color: 0x1e0d00 }),
  hole:   new THREE.MeshLambertMaterial({ color: 0x000000, side: THREE.DoubleSide }),
  creat:  new THREE.MeshLambertMaterial({ color: 0x1a3300, emissive: 0x001a00 }),
  coinM:  new THREE.MeshLambertMaterial({ color: 0x00e5ff, emissive: 0x009999 }),
  beastM: new THREE.MeshLambertMaterial({ color: 0x040404, emissive: 0x110000 }),
  eyeRed: new THREE.MeshLambertMaterial({ color: 0xff0000, emissive: 0xff0000 }),
  goril:  new THREE.MeshLambertMaterial({ color: 0x1a1200, emissive: 0x050500 }),
  rain:   new THREE.MeshLambertMaterial({ color: 0x99ddff, transparent: true, opacity: 0.45 }),
  leaf:   new THREE.MeshLambertMaterial({ color: 0x1a4400, side: THREE.DoubleSide, transparent: true, opacity: 0.8 }),
};

const coinGeo = new THREE.TorusGeometry(0.22, 0.07, 8, 20);

// ── OBSTACLE SPAWN ──────────────────────────
function spawnObs() {
  const types = ['vine','mud','bolt','tree','hole','creat'];
  const type  = types[Math.floor(Math.random() * types.length)];
  const li    = Math.floor(Math.random() * 3);
  const g     = new THREE.Group();
  let slideable = false, ground = false;

  if (type === 'vine') {
    const bar = new THREE.Mesh(new THREE.BoxGeometry(2.0, 0.16, 0.16), M.vine);
    bar.position.y = 1.1; g.add(bar);
    for (let k = 0; k < 5; k++) {
      const ten = new THREE.Mesh(new THREE.CylinderGeometry(0.03,0.03, 0.7+Math.random()*0.5, 5), M.vine);
      ten.position.set((Math.random()-0.5)*1.6, 0.55+Math.random()*0.3, 0); g.add(ten);
    }
    slideable = true;
  } else if (type === 'mud') {
    const pool = new THREE.Mesh(new THREE.CylinderGeometry(1.1,1.1,0.12,14), M.mud);
    pool.position.y = 0.06; g.add(pool); ground = true;
  } else if (type === 'bolt') {
    const pillar = new THREE.Mesh(new THREE.CylinderGeometry(0.09,0.09,3.6,6), M.bolt);
    pillar.position.y = 1.8; g.add(pillar);
    const gl = new THREE.PointLight(0xffff00, 2, 5); gl.position.y = 1.5; g.add(gl);
  } else if (type === 'tree') {
    const t = new THREE.Mesh(new THREE.CylinderGeometry(0.28,0.38,3,8), M.trunk);
    t.rotation.z = Math.PI * 0.44; t.position.set(-0.55, 0.55, 0); g.add(t);
  } else if (type === 'hole') {
    const h = new THREE.Mesh(new THREE.CircleGeometry(1.0,16), M.hole);
    h.rotation.x = -Math.PI/2; h.position.y = 0.02; g.add(h);
    const rim = new THREE.Mesh(new THREE.TorusGeometry(1.0,0.08,6,16), new THREE.MeshLambertMaterial({color:0x1a0f00}));
    rim.rotation.x = -Math.PI/2; rim.position.y = 0.04; g.add(rim);
    ground = true;
  } else { // creat
    const b = new THREE.Mesh(new THREE.SphereGeometry(0.55,9,8), M.creat);
    b.position.y = 0.6; g.add(b);
    const eL2 = new THREE.Mesh(new THREE.SphereGeometry(0.1,5,5), M.eyeRed);
    eL2.position.set(-0.2,0.8,0.48); g.add(eL2);
    const eR2 = eL2.clone(); eR2.position.x = 0.2; g.add(eR2);
  }

  g.position.set(LANE[li], 0, -80 - Math.random()*20);
  g.userData = { li, slideable, ground };
  scene.add(g); obs.push(g);
}

// ── COIN SPAWN ──────────────────────────────
function spawnCoins() {
  const li = Math.floor(Math.random()*3);
  const cnt = 4 + Math.floor(Math.random()*5);
  for (let i = 0; i < cnt; i++) {
    const m = new THREE.Mesh(coinGeo, M.coinM);
    m.rotation.x = Math.PI/2;
    m.position.set(LANE[li], 1.05, -75 - i*3 - Math.random()*15);
    m.userData.collected = false;
    scene.add(m); coinMeshes.push(m);
  }
}

// ── POWERUP SPAWN ────────────────────────────
const PUPS = [
  { id:'shield',  col:0xffcc00, label:'🛡 SHIELD',        dur:6000 },
  { id:'boost',   col:0xff6600, label:'⚡ SPEED BOOST',   dur:4000 },
  { id:'magnet',  col:0xaa00ff, label:'🧲 MAGNET',        dur:7000 },
  { id:'ghost',   col:0x00ffff, label:'👻 GHOST MODE',    dur:4500 },
  { id:'rain',    col:0x0055ff, label:'🌧 RAIN CONTROL',  dur:9000 },
];
function spawnPup() {
  const p = PUPS[Math.floor(Math.random()*PUPS.length)];
  const m = new THREE.Mesh(new THREE.OctahedronGeometry(0.42,0), new THREE.MeshLambertMaterial({color:p.col,emissive:p.col}));
  m.position.set(LANE[Math.floor(Math.random()*3)], 1.15, -90 - Math.random()*20);
  m.userData = { id: p.id, collected: false };
  const gl = new THREE.PointLight(p.col, 2.5, 6); m.add(gl);
  scene.add(m); pupMeshes.push(m);
}

// ── SHADOW BEAST ────────────────────────────
const beastGrp = new THREE.Group();
const beastBody = new THREE.Mesh(new THREE.SphereGeometry(1.1,12,10), M.beastM);
const bEL = new THREE.Mesh(new THREE.SphereGeometry(0.18,7,7), M.eyeRed);
bEL.position.set(-0.38, 0.3, 1.0);
const bER = bEL.clone(); bER.position.x = 0.38;
beastBody.add(bEL); beastBody.add(bER);
beastGrp.add(beastBody);
for (let i = 0; i < 4; i++) { // tentacle arms
  const ten = new THREE.Mesh(new THREE.CylinderGeometry(0.06,0.18,1.5,5), M.beastM);
  ten.position.set(Math.cos(i/4*Math.PI*2)*1.0, -0.6, Math.sin(i/4*Math.PI*2)*0.5);
  ten.rotation.z = (Math.random()-0.5)*0.8;
  beastGrp.add(ten);
}
const bGlow = new THREE.PointLight(0xff0000, 2, 16);
beastGrp.add(bGlow);
beastGrp.position.set(0, 1.5, 30);
beastGrp.visible = false;
scene.add(beastGrp);

// ── GORILLAS (4) ─────────────────────────────
const gorillas = [];
function makeGorilla(x, z) {
  const g = new THREE.Group();
  const body = new THREE.Mesh(new THREE.SphereGeometry(0.68, 10, 8), M.goril);
  body.position.y = 0.8; g.add(body);
  const head = new THREE.Mesh(new THREE.SphereGeometry(0.4, 9, 8), M.goril);
  head.position.y = 1.68; g.add(head);
  // arms (boxes instead of capsule)
  [-0.62, 0.62].forEach(ax => {
    const arm = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.7, 0.22), M.goril);
    arm.position.set(ax, 0.7, 0.25); g.add(arm);
    const hand = new THREE.Mesh(new THREE.SphereGeometry(0.18,7,6), M.goril);
    hand.position.set(ax, 0.32, 0.28); g.add(hand);
  });
  const eG2 = new THREE.SphereGeometry(0.09,6,6);
  const eL3 = new THREE.Mesh(eG2, M.eyeRed); eL3.position.set(-0.16,1.76,0.36); g.add(eL3);
  const eR3 = eL3.clone(); eR3.position.x = 0.16; g.add(eR3);
  g.position.set(x, 0, z);
  g.visible = false;
  scene.add(g); gorillas.push(g);
}
makeGorilla(-4, 25); makeGorilla(4, 30);
makeGorilla(-5, 38); makeGorilla(5, 44);

// ── RAIN PARTICLES ──────────────────────────
const rainMeshes = [];
for (let i = 0; i < 220; i++) {
  const m = new THREE.Mesh(new THREE.CylinderGeometry(0.012,0.012,0.55,4), M.rain);
  m.position.set((Math.random()-0.5)*22, Math.random()*17, (Math.random()-0.5)*35-8);
  m.userData.vy = -(0.28 + Math.random()*0.25);
  m.visible = false;
  scene.add(m); rainMeshes.push(m);
}

// ── LEAF PARTICLES ──────────────────────────
const leafMeshes = [];
for (let i = 0; i < 35; i++) {
  const l = new THREE.Mesh(new THREE.PlaneGeometry(0.18,0.12), M.leaf);
  l.position.set((Math.random()-0.5)*16, Math.random()*10, (Math.random()-0.5)*30-5);
  l.userData = { vy:-(0.02+Math.random()*0.04), vx:(Math.random()-0.5)*0.02, spin:(Math.random()-0.5)*0.06 };
  scene.add(l); leafMeshes.push(l);
}

// ═══════════════════════════════════════════════
//  GAME STATE
// ═══════════════════════════════════════════════
let gState = 'menu'; // menu | playing | gameover
let paused = false;
let score=0, coinCount=0, dist=0, jumps=0;
let speed=0.18, baseSpeed=0.18;
let lane=1, tX=0, pX=0;
let jumping=false, sliding=false, jumpV=0, pY=0;
let shield=false, ghost=false, magnet=false, boost=false;
let rainOn=false, rainT=0, rainI=0, lastRain=0;
let beastOn=false, gorilOn=false, nickHit=false;
let startMs=0, animT=0;
let activePupList = [];
const obs=[], coinMeshes=[], pupMeshes=[];
let spT=0, coT=0, puT=0;

const MISSIONS_DEF = [
  { label:'💎 Collect 50 coins',   done:()=>coinCount>=50  },
  { label:'⏱ Survive 2 minutes',  done:()=>((Date.now()-startMs)/1000)>=120 },
  { label:'↑ Jump 10 times',       done:()=>jumps>=10      },
];

// ── UI helpers ──────────────────────────────
function setHUD() {
  document.getElementById('score').textContent = '🏃 '+Math.floor(dist)+'m';
  document.getElementById('coins').textContent = '💎 '+coinCount;
  const ms = MISSIONS_DEF.map(m => m.done() ? '✅' : m.label).join('  ');
  document.getElementById('missions').textContent = ms;
}
function setPups() {
  document.getElementById('pups').innerHTML = activePupList.map(p=>`<div class="pup">${p}</div>`).join('');
}
function flashDmg() {
  const f=document.getElementById('flash'); f.style.opacity='1';
  setTimeout(()=>f.style.opacity='0',180);
}

// ── Activate powerup ────────────────────────
function activatePup(id) {
  const p = PUPS.find(x=>x.id===id);
  if (!p) return;
  if (id==='shield')  { shield=true;  document.getElementById('shieldHUD').style.opacity='1'; }
  if (id==='boost')   boost = true;
  if (id==='magnet')  magnet = true;
  if (id==='ghost')   ghost = true;
  if (id==='rain')    rainI = Math.max(0, rainI-0.4);
  activePupList.push(p.label); setPups();
  setTimeout(()=>{
    if (id==='shield') { shield=false; document.getElementById('shieldHUD').style.opacity='0'; }
    if (id==='boost')  boost=false;
    if (id==='magnet') magnet=false;
    if (id==='ghost')  ghost=false;
    activePupList = activePupList.filter(l=>l!==p.label); setPups();
  }, p.dur);
}

// ── Collision ───────────────────────────────
function hit(obj) {
  const dx = Math.abs(player.position.x - obj.position.x);
  const dz = Math.abs(player.position.z - obj.position.z);
  const margin = CHARS[selChar].ability==='reaction' ? 0.78 : 0.92;
  return dx < margin && dz < 2.0;
}

// ── Kill ────────────────────────────────────
function die() {
  gState = 'gameover';
  const t = Math.floor((Date.now()-startMs)/1000);
  document.getElementById('goDist').textContent  = Math.floor(dist)+'m';
  document.getElementById('goCoins').textContent = coinCount;
  document.getElementById('goTime').textContent  = t+'s';
  document.getElementById('goJumps').textContent = jumps;
  const done = MISSIONS_DEF.filter(m=>m.done()).map(m=>m.label);
  document.getElementById('mtext').textContent = done.length
    ? '🏆 Done: '+done.join(' | ')
    : 'No missions completed this run.';
  document.getElementById('goScreen').style.display = 'flex';
  rainMeshes.forEach(r=>r.visible=false);
  document.getElementById('rainHUD').classList.remove('on');
  lLight.intensity = 0;
}

// ── START ───────────────────────────────────
function startGame() {
  document.getElementById('startScreen').style.display = 'none';
  document.getElementById('goScreen').style.display    = 'none';
  document.getElementById('pauseScreen').style.display = 'none';
  gState='playing'; paused=false;
  document.getElementById('pauseBtn').textContent = '⏸ PAUSE';

  score=0; coinCount=0; dist=0; jumps=0;
  baseSpeed = CHARS[selChar].ability==='slowspeed' ? 0.14 : 0.18;
  speed = baseSpeed;
  lane=1; tX=LANE[1]; pX=LANE[1];
  jumping=false; sliding=false; jumpV=0; pY=0;
  shield=false; ghost=false; magnet=false; boost=false; nickHit=false;
  rainOn=false; rainT=0; rainI=0; lastRain=0;
  beastOn=false; gorilOn=false;
  activePupList=[]; setPups();
  startMs = Date.now(); animT=0; spT=0; coT=0; puT=0;

  document.getElementById('shieldHUD').style.opacity='0';
  document.getElementById('beastHUD').style.opacity='0';
  document.getElementById('rainHUD').classList.remove('on');
  document.getElementById('rainMsg').style.opacity='0';
  lLight.intensity=0;
  rainMeshes.forEach(r=>r.visible=false);

  obs.forEach(o=>scene.remove(o));         obs.length=0;
  coinMeshes.forEach(c=>scene.remove(c));  coinMeshes.length=0;
  pupMeshes.forEach(p=>scene.remove(p));   pupMeshes.length=0;
  gorillas.forEach(g=>g.visible=false);

  player = buildPlayer(selChar);
  if (selChar===3) { shield=true; document.getElementById('shieldHUD').style.opacity='1'; }
}

function toMenu() {
  paused=false;
  document.getElementById('pauseScreen').style.display='none';
  document.getElementById('goScreen').style.display='none';
  gState='menu';
  document.getElementById('startScreen').style.display='flex';
  rainMeshes.forEach(r=>r.visible=false);
  document.getElementById('rainHUD').classList.remove('on');
  lLight.intensity=0;
}

function resume() {
  paused=false;
  document.getElementById('pauseScreen').style.display='none';
  document.getElementById('pauseBtn').textContent='⏸ PAUSE';
}

// ── INPUT ────────────────────────────────────
function moveL()  { if (gState==='playing'&&!paused&&lane>0) { lane--; } }
function moveR()  { if (gState==='playing'&&!paused&&lane<2) { lane++; } }
function doJump() { if (gState==='playing'&&!paused&&!jumping) { jumping=true; jumpV=0.21; jumps++; } }
function doSlide(){ if (gState==='playing'&&!paused&&!sliding) { sliding=true; setTimeout(()=>sliding=false,720); } }
function doPause(){
  if (gState!=='playing') return;
  paused=!paused;
  document.getElementById('pauseBtn').textContent=paused?'▶ RESUME':'⏸ PAUSE';
  document.getElementById('pauseScreen').style.display=paused?'flex':'none';
}

document.addEventListener('keydown', e=>{
  switch(e.key){
    case 'ArrowLeft': case 'a': case 'A': e.preventDefault(); moveL(); break;
    case 'ArrowRight':case 'd': case 'D': e.preventDefault(); moveR(); break;
    case 'ArrowUp':  case 'w': case 'W': case ' ': e.preventDefault(); doJump(); break;
    case 'ArrowDown':case 's': case 'S': e.preventDefault(); doSlide(); break;
    case 'Escape':case 'p':case 'P': doPause(); break;
  }
});

document.getElementById('L').addEventListener('click', moveL);
document.getElementById('R').addEventListener('click', moveR);
document.getElementById('J').addEventListener('click', doJump);
document.getElementById('S').addEventListener('click', doSlide);
document.getElementById('pauseBtn').addEventListener('click', doPause);

// Swipe
let tx0=0,ty0=0;
document.addEventListener('touchstart',e=>{tx0=e.touches[0].clientX;ty0=e.touches[0].clientY;},{passive:true});
document.addEventListener('touchend',e=>{
  if(gState!=='playing'||paused)return;
  const dx=e.changedTouches[0].clientX-tx0, dy=e.changedTouches[0].clientY-ty0;
  if(Math.abs(dx)>Math.abs(dy)){if(dx<-28)moveL();else if(dx>28)moveR();}
  else{if(dy<-28)doJump();else if(dy>28)doSlide();}
});

// Char select
document.querySelectorAll('.cc').forEach(c=>{
  c.addEventListener('click',()=>{
    document.querySelectorAll('.cc').forEach(x=>x.classList.remove('sel'));
    c.classList.add('sel'); selChar=parseInt(c.dataset.i);
  });
});
document.getElementById('startBtn').addEventListener('click', startGame);

// ═══════════════════════════════════════════════
//  MAIN LOOP
// ═══════════════════════════════════════════════
let lastNow = 0;
function loop(now) {
  requestAnimationFrame(loop);

  const rawDt = Math.min((now - lastNow)/16.667, 3);
  lastNow = now;

  if (gState !== 'playing' || paused) {
    renderer.render(scene, cam); return;
  }

  animT += rawDt;
  const dt = rawDt;
  const elapsed = (Date.now()-startMs)/1000;
  const curSpd = speed * (boost ? 2 : 1);

  // Speed ramp
  const sg = CHARS[selChar].ability==='slowspeed' ? 0.000028 : 0.000052;
  speed = Math.min(speed + sg*dt, 0.60);
  dist += curSpd * dt * 2.5;

  // ── GROUND SCROLL ──
  tiles.forEach(t => {
    t.position.z += curSpd * dt * 2.5;
    if (t.position.z > 30) t.position.z -= 280;
  });

  // ── LEAVES ──
  leafMeshes.forEach(l => {
    l.position.y += l.userData.vy * dt;
    l.position.x += l.userData.vx * dt;
    l.position.z += curSpd * dt * 0.5;
    l.rotation.z += l.userData.spin * dt;
    if (l.position.y < -1) l.position.y = 12;
    if (l.position.z > 12) l.position.z = -35;
  });

  // ── SPAWNING ──
  spT += curSpd*dt;
  if (spT > 13+Math.random()*8) { spawnObs(); spT=0; }
  coT += curSpd*dt;
  if (coT > 9) { spawnCoins(); coT=0; }
  puT += curSpd*dt;
  if (puT > 52) { spawnPup(); puT=0; }

  // ── RAIN ──
  if (elapsed - lastRain >= 60 && !rainOn) {
    rainOn=true; rainT=0; lastRain=elapsed;
    document.getElementById('rainHUD').classList.add('on');
    const rm=document.getElementById('rainMsg');
    rm.style.opacity='1'; setTimeout(()=>rm.style.opacity='0',2200);
    rainMeshes.forEach(r=>r.visible=true);
  }
  if (rainOn) {
    rainT+=dt;
    rainI = Math.min(rainI+0.0012*dt, 0.55);
    if (Math.random()>0.986) {
      lLight.intensity=10+Math.random()*8;
      setTimeout(()=>lLight.intensity=0, 80+Math.random()*120);
    }
    rainMeshes.forEach(r=>{
      r.position.y += r.userData.vy * dt * (1+rainI*1.4);
      r.position.z += curSpd*dt;
      if(r.position.y<-2) r.position.y=17;
      if(r.position.z>12) r.position.z=-30;
    });
    if (rainT>22) {
      rainOn=false;
      document.getElementById('rainHUD').classList.remove('on');
      rainMeshes.forEach(r=>r.visible=false);
    }
  }

  // ── PLAYER ──
  tX = LANE[lane];
  pX += (tX - pX) * 0.17 * dt;
  player.position.x = pX;

  if (jumping) {
    pY += jumpV * dt;
    jumpV -= 0.013*dt;
    if (pY<=0) { pY=0; jumping=false; jumpV=0; }
  }
  player.scale.y = sliding ? 0.5 : 1;
  player.position.y = pY*3.2;
  if (!jumping&&!sliding) {
    player.position.y += Math.sin(animT*0.14)*0.05;
    player.rotation.z  = Math.sin(animT*0.10)*0.04;
  }
  pLight.position.x = player.position.x;

  // ── OBSTACLES ──
  for (let i=obs.length-1; i>=0; i--) {
    const o=obs[i];
    o.position.z += curSpd*dt*2.5;
    if (o.position.z > 14) { scene.remove(o); obs.splice(i,1); continue; }

    if (hit(o) && !ghost) {
      const canSlide  = o.userData.slideable && sliding;
      const canJump   = jumping && pY > 0.85;
      const canEscape = o.userData.ground && !jumping; // ground traps on flat ground
      if (!canSlide && !canJump) {
        if (shield) {
          shield=false;
          activePupList=activePupList.filter(l=>!l.includes('SHIELD')); setPups();
          document.getElementById('shieldHUD').style.opacity='0';
          flashDmg(); scene.remove(o); obs.splice(i,1);
        } else if (selChar===3 && !nickHit) {
          nickHit=true; flashDmg(); scene.remove(o); obs.splice(i,1);
        } else { die(); return; }
      }
    }
  }

  // ── COINS ──
  for (let i=coinMeshes.length-1; i>=0; i--) {
    const c=coinMeshes[i];
    c.position.z += curSpd*dt*2.5;
    c.rotation.y += 0.07*dt;
    if (c.position.z>14) { scene.remove(c); coinMeshes.splice(i,1); continue; }
    const dx=Math.abs(player.position.x-c.position.x);
    const dz=Math.abs(player.position.z-c.position.z);
    const range = magnet ? 4.2 : 1.2;
    if (dx<range && dz<range && !c.userData.collected) {
      c.userData.collected=true;
      coinCount += CHARS[selChar].ability==='coins' ? 2 : 1;
      scene.remove(c); coinMeshes.splice(i,1);
    }
  }

  // ── POWERUPS ──
  for (let i=pupMeshes.length-1; i>=0; i--) {
    const p=pupMeshes[i];
    p.position.z += curSpd*dt*2.5;
    p.rotation.y += 0.055*dt;
    p.position.y = 1.15+Math.sin(animT*0.08)*0.22;
    if (p.position.z>14) { scene.remove(p); pupMeshes.splice(i,1); continue; }
    const dx=Math.abs(player.position.x-p.position.x);
    const dz=Math.abs(player.position.z-p.position.z);
    if (dx<1.4 && dz<1.6 && !p.userData.collected) {
      p.userData.collected=true;
      activatePup(p.userData.id);
      scene.remove(p); pupMeshes.splice(i,1);
    }
  }

  // ── SHADOW BEAST ──
  if (elapsed>45 && Math.random()<0.0018 && !beastOn) {
    beastOn=true; beastGrp.visible=true;
    beastGrp.position.set(0, 1.5, 28);
    document.getElementById('beastHUD').style.opacity='1';
  }
  if (beastOn) {
    beastGrp.position.z -= curSpd*0.5*dt;
    beastGrp.position.x += (player.position.x - beastGrp.position.x)*0.012;
    beastBody.rotation.y += 0.03*dt;
    const bd=beastGrp.position.z - player.position.z;
    if (bd < 1.8) {
      if (shield) {
        shield=false; beastOn=false; beastGrp.visible=false;
        document.getElementById('beastHUD').style.opacity='0';
        document.getElementById('shieldHUD').style.opacity='0'; flashDmg();
      } else if (selChar===3&&!nickHit) {
        nickHit=true; beastOn=false; beastGrp.visible=false;
        document.getElementById('beastHUD').style.opacity='0'; flashDmg();
      } else { die(); return; }
    }
    if (beastGrp.position.z < -18) {
      beastOn=false; beastGrp.visible=false;
      document.getElementById('beastHUD').style.opacity='0';
    }
  }

  // ── GORILLAS ──
  if (dist > 250 && !gorilOn) {
    gorilOn=true; gorillas.forEach(g=>g.visible=true);
  }
  if (gorilOn) {
    gorillas.forEach((g,i)=>{
      g.position.z += curSpd*0.28*dt;
      g.position.y = Math.abs(Math.sin(animT*0.055+i*0.7))*0.3;
      if (g.position.z>14) g.position.z=-45-i*8;
    });
  }

  // ── CAMERA SHAKE ──
  const sh = rainOn ? rainI*0.12 : 0;
  cam.position.x = (Math.random()-0.5)*sh;
  cam.position.y = 4.2 + (Math.random()-0.5)*sh*0.4;

  setHUD();
  renderer.render(scene, cam);
}

requestAnimationFrame(loop);
</script>
</body>
</html>
