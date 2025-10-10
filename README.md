
<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>cloudforge-water｜三層水庫九馬達面板</title>
  <style>
    :root{
      --bg:#0b1020; --card:#121a35; --ink:#e8ecff; --muted:#9fb0ff;
      --red:#ff3b30; --yellow:#ffd60a; --blue:#0aa0ff; --green:#22c55e;
      --accent:#5b7cff; --line:#25335f;
    }
    *{box-sizing:border-box} body{margin:0;background:linear-gradient(180deg,#0a0f22,#0b1020 40%,#070b17);
      color:var(--ink);font-family:system-ui, "Noto Sans TC", Segoe UI, Roboto, Helvetica, Arial, sans-serif}
    header{padding:20px 16px;border-bottom:1px solid var(--line);position:sticky;top:0;background:rgba(7,11,23,.9);backdrop-filter:saturate(140%) blur(8px)}
    h1{margin:0;font-size:20px;letter-spacing:.3px}
    .wrap{max-width:1100px;margin:0 auto;padding:18px}
    .panel{background:var(--card);border:1px solid var(--line);border-radius:14px;padding:16px 14px;margin-bottom:16px}
    .panel h2{margin:0 0 10px 0;font-size:16px;color:#cfe0ff}
    .cfg-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
    .cfg-grid label{font-size:12px;color:var(--muted)}
    .cfg-grid input{width:100%;padding:10px;border-radius:10px;border:1px solid #3a4a86;background:#0e1530;color:var(--ink)}
    .req{border-color:#ff5b5b !important; box-shadow:0 0 0 2px rgba(255,91,91,.2) inset}
    .hint{font-size:12px;color:#ffb4b4;margin-top:6px}
    .btn{cursor:pointer;border:0;border-radius:10px;padding:10px 14px;color:#071021;background:linear-gradient(90deg,#9ac0ff,#7aa0ff);font-weight:600}
    .btn.ghost{background:#111a39;color:#cfe0ff;border:1px solid #384a86}
    .flex{display:flex;gap:8px;flex-wrap:wrap}
    /* LAYERS */
    .layers{display:grid;grid-template-columns:repeat(3,1fr);gap:14px}
    @media (max-width:900px){.layers{grid-template-columns:1fr}}
    .layer{background:#0f1733;border:1px solid var(--line);border-radius:14px;padding:12px}
    .layer h3{margin:0 0 8px 0;font-size:15px;color:#d8e4ff}
    .beacons{display:flex;gap:12px;margin-bottom:8px}
    /* LEDs */
    .led{width:12px;height:12px;border-radius:50%;background:#333;box-shadow:0 0 0 2px #000 inset, 0 0 0px rgba(0,0,0,.2)}
    .led.red.on{background:var(--red);box-shadow:0 0 0 2px #000 inset, 0 0 10px var(--red), 0 0 22px var(--red)}
    .led.yellow.on{background:var(--yellow);box-shadow:0 0 0 2px #000 inset, 0 0 10px var(--yellow), 0 0 22px var(--yellow)}
    .led.blue.on{background:var(--blue);box-shadow:0 0 0 2px #000 inset, 0 0 10px var(--blue), 0 0 22px var(--blue)}
    .led.green.on{background:var(--green);box-shadow:0 0 0 2px #000 inset, 0 0 10px var(--green), 0 0 22px var(--green)}
    /* Big beacons (溢水/缺水) */
    .beacon{display:flex;align-items:center;gap:8px;padding:8px 10px;border-radius:10px;background:#0c1330;border:1px dashed #2a3c77}
    .beacon .dot{width:18px;height:18px;border-radius:50%;background:#333}
    .beacon.red .dot.on{background:var(--red);animation:blink .6s infinite}
    .beacon.yellow .dot.on{background:var(--yellow);animation:blink 1.2s infinite}
    .label{font-size:12px;color:#a9bbff}
    @keyframes blink{50%{filter:brightness(2) drop-shadow(0 0 10px currentColor)}}
    /* motors grid */
    .motors{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
    .motor{background:#121a3e;border:1px solid #2a3a6d;border-radius:12px;padding:10px}
    .motor .top{display:flex;align-items:center;justify-content:space-between;margin-bottom:8px}
    .tag{font-size:12px;color:#8fb3ff;background:#0a1233;border:1px solid #2a3c77;border-radius:8px;padding:3px 8px}
    .state{display:flex;align-items:center;gap:8px}
    .pill{font-size:11px;padding:3px 8px;border-radius:999px;border:1px solid #2a3c77;color:#cfe0ff;background:#0a1233}
    .ctrl{display:flex;gap:6px}
    .ctrl button{flex:1;border:1px solid #34478a;background:#0d1540;color:#cfe0ff;padding:6px 8px;border-radius:8px;cursor:pointer;font-size:12px}
    .ctrl button.active{outline:2px solid #7aa0ff}
    footer{opacity:.7;font-size:12px;text-align:center;margin:18px 0}
  </style>
</head>
<body>
  <header>
    <h1>cloudforge-water｜三層水庫九馬達（GitHub Pages）</h1>
  </header>

  <div class="wrap">
    <!-- 設定區 -->
    <section class="panel">
      <h2>連線設定（可先空著，支援離線模擬）</h2>
      <div class="cfg-grid">
        <div>
          <label>MQTT WebSocket URL（例：wss://broker.example.com:8083/mqtt）</label>
          <input id="mqttUrl" class="req" placeholder="【必填】若要連線 MQTT，請填這裡">
          <div class="hint">↑ 若你只想先看畫面與手動模擬，可先留空。</div>
        </div>
        <div>
          <label>MQTT 使用者名稱（可留空）</label>
          <input id="mqttUser" placeholder="（選填）">
        </div>
        <div>
          <label>MQTT 密碼（可留空）</label>
          <input id="mqttPass" type="password" placeholder="（選填）">
        </div>
        <div>
          <label>Blynk Token（備用，若你要接 Blynk）</label>
          <input id="blynkToken" class="req" placeholder="【必填】要接 Blynk 才需要填">
        </div>
      </div>
      <div class="flex" style="margin-top:10px">
        <button class="btn" id="saveCfg">儲存設定</button>
        <button class="btn ghost" id="clearCfg">清除</button>
      </div>
    </section>

    <!-- 模擬控制提示 -->
    <section class="panel">
      <h2>狀態說明（本頁的顯示規格）</h2>
      <div style="font-size:14px;color:#cfe0ff;line-height:1.7">
        <b>每顆馬達</b>：<span class="pill">🔴 紅＝啟動</span>　<span class="pill">🔵 藍＝休息</span>　<span class="pill">🟡 黃＝故障</span><br/>
        <b>每個水庫</b>：<span class="pill">🔴 大紅燈＝溢水（快閃）</span>　<span class="pill">🟡 大黃燈＝缺水（慢閃）</span>
      </div>
    </section>

    <!-- 三層水庫 -->
    <section class="layers" id="layers"></section>

    <footer>© cloudforge-water · GitHub Pages 版面板（僅前端示範，之後可接 MQTT/Blynk）</footer>
  </div>

  <script>
    /* =========================
       基本資料結構：三層 × 三馬達
       ========================= */
    const LAYERS = [
      { id:"H", name:"高層水庫（觀天下 A）" },
      { id:"M", name:"中層水庫（57 巷中繼）" },
      { id:"L", name:"低層水庫（車子路）" }
    ];
    const DEFAULT_STATE = {
      // 大燈：false=關、true=亮（會自動閃爍）
      overflow:false, // 大紅
      low:false,      // 大黃
      motors:[
        {id:1, state:"rest"}, // run/rest/fault
        {id:2, state:"rest"},
        {id:3, state:"rest"},
      ]
    };
    const db = {};
    LAYERS.forEach(l=>db[l.id]=structuredClone(DEFAULT_STATE));

    /* =============== UI 生成 =============== */
    const $layers = document.getElementById('layers');
    function ledDot(color, on){return `<span class="led ${color} ${on?'on':''}"></span>`}
    function motorCard(layerId, m){
      const st = m.state;
      const rOn = st==="run", yOn=st==="fault", bOn=st==="rest";
      return `
      <div class="motor" data-layer="${layerId}" data-motor="${m.id}">
        <div class="top">
          <span class="tag">馬達 ${m.id}</span>
          <div class="state">
            ${ledDot('red', rOn)} ${ledDot('yellow', yOn)} ${ledDot('blue', bOn)}
          </div>
        </div>
        <div class="ctrl">
          <button data-set="run"   class="${rOn?'active':''}">啟動(紅)</button>
          <button data-set="rest"  class="${bOn?'active':''}">休息(藍)</button>
          <button data-set="fault" class="${yOn?'active':''}">故障(黃)</button>
        </div>
      </div>`;
    }
    function layerBlock(l){
      const d = db[l.id];
      return `
      <div class="layer" data-layer="${l.id}">
        <h3>${l.name}</h3>
        <div class="beacons">
          <div class="beacon red" data-bc="overflow">
            <span class="dot ${d.overflow?'on':''}"></span>
            <span class="label">大紅燈（溢水）</span>
          </div>
          <div class="beacon yellow" data-bc="low">
            <span class="dot ${d.low?'on':''}"></span>
            <span class="label">大黃燈（缺水）</span>
          </div>
        </div>
        <div class="motors">
          ${d.motors.map(m=>motorCard(l.id, m)).join('')}
        </div>
      </div>`;
    }
    function render(){
      $layers.innerHTML = LAYERS.map(layerBlock).join('');
    }
    render();

    /* =============== 互動事件（本地模擬） =============== */
    // 切換馬達狀態
    $layers.addEventListener('click', (e)=>{
      const btn = e.target.closest('button[data-set]');
      if(!btn) return;
      const box = btn.closest('.motor');
      const layerId = box.dataset.layer;
      const motorId = +box.dataset.motor;
      db[layerId].motors.find(m=>m.id===motorId).state = btn.dataset.set;
      render();
    });
    // 切換大燈
    $layers.addEventListener('click', (e)=>{
      const bc = e.target.closest('.beacon');
      if(!bc) return;
      const layerId = bc.closest('.layer').dataset.layer;
      const key = bc.dataset.bc;
      db[layerId][key] = !db[layerId][key];
      render();
    });

    /* =============== 設定儲存（localStorage） =============== */
    const $mqttUrl = document.getElementById('mqttUrl');
    const $mqttUser= document.getElementById('mqttUser');
    const $mqttPass= document.getElementById('mqttPass');
    const $blynk   = document.getElementById('blynkToken');
    const KEY = 'cloudforge-water.cfg';
    // 還原
    try{
      const raw = localStorage.getItem(KEY);
      if(raw){
        const cfg = JSON.parse(raw);
        $mqttUrl.value = cfg.mqttUrl||"";
        $mqttUser.value= cfg.mqttUser||"";
        $mqttPass.value= cfg.mqttPass||"";
        $blynk.value   = cfg.blynk||"";
      }
    }catch{}
    document.getElementById('saveCfg').onclick=()=>{
      const cfg = {
        mqttUrl:$mqttUrl.value.trim(),
        mqttUser:$mqttUser.value.trim(),
        mqttPass:$mqttPass.value,
        blynk:$blynk.value.trim()
      };
      localStorage.setItem(KEY, JSON.stringify(cfg));
      alert('設定已儲存（存在此裝置的瀏覽器）');
    };
    document.getElementById('clearCfg').onclick=()=>{
      localStorage.removeItem(KEY);
      $mqttUrl.value=$mqttUser.value=$mqttPass.value=$blynk.value="";
    };

    /* ===============（預留）未來要接 MQTT/Blynk 時用 =============== */
    // 你之後只要在這裡初始化 MQTT 客戶端（如 Paho 或 MQTT.js）
    // 並把遠端狀態映射到 db[] → render() 即可。
    // 範例 topic 規劃（自行調整）：
    // cloudforge/water/<layer:{H|M|L}>/motor/<1|2|3>/state   // run|rest|fault
    // cloudforge/water/<layer>/overflow                      // true|false
    // cloudforge/water/<layer>/low                           // true|false
  </script>
</body>
</html>
從我的iPhone傳送
