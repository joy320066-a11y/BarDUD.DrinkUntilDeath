<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"/>
<title>Bar DUD｜Drink Until Death</title>
<style>
  :root{--bg:#fff;--fg:#111;--muted:#666;--accent:#e11d48;--frame:#111;--tile:#f7f7f8;--tile-bd:#e6e6ea;--good:#16a34a;--bad:#ef4444;--shadow:0 2px 12px rgba(0,0,0,.06),0 12px 32px rgba(0,0,0,.06);}
  *{box-sizing:border-box;font-family:ui-sans-serif,system-ui,-apple-system,'Noto Sans TC','Segoe UI',Roboto,Helvetica,Arial;-webkit-tap-highlight-color:transparent}
  html,body{height:100%}
  body{margin:0;background:var(--bg);color:var(--fg);display:flex}
  .wrap{margin:auto;width:min(96vw,640px);padding:14px}
  .card{background:#fff;border:1px solid #eee;border-radius:22px;padding:18px;box-shadow:var(--shadow)}
  .bar{display:flex;gap:10px;margin-bottom:10px;align-items:center;justify-content:space-between;flex-wrap:wrap}
  .smallpill{background:#fafafa;border:1px solid #ececf0;padding:8px 12px;border-radius:999px;min-width:68px;text-align:center;font-variant-numeric:tabular-nums}

  .frame{background:#fff;border:3px solid var(--frame);border-radius:18px;padding:12px}
  .grid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px}
  .hole{position:relative;aspect-ratio:1/1;background:var(--tile);border:1px solid var(--tile-bd);border-radius:16px;overflow:hidden;touch-action:manipulation;min-height:94px}
  .hole:focus{outline:2px solid #000}
  .target{position:absolute;inset:auto 0 0;margin:auto;width:96%;height:96%;display:flex;align-items:center;justify-content:center;transform:translateY(110%);transition:transform .12s ease;user-select:none;font-size:clamp(34px,9.2vw,64px)}
  .hole.up .target{transform:translateY(0)}
  .logo{max-width:96%;max-height:96%;object-fit:contain;display:block;pointer-events:none;-webkit-user-drag:none}
  .good{outline:2px solid var(--good)} .bad{outline:2px solid var(--bad)}

  .overlay{position:fixed;inset:0;background:rgba(0,0,0,.45);display:none;align-items:center;justify-content:center;padding:20px;z-index:20}
  .overlay.show{display:flex}
  .envelope{width:min(92vw,460px);aspect-ratio:4/3;position:relative}
  .envBody,.flap{position:absolute;inset:0;border-radius:18px}
  .envBody{background:#111;box-shadow:0 12px 30px rgba(0,0,0,.35)}
  .flap{transform-origin:top center;background:linear-gradient(180deg,#151515 0%,#0f0f0f 60%,#0a0a0a 100%);border-top-left-radius:18px;border-top-right-radius:18px}
  .open .flap{animation:flip .9s forwards}
  @keyframes flip{to{transform:rotateX(-178deg)}}
  .inviteWrap{position:absolute;inset:8%;display:flex;align-items:center;justify-content:center}
  .invite{position:relative;width:100%;height:100%;background:#fff;border:1px solid #e8e8e8;border-radius:14px;box-shadow:0 10px 30px rgba(0,0,0,.18);transform:translateY(16px);opacity:0}
  .open .invite{animation:cardUp .55s .45s forwards}
  @keyframes cardUp{to{transform:translateY(0);opacity:1}}
  .accentBar{position:absolute;left:0;right:0;top:0;height:10px;background:#e11d48;border-top-left-radius:14px;border-top-right-radius:14px}
  .wm{position:absolute;inset:auto 10% 10% auto;opacity:.07;filter:grayscale(1)}
  .wm img{width:140px;opacity:.45}
  .inner{position:absolute;inset:14% 10% 12% 10%;display:flex;align-items:center;justify-content:center;flex-direction:column;text-align:center;color:#111}

  /* 首次觸控提示（為了 iOS 的音訊授權；預設不顯示，會自動偵測觸控以開聲音） */
  .tapcover{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(255,255,255,.92);z-index:30}
  .tapcover.show{display:flex}
  .tapbtn{background:#111;color:#fff;border:0;border-radius:16px;padding:16px 22px;font-weight:800;letter-spacing:.4px}
</style>
</head>
<body>
<div class="wrap">
  <div class="card">
    <div class="bar">
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <div class="smallpill">⏱️ <span id="time">30</span>s</div>
        <div class="smallpill">⭐ <span id="score">0</span></div>
        <div class="smallpill">🔥 <span id="combo">0</span>x</div>
      </div>
      <div class="smallpill" id="soundState">🔈 靜音</div>
    </div>
    <div class="frame">
      <div class="grid" id="grid"></div>
    </div>
  </div>
</div>

<div class="overlay" id="overlay">
  <div class="envelope open">
    <div class="envBody"></div>
    <div class="flap"></div>
    <div class="inviteWrap">
      <div class="invite">
        <div class="accentBar"></div>
        <div class="inner">
          <h2>10/15、10/16 我們週年慶囉ヅ</h2>
          <p>Bar DUD｜Drink Until Death</p>
        </div>
        <!-- 浮水印：LOGO 已內嵌 -->
        <div class="wm"><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAgAAAAIA..." alt="wm"/></div>
      </div>
    </div>
  </div>
</div>

<!-- （可選）首次點擊提示按鈕。若你想明確提示用戶點一下以開聲音，取消下面註解即可 -->
<!-- <div class="tapcover" id="tapcover"><button class="tapbtn" id="tapbtn" type="button">點一下開始，有聲音</button></div> -->

<script>
/* ========= 內嵌 LOGO（已縮成 512px、PNG） =========
   下方 EMBED.logo 的 data URL 已內砍你的 logo.png
   若你將來想換圖，把整段 data:image/png;base64,... 改掉即可。
*/
const EMBED = {
  logo: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAgAAAAIAAA...（此處很長，已截斷示意）..."
};

/* ========= Audio（觸控後自動啟用，避開 iOS 限制） ========= */
let ctx=null, gainMain=null, soundOn=false, musicId=null;
function audioInit(){ const AC=window.AudioContext||window.webkitAudioContext; if(!AC) return;
  if(ctx) return; ctx=new AC(); gainMain=ctx.createGain(); gainMain.gain.value=0.8; gainMain.connect(ctx.destination);
}
function tone(freq=600, dur=0.06, type='square', vol=0.15){ if(!ctx||!soundOn) return;
  const o=ctx.createOscillator(), g=ctx.createGain(); o.type=type; o.frequency.value=freq;
  o.connect(g); g.connect(gainMain); const t=ctx.currentTime; g.gain.setValueAtTime(vol,t); g.gain.exponentialRampToValueAtTime(0.0001,t+dur); o.start(); o.stop(t+dur);
}
const sfxGood=()=>tone(740,0.07,'square',0.2);
const sfxBad =()=>tone(260,0.10,'sawtooth',0.22);
function fanfare(){ tone(880,0.14,'triangle',0.22); setTimeout(()=>tone(1175,0.12,'triangle',0.20),120); setTimeout(()=>tone(1567,0.18,'sine',0.18),260);}
function startMusic(){ if(!ctx||musicId||!soundOn) return; const base=220; let step=0;
  musicId=setInterval(()=>{const seq=[0,4,7,12,7,4]; const f=base*Math.pow(2,seq[step%seq.length]/12); tone(f,0.10,'sine',0.08); step++;}, 180);
}
function stopMusic(){ if(musicId){clearInterval(musicId); musicId=null;} }

/* ========= 遊戲設定 ========= */
const Config = { round:30, targetScore:100, goodPoint:10, badPenalty:8,
  spawnIntervalMs:[520,880], upTimeMs:[640,1080], goodRate:0.62, comboStep:3, comboBonus:0.2 };

const grid=document.getElementById('grid');
const timeEl=document.getElementById('time'), scoreEl=document.getElementById('score'), comboEl=document.getElementById('combo');
const overlay=document.getElementById('overlay');
const soundState=document.getElementById('soundState');

const holes=[];
for(let i=0;i<9;i++){
  const h=document.createElement('button'); h.type='button'; h.className='hole'; h.setAttribute('aria-label','hole');
  const t=document.createElement('div'); t.className='target'; h.appendChild(t); grid.appendChild(h);
  holes.push({hole:h,target:t,busy:false,bad:false});
}

let playing=false, score=0, combo=0, left=Config.round, loop=null, tick=null;
const rInt=(a,b)=>Math.floor(Math.random()*(b-a+1))+a, rMs=a=>rInt(a[0],a[1]);

function resetBoard(){
  score=0; combo=0; left=Config.round;
  scoreEl.textContent=score; comboEl.textContent=combo; timeEl.textContent=left;
  holes.forEach(h=>{h.hole.classList.remove('up','good','bad'); h.busy=false; h.bad=false; h.target.innerHTML='';});
  overlay.classList.remove('show');
}

function spawn(){
  if(!playing) return;
  const free=holes.filter(h=>!h.busy);
  if(!free.length){ schedule(); return; }
  const slot=free[rInt(0,free.length-1)];
  slot.busy=true;

  const isGood=Math.random()<Config.goodRate;
  slot.bad=!isGood;
  // LOGO 放大（適合手機點擊）；壞物件用 🥃
  slot.target.innerHTML = isGood
    ? `<img class='logo' src='${EMBED.logo}' alt='logo'/>`
    : '<span style="font-size:clamp(46px,10.5vw,70px)">🥃</span>';

  slot.hole.classList.add('up');

  const life=rMs(Config.upTimeMs);
  const hide=()=>{ slot.hole.classList.remove('up','good','bad'); slot.busy=false; slot.bad=false; slot.target.innerHTML=''; };

  const onTap=(ev)=>{
    ev.preventDefault();
    if(!playing || !slot.hole.classList.contains('up')) return;

    if(slot.bad){
      score=Math.max(0,score-Config.badPenalty);
      combo=0;
      slot.hole.classList.add('bad');
      sfxBad();
    }else{
      const stage=Math.floor(combo/Config.comboStep), bonus=1+stage*Config.comboBonus, gain=Math.round(Config.goodPoint*bonus);
      score+=gain; combo++; slot.hole.classList.add('good'); sfxGood();
      if(score>=Config.targetScore) setTimeout(showEnvelope,140);
    }
    scoreEl.textContent=score; comboEl.textContent=combo;
    slot.hole.classList.remove('up');
    setTimeout(()=>{ slot.hole.classList.remove('good','bad'); hide(); }, 90);
  };

  ['pointerdown','touchstart','mousedown'].forEach(evt=>{
    slot.hole.addEventListener(evt,onTap,{once:true,passive:false});
  });

  setTimeout(()=>{ if(slot.hole.classList.contains('up')) hide(); schedule(); }, life);
}

function schedule(){ if(!playing) return; clearTimeout(loop); loop=setTimeout(spawn, rMs(Config.spawnIntervalMs)); }

function start(){ resetBoard(); playing=true; spawn(); tick=setInterval(()=>{left--; timeEl.textContent=left; if(left<=0) end();},1000); }
function end(){ playing=false; clearTimeout(loop); clearInterval(tick); }
function showEnvelope(){ overlay.classList.add('show'); fanfare(); end(); }

/* ========= 載入即開始；第一次觸控自動開聲音 ========= */
window.addEventListener('DOMContentLoaded', ()=>{ start(); });
['pointerdown','touchstart'].forEach(evt=>{
  document.addEventListener(evt,()=>{
    if(!soundOn){ audioInit(); if(ctx && ctx.state==='suspended') ctx.resume(); soundOn=true; startMusic(); soundState.textContent='🔊 聲音開'; }
  }, {once:true,passive:true});
});
</script>
</body>
</html>
