# Terminal_display
Profile terminal 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>WKT12 Terminal Monitor</title>
<style>
  body {
    margin: 0;
    background: #02040a;
    font-family: system-ui, sans-serif;
    color: #e5f7ff;
  }
  .terminal {
    max-width: 960px;
    margin: 40px auto;
    padding: 24px;
    background: radial-gradient(circle at top left, #101522 0, #05060a 60%);
    border: 1px solid #1b2338;
    box-shadow: 0 0 40px rgba(0,0,0,0.8);
  }
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }
  .header-left {
    font-size: 14px;
    letter-spacing: 0.08em;
    color: #8fb9ff;
  }
  .header-right span {
    display: inline-block;
    margin-left: 12px;
    font-size: 12px;
    color: #6f8bbf;
  }
  .pill {
    display: inline-block;
    padding: 2px 8px;
    border-radius: 999px;
    background: #0b1220;
    border: 1px solid #24345a;
    color: #9fd3ff;
  }
  .section-title {
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 0.16em;
    color: #6f8bbf;
    margin: 18px 0 8px;
  }
  .grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 16px;
  }
  .card {
    background: #050814;
    border: 1px solid #1b2338;
    padding: 12px;
    font-size: 12px;
  }
  .label-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 6px;
    color: #9fb4d9;
  }
  .value {
    color: #e5f7ff;
    font-weight: 600;
  }
  .bar {
    position: relative;
    height: 8px;
    border-radius: 999px;
    background: #0b1220;
    overflow: hidden;
  }
  .bar-fill {
    position: absolute;
    height: 100%;
    border-radius: 999px;
    background: linear-gradient(90deg, #00e5ff, #ff00aa);
    width: 40%;
    transition: width 0.4s ease-out;
  }
  .battery-shell {
    position: relative;
    height: 10px;
    border-radius: 3px;
    border: 1px solid #3b4a70;
    margin-top: 4px;
    background: #050814;
  }
  .battery-fill {
    position: absolute;
    height: 100%;
    background: linear-gradient(90deg, #00ff99, #ffdd00);
    width: 70%;
    transition: width 0.4s ease-out;
  }
  .battery-tip {
    position: absolute;
    right: -6px;
    top: 2px;
    width: 4px;
    height: 6px;
    background: #3b4a70;
    border-radius: 1px;
  }
  .log {
    margin-top: 20px;
    background: #050814;
    border: 1px solid #1b2338;
    padding: 12px;
    font-family: "SF Mono", Menlo, monospace;
    font-size: 11px;
    color: #8fb9ff;
    max-height: 160px;
    overflow-y: auto;
  }
  .log-line::before {
    content: "wkt12@intel:~$ ";
    color: #3ea8ff;
  }
  .accent {
    color: #ff00aa;
  }
</style>
</head>
<body>
<div class="terminal">
  <div class="header">
    <div class="header-left">WKT12 INTEL MONITOR</div>
    <div class="header-right">
      <span class="pill">SESSION: LIVE</span>
      <span>NODE: soc‑edge‑01</span>
      <span>MODE: DARK</span>
    </div>
  </div>

  <div class="section-title">SYSTEM METRICS</div>
  <div class="grid">
    <div class="card">
      <div class="label-row">
        <span>CPU Load</span>
        <span class="value" id="cpu-val">37%</span>
      </div>
      <div class="bar">
        <div class="bar-fill" id="cpu-bar"></div>
      </div>
    </div>
    <div class="card">
      <div class="label-row">
        <span>Memory</span>
        <span class="value" id="mem-val">61%</span>
      </div>
      <div class="bar">
        <div class="bar-fill" id="mem-bar"></div>
      </div>
    </div>
    <div class="card">
      <div class="label-row">
        <span>Battery</span>
        <span class="value" id="bat-val">82%</span>
      </div>
      <div class="battery-shell">
        <div class="battery-fill" id="bat-bar"></div>
        <div class="battery-tip"></div>
      </div>
    </div>
  </div>

  <div class="section-title">NETWORK & IO</div>
  <div class="grid">
    <div class="card">
      <div class="label-row">
        <span>RX / TX</span>
        <span class="value" id="net-val">312 / 128 kbps</span>
      </div>
      <div class="bar">
        <div class="bar-fill" id="net-bar"></div>
      </div>
    </div>
    <div class="card">
      <div class="label-row">
        <span>Disk IO</span>
        <span class="value" id="io-val">41 ops/s</span>
      </div>
      <div class="bar">
        <div class="bar-fill" id="io-bar"></div>
      </div>
    </div>
    <div class="card">
      <div class="label-row">
        <span>Temp</span>
        <span class="value" id="temp-val">54°C</span>
      </div>
      <div class="bar">
        <div class="bar-fill" id="temp-bar"></div>
      </div>
    </div>
  </div>

  <div class="section-title">EVENT LOG</div>
  <div class="log" id="log">
    <div class="log-line">boot sequence <span class="accent">wkt12‑monitor</span> initialized</div>
    <div class="log-line">binding sensors: cpu, mem, net, io, temp, battery</div>
    <div class="log-line">starting fake telemetry stream...</div>
  </div>
</div>

<script>
  function rand(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  const metrics = [
    { idVal: 'cpu-val', idBar: 'cpu-bar', min: 12, max: 93, suffix: '%' },
    { idVal: 'mem-val', idBar: 'mem-bar', min: 24, max: 88, suffix: '%' },
    { idVal: 'bat-val', idBar: 'bat-bar', min: 40, max: 100, suffix: '%' },
    { idVal: 'net-val', idBar: 'net-bar', custom: true },
    { idVal: 'io-val', idBar: 'io-bar', min: 10, max: 120, suffix: ' ops/s' },
    { idVal: 'temp-val', idBar: 'temp-bar', min: 40, max: 78, suffix: '°C' }
  ];

  const logEl = document.getElementById('log');

  function updateMetrics() {
    metrics.forEach(m => {
      const valEl = document.getElementById(m.idVal);
      const barEl = document.getElementById(m.idBar);
      if (!valEl || !barEl) return;

      if (m.custom) {
        const rx = rand(80, 600);
        const tx = rand(40, 300);
        valEl.textContent = rx + ' / ' + tx + ' kbps';
        const pct = Math.min(100, (rx + tx) / 10);
        barEl.style.width = pct + '%';
      } else {
        const v = rand(m.min, m.max);
        valEl.textContent = v + m.suffix;
        barEl.style.width = v + '%';
      }
    });

    const msgs = [
      'probe: cpu=' + document.getElementById('cpu-val').textContent +
        ' mem=' + document.getElementById('mem-val').textContent,
      'net tick: ' + document.getElementById('net-val').textContent,
      'io sample: ' + document.getElementById('io-val').textContent +
        ' temp=' + document.getElementById('temp-val').textContent,
      'battery status: ' + document.getElementById('bat-val').textContent
    ];
    const msg = msgs[rand(0, msgs.length - 1)];
    const line = document.createElement('div');
    line.className = 'log-line';
    line.textContent = msg;
    logEl.appendChild(line);
    logEl.scrollTop = logEl.scrollHeight;
  }

  setInterval(updateMetrics, 1200);
</script>
</body>
</html>
