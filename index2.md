[index.html](https://github.com/user-attachments/files/33700799/index.html)
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>소리 파형 연구실</title>
<style>
:root{font-family:system-ui,'Malgun Gothic',sans-serif;color:#172339;background:#f2f6fd}
*{box-sizing:border-box}
html{-webkit-text-size-adjust:100%;touch-action:manipulation}
body{margin:0;padding:clamp(12px,2.2vw,24px);padding-bottom:calc(22px + env(safe-area-inset-bottom));background:#f2f6fd}
main{max-width:1150px;margin:auto}
header{margin-bottom:18px}
h1{font-size:30px;margin:0 0 8px}
p{color:#596a80;margin:6px 0}
.panel{background:#fff;border:1px solid #e2e9f3;border-radius:18px;padding:20px;margin:14px 0;box-shadow:0 5px 22px #152f5510}
.toolbar{display:flex;gap:12px;align-items:center;flex-wrap:wrap}
button,select,input{font:inherit}
button,select,input[type=text]{min-height:50px}
button{cursor:pointer;border:1px solid #d5dfed;border-radius:10px;background:#fff;padding:12px 18px;font-size:16px;font-weight:650;color:#1d2a45;touch-action:manipulation}
button.primary{background:#285fe7;color:#fff;border-color:#285fe7}
button.danger{color:#bb3351}
button:disabled{opacity:.4;cursor:default}
label{font-size:15px;color:#485a70}
input[type=color]{width:54px;height:50px;padding:2px;border:1px solid #d5dfed;background:#fff;border-radius:8px;vertical-align:middle;touch-action:manipulation}
input[type=checkbox]{width:22px;height:22px;vertical-align:middle;cursor:pointer;accent-color:#285fe7}
input[type=text]{padding:10px;border:1px solid #d5dfed;border-radius:9px;background:white}
#name{width:156px}
.chart{background:#0e1a31;border-radius:14px;overflow:hidden;position:relative}
canvas{display:block;width:100%;height:340px}
.status{padding:7px 0;font-size:14px;color:#435571;min-height:32px;line-height:1.6}
.dot{display:inline-block;width:9px;height:9px;border-radius:50%;background:#9ca8b7;margin-right:5px}.dot.on{background:#25b986}
.legend{display:flex;flex-wrap:wrap;gap:10px;margin:14px 0 0}
.trace{display:flex;align-items:center;gap:9px;padding:9px 11px;border-radius:10px;background:#f3f6fb;font-size:14px;min-height:62px;flex-wrap:wrap}
.trace .swatch{width:18px;height:18px;border-radius:50%;flex:none}
.trace button{min-height:44px;font-size:14px;padding:8px 12px}
.trace input[type=color]{height:44px;width:48px}
.small{font-size:13px}
.tip{font-size:13px;line-height:1.7}
.note{border-left:3px solid #4774e9;padding-left:12px}
.hint{background:#eef5ff;border-radius:12px;padding:12px 14px;color:#25456d;font-size:14px;line-height:1.6}
button:focus-visible,input:focus-visible,select:focus-visible{outline:3px solid #739bef;outline-offset:2px}
@media(max-width:700px){body{padding:10px}h1{font-size:25px}.panel{padding:13px;margin:11px 0}.toolbar{gap:9px}.toolbar>button{flex:1 1 135px}#name{width:100%;flex:1 1 180px}.chart canvas{height:300px}.trace{width:100%}}
@media(orientation:landscape) and (max-height:650px){.chart canvas{height:240px}}
.choice{display:flex;gap:5px;flex-wrap:wrap}.choice button{min-height:46px;padding:9px 15px}.choice button[aria-pressed="true"]{background:#285fe7;color:white;border-color:#285fe7}.setting{display:flex;align-items:center;flex-wrap:wrap;gap:10px;margin-top:12px}.setting strong{min-width:45px;display:inline-block}.setting input[type=range]{width:min(230px,48vw);accent-color:#285fe7;height:36px}.reading{font-size:14px;font-weight:600;color:#285075}.meter{height:10px;background:#e7edf5;border-radius:10px;overflow:hidden;max-width:240px;flex:1;min-width:110px}.meter>div{height:100%;width:0%;background:#26a998;transition:width .15s}.freeze{background:#d9f0ed!important;border-color:#64b9ab!important}.secondary-note{background:#fff8e8;padding:10px 12px;border-radius:10px;font-size:13px;color:#70571a}
</style>
</head>
<body>
<main>
<header>
  <h1>🎵 소리 파형 연구실</h1>
  <p>악기의 소리를 실시간으로 관찰하고, 색을 달리해 여러 파형을 겹쳐 비교해 보세요.</p>
</header>

<section class="panel">
  <div class="hint">① 마이크 시작 → ② 악기 연주 → ③ 캡처 색상 선택 → ④ 파형 캡처 → ⑤ 다른 소리와 비교</div>
  <div class="toolbar" style="margin-top:12px">
    <button id="start" class="primary">🎤 마이크 시작</button>
    <button id="stop" disabled>■ 마이크 중지</button>
    <label>시간축</label><div class="choice" id="durations"><button type="button" data-ms="10" aria-pressed="false">10ms</button><button type="button" data-ms="20" aria-pressed="true">20ms</button><button type="button" data-ms="40" aria-pressed="false">40ms</button></div>
  </div>
  <div class="setting"><label for="gain">화면 진폭 감도</label><input type="range" id="gain" min="1" max="20" value="1" step="1"><strong id="gainValue">1배</strong><span class="small">태블릿에서 파형이 작으면 3~10배부터 조절</span></div>
  <div class="setting"><label for="speed">파형 화면 갱신</label><select id="speed"><option value="100">빠르게 (0.1초)</option><option value="400" selected>보통 (0.4초)</option><option value="1000">천천히 (1초)</option><option value="2000">매우 천천히 (2초)</option></select><button id="freeze" disabled>⏸ 화면 일시정지</button><span class="small">일시정지한 파형을 그대로 캡처할 수 있어요.</span></div>
  <div class="setting"><span class="reading" id="level">입력 진폭(최대): 0.000</span><div class="meter"><div id="meterBar"></div></div></div>
  <p class="secondary-note">감도는 화면의 파형을 확대하는 기능이며 태블릿 마이크 자체의 녹음 감도를 바꾸지는 않습니다. 1보다 크게 설정하면 진폭의 원본 값과 화면 표시값이 다릅니다.</p>
  <div id="status" class="status" role="status"><span class="dot" id="dot"></span>마이크 시작 버튼을 누르고 크롬에서 마이크 사용을 허용하세요.</div>
</section>

<section class="panel">
  <div class="chart"><canvas id="scope" aria-label="시간에 따른 소리 진폭 파형 그래프"></canvas></div>
  <p class="small">가로축: 시간(ms) 0~<span id="durationText">20</span>ms · 세로축: 진폭 -1~+1 (0.5 간격 눈금) · 캡처는 선택한 시간축의 신호를 저장</p>
  <div class="toolbar" style="margin-top:15px">
    <label for="name">소리 이름</label>
    <input id="name" type="text" maxlength="30" placeholder="예: 리코더 높은음">
    <label for="color">캡처 색상</label>
    <input id="color" type="color" value="#ff8c42">
    <button id="capture" class="primary" disabled>📌 파형 캡처</button>
    <button id="clear" class="danger">전체 지우기</button>
    <button id="save">🖼 그래프 PNG 저장</button>
  </div>
  <div class="legend" id="legend"><span class="small">저장된 파형이 아직 없습니다.</span></div>
</section>

<section class="panel">
  <strong>🧪 수업 실험 예시</strong>
  <p class="tip">① 같은 악기의 낮은음과 높은음을 각각 캡처 → 같은 시간축에서 진동 횟수 비교<br>
  ② 같은 음을 약하게/세게 연주해 캡처 → 파동의 진폭 비교<br>
  ③ 서로 다른 악기로 같은 음을 연주해 캡처 → 파형 모양 비교</p>
  <p class="tip note">관찰 팁: 짧은 시간축 화면에서는 소리의 미세한 파동 모양과 진동 횟수를 비교하기 좋습니다. 다만 마이크 입력의 시작 지점에 따라 파형의 위치가 조금씩 달라질 수 있습니다. 세로축은 -1~+1 범위로 표시하며, 눈금은 0.5 간격으로 나타냅니다.</p>
</section>
</main>

<script>
const canvas = document.getElementById('scope');
const ctx = canvas.getContext('2d');
const startBtn = document.getElementById('start');
const stopBtn = document.getElementById('stop');
const capBtn = document.getElementById('capture');
const legend = document.getElementById('legend');
const stat = document.getElementById('status');
const dot = document.getElementById('dot');
const liveToggle = document.getElementById('live');
const colorInput = document.getElementById('color');
const nameInput = document.getElementById('name');

let windowMs = 20;
let displayGain = 1;
let refreshMs = 400;
let frozen = false, displayed = null, lastRefresh = 0;
const MAX_WINDOW_MS = 40;
const palette = ['#ff8c42','#2cc7bd','#f05aa8','#a987ff','#f4cb48','#54a9ff'];
let sampleRate = 48000;
let WINDOW_SAMPLES = Math.round(sampleRate * MAX_WINDOW_MS / 1000);
let ring = new Float32Array(WINDOW_SAMPLES);
let writeIndex = 0;
let filled = 0;
let ac, stream, source, workerNode, fallbackNode, silentGain, raf = 0;
let running = false, starting = false;
let captures = [], serial = 0;

function setStatus(message, on=false){
  stat.lastChild.textContent = message;
  dot.classList.toggle('on', on);
}

function resetBuffer(){
  WINDOW_SAMPLES = Math.max(1, Math.round(sampleRate * MAX_WINDOW_MS / 1000));
  ring = new Float32Array(WINDOW_SAMPLES);
  writeIndex = 0;
  filled = 0;
}

function setSize(){
  const rect = canvas.getBoundingClientRect();
  const d = Math.min(devicePixelRatio || 1, 2);
  canvas.width = Math.max(1, Math.round(rect.width * d));
  canvas.height = Math.max(1, Math.round(rect.height * d));
  render();
}

function ingest(samples){
  if(!running) return;
  for(let i=0;i<samples.length;i++){
    ring[writeIndex] = samples[i];
    writeIndex = (writeIndex + 1) % WINDOW_SAMPLES;
    if(filled < WINDOW_SAMPLES) filled++;
  }
}

const workletSource = `
class WaveCollector extends AudioWorkletProcessor {
  process(inputs){
    const a = inputs[0] && inputs[0][0];
    if(a) this.port.postMessage(a.slice());
    return true;
  }
}
registerProcessor('wave-collector', WaveCollector);
`;

async function startMic(){
  if(starting || running) return;
  starting = true;
  startBtn.disabled = true;
  setStatus(' 마이크 연결 중입니다. 권한을 허용해 주세요.');
  let s = null, context = null;
  try{
    if(!window.isSecureContext || !navigator.mediaDevices?.getUserMedia){
      throw new Error('HTTPS 주소 또는 PC의 localhost에서 접속해 주세요.');
    }
    s = await navigator.mediaDevices.getUserMedia({
      audio:{echoCancellation:false, noiseSuppression:false, autoGainControl:false, channelCount:1},
      video:false
    });
    context = new (window.AudioContext || window.webkitAudioContext)();
    await context.resume();
    ac = context;
    stream = s;
    source = context.createMediaStreamSource(s);
    sampleRate = context.sampleRate || 48000;
    resetBuffer();
    silentGain = context.createGain();
    silentGain.gain.value = 0;

    if(context.audioWorklet && typeof AudioWorkletNode !== 'undefined'){
      try{
        const u = URL.createObjectURL(new Blob([workletSource], {type:'text/javascript'}));
        try{ await context.audioWorklet.addModule(u); }
        finally{ URL.revokeObjectURL(u); }
        workerNode = new AudioWorkletNode(context, 'wave-collector');
        workerNode.port.onmessage = e => ingest(e.data);
        source.connect(workerNode);
        workerNode.connect(silentGain);
      }catch(e){
        workerNode?.disconnect();
        workerNode = null;
      }
    }

    if(!workerNode){
      fallbackNode = context.createScriptProcessor(2048,1,1);
      fallbackNode.onaudioprocess = e => {
        ingest(e.inputBuffer.getChannelData(0));
        e.outputBuffer.getChannelData(0).fill(0);
      };
      source.connect(fallbackNode);
      fallbackNode.connect(silentGain);
    }

    silentGain.connect(context.destination);
    running = true;
    stopBtn.disabled = false;
    capBtn.disabled = false;
    displayed=null;frozen=false;lastRefresh=0;document.getElementById('freeze').disabled=false;document.getElementById('freeze').textContent='⏸ 화면 일시정지';document.getElementById('freeze').classList.remove('freeze');
    setStatus(' 녹음 중 · '+windowMs+'ms 파형을 표시합니다.', true);
    loop();
  }catch(e){
    s?.getTracks().forEach(t => t.stop());
    if(context) await context.close().catch(()=>{});
    ac = null; stream = null; source = null; workerNode = null; fallbackNode = null; silentGain = null;
    const msg = e.name === 'NotAllowedError' ? '마이크 권한이 거부되었습니다. 사이트 설정에서 마이크를 허용해 주세요.'
      : e.name === 'NotFoundError' ? '마이크 장치를 찾을 수 없습니다.'
      : e.name === 'NotReadableError' ? '다른 앱에서 마이크를 사용 중일 수 있습니다.'
      : (e.message || '마이크를 시작할 수 없습니다.');
    setStatus(' ' + msg);
  }finally{
    starting = false;
    startBtn.disabled = running;
  }
}

function stopMic(){
  if(!running) return;
  running = false;
  cancelAnimationFrame(raf);
  stream?.getTracks().forEach(t => t.stop());
  workerNode?.disconnect(); fallbackNode?.disconnect(); source?.disconnect(); silentGain?.disconnect();
  ac?.close().catch(()=>{});
  ac = null; stream = null; source = null; workerNode = null; fallbackNode = null; silentGain = null;
  startBtn.disabled = false;
  document.getElementById('freeze').disabled=true;
  stopBtn.disabled = true;
  capBtn.disabled = true;
  setStatus(' 마이크 중지됨 · 저장된 파형은 계속 비교할 수 있습니다.');
  render();
}

function loop(timestamp=0){
  if(!running) return;
  if(!frozen && (!displayed || timestamp-lastRefresh>=refreshMs)){
    displayed=snapshot();lastRefresh=timestamp;
    render();
    const max=displayed.reduce((m,v)=>Math.max(m,Math.abs(v)),0);
    document.getElementById('level').textContent='입력 진폭(최대): '+max.toFixed(3);
    document.getElementById('meterBar').style.width=Math.min(100,max*100)+'%';
  }
  raf=requestAnimationFrame(loop);
}

function snapshot(){
  const count=Math.max(1,Math.round(sampleRate*windowMs/1000));
  const out=new Float32Array(count);
  const n=Math.min(count,filled);
  for(let i=0;i<n;i++){
    out[count-n+i]=ring[(writeIndex-n+i+WINDOW_SAMPLES)%WINDOW_SAMPLES];
  }
  return out;
}

function drawAxes(){
  const w = canvas.width, h = canvas.height, d = w / Math.max(1, canvas.clientWidth);
  const l = 57*d, r = w - 18*d, t = 20*d, b = h - 43*d;
  ctx.fillStyle = '#0e1a31';
  ctx.fillRect(0,0,w,h);
  ctx.font = `${12*d}px system-ui`;
  ctx.textBaseline = 'middle';
  ctx.lineWidth = d;

  for(let k=0;k<=10;k++){
    const x = l + (r-l) * k / 10;
    ctx.beginPath();
    ctx.strokeStyle = '#35425a';
    ctx.moveTo(x,t); ctx.lineTo(x,b); ctx.stroke();
    ctx.textAlign = 'center';
    ctx.fillStyle = '#d4ddec';
    ctx.fillText(String(k*windowMs/10), x, h - 26*d);
  }

  ctx.textAlign = 'right';
  for(let i=0; i<=4; i++){
    const v = 1 - i * 0.5;
    const y = t + (b-t) * i / 4;
    ctx.beginPath();
    ctx.strokeStyle = (Math.abs(v) < 1e-9) ? '#9aaac4' : '#35425a';
    ctx.moveTo(l,y); ctx.lineTo(r,y); ctx.stroke();
    ctx.fillStyle = '#d4ddec';
    const label = Number.isInteger(v) ? String(v) : String(v);
    ctx.fillText(label, l - 10*d, y);
  }

  ctx.textAlign = 'center';
  ctx.fillText('시간 (ms)', (l+r)/2, h - 9*d);
  ctx.save();
  ctx.translate(13*d, (t+b)/2);
  ctx.rotate(-Math.PI/2);
  ctx.fillText('진폭', 0, 0);
  ctx.restore();

  return {l,r,t,b};
}

function plotLine(samples, color, width=1.5, opacity=1){
  if(!samples || !samples.length) return;
  const {l,r,t,b} = drawAxesBounds;
  const mid = (t+b)/2;
  const half = (b-t)/2;
  const d = canvas.width / Math.max(1, canvas.clientWidth);
  ctx.save();
  ctx.strokeStyle = color;
  ctx.globalAlpha = opacity;
  ctx.lineWidth = width * d;
  ctx.beginPath();
  for(let i=0;i<samples.length;i++){
    const x = l + (r-l) * i / Math.max(1, samples.length - 1);
    let v = samples[i] * displayGain;
    if(v > 1) v = 1;
    if(v < -1) v = -1;
    const y = mid - v * half;
    if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
  }
  ctx.stroke();
  ctx.restore();
}

let drawAxesBounds = null;
function render(){
  if(!canvas.width) return;
  drawAxesBounds = drawAxes();
  for(const item of captures){
    if(item.visible) plotLine(item.data, item.color, 2, 0.82);
  }
  if(displayed){
    plotLine(displayed, '#f8fbff', 1.4, 0.96);
  }
}

function addCapture(){
  if(!running || filled===0) return;
  const color = colorInput.value;
  const name = nameInput.value.trim() || '파형 ' + (++serial);
  captures.push({
    id: Date.now() + Math.random(),
    name,
    color,
    data: displayed ? new Float32Array(displayed) : snapshot(),
    visible: true
  });
  nameInput.value = '';
  colorInput.value = palette[captures.length % palette.length];
  drawLegend();
  render();
  setStatus(' ' + name + ' 캡처 완료 · '+windowMs+'ms의 파형이 겹쳐 표시됩니다.', true);
}

function drawLegend(){
  legend.replaceChildren();
  if(!captures.length){
    const el = document.createElement('span');
    el.className = 'small';
    el.textContent = '저장된 파형이 아직 없습니다.';
    legend.append(el);
    return;
  }
  for(const c of captures){
    const wrap = document.createElement('div');
    wrap.className = 'trace';

    const check = document.createElement('input');
    check.type = 'checkbox';
    check.checked = c.visible;
    check.setAttribute('aria-label', c.name + ' 보기');
    check.onchange = () => { c.visible = check.checked; render(); };

    const sw = document.createElement('span');
    sw.className = 'swatch';
    sw.style.background = c.color;

    const txt = document.createElement('span');
    txt.textContent = c.name;

    const clr = document.createElement('input');
    clr.type = 'color';
    clr.value = c.color;
    clr.title = '저장된 파형 색 바꾸기';
    clr.oninput = () => { c.color = clr.value; sw.style.background = c.color; render(); };

    const del = document.createElement('button');
    del.textContent = '삭제';
    del.onclick = () => { captures = captures.filter(x => x !== c); drawLegend(); render(); };

    wrap.append(check, sw, txt, clr, del);
    legend.append(wrap);
  }
}

startBtn.onclick = startMic;
stopBtn.onclick = stopMic;
capBtn.onclick = addCapture;
document.getElementById('gain').oninput=e=>{
  displayGain=Number(e.target.value);
  document.getElementById('gainValue').textContent=displayGain+'배';render();
};
document.getElementById('speed').onchange=e=>{refreshMs=Number(e.target.value);lastRefresh=0;};
document.getElementById('freeze').onclick=()=>{
  if(!running)return;
  frozen=!frozen;
  const b=document.getElementById('freeze');
  b.textContent=frozen?'▶ 실시간 재개':'⏸ 화면 일시정지';
  b.classList.toggle('freeze',frozen);
  if(!frozen)lastRefresh=0;
  setStatus(frozen?' 화면 일시정지 · 지금 보이는 파형을 캡처하세요.':' 실시간 파형 관찰 중',true);
  render();
};
document.querySelectorAll('#durations button').forEach(b=>b.onclick=()=>{
  const next=Number(b.dataset.ms);
  if(next===windowMs)return;
  if(captures.length&&!confirm('시간축을 바꾸면 기존 캡처를 지웁니다. 계속할까요?'))return;
  captures=[];drawLegend();windowMs=next;displayed=null;lastRefresh=0;
  document.getElementById('durationText').textContent=next;
  document.querySelectorAll('#durations button').forEach(x=>x.setAttribute('aria-pressed',String(x===b)));
  render();
});
document.getElementById('clear').onclick = () => {
  if(captures.length && !confirm('저장된 파형을 모두 삭제할까요?')) return;
  captures = [];
  drawLegend();
  render();
};

document.getElementById('save').onclick = () => {
  render();
  canvas.toBlob(async blob => {
    if(!blob) return;
    const file = new File([blob], '소리_파형_비교.png', {type:'image/png'});
    if(navigator.canShare?.({files:[file]})){
      try{ await navigator.share({files:[file], title:'소리 파형 비교'}); return; }
      catch(e){ if(e.name === 'AbortError') return; }
    }
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.download = file.name;
    a.href = url;
    a.click();
    setTimeout(() => URL.revokeObjectURL(url), 3000);
  }, 'image/png');
};

window.addEventListener('resize', setSize);
window.addEventListener('pagehide', () => { if(running) stopMic(); });
setSize();
</script>
</body>
</html>
