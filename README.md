
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Lavender: Chronos Trinity</title>
    <style>
        :root { --bg: #e0e0f0; --lila-soft: #dcd6f7; --lila-dark: #6c5ce7; --shadow-light: #ffffff; --shadow-dark: #b8b8d1; --text: #444466; }
        body.dark-mode { --bg: #1e1e2f; --lila-soft: #3d3d5c; --lila-dark: #a696c8; --shadow-light: #2a2a40; --shadow-dark: #0f0f1a; --text: #c0c0d0; }
        body { margin:0; padding:0; background:#cfcfe2; font-family:system-ui, sans-serif; color:var(--text); width: 100vw; height: 100vh; overflow: hidden; position: relative; }
        
        /* Contenedor Blindado */
        .app-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; justify-content: center; align-items: center; background: #cfcfe2; z-index: 9999; box-sizing: border-box; padding: 10px; }
        
        .app { width: 100%; max-width: 360px; height: auto; max-height: 98vh; background: var(--bg); border-radius: 35px; padding: 15px; box-sizing: border-box; display: flex; flex-direction: column; gap: 10px; box-shadow: 10px 10px 20px var(--shadow-dark), -8px -8px 20px var(--shadow-light); overflow-y: auto; }
        
        .neu-inset { background:var(--bg); border-radius:15px; box-shadow:inset 3px 3px 6px var(--shadow-dark), inset -3px -3px 6px var(--shadow-light); border:none; padding:10px; box-sizing: border-box; }
        .neu-flat { background:var(--bg); border-radius:15px; border:none; box-shadow:3px 3px 8px var(--shadow-dark), -3px -3px 8px var(--shadow-light); cursor:pointer; color:var(--text); padding:10px; font-size: 10px; font-weight: bold; }
        .neu-flat:active { box-shadow:inset 2px 2px 5px var(--shadow-dark), inset -2px -2px 5px var(--shadow-light); }
        
        .header h1 { margin:0; font-size:20px; color:var(--lila-dark); font-weight:900; text-align:center; }
        .trinity-container { display:flex; align-items:center; justify-content:space-between; margin:2px 0; gap: 5px; }
        .coffee-display { text-align:center; flex-grow:1; display: flex; flex-direction: column; align-items: center; }
        .coffee-icon-main { font-size:45px; animation:steam 4s ease-in-out infinite; filter: opacity(0.8); }
        @keyframes steam { 0%, 100% { transform:translateY(0); } 50% { transform:translateY(-4px); } }
        
        .grid-7 { display:grid; grid-template-columns:repeat(7, 1fr); gap:1px; text-align:center; font-size:9px; }
        .cal-day { padding:6px 0; border-radius:4px; font-size: 8px; cursor: pointer; }
        .cal-day.has-data { color:var(--lila-dark); font-weight:900; text-decoration: underline; }
        .cal-day.selected { background: var(--lila-soft); border: 1px solid var(--lila-dark); }
        
        .overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:var(--bg); z-index:10000; flex-direction:column; padding:20px; box-sizing:border-box; overflow-y: auto; }
        .wrapped-item { display:flex; justify-content:space-between; padding:8px; border-bottom:1px solid var(--shadow-dark); font-size:10px; font-weight:bold; }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="app">
            <div class="header"><h1>Lavender</h1></div>
            
            <div class="neu-inset" style="min-height: 55px; display: flex; flex-direction: column; justify-content: center; text-align: center;">
                <span id="quote" style="font-size: 9px; font-style: italic;">"Cargando ritual..."</span>
                <small id="philosopher" style="color:var(--lila-dark); font-weight:bold; font-size:8px; margin-top:3px;"></small>
            </div>

            <div class="trinity-container">
                <button class="neu-flat" style="width:35px; height:35px; border-radius:50%;" onclick="prevT()">‹</button>
                <div class="coffee-display">
                    <div id="main-icon" class="coffee-icon-main">☕</div>
                    <div id="main-name" style="font-size:9px; font-weight:900; color:var(--lila-dark); text-transform: uppercase;">ESPRESSO</div>
                </div>
                <button class="neu-flat" style="width:35px; height:35px; border-radius:50%;" onclick="nextT()">›</button>
            </div>

            <div style="text-align:center; font-weight:900; font-size:10px; color:var(--lila-dark);">
                <span id="count">0</span> rituales hoy
            </div>

            <input type="text" id="note" class="neu-inset" style="font-size:10px;" placeholder="Nota rápida...">

            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 8px;">
                <button class="neu-flat" onclick="undo()">UNDO</button>
                <button class="neu-flat" style="color:var(--lila-dark);" onclick="add()">REGISTRAR</button>
            </div>

            <div class="neu-inset" style="padding:8px;">
                <div style="display:flex; justify-content:space-between; font-size:10px; font-weight:bold; margin-bottom:5px;">
                    <span id="month-name">Mes</span>
                    <div><span onclick="changeMonth(-1)" style="margin-right:8px; cursor:pointer;">‹</span><span onclick="changeMonth(1)" style="cursor:pointer;">›</span></div>
                </div>
                <div id="calendar" class="grid-7"></div>
                <div id="history-display" style="font-size:8px; margin-top:5px; max-height:40px; overflow-y:auto; border-top:1px dashed var(--shadow-dark);"></div>
            </div>

            <div style="display:grid; grid-template-columns:1fr 1fr; gap:8px;">
                <button class="neu-flat" onclick="openOverlay('codex')">GRIMORIO</button>
                <button class="neu-flat" onclick="openOverlay('wrapped')">WRAPPED</button>
            </div>
        </div>
    </div>

    <div id="codex" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark); font-size:18px;">Grimorio</h2>
        <div id="codex-grid" style="flex-grow:1;"></div>
        <button onclick="closeOverlay('codex')" class="neu-flat">CERRAR</button>
    </div>

    <div id="wrapped" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark); font-size:18px;">Wrapped</h2>
        <div id="wrapped-list" class="neu-inset" style="margin-bottom:10px;"></div>
        <div id="wrapped-peak" class="neu-inset" style="text-align:center; font-size:11px; font-weight:bold; margin-bottom:10px;"></div>
        <div id="wrapped-total" class="neu-inset" style="text-align:center; padding:15px;"></div>
        <button onclick="closeOverlay('wrapped')" class="neu-flat" style="margin-top:10px;">VOLVER</button>
    </div>

    <script>
        /* AQUÍ PEGA LA PARTE 2 QUE TE ENVIARÉ A CONTINUACIÓN */
    </script>
</body>
</html>
const FRASES = { 
    0: [{q:"La existencia es una distracción.", p:"Kafka"}, {q:"Existir es una imperfección.", p:"Cioran"}, {q:"El vacío es libertad.", p:"Sartre"}, {q:"La inacción es reflexión.", p:"Platón"}, {q:"Nacer es comenzar a morir.", p:"Heidegger"}, {q:"Solo sé que no sé nada.", p:"Sócrates"}], 
    1: [{q:"Pienso, luego existo.", p:"Descartes"}, {q:"Sapere Aude.", p:"Kant"}, {q:"La duda es el origen.", p:"Aristóteles"}, {q:"El hombre es medida.", p:"Protágoras"}, {q:"Todo fluye.", p:"Heráclito"}, {q:"La razón es reina.", p:"Spinoza"}], 
    2: [{q:"Somos lo que hacemos.", p:"Aristóteles"}, {q:"Amor Fati.", p:"Nietzsche"}, {q:"La vida es voluntad.", p:"Schopenhauer"}, {q:"El deber es ley.", p:"Kant"}, {q:"Persistencia es clave.", p:"Dalí"}, {q:"El ahora es eterno.", p:"Hegel"}], 
    3: [{q:"Parece imposible.", p:"Mandela"}, {q:"Libertad: responsabilidad.", p:"Sartre"}, {q:"Crea tu destino.", p:"Camus"}, {q:"El poder es acción.", p:"Foucault"}, {q:"Vence al miedo.", p:"Marco Aurelio"}, {q:"El fuego purifica.", p:"Zenón"}], 
    4: [{q:"Todo es real.", p:"Picasso"}, {q:"Realidad es lienzo.", p:"Dalí"}, {q:"Supera al hombre.", p:"Nietzsche"}, {q:"Danza en el caos.", p:"Zarathustra"}, {q:"Eterno retorno.", p:"Nietzsche"}, {q:"La fuerza es vida.", p:"Heráclito"}] 
};

const BASE_DB = {
    'r1':{n:'Espresso',e:'☕'},
    'r2':{n:'Cortado',e:'☕🥛'},
    'r3':{n:'Americano',e:'☕💧'},
    'r4':{n:'Cappuccino',e:'☕☁️'},
    'r5':{n:'Latte',e:'☕🍶'}
};

let logs = JSON.parse(localStorage.getItem('lav_final_logs')) || {};
let activeTrinity = ['r1', 'r2', 'r3'];
let cNavDate = new Date();
let selDateKey = new Date().toLocaleDateString('es-ES');
let cIdx = 0;

function update() {
    localStorage.setItem('lav_final_logs', JSON.stringify(logs));
    const day = logs[selDateKey] || [];
    const count = day.length;
    document.getElementById('count').innerText = count;

    const level = Math.min(count, 4);
    const pick = FRASES[level][Math.floor(Math.random() * 6)];
    document.getElementById('quote').innerText = `"${pick.q}"`;
    document.getElementById('philosopher').innerText = `- ${pick.p}`;

    const r = BASE_DB[activeTrinity[cIdx]];
    document.getElementById('main-icon').innerHTML = r.e;
    document.getElementById('main-name').innerText = r.n;

    renderCal();
    showDay();
}

function nextT() { cIdx = (cIdx + 1) % 3; update(); }
function prevT() { cIdx = (cIdx - 1 + 3) % 3; update(); }

function add() {
    const now = new Date();
    if (!logs[selDateKey]) logs[selDateKey] = [];
    logs[selDateKey].push({
        id: activeTrinity[cIdx],
        h: now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
        t: now.getHours(),
        n: document.getElementById('note').value
    });
    document.getElementById('note').value = '';
    update();
}

function undo() {
    if (logs[selDateKey]?.length > 0) {
        logs[selDateKey].pop();
        update();
    }
}

function renderCal() {
    const cal = document.getElementById('calendar');
    cal.innerHTML = '';
    const y = cNavDate.getFullYear(), m = cNavDate.getMonth();
    document.getElementById('month-name').innerText = new Intl.DateTimeFormat('es-ES', { month: 'short', year: 'numeric' }).format(cNavDate);
    
    const start = (new Date(y, m, 1).getDay() + 6) % 7;
    const days = new Date(y, m + 1, 0).getDate();

    for (let i = 0; i < start; i++) cal.innerHTML += '<div></div>';
    for (let i = 1; i <= days; i++) {
        const dk = new Date(y, m, i).toLocaleDateString('es-ES');
        cal.innerHTML += `<div class="cal-day ${logs[dk]?.length ? 'has-data' : ''} ${dk === selDateKey ? 'selected' : ''}" onclick="selDateKey='${dk}';update()">${i}</div>`;
    }
}

function showDay() {
    const d = logs[selDateKey] || [];
    document.getElementById('history-display').innerHTML = d.map(l => 
        `<span><b>${l.h}</b> ${BASE_DB[l.id].n} ${l.n ? '(' + l.n + ')' : ''}</span>`
    ).join(' | ');
}

function renderWrapped() {
    const l = document.getElementById('wrapped-list'), tDiv = document.getElementById('wrapped-total'), pDiv = document.getElementById('wrapped-peak');
    l.innerHTML = ''; let s = {}, t = 0, h = {};
    
    Object.values(logs).forEach(day => day.forEach(rit => {
        s[rit.id] = (s[rit.id] || 0) + 1;
        h[rit.t] = (h[rit.t] || 0) + 1;
        t++;
    }));

    Object.keys(s).forEach(id => {
        l.innerHTML += `<div class="wrapped-item"><span>${BASE_DB[id].e} ${BASE_DB[id].n}</span><span>x${s[id]}</span></div>`;
    });

    let peak = Object.entries(h).sort((a, b) => b[1] - a[1])[0];
    pDiv.innerText = peak ? `HORA PICO: ${peak[0]}:00 hs` : "SIN DATOS";
    tDiv.innerHTML = `<small>TOTAL RITUALES</small><h1 style="margin:0;color:var(--lila-dark);font-size:45px;">${t}</h1>`;
}

function openOverlay(id) { document.getElementById(id).style.display = 'flex'; if (id === 'wrapped') renderWrapped(); }
function closeOverlay(id) { document.getElementById(id).style.display = 'none'; }
function changeMonth(d) { cNavDate.setMonth(cNavDate.getMonth() + d); renderCal(); }

// Inicio
update();
