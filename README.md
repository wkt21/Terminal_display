<div align="center">

<!-- WKT12 TERMINAL DISPLAY (updated to look like a real terminal) -->
<style>
:root{--bg:#000;--card:#020202;--panel:#071109;--text:#33ff66;--muted:#8fbf9a;--accent:#39ff14}
.wkt12-term{
  width:760px;max-width:95%;background:var(--bg);border-radius:6px;border:1px solid #051005;padding:18px;font-family:'SFMono-Regular',Menlo,Consolas,Monaco,monospace;color:var(--text);box-shadow:0 10px 50px rgba(0,0,0,0.8);
}
.wkt12-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px}
.wkt12-title{font-weight:700;color:var(--muted)}
.wkt12-pill{padding:4px 10px;border:1px solid rgba(57,255,20,0.12);border-radius:999px;font-size:12px;color:var(--muted);background:rgba(57,255,20,0.02)}
.wkt12-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;margin-top:8px}
.wkt12-card{background:transparent;border:1px solid rgba(57,255,20,0.06);padding:10px;font-size:12px;border-radius:4px}
.wkt12-label{display:flex;justify-content:space-between;margin-bottom:8px;color:var(--muted);font-size:12px}
.wkt12-value{color:var(--text);font-weight:700}
.wkt12-bar{height:10px;background:rgba(57,255,20,0.02);border-radius:6px;overflow:hidden;border:1px solid rgba(57,255,20,0.02)}
.wkt12-fill{height:100%;background:linear-gradient(90deg,rgba(57,255,20,0.16),var(--accent));width:40%;transition:width 0.6s cubic-bezier(.2,.8,.2,1)}
.wkt12-log{margin-top:16px;background:transparent;border:1px solid rgba(57,255,20,0.04);padding:12px;height:220px;overflow-y:auto;font-size:13px;color:var(--text);text-align:left}
.wkt12-log-line{display:block;padding:2px 0;white-space:pre-wrap}
.wkt12-log-line::before{content:"wkt12@intel:~$ ";color:#9cff9a;font-weight:600;margin-right:6px}
/* make values monospace and slightly brighter */
.wkt12-value, .wkt12-log{font-family:Menlo,Monaco,Consolas,monospace}
/* responsive */
@media (max-width:600px){.wkt12-grid{grid-template-columns:repeat(2,1fr)}}
</style>

<div class="wkt12-term" role="region" aria-label="Terminal monitor">
  <div class="wkt12-header">
    <div class="wkt12-title">WKT12 TERMINAL MONITOR</div>
    <div class="wkt12-pill">SESSION ACTIVE</div>
  </div>

  <div class="wkt12-grid">
    <div class="wkt12-card">
      <div class="wkt12-label"><span>CPU</span><span class="wkt12-value" id="cpu-val">37%</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="cpu-bar" style="width:37%"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>RAM</span><span class="wkt12-value" id="ram-val">61%</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="ram-bar" style="width:61%"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>NET</span><span class="wkt12-value" id="net-val">312kbps</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="net-bar" style="width:31%"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>TEMP</span><span class="wkt12-value" id="temp-val">54°C</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="temp-bar" style="width:50%"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>DISK</span><span class="wkt12-value" id="disk-val">41 ops/s</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="disk-bar" style="width:35%"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>BATT</span><span class="wkt12-value" id="batt-val">82%</span></div>
      <div class="wkt12-bar" aria-hidden="true"><div class="wkt12-fill" id="batt-bar" style="width:82%"></div></div>
    </div>
  </div>

  <div class="wkt12-log" id="wkt12-log" aria-live="polite">
    <div class="wkt12-log-line">initializing wkt12 telemetry...</div>
    <div class="wkt12-log-line">binding sensors: cpu ram net temp disk batt</div>
    <div class="wkt12-log-line">stream online</div>
  </div>
</div>

<script>
(function(){
  function r(min,max){return Math.floor(Math.random()*(max-min+1))+min;}

  // returns percent (0-100) scaled from a value between min..max
  function toPercent(v,min,max){ if(max<=min) return 0; return Math.max(0, Math.min(100, Math.round((v-min)/(max-min)*100))); }

  function updateTextAndBar(valId, barId, min, max, suffix, rawValue){
    const v = (typeof rawValue === 'number')? rawValue : r(min,max);
    const percent = toPercent(v,min,max);
    document.getElementById(valId).textContent = v + (suffix||'');
    const bar = document.getElementById(barId);
    if(bar) bar.style.width = percent+"%";
  }

  function tick(){
    // CPU & RAM & BATT are percentages
    updateTextAndBar('cpu-val','cpu-bar',0,100,'%', r(12,93));
    updateTextAndBar('ram-val','ram-bar',0,100,'%', r(24,88));
    updateTextAndBar('batt-val','batt-bar',0,100,'%', r(40,100));

    // TEMP: map 0-100C but show degrees
    const tempVal = r(35,78);
    updateTextAndBar('temp-val','temp-bar',0,100,'°C', tempVal);

    // DISK: ops/s, map arbitrary 0..200 -> percent
    const diskVal = r(5,140);
    updateTextAndBar('disk-val','disk-bar',0,200,' ops/s', diskVal);

    // NET: kbps (rx). Map 0..1000kbps
    const rx = r(30,900);
    document.getElementById('net-val').textContent = rx + 'kbps';
    document.getElementById('net-bar').style.width = Math.min(100, Math.round(rx/10)) + '%';

    // append a log line
    const log = document.getElementById('wkt12-log');
    const msgs = [
      'cpu=' + document.getElementById('cpu-val').textContent + ' ram=' + document.getElementById('ram-val').textContent,
      'net=' + document.getElementById('net-val').textContent,
      'temp=' + document.getElementById('temp-val').textContent,
      'disk=' + document.getElementById('disk-val').textContent,
      'battery=' + document.getElementById('batt-val').textContent
    ];
    const line = document.createElement('div');
    line.className = 'wkt12-log-line';
    line.textContent = msgs[r(0,msgs.length-1)];
    log.appendChild(line);
    // keep a reasonable log length
    if(log.children.length > 200) log.removeChild(log.children[0]);
    log.scrollTop = log.scrollHeight;
  }

  // first tick to initialize
  tick();
  setInterval(tick,1200);
})();
</script>

</div>
