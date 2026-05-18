# NEON CIRCUIT RUNNER

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Neon Circuit Runner</title>
  <style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap');

  * {
  box-sizing: border-box;
  user-select: none;
  -webkit-touch-callout: none;
  }

  body, html {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  background-color: #000000;
  color: #39FF14;
  font-family: 'Share Tech Mono', monospace;
  overflow: hidden;
  touch-action: none; /* 모바일 제스처 방지 */
  cursor: crosshair; 
  }

  #gameCanvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  }

  #ui-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  pointer-events: none; 
  display: flex;
  flex-direction: column;
  }

  .neon-text {
  text-shadow: 0 0 5px #39FF14, 0 0 10px #39FF14, 0 0 20px #39FF14;
  }
  .hidden { display: none !important; }
  .flex-center { display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100%; width: 100%; }

  #boot-screen {
  padding: 20px;
  font-size: 1.2rem;
  line-height: 1.8;
  text-align: left;
  width: 100%;
  height: 100%;
  background: #000;
  }

  #title-screen h1 {
  font-size: 4rem;
  margin-bottom: 50px;
  text-align: center;
  animation: glitch 2s infinite;
  }

  @keyframes glitch {
  0% { text-shadow: 0 0 5px #39FF14, 2px 2px 0px rgba(57,255,20,0.5), -2px -2px 0px rgba(255,255,255,0.5); }
  5% { text-shadow: 0 0 5px #39FF14, -2px 2px 0px rgba(57,255,20,0.5), 2px -2px 0px rgba(255,255,255,0.5); transform: translate(-2px, 2px); }
  10% { text-shadow: 0 0 5px #39FF14, 2px -2px 0px rgba(57,255,20,0.5), -2px 2px 0px rgba(255,255,255,0.5); transform: translate(2px, -2px); }
  15% { transform: translate(0, 0); }
  100% { transform: translate(0, 0); }
  }

  .neon-btn {
  background: transparent;
  color: #39FF14;
  border: 2px solid #39FF14;
  padding: 15px 40px;
  font-size: 1.5rem;
  font-family: inherit;
  cursor: pointer;
  pointer-events: auto;
  text-shadow: 0 0 5px #39FF14;
  box-shadow: 0 0 10px #39FF14 inset, 0 0 10px #39FF14;
  transition: all 0.1s;
  outline: none;
  }

  .neon-btn:active {
  background: #39FF14;
  color: #000;
  box-shadow: 0 0 20px #39FF14 inset, 0 0 30px #39FF14;
  }

  #hud {
  position: absolute;
  top: 20px;
  left: 20px;
  right: 20px;
  display: flex;
  justify-content: space-between;
  font-size: 1.5rem;
  pointer-events: none;
  }

  #combo-display {
  position: absolute;
  top: 60px;
  left: 20px;
  font-size: 1.2rem;
  color: #fff;
  text-shadow: 0 0 10px #39FF14, 0 0 20px #39FF14;
  transition: transform 0.1s;
  }

  #fever-display {
  position: absolute;
  top: 100px;
  left: 20px;
  font-size: 1.2rem;
  color: #fff;
  animation: pulse 0.5s infinite;
  }

  @keyframes pulse {
  0% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.1); }
  100% { opacity: 1; transform: scale(1); }
  }

  #gameover-screen {
  background: rgba(0, 0, 0, 0.85);
  }

  #gameover-title {
  font-size: 4rem;
  margin-bottom: 20px;
  animation: flash 0.5s infinite alternate;
  }

  @keyframes flash {
  from { opacity: 1; text-shadow: 0 0 20px #39FF14, 0 0 40px #39FF14; }
  to { opacity: 0.2; text-shadow: none; }
  }

  .stats-box {
  text-align: center;
  margin-bottom: 40px;
  font-size: 1.5rem;
  }

  #tier-text {
  font-size: 2.5rem;
  color: #fff;
  margin-top: 10px;
  text-shadow: 0 0 10px #39FF14, 0 0 20px #39FF14;
  }

  .scanlines {
  position: absolute;
  top: 0; left: 0; width: 100%; height: 100%;
  background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
  background-size: 100% 4px, 6px 100%;
  z-index: 100;
  pointer-events: none;
  opacity: 0.4;
  }
  </style>
</head>
<body>

  <canvas id="gameCanvas"></canvas>
  <div class="scanlines"></div>

  <div id="ui-layer">
  <div id="boot-screen" class="hidden">
  <div id="boot-text" class="neon-text"></div>
  </div>

  <div id="title-screen" class="flex-center hidden">
  <h1 class="neon-text">NEON<br>CIRCUIT</h1>
  <button id="btn-execute" class="neon-btn">[ EXECUTE ]</button>
  </div>

  <div id="hud" class="hidden">
  <div class="neon-text">SCORE: <span id="score-val">0</span></div>
  <div class="neon-text">ORB: <span id="orb-val">0</span>/5</div>
  </div>
  <div id="combo-display" class="hidden neon-text">COMBO x<span id="combo-val">0</span></div>
  <div id="fever-display" class="hidden neon-text">SYSTEM OVERRIDE (FEVER)</div>

  <div id="gameover-screen" class="flex-center hidden">
  <div id="gameover-title" class="neon-text">GAME OVER</div>
  <div class="stats-box neon-text">
  <div>FINAL SCORE: <span id="final-score">0</span></div>
  <div>TIER:</div>
  <div id="tier-text">브론즈 손가락</div>
  </div>
  <button id="btn-retry" class="neon-btn">[ RETRY ]</button>
  </div>
  </div>

<script>
/**
 * 30년 차 개발자의 쌈뽕한 탄막 서바이벌 코어 엔진
 */

// --- 전역 변수 및 설정 ---
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
let w, h;

// UI 요소
const uiBoot = document.getElementById('boot-screen');
const uiTitle = document.getElementById('title-screen');
const uiHud = document.getElementById('hud');
const uiGameover = document.getElementById('gameover-screen');
const bootTextEl = document.getElementById('boot-text');

// 게임 상태
let gameState = 'init';
let reqAnimFrame;
let shakeTime = 0;

// 인게임 데이터
let score = 0;
let baseSpeed = 5;
let currentSpeed = baseSpeed;
let frameCount = 0;
let level = 1;
let combo = 0;
let orbs = 0;
let feverTimer = 0;
let slowmoTimer = 0;

// 엔티티 배열
let lasers = []; 
let bombs = []; 
let bullets = []; 
let items = [];
let particles = [];
let floatingTexts = [];
let drones = []; 

// 보스전 관련 변수
let boss = null;
let bossActive = false;
let boss1Defeated = false; // 1000점 보스
let midBossDefeated = false; // 6000점 중간 보스
let boss2Defeated = false; // 10000점 보스

// 입력 상태 (즉각 추적)
let input = { tx: window.innerWidth / 2, ty: window.innerHeight / 2, pointerActive: false };

// --- 윈도우 리사이즈 처리 ---
function resize() {
  w = canvas.width = window.innerWidth;
  h = canvas.height = window.innerHeight;
}
window.addEventListener('resize', resize);
resize();

// --- 입력 컨트롤 바인딩 ---
window.addEventListener('mousemove', e => {
  if(gameState !== 'playing') return;
  input.tx = e.clientX;
  input.ty = e.clientY;
  input.pointerActive = true;
});

// 모바일 터치 대응 (캔버스에 직접 바인딩하여 확실하게 기본 동작 차단)
canvas.addEventListener('touchmove', e => {
  if(gameState !== 'playing') return;
  e.preventDefault(); // 화면 스크롤/스와이프 등 기본 동작 방지
  input.tx = e.touches[0].clientX;
  input.ty = e.touches[0].clientY;
  input.pointerActive = true;
}, { passive: false });

canvas.addEventListener('touchstart', e => {
  if(gameState !== 'playing') return;
  e.preventDefault(); // 터치 딜레이 방지 및 즉각 반응
  input.tx = e.touches[0].clientX;
  input.ty = e.touches[0].clientY;
  input.pointerActive = true;
}, { passive: false });

// --- 객체 클래스 ---
class Player {
  constructor() {
  this.size = 20;
  this.x = w / 2;
  this.y = h / 2 + 100;
  this.trail = [];
  }

  update() {
  if (input.pointerActive) {
  this.x = input.tx;
  this.y = input.ty;
  }
  if (this.x < this.size/2) { this.x = this.size/2; }
  if (this.x > w - this.size/2) { this.x = w - this.size/2; }
  if (this.y < this.size/2) { this.y = this.size/2; }
  if (this.y > h - this.size/2) { this.y = h - this.size/2; }

  this.trail.push({x: this.x, y: this.y});
  if(this.trail.length > 8) this.trail.shift();
  }

  draw(ctx) {
  ctx.save();
  for(let i=0; i<this.trail.length; i++) {
  let alpha = i / this.trail.length;
  ctx.fillStyle = feverTimer > 0 ? `rgba(255, 255, 255, ${alpha * 0.5})` : `rgba(57, 255, 20, ${alpha * 0.3})`;
  let tSize = this.size * (0.5 + 0.5 * alpha);
  ctx.fillRect(this.trail[i].x - tSize/2, this.trail[i].y - tSize/2, tSize, tSize);
  }
  ctx.restore();

  ctx.save();
  ctx.shadowBlur = feverTimer > 0 ? 30 : 15;
  ctx.shadowColor = feverTimer > 0 ? '#FFFFFF' : '#39FF14';
  ctx.fillStyle = feverTimer > 0 ? '#FFFFFF' : '#39FF14';
  
  let pSize = feverTimer > 0 ? this.size * 1.5 : this.size;
  ctx.fillRect(this.x - pSize/2, this.y - pSize/2, pSize, pSize);
  ctx.restore();
  }
}
let player = new Player();

// --- 유틸 함수 ---
function addShake(amount) { shakeTime = amount; }
function random(min, max) { return Math.random() * (max - min) + min; }

function spawnFloatingText(x, y, text, color) {
  floatingTexts.push({ x: x, y: y, text: text, color: color, life: 60, maxLife: 60 });
}

function spawnParticles(x, y, color, count) {
  for(let i=0; i<count; i++) {
  particles.push({
  x: x, y: y,
  vx: random(-5, 5), vy: random(-5, 5),
  life: random(20, 40),
  color: color, size: random(2, 6)
  });
  }
}

// --- 게임 코어 로직 ---
function resetGame() {
  player = new Player();
  lasers = [];
  bombs = [];
  bullets = [];
  items = [];
  particles = [];
  floatingTexts = [];
  drones = [];
  boss = null;
  bossActive = false;
  boss1Defeated = false;
  midBossDefeated = false;
  boss2Defeated = false;
  score = 0;
  combo = 0;
  orbs = 0;
  level = 1;
  baseSpeed = 7;
  currentSpeed = baseSpeed;
  frameCount = 0;
  feverTimer = 0;
  slowmoTimer = 0;
  
  input.tx = w / 2;
  input.ty = h / 2 + 100;
  
  updateHUD();
}

function getTier(sc) {
  if(sc <= 500) return "브론즈 손가락";
  if(sc <= 2000) return "실버 생존자";
  if(sc <= 5000) return "골드 네온러";
  if(sc <= 9000) return "플래티넘 댄서";
  if(sc <= 20000) return "다이아몬드 서퍼";
  return "오메가 챌린저";
}

function triggerFever() {
  feverTimer = 180;
  orbs = 0;
  addShake(10);
  spawnFloatingText(w/2, h/2, "SYSTEM OVERRIDE!", "#FFFFFF");
  document.getElementById('fever-display').classList.remove('hidden');
  updateHUD();
}

function updateHUD() {
  document.getElementById('score-val').innerText = Math.floor(score);
  document.getElementById('orb-val').innerText = orbs;
  
  const comboDisplay = document.getElementById('combo-display');
  if (combo > 1) {
  comboDisplay.classList.remove('hidden');
  document.getElementById('combo-val').innerText = combo;
  comboDisplay.style.transform = `scale(${1 + Math.min(combo * 0.1, 0.5)})`;
  setTimeout(() => { comboDisplay.style.transform = 'scale(1)'; }, 50);
  } else {
  comboDisplay.classList.add('hidden');
  }
}

function startBootSequence() {
  gameState = 'boot';
  uiBoot.classList.remove('hidden');
  
  const messages = [
  "> INIT_SECURE_CONNECTION...",
  "> ACCESSING CIRCUIT...",
  "> DECRYPTING DATA [||||||||||] 100%",
  "> BYPASSING FIREWALL...",
  "> NEON_SYSTEM_READY.",
  "> PRESS ANY KEY OR TOUCH TO EXECUTE"
  ];
  
  let msgIdx = 0;
  bootTextEl.innerHTML = "";
  
  let bootInterval = setInterval(() => {
  if(msgIdx < messages.length) {
  bootTextEl.innerHTML += messages[msgIdx] + "<br>";
  msgIdx++;
  } else {
  clearInterval(bootInterval);
  const goToTitle = () => {
  window.removeEventListener('pointerdown', goToTitle);
  window.removeEventListener('keydown', goToTitle);
  showTitle();
  };
  setTimeout(() => {
  window.addEventListener('pointerdown', goToTitle);
  window.addEventListener('keydown', goToTitle);
  }, 500);
  }
  }, 400);
}

function showTitle() {
  gameState = 'title';
  uiBoot.classList.add('hidden');
  uiTitle.classList.remove('hidden');
  uiGameover.classList.add('hidden');
  document.getElementById('fever-display').classList.add('hidden');
  resetGame();
  if(!reqAnimFrame) gameLoop();
}

document.getElementById('btn-execute').addEventListener('click', startGame);
document.getElementById('btn-retry').addEventListener('click', startGame);

function startGame() {
  gameState = 'playing';
  uiTitle.classList.add('hidden');
  uiGameover.classList.add('hidden');
  uiHud.classList.remove('hidden');
  resetGame();
}

function gameOver() {
  gameState = 'gameover';
  uiHud.classList.add('hidden');
  uiGameover.classList.remove('hidden');
  
  document.getElementById('final-score').innerText = Math.floor(score);
  document.getElementById('tier-text').innerText = getTier(score);
  addShake(20);
}

function drawBackground() {
  ctx.fillStyle = 'rgba(0, 0, 0, 0.3)';
  ctx.fillRect(0, 0, w, h);

  ctx.save();
  ctx.strokeStyle = 'rgba(57, 255, 20, 0.15)';
  ctx.lineWidth = 2;
  ctx.beginPath();
  
  let gridOffset = (frameCount * (currentSpeed * 0.5)) % 50;
  
  for(let y = gridOffset; y < h; y += 50) {
  ctx.moveTo(0, y);
  ctx.lineTo(w, y);
  }
  for(let x = 0; x < w; x += 50) {
  ctx.moveTo(x, 0);
  ctx.lineTo(x, h);
  }
  ctx.stroke();
  ctx.restore();
}

function gameLoop() {
  reqAnimFrame = requestAnimationFrame(gameLoop);

  ctx.save();
  if (shakeTime > 0) {
  let dx = random(-shakeTime, shakeTime) * 0.5;
  let dy = random(-shakeTime, shakeTime) * 0.5;
  ctx.translate(dx, dy);
  shakeTime--;
  }

  drawBackground();

  if (gameState === 'title') {
  ctx.restore();
  frameCount++;
  return;
  }

  if (gameState === 'playing') {
  frameCount++;

  if (slowmoTimer > 0) {
  slowmoTimer--;
  currentSpeed = baseSpeed * 0.4;
  } else {
  currentSpeed = baseSpeed;
  }

  if (feverTimer > 0) {
  feverTimer--;
  currentSpeed = baseSpeed * 2;
  if(feverTimer <= 0) document.getElementById('fever-display').classList.add('hidden');
  }

  if (frameCount % 1200 === 0 && !bossActive) {
  level++;
  baseSpeed += 1.0;
  spawnFloatingText(w/2, h/3, "LEVEL UP!", "#39FF14");
  addShake(15);
  }

  if (!bossActive) {
  score += (currentSpeed * 0.1) * (1 + combo * 0.5);
  } else {
  score += 0.5;
  }

  player.update();

  // --- 보스 트리거 시스템 ---
  // 1보스 (1000점)
  if (score >= 1000 && score < 6000 && !bossActive && !boss1Defeated) {
  bossActive = true;
  lasers = []; bombs = []; bullets = []; items = []; drones = [];
  addShake(30);
  spawnFloatingText(w/2, h/2, "WARNING: SYSTEM ANOMALY DETECTED", "#FF003C");
  boss = { type: 1, x: w/2, y: -150, targetY: 150, hp: 5, maxHp: 5, phaseTimer: 0 };
  }
  
  // 1.5보스 (중간보스 - 6000점)
  if (score >= 6000 && score < 10000 && !bossActive && !midBossDefeated) {
  bossActive = true;
  lasers = []; bombs = []; bullets = []; items = []; drones = [];
  addShake(40);
  spawnFloatingText(w/2, h/2, "WARNING: PHANTOM ANOMALY DETECTED", "#00FFFF");
  boss = { type: 3, x: w/2, y: -150, targetY: 150, hp: 7, maxHp: 7, phaseTimer: 0 };
  }

  // 2보스 (10000점)
  if (score >= 10000 && !bossActive && !boss2Defeated) {
  bossActive = true;
  lasers = []; bombs = []; bullets = []; items = []; drones = [];
  addShake(50);
  spawnFloatingText(w/2, h/2, "CRITICAL: OMEGA ANOMALY DETECTED", "#FF00FF");
  boss = { type: 2, x: w/2, y: -200, targetY: 150, hp: 10, maxHp: 10, phaseTimer: 0 };
  }

  let spawnRate = Math.max(30, 80 - (level * 4));
  if (feverTimer > 0) spawnRate = 15;

  // --- 일반 구간 장애물 스폰 ---
  if (!bossActive && frameCount % Math.floor(spawnRate) === 0) {
  
  // 궤도 레이저
  let laserCount = Math.min(3, 1 + Math.floor(level / 3));
  for(let i=0; i<laserCount; i++) {
  let isVertical = random(0, 100) > 50;
  let targetPos = (random(0, 100) > 40) ? (isVertical ? player.x : player.y) : (isVertical ? random(50, w-50) : random(50, h-50));
  let warnTime = Math.max(30, 60 - (level * 2));
  lasers.push({ isVert: isVertical, pos: targetPos, timer: warnTime, state: 'warning', width: random(30, 50) + (level * 2), nearMissed: false });
  }
  
  // 펄스 폭탄
  if (random(0, 100) < 30 + (level * 5)) {
  bombs.push({ x: random(50, w-50), y: random(50, h-50), radius: 0, maxRadius: random(80, 120) + (level * 5), timer: 75, state: 'warning', nearMissed: false });
  }

  // 아이템
  if (random(0, 100) < 15) { 
  items.push({ x: random(50, w-50), y: -30, type: 'orb', radius: 20 });
  } else if (random(0, 100) < 3) {
  items.push({ x: random(50, w-50), y: -30, type: 'slowmo', radius: 10 });
  }
  
  // 탄막
  if (level >= 2 && random(0, 100) < 40) {
  let side = random(0, 100);
  let bx, by;
  if (side < 25) { bx = random(0, w); by = -30; }
  else if (side < 50) { bx = random(0, w); by = h + 30; }
  else if (side < 75) { bx = -30; by = random(0, h); }
  else { bx = w + 30; by = random(0, h); }
  
  let dx = player.x - bx; let dy = player.y - by;
  let dist = Math.sqrt(dx*dx + dy*dy);
  let spd = 4 + (level * 0.5);
  bullets.push({ x: bx, y: by, vx: (dx/dist) * spd, vy: (dy/dist) * spd, radius: 8, color: '#FFA500' });
  }

  // NEW! 추적 드론 스폰 (레벨 3부터 등장)
  if (level >= 3 && random(0, 100) < 25) {
  let bx = random(0, 100) > 50 ? -30 : w + 30;
  let by = random(0, h);
  drones.push({ x: bx, y: by, speed: 2 + level * 0.2, timer: 300, angle: 0 });
  }
  }

  // --- 보스전 로직 ---
  if (bossActive && boss) {
  boss.phaseTimer++;
  if (boss.y < boss.targetY) boss.y += 2;
  
  boss.x = w/2 + Math.sin(boss.phaseTimer * 0.02) * (w/3);
  
  if (boss.type === 1) {
  // [보스 1 패턴] 레드
  if (boss.y >= boss.targetY && boss.phaseTimer % 50 === 0) {
  addShake(5);
  for(let i=0; i<8; i++) {
  let angle = (i/8) * Math.PI * 2 + (boss.phaseTimer * 0.05);
  bullets.push({ x: boss.x, y: boss.y, vx: Math.cos(angle)*6, vy: Math.sin(angle)*6, radius: 10, color: '#FF003C' });
  }
  }
  if (boss.y >= boss.targetY && boss.phaseTimer % 180 === 0) {
  lasers.push({ isVert: true, pos: player.x, timer: 40, state: 'warning', width: 60, nearMissed: false });
  }
  } else if (boss.type === 3) {
  // [중간보스 패턴] 시안
  // 패턴 1: 플레이어를 향하는 3갈래 샷
  if (boss.y >= boss.targetY && boss.phaseTimer % 70 === 0) {
  addShake(5);
  let dx = player.x - boss.x;
  let dy = player.y - boss.y;
  let baseAngle = Math.atan2(dy, dx);
  for(let i=-1; i<=1; i++) {
  let angle = baseAngle + (i * 0.25);
  bullets.push({ x: boss.x, y: boss.y, vx: Math.cos(angle)*8, vy: Math.sin(angle)*8, radius: 10, color: '#00FFFF' });
  }
  }
  // 패턴 2: 추적 드론 양옆 스폰
  if (boss.y >= boss.targetY && boss.phaseTimer % 200 === 0) {
  drones.push({ x: boss.x - 50, y: boss.y, speed: 3, timer: 300, angle: 0 });
  drones.push({ x: boss.x + 50, y: boss.y, speed: 3, timer: 300, angle: 0 });
  }
  // 패턴 3: 플레이어를 가두는 양옆 레이저
  if (boss.y >= boss.targetY && boss.phaseTimer % 160 === 0) {
  lasers.push({ isVert: true, pos: player.x - 100, timer: 35, state: 'warning', width: 40, nearMissed: false });
  lasers.push({ isVert: true, pos: player.x + 100, timer: 35, state: 'warning', width: 40, nearMissed: false });
  }
  } else if (boss.type === 2) {
  // [보스 2 (OMEGA) 패턴] 마젠타
  if (boss.y >= boss.targetY && boss.phaseTimer % 40 === 0) {
  addShake(8);
  for(let i=0; i<12; i++) {
  let angle = (i/12) * Math.PI * 2 - (boss.phaseTimer * 0.05);
  bullets.push({ x: boss.x, y: boss.y, vx: Math.cos(angle)*7, vy: Math.sin(angle)*7, radius: 10, color: '#FF00FF' });
  }
  }
  if (boss.y >= boss.targetY && boss.phaseTimer % 150 === 0) {
  lasers.push({ isVert: true, pos: player.x, timer: 30, state: 'warning', width: 80, nearMissed: false });
  lasers.push({ isVert: false, pos: player.y, timer: 30, state: 'warning', width: 80, nearMissed: false });
  }
  if (boss.y >= boss.targetY && boss.phaseTimer % 120 === 0) {
  bombs.push({ x: random(50, w-50), y: random(50, h-50), radius: 0, maxRadius: 150, timer: 60, state: 'warning', nearMissed: false });
  }
  }

  // 해킹 오브 스폰 (보스 공통)
  if (boss.y >= boss.targetY && boss.phaseTimer % 100 === 0 && items.filter(i=>i.type==='hack').length < 2) {
  items.push({ x: random(50, w-50), y: -30, type: 'hack', radius: 25 });
  }
  }

  // --- 추적 드론(Seeker Drone) 처리 ---
  for (let i = drones.length - 1; i >= 0; i--) {
  let d = drones[i];
  d.timer -= (currentSpeed / baseSpeed);
  if (d.timer <= 0) {
  spawnParticles(d.x, d.y, '#FF4500', 15);
  drones.splice(i, 1);
  continue;
  }

  let dx = player.x - d.x;
  let dy = player.y - d.y;
  let dist = Math.sqrt(dx*dx + dy*dy);
  
  // 플레이어 향해 방향 전환 및 이동
  d.x += (dx/dist) * d.speed * (currentSpeed / baseSpeed);
  d.y += (dy/dist) * d.speed * (currentSpeed / baseSpeed);
  d.angle = Math.atan2(dy, dx);

  // 충돌
  if (dist < player.size/2 + 12) {
  if (feverTimer > 0) {
  spawnParticles(d.x, d.y, '#FF4500', 15);
  drones.splice(i, 1);
  score += 50;
  } else {
  spawnParticles(player.x, player.y, '#FF4500', 30);
  gameOver();
  break;
  }
  }
  }

  // 3. 펄스 폭탄 처리
  for (let i = bombs.length - 1; i >= 0; i--) {
  let b = bombs[i];
  if (b.state === 'warning') {
  b.timer -= (currentSpeed / baseSpeed);
  b.radius = b.maxRadius * (1 - Math.max(0, b.timer) / 75);
  if (b.timer <= 0) {
  b.state = 'explode';
  b.timer = 15;
  addShake(10);
  }
  } else if (b.state === 'explode') {
  b.timer -= (currentSpeed / baseSpeed);
  if (b.timer <= 0) { bombs.splice(i, 1); continue; }

  let dx = player.x - b.x; let dy = player.y - b.y;
  let dist = Math.sqrt(dx*dx + dy*dy);

  if (dist < player.size/2 + b.maxRadius) {
  if (feverTimer <= 0) { spawnParticles(player.x, player.y, '#FF003C', 30); gameOver(); break; }
  } else if (!b.nearMissed && feverTimer <= 0) {
  if (dist < player.size/2 + b.maxRadius + 30) {
  b.nearMissed = true; combo++;
  spawnFloatingText(player.x, player.y - 30, "BOMB EVADE!", "#FFFFFF"); updateHUD();
  }
  }
  }
  }

  // 4. 궤도 레이저 처리
  for (let i = lasers.length - 1; i >= 0; i--) {
  let l = lasers[i];
  l.timer -= (currentSpeed / baseSpeed);
  if (l.state === 'warning' && l.timer <= 0) {
  l.state = 'firing'; l.timer = 5; addShake(8);
  } else if (l.state === 'firing' && l.timer <= 0) {
  lasers.splice(i, 1); continue;
  }

  if (l.state === 'firing') {
  let pRect = { left: player.x - player.size/2, right: player.x + player.size/2, top: player.y - player.size/2, bottom: player.y + player.size/2 };
  let lRect = l.isVert ? { left: l.pos - l.width/2, right: l.pos + l.width/2, top: 0, bottom: h } : { left: 0, right: w, top: l.pos - l.width/2, bottom: l.pos + l.width/2 };

  let isIntersecting = !(pRect.right < lRect.left || pRect.left > lRect.right || pRect.bottom < lRect.top || pRect.top > lRect.bottom);

  if (isIntersecting) {
  if (feverTimer <= 0) { spawnParticles(player.x, player.y, '#FF003C', 30); gameOver(); break; }
  } else if (!l.nearMissed && feverTimer <= 0) {
  let nmRect = { left: pRect.left - 20, right: pRect.right + 20, top: pRect.top - 20, bottom: pRect.bottom + 20 };
  let nearIntersecting = !(nmRect.right < lRect.left || nmRect.left > lRect.right || nmRect.bottom < lRect.top || nmRect.top > lRect.bottom);
  if (nearIntersecting) {
  l.nearMissed = true; combo += 2;
  spawnFloatingText(player.x, player.y - 30, "LASER MISS!", "#FFFFFF"); updateHUD();
  }
  }
  }
  }

  // --- 날아오는 탄막 처리 ---
  for (let i = bullets.length - 1; i >= 0; i--) {
  let b = bullets[i];
  b.x += b.vx * (currentSpeed / baseSpeed);
  b.y += b.vy * (currentSpeed / baseSpeed);
  
  let dx = player.x - b.x; let dy = player.y - b.y;
  let dist = Math.sqrt(dx*dx + dy*dy);
  
  if (dist < player.size/2 + b.radius) {
  if (feverTimer <= 0) {
  spawnParticles(player.x, player.y, b.color, 30);
  gameOver(); break;
  }
  }
  if (b.x < -100 || b.x > w+100 || b.y < -100 || b.y > h+100) bullets.splice(i, 1);
  }

  // 5. 아이템 처리
  for (let i = items.length - 1; i >= 0; i--) {
  let item = items[i];
  item.y += currentSpeed; 

  let dx = player.x - item.x; let dy = player.y - item.y;
  let dist = Math.sqrt(dx*dx + dy*dy);

  if (dist < player.size/2 + item.radius) {
  if(item.type === 'orb') {
  orbs++; spawnParticles(item.x, item.y, '#39FF14', 10);
  if(orbs >= 5) triggerFever();
  } else if (item.type === 'slowmo') {
  slowmoTimer = 120; spawnParticles(item.x, item.y, '#00FFFF', 15);
  spawnFloatingText(player.x, player.y - 30, "SLOW-MO", "#00FFFF");
  } else if (item.type === 'hack') {
  spawnParticles(item.x, item.y, '#B026FF', 20);
  if (bossActive && boss) {
  boss.hp--;
  addShake(20);
  spawnFloatingText(boss.x, boss.y, "CRITICAL HIT!", "#B026FF");
  if (boss.hp <= 0) {
  bossActive = false;
  if (boss.type === 1) { boss1Defeated = true; score += 5000; } 
  else if (boss.type === 3) { midBossDefeated = true; score += 3000; }
  else { boss2Defeated = true; score += 20000; }
  bullets = []; lasers = []; bombs = []; items = []; drones = [];
  addShake(50);
  spawnFloatingText(w/2, h/2, "SYSTEM RESTORED", "#39FF14");
  triggerFever();
  }
  }
  }
  items.splice(i, 1); updateHUD(); continue;
  }
  if (item.y > h + 50) items.splice(i, 1);
  }

  // 6. 렌더링 파트
  // 추적 드론 그리기
  drones.forEach(d => {
  ctx.save();
  ctx.translate(d.x, d.y);
  ctx.rotate(d.angle);
  ctx.beginPath();
  ctx.moveTo(12, 0); ctx.lineTo(-10, 8); ctx.lineTo(-10, -8);
  ctx.closePath();
  ctx.fillStyle = '#FF4500';
  ctx.shadowColor = '#FF4500';
  ctx.shadowBlur = 15;
  ctx.fill();
  ctx.restore();
  });

  // 폭탄 그리기
  bombs.forEach(b => {
  ctx.save();
  ctx.beginPath();
  ctx.arc(b.x, b.y, b.maxRadius, 0, Math.PI*2);
  if (b.state === 'warning') {
  ctx.strokeStyle = (Math.floor(frameCount / 5) % 2 === 0) ? '#FF003C' : 'rgba(255, 0, 60, 0.2)';
  ctx.lineWidth = 2; ctx.setLineDash([8, 8]); ctx.stroke();
  ctx.beginPath(); ctx.arc(b.x, b.y, b.radius, 0, Math.PI*2);
  ctx.fillStyle = 'rgba(255, 0, 60, 0.15)'; ctx.fill();
  } else if (b.state === 'explode') {
  ctx.fillStyle = '#FF003C'; ctx.shadowColor = '#FF003C'; ctx.shadowBlur = 20; ctx.fill();
  }
  ctx.restore();
  });

  // 레이저 그리기
  lasers.forEach(l => {
  ctx.save();
  let laserColor = (bossActive && boss && boss.type === 2) ? '#FF00FF' : ((bossActive && boss && boss.type === 3) ? '#00FFFF' : '#FF003C');
  if (l.state === 'warning') {
  ctx.strokeStyle = (Math.floor(frameCount / 5) % 2 === 0) ? laserColor : `rgba(255,255,255, 0.2)`;
  ctx.lineWidth = 2; ctx.setLineDash([15, 10]);
  ctx.beginPath();
  if (l.isVert) { ctx.moveTo(l.pos, 0); ctx.lineTo(l.pos, h); }
  else { ctx.moveTo(0, l.pos); ctx.lineTo(w, l.pos); }
  ctx.stroke();
  } else if (l.state === 'firing') {
  ctx.fillStyle = laserColor; ctx.shadowColor = laserColor; ctx.shadowBlur = 30;
  if (l.isVert) { ctx.fillRect(l.pos - l.width/2, 0, l.width, h); }
  else { ctx.fillRect(0, l.pos - l.width/2, w, l.width); }
  }
  ctx.restore();
  });

  // 아이템 그리기
  items.forEach(item => {
  ctx.beginPath(); ctx.arc(item.x, item.y, item.radius, 0, Math.PI*2);
  if (item.type === 'orb') ctx.fillStyle = '#39FF14';
  else if (item.type === 'slowmo') ctx.fillStyle = '#00FFFF';
  else if (item.type === 'hack') ctx.fillStyle = '#B026FF'; 
  ctx.shadowColor = ctx.fillStyle; ctx.shadowBlur = 15; ctx.fill();
  ctx.beginPath(); ctx.arc(item.x, item.y, item.radius*0.5, 0, Math.PI*2);
  ctx.fillStyle = '#FFF'; ctx.fill();
  });

  // 탄막 그리기
  bullets.forEach(b => {
  ctx.beginPath(); ctx.arc(b.x, b.y, b.radius, 0, Math.PI*2);
  ctx.fillStyle = b.color; ctx.shadowColor = b.color; ctx.shadowBlur = 10; ctx.fill();
  ctx.beginPath(); ctx.arc(b.x, b.y, b.radius*0.4, 0, Math.PI*2);
  ctx.fillStyle = '#FFF'; ctx.fill();
  });

  // 보스 그리기
  if (bossActive && boss) {
  ctx.save();
  ctx.translate(boss.x, boss.y);
  
  if (boss.type === 1) { // 1보스 (레드)
  ctx.rotate(boss.phaseTimer * 0.05);
  ctx.beginPath(); ctx.moveTo(0, -60); ctx.lineTo(60, 0); ctx.lineTo(0, 60); ctx.lineTo(-60, 0); ctx.closePath();
  ctx.strokeStyle = '#FF003C'; ctx.lineWidth = 5; ctx.shadowColor = '#FF003C'; ctx.shadowBlur = 20 + Math.sin(frameCount*0.2)*10; ctx.stroke();
  ctx.beginPath(); ctx.arc(0, 0, 20 + Math.sin(frameCount*0.1)*5, 0, Math.PI*2); ctx.fillStyle = '#FF003C'; ctx.fill();
  } else if (boss.type === 3) { // 중간보스 (시안 육각형)
  ctx.rotate(boss.phaseTimer * 0.05);
  ctx.beginPath();
  for(let i=0; i<6; i++) {
  let a = (i/6)*Math.PI*2; let r = 55 + Math.sin(frameCount*0.2)*5;
  if(i===0) ctx.moveTo(Math.cos(a)*r, Math.sin(a)*r); else ctx.lineTo(Math.cos(a)*r, Math.sin(a)*r);
  }
  ctx.closePath();
  ctx.strokeStyle = '#00FFFF'; ctx.lineWidth = 5; ctx.shadowColor = '#00FFFF'; ctx.shadowBlur = 20 + Math.sin(frameCount*0.2)*10; ctx.stroke();
  ctx.beginPath(); ctx.arc(0, 0, 15 + Math.sin(frameCount*0.1)*5, 0, Math.PI*2); ctx.fillStyle = '#00FFFF'; ctx.fill();
  } else if (boss.type === 2) { // 2보스 (마젠타)
  ctx.rotate(-boss.phaseTimer * 0.08);
  ctx.beginPath();
  for(let i=0; i<8; i++) {
  let a = (i/8)*Math.PI*2; let r = 70 + Math.sin(frameCount*0.3)*10;
  if(i===0) ctx.moveTo(Math.cos(a)*r, Math.sin(a)*r); else ctx.lineTo(Math.cos(a)*r, Math.sin(a)*r);
  }
  ctx.closePath();
  ctx.strokeStyle = '#FF00FF'; ctx.lineWidth = 6; ctx.shadowColor = '#FF00FF'; ctx.shadowBlur = 30 + Math.sin(frameCount*0.3)*15; ctx.stroke();
  ctx.beginPath(); ctx.arc(0, 0, 25 + Math.sin(frameCount*0.2)*5, 0, Math.PI*2); ctx.fillStyle = '#FF00FF'; ctx.fill();
  }
  
  ctx.restore();
  
  // 보스 체력바 UI
  let barColor = boss.type === 1 ? '#FF003C' : (boss.type === 2 ? '#FF00FF' : '#00FFFF');
  let barBgColor = boss.type === 1 ? 'rgba(255, 0, 60, 0.3)' : (boss.type === 2 ? 'rgba(255, 0, 255, 0.3)' : 'rgba(0, 255, 255, 0.3)');
  let bossName = boss.type === 1 ? "SYSTEM ANOMALY CORE" : (boss.type === 2 ? "OMEGA ANOMALY CORE" : "PHANTOM ANOMALY CORE");

  ctx.fillStyle = barBgColor; ctx.fillRect(w/2 - 150, 80, 300, 20);
  ctx.fillStyle = barColor; ctx.fillRect(w/2 - 150, 80, 300 * (boss.hp / boss.maxHp), 20);
  ctx.strokeStyle = '#FFF'; ctx.lineWidth = 2; ctx.strokeRect(w/2 - 150, 80, 300, 20);
  
  ctx.font = "bold 16px 'Share Tech Mono', monospace";
  ctx.fillStyle = '#FFF'; ctx.textAlign = 'center'; ctx.shadowBlur = 5; ctx.shadowColor = '#FFF';
  ctx.fillText(bossName, w/2, 70); ctx.shadowBlur = 0;
  }

  player.draw(ctx);

  // 파티클
  for(let i = particles.length - 1; i >= 0; i--) {
  let p = particles[i]; p.x += p.vx; p.y += p.vy; p.life--;
  ctx.fillStyle = p.color; ctx.globalAlpha = p.life / 40; ctx.shadowBlur = 5; ctx.fillRect(p.x, p.y, p.size, p.size);
  if(p.life <= 0) particles.splice(i, 1);
  }
  ctx.globalAlpha = 1; ctx.shadowBlur = 0;

  // 플로팅 텍스트
  for(let i = floatingTexts.length - 1; i >= 0; i--) {
  let ft = floatingTexts[i]; ft.y -= 1; ft.life--;
  ctx.font = "20px 'Share Tech Mono', monospace"; ctx.fillStyle = ft.color; ctx.globalAlpha = ft.life / ft.maxLife;
  ctx.textAlign = "center"; ctx.fillText(ft.text, ft.x, ft.y);
  if(ft.life <= 0) floatingTexts.splice(i, 1);
  }
  ctx.globalAlpha = 1;

  if(frameCount % 5 === 0) updateHUD();
  }

  ctx.restore(); 
}

window.onload = () => {
  setTimeout(() => { startBootSequence(); }, 500);
};

</script>
</body>
</html>
