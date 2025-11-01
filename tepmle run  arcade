<!doctype html>
<html lang="az">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Temple Run – Desert Edition</title>
  <style>
    html,body{height:100%;margin:0;background:#d6b77e;color:#fff;font-family:Inter,Segoe UI,Arial;overflow:hidden}
    .wrap{display:flex;align-items:center;justify-content:center;height:100%;}
    canvas{background:url('desert.jpg') center/cover no-repeat;border-radius:8px;box-shadow:0 10px 30px rgba(0,0,0,.6)}
    .hud{position:fixed;left:20px;top:20px;font-size:16px;text-shadow:1px 1px 3px #000}
    .hint{position:fixed;right:20px;top:20px;text-align:right;opacity:.9;text-shadow:1px 1px 3px #000}
    .center-msg{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);text-align:center}
  </style>
</head>
<body>
  <div class="wrap">
    <canvas id="game" width="900" height="500"></canvas>
  </div>
  <div class="hud">Score: <span id="score">0</span> &nbsp;|&nbsp; Speed: <span id="speed">1.0</span></div>
  <div class="hint">← → hərəkət • ↑ tullan • R yenidən başla</div>
  <div class="center-msg" id="centerMsg"></div>

  <script>
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  const W = canvas.width, H = canvas.height;

  let lanes = [W*0.25, W*0.5, W*0.75];
  let laneY = H - 110;
  let gravity = 0.9;
  let gameOver = false;
  let score = 0;
  let speed = 4;
  let spawnTimer = 0;
  let obstacles = [];
  let particles = [];

  const player = {
    lane: 1,
    x: lanes[1],
    y: laneY,
    vy: 0,
    width: 48,
    height: 80,
    jumping: false,
    color: '#ffd760'
  };

  function rand(min,max){return Math.random()*(max-min)+min}

  function spawnObstacle(){
    const lane = Math.floor(rand(0,3));
    const type = Math.random() < 0.2 ? 'tall' : 'low';
    obstacles.push({
      lane, x: lanes[lane]+(Math.random()-0.5)*10, y: laneY, 
      width: type==='tall'?60:48, height: type==='tall'?120:48,
      type, passed:false
    });
  }

  function spawnParticles(x,y,n){
    for(let i=0;i<n;i++) particles.push({x,y,vx:rand(-3,3),vy:rand(-6,-1),life:rand(20,60)})
  }

  const keys = {};
  window.addEventListener('keydown',e=>{
    keys[e.key]=true;
    if(e.key==='ArrowLeft'){ if(!gameOver) movePlayer(-1); }
    if(e.key==='ArrowRight'){ if(!gameOver) movePlayer(1); }
    if(e.key==='ArrowUp' || e.key===' '){ if(!gameOver) jumpPlayer(); }
    if(e.key==='r' || e.key==='R'){ if(gameOver) resetGame(); }
  });

  function movePlayer(dir){
    const newLane = player.lane + dir;
    if(newLane>=0 && newLane<=2){ player.lane = newLane; }
  }

  function jumpPlayer(){ if(!player.jumping){ player.vy = -16; player.jumping = true; } }

  function checkCollision(a,b){
    return !(a.x + a.width/2 < b.x - b.width/2 || a.x - a.width/2 > b.x + b.width/2 || a.y - a.height > b.y || a.y < b.y - b.height);
  }

  let lastTime = 0;
  function update(t){
    const dt = Math.min(50, t - lastTime || 16);
    lastTime = t;
    if(!gameOver){
      speed += 0.0008 * dt;
      score += 0.02 * dt * (speed/4);
      document.getElementById('score').textContent = Math.floor(score);
      document.getElementById('speed').textContent = speed.toFixed(2);

      spawnTimer += dt;
      if(spawnTimer > Math.max(300 - speed*20, 140)){
        spawnTimer = 0; spawnObstacle();
      }

      const targetX = lanes[player.lane];
      player.x += (targetX - player.x) * 0.25;

      player.vy += gravity * (dt/16);
      player.y += player.vy;
      if(player.y >= laneY){ player.y = laneY; player.vy = 0; player.jumping = false; }

      for(let i=obstacles.length-1;i>=0;i--){
        const ob = obstacles[i];
        ob.x -= speed * (dt/16) * 6;
        if(!ob.passed && ob.x < player.x - 60){ ob.passed = true; score += 10; }
        if(ob.x < -100) obstacles.splice(i,1);
      }

      for(const ob of obstacles){
        const a = {x:player.x, y:player.y+player.height/2, width:player.width, height:player.height};
        const b = {x:ob.x, y:ob.y+ob.height/2, width:ob.width, height:ob.height};
        if(checkCollision(a,b)) triggerGameOver();
      }

      for(let i=particles.length-1;i>=0;i--){
        const p = particles[i];
        p.vy += 0.4; p.x += p.vx; p.y += p.vy; p.life -= dt/2;
        if(p.life<=0) particles.splice(i,1);
      }
    }

    render();
    requestAnimationFrame(update);
  }

  function render(){
    ctx.clearRect(0,0,W,H);

    // draw rocky desert ground
    const grad = ctx.createLinearGradient(0,H-150,0,H);
    grad.addColorStop(0,'#c49a6c');
    grad.addColorStop(1,'#7a552a');
    ctx.fillStyle = grad;
    ctx.fillRect(0,H-150,W,150);

    // draw rocks
    for(let i=0;i<6;i++){
      ctx.fillStyle = '#5b4225';
      const rx = (i*180 - (Date.now()/20*speed)%180);
      const ry = H-120+Math.sin(i)*8;
      ctx.beginPath(); ctx.ellipse(rx,ry,rand(30,60),rand(14,24),0,0,Math.PI*2); ctx.fill();
    }

    // obstacles
    for(const ob of obstacles){
      ctx.save(); ctx.translate(ob.x, ob.y - ob.height/2);
      ctx.fillStyle = '#8b2'; ctx.fillRect(-ob.width/2, 0, ob.width, ob.height);
      ctx.fillStyle = '#654'; ctx.fillRect(-ob.width/2, ob.height-20, ob.width, 20);
      ctx.restore();
    }

    // player
    ctx.save(); ctx.translate(player.x, player.y - player.height/2);
    ctx.fillStyle = player.color; ctx.fillRect(-player.width/2, 0, player.width, player.height);
    ctx.fillStyle = '#ffb870'; ctx.beginPath(); ctx.arc(0, -8, 14, 0, Math.PI*2); ctx.fill();
    ctx.fillStyle = '#222'; ctx.fillRect(-6,-12,4,4); ctx.fillRect(2,-12,4,4);
    ctx.restore();

    for(const p of particles){ ctx.fillStyle = 'rgba(255,200,100,0.9)'; ctx.fillRect(p.x,p.y,3,3); }

    if(gameOver){
      const msg = document.getElementById('centerMsg');
      msg.innerHTML = `<div style="background:rgba(0,0,0,0.55);padding:18px;border-radius:12px;min-width:320px">
        <h2 style="margin:6px 0">Oyun bitdi</h2>
        <div style="font-size:18px">Nəticə: <strong>${Math.floor(score)}</strong></div>
        <div style="margin-top:10px">Basın <span style='font-weight:700'>R</span> təkrar oynamaq üçün</div>
      </div>`;
    } else {
      document.getElementById('centerMsg').innerHTML = '';
    }
  }

  function triggerGameOver(){ if(gameOver) return; gameOver = true; spawnParticles(player.x, player.y, 18); }

  function resetGame(){
    gameOver = false; score=0; speed=4; spawnTimer=0; obstacles=[]; particles=[];
    player.lane=1; player.x=lanes[1]; player.y=laneY; player.vy=0; player.jumping=false;
    document.getElementById('score').textContent='0'; document.getElementById('speed').textContent=speed.toFixed(2);
  }

  resetGame(); requestAnimationFrame(update);

  window.addEventListener('resize', ()=>{
    const maxW=Math.min(window.innerWidth-40,1200); const maxH=Math.min(window.innerHeight-80,700);
    const scale=Math.min(maxW/900,maxH/500);
    canvas.style.width=Math.round(900*scale)+'px'; canvas.style.height=Math.round(500*scale)+'px';
  });
  window.dispatchEvent(new Event('resize'));
  canvas.addEventListener('pointerdown', ()=>{ if(!gameOver) jumpPlayer(); else resetGame(); });
  </script>
</body>
</html>
