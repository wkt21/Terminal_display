<div align="center">

<!-- WKT12 TERMINAL DISPLAY -->
<style>
.wkt12-term {
  width: 720px;
  background: #05060a;
  border: 1px solid #1a1f2b;
  padding: 20px;
  font-family: monospace;
  color: #c7d9ff;
  box-shadow: 0 0 40px rgba(0,0,0,0.7);
}
.wkt12-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  color: #7aa2ff;
}
.wkt12-pill {
  padding: 2px 8px;
  border: 1px solid #2a3550;
  border-radius: 6px;
  font-size: 12px;
}
.wkt12-grid {
  display: grid;
  grid-template-columns: repeat(3,1fr);
  gap: 12px;
  margin-top: 12px;
}
.wkt12-card {
  background: #0a0d14;
  border: 1px solid #1a1f2b;
  padding: 10px;
  font-size: 12px;
}
.wkt12-label {
  display: flex;
  justify-content: space-between;
  margin-bottom: 6px;
  color: #9fb4d9;
}
.wkt12-value {
  color: #e5f7ff;
  font-weight: bold;
}
.wkt12-bar {
  height: 8px;
  background: #0f1625;
  border-radius: 4px;
  overflow: hidden;
}
.wkt12-fill {
  height: 100%;
  background: linear-gradient(90deg,#00e5ff,#ff00aa);
  width: 40%;
  transition: width 0.4s ease-out;
}
.wkt12-log {
  margin-top: 20px;
  background: #0a0d14;
  border: 1px solid #1a1f2b;
  padding: 10px;
  height: 120px;
  overflow-y: auto;
  font-size: 11px;
  color: #7aa2ff;
}
.wkt12-log-line::before {
  content: "wkt12@intel:~$ ";
  color: #3ea8ff;
}
</style>

<div class="wkt12-term">
  <div class="wkt12-header">
    <div>WKT12 TERMINAL MONITOR</div>
    <div class="wkt12-pill">SESSION ACTIVE</div>
  </div>

  <div class="wkt12-grid">
    <div class="wkt12-card">
      <div class="wkt12-label"><span>CPU</span><span class="wkt12-value" id="cpu-val">37%</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="cpu-bar"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>RAM</span><span class="wkt12-value" id="ram-val">61%</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="ram-bar"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>NET</span><span class="wkt12-value" id="net-val">312kbps</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="net-bar"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>TEMP</span><span class="wkt12-value" id="temp-val">54°C</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="temp-bar"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>DISK</span><span class="wkt12-value" id="disk-val">41 ops/s</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="disk-bar"></div></div>
    </div>

    <div class="wkt12-card">
      <div class="wkt12-label"><span>BATT</span><span class="wkt12-value" id="batt-val">82%</span></div>
      <div class="wkt12-bar"><div class="wkt12-fill" id="batt-bar"></div></div>
    </div>
  </div>

  <div class="wkt12-log" id="wkt12-log">
    <div class="wkt12-log-line">initializing wkt12 telemetry...</div>
    <div class="wkt12-log-line">binding sensors: cpu ram net temp disk batt</div>
    <div class="wkt12-log-line">stream online</div>
  </div>
</div>

<script>
function r(min,max){return Math.floor(Math.random()*(max-min+1))+min;}

function update(idVal,idBar,min,max,suffix){
  const v=r(min,max);
  document.getElementById(idVal).textContent=v+suffix;
  document.getElementById(idBar).style.width=v+"%";
}

function tick(){
  update("cpu-val","cpu-bar",12,93,"%");
  update("ram-val","ram-bar",24,88,"%");
  update("temp-val","temp-bar",40,78,"°C");
  update("disk-val","disk-bar",10,120," ops/s");
  update("batt-val","batt-bar",40,100,"%");

  const rx=r(80,600), tx=r(40,300);
  document.getElementById("net-val").textContent=rx+"kbps";
  document.getElementById("net-bar").style.width=Math.min(100,(rx/10))+"%";

  const log=document.getElementById("wkt12-log");
  const msgs=[
    "cpu="+document.getElementById("cpu-val").textContent+
    " ram="+document.getElementById("ram-val").textContent,
    "net="+document.getElementById("net-val").textContent,
    "temp="+document.getElementById("temp-val").textContent,
    "disk="+document.getElementById("disk-val").textContent,
    "battery="+document.getElementById("batt-val").textContent
  ];
  const line=document.createElement("div");
  line.className="wkt12-log-line";
  line.textContent=msgs[r(0,msgs.length-1)];
  log.appendChild(line);
  log.scrollTop=log.scrollHeight;
}

setInterval(tick,1200);
</script>

</div>
