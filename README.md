<html lang="es">
<head>
    <meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Lavender: Chronos Trinity V2</title>
    <style>
        :root { --bg: #e0e0f0; --lila-soft: #dcd6f7; --lila-dark: #6c5ce7; --shadow-light: #ffffff; --shadow-dark: #b8b8d1; --text: #444466; }
        body.dark-mode { --bg: #1e1e2f; --lila-soft: #3d3d5c; --lila-dark: #a696c8; --shadow-light: #2a2a40; --shadow-dark: #0f0f1a; --text: #c0c0d0; }
        
        /* Bloqueo de texto externo */
        body { margin:0; background:#cfcfe2; font-family:system-ui, sans-serif; color:var(--text); width:100vw; height:100vh; overflow:hidden; position:relative; }
        .app-container { position:absolute; inset:0; display:flex; justify-content:center; align-items:center; background:#cfcfe2; z-index:9999; padding:10px; box-sizing:border-box; }

        .app { width:100%; max-width:360px; background:var(--bg); border-radius:40px; padding:20px; box-sizing:border-box; display:flex; flex-direction:column; gap:12px; box-shadow:15px 15px 30px var(--shadow-dark), -12px -12px 30px var(--shadow-light); max-height: 98vh; overflow-y: auto; }
        
        .neu-inset { background:var(--bg); border-radius:18px; box-shadow:inset 5px 5px 10px var(--shadow-dark), inset -5px -5px 10px var(--shadow-light); }
        .neu-flat { background:var(--bg); border-radius:18px; border:none; box-shadow:5px 5px 10px var(--shadow-dark), -5px -5px 10px var(--shadow-light); cursor:pointer; color:var(--text); outline:none; }
        .neu-flat:active { box-shadow:inset 3px 3px 6px var(--shadow-dark), inset -3px -3px 6px var(--shadow-light); }
        
        .header h1 { margin:0; font-size:22px; color:var(--lila-dark); font-weight:900; }
        .coffee-icon-main { font-size:55px; animation:steam 4s ease-in-out infinite; }
        @keyframes steam { 0%, 100% { transform:translateY(0); } 50% { transform:translateY(-6px); } }
        
        .grid-7 { display:grid; grid-template-columns:repeat(7, 1fr); gap:3px; text-align:center; font-size:9px; }
        .cal-day { padding:5px 0; border-radius:6px; cursor:pointer; }
        .cal-day.has-data { color:var(--lila-dark); font-weight:900; text-decoration:underline; }
        .cal-day.selected { border:1px solid var(--lila-dark); background: var(--lila-soft); }
        
        .overlay { display:none; position:fixed; inset:0; background:var(--bg); z-index:10000; flex-direction:column; padding:25px; box-sizing:border-box; overflow-y:auto; }
        .codex-item { padding:10px; margin-bottom:8px; display:flex; justify-content:space-between; align-items:center; }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="app">
            <div class="header">
                <h1>Lavender</h1>
                <button class="neu-flat" style="width:35px; height:35px; border-radius:50%;" onclick="toggleTheme()">🌓</button>
            </div>

            <div class="neu-inset" style="padding:10px; min-height:60px; display:flex; flex-direction:column; justify-content:center; text-align:center;">
                <span id="quote" style="font-size:9px; font-style:italic;">"..."</span>
                <small id="philosopher" style="color:var(--lila-dark); margin-top:4px; font-weight:bold; font-size:8px;"></small>
            </div>

            <div style="display:flex; align-items:center; justify-content:space-between;">
                <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="prevT()">‹</button>
                <div style="text-align:center;">
                    <div id="main-icon" class="coffee-icon-main">☕</div>
                    <div id="main-name" style="font-size:10px; font-weight:900; color:var(--lila-dark); margin-top:5px; text-transform:uppercase;">ESPRESSO</div>
                </div>
                <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="nextT()">›</button>
            </div>

            <div style="text-align:center; font-weight:900; font-size:11px; color:var(--lila-dark);">
                <span id="date-label">Hoy</span>: <span id="count">0</span> rituales
            </div>

            <div style="display:flex; gap:8px;">
                <button class="neu-flat" style="padding:10px; font-size:9px; width:60px;" onclick="undo()">UNDO</button>
                <input type="text" id="note" class="neu-inset" style="flex-grow:1; border:none; padding:10px; font-size:10px; outline:none;" placeholder="Nota...">
                <button class="neu-flat" style="padding:10px; font-size:10px; color:var(--lila-dark); font-weight:bold;" onclick="add()">OK</button>
            </div>

            <div class="neu-inset" style="padding:10px;">
                <div style="display:flex; justify-content:space-between; font-size:10px; margin-bottom:8px; font-weight:bold; color:var(--lila-dark);">
                    <button class="neu-flat" style="padding:2px 8px;" onclick="changeMonth(-1)">‹</button>
                    <span id="month-name">Mes</span>
                    <button class="neu-flat" style="padding:2px 8px;" onclick="changeMonth(1)">›</button>
                </div>
                <div id="calendar" class="grid-7"></div>
                <div id="history-display" style="font-size:8px; margin-top:8px; border-top:1px dashed var(--shadow-dark); padding-top:5px; max-height:50px; overflow-y:auto;"></div>
            </div>

            <div style="display:grid; grid-template-columns:1fr 1fr; gap:12px;">
                <button class="neu-flat" style="padding:12px; font-size:10px; font-weight:bold;" onclick="openOverlay('codex')">GRIMORIO</button>
                <button class="neu-flat" style="padding:12px; font-size:10px; font-weight:bold;" onclick="openOverlay('wrapped')">WRAPPED</button>
            </div>
        </div>
    </div>

    <div id="codex" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark)">Grimorio</h2>
        <div class="neu-inset" style="padding:10px; margin-bottom:15px; display:flex; flex-direction:column; gap:8px;">
            <div style="display:flex; gap:5px;">
                <input type="text" id="cust-e" class="neu-inset" style="width:40px; border:none; text-align:center; padding:8px;" placeholder="☕">
                <input type="text" id="cust-n" class="neu-inset" style="flex-grow:1; border:none; padding:8px;" placeholder="Nombre">
            </div>
            <button onclick="saveCustom()" class="neu-flat" style="padding:10px; font-weight:bold; color:var(--lila-dark);">CREAR</button>
        </div>
        <div id="codex-grid" style="flex-grow:1; overflow-y:auto;"></div>
        <button onclick="closeOverlay('codex')" class="neu-flat" style="padding:12px; margin-top:10px;">CERRAR</button>
    </div>

    <div id="wrapped" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark)">Wrapped</h2>
        <div id="wrapped-list" class="neu-inset" style="padding:15px; margin-bottom:15px; flex-grow:1; overflow-y:auto;"></div>
        <div id="wrapped-total" class="neu-inset" style="padding:20px; text-align:center;"></div>
        <button onclick="closeOverlay('wrapped')" class="neu-flat" style="padding:12px; margin-top:10px;">VOLVER</button>
    </div>

    <script>
        /* PEGA AQUÍ LA PARTE 2 */
    </script>
</body>
</html>
cument.getElementById('month-name').innerText = new Intl.DateTimeFormat('es-ES', { month: 'short', year: 'numeric' }).format(cNavDate);
    
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
const FRASES = { 
    0: [{q:"La existencia es una distracción.", p:"Kafka"}, {q:"Existir es una imperfección.", p:"Cioran"}, {q:"El vacío es libertad.", p:"Sartre"}, {q:"La inacción es reflexión.", p:"Platón"}, {q:"Nacer es comenzar a morir.", p:"Heidegger"}, {q:"Solo sé que no sé nada.", p:"Sócrates"}], 
    1: [{q:"Pienso, luego existo.", p:"Descartes"}, {q:"Sapere Aude.", p:"Kant"}, {q:"La duda es el origen.", p:"Aristóteles"}, {q:"El hombre es medida.", p:"Protágoras"}, {q:"Todo fluye.", p:"Heráclito"}, {q:"La razón es reina.", p:"Spinoza"}], 
    2: [{q:"Somos lo que hacemos.", p:"Aristóteles"}, {q:"Amor Fati.", p:"Nietzsche"}, {q:"La vida es voluntad.", p:"Schopenhauer"}, {q:"El deber es ley.", p:"Kant"}, {q:"Persistencia es clave.", p:"Dalí"}, {q:"El ahora es eterno.", p:"Hegel"}], 
    3: [{q:"Parece imposible.", p:"Mandela"}, {q:"Libertad: responsabilidad.", p:"Sartre"}, {q:"Crea tu destino.", p:"Camus"}, {q:"El poder es acción.", p:"Foucault"}, {q:"Vence al miedo.", p:"Marco Aurelio"}, {q:"El fuego purifica.", p:"Zenón"}], 
    4: [{q:"Todo es real.", p:"Picasso"}, {q:"Realidad es lienzo.", p:"Dalí"}, {q:"Supera al hombre.", p:"Nietzsche"}, {q:"Danza en el caos.", p:"Zarathustra"}, {q:"Eterno retorno.", p:"Nietzsche"}, {q:"La fuerza es vida.", p:"Heráclito"}] 
};
const BASE_DB = {'r1':{n:'Espresso',e:'☕'},'r2':{n:'Cortado',e:'☕🥛'},'r3':{n:'Americano',e:'☕💧'},'r4':{n:'Cappuccino',e:'☕☁️'},'r5':{n:'Latte',e:'☕🍶'}};

let logs=JSON.parse(localStorage.getItem('lav_tr_v2_logs'))||{}, 
    customDB=JSON.parse(localStorage.getItem('lav_tr_v2_cust'))||{}, 
    activeTrinity=JSON.parse(localStorage.getItem('lav_tr_v2_act'))||['r1','r2','r3'], 
    cNavDate=new Date(), 
    selDateKey=new Date().toLocaleDateString('es-ES'), 
    cIdx=0;

function haptic(){if(navigator.vibrate)navigator.vibrate(25);}
function toggleTheme(){haptic();document.body.classList.toggle('dark-mode');}

function update(){
    localStorage.setItem('lav_tr_v2_logs',JSON.stringify(logs));
    localStorage.setItem('lav_tr_v2_cust',JSON.stringify(customDB));
    localStorage.setItem('lav_tr_v2_act',JSON.stringify(activeTrinity));
    
    const day=logs[selDateKey]||[]; 
    document.getElementById('count').innerText=day.length;
    document.getElementById('date-label').innerText=selDateKey===new Date().toLocaleDateString('es-ES')?"Hoy":selDateKey;
    
    const level=Math.min(day.length,4), pick=FRASES[level][Math.floor(Math.random()*6)];
    document.getElementById('quote').innerText=`"${pick.q}"`; 
    document.getElementById('philosopher').innerText=`- ${pick.p}`;
    
    const r=BASE_DB[activeTrinity[cIdx]]||customDB[activeTrinity[cIdx]]||BASE_DB['r1'];
    document.getElementById('main-icon').innerHTML=r.e; 
    document.getElementById('main-name').innerText=r.n;
    
    renderCal(); 
    showDay();
}

function nextT(){haptic();cIdx=(cIdx+1)%3;update();}
function prevT(){haptic();cIdx=(cIdx-1+3)%3;update();}

function add(){
    haptic();
    const id=activeTrinity[cIdx]; 
    const now = new Date();
    if(!logs[selDateKey]) logs[selDateKey]=[]; 
    logs[selDateKey].push({id, h:now.toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}), t:now.getHours(), n:document.getElementById('note').value}); 
    document.getElementById('note').value=''; 
    update();
}

function undo(){if(logs[selDateKey]?.length>0){haptic();logs[selDateKey].pop();update();}}

function renderCal(){
    const cal=document.getElementById('calendar');cal.innerHTML=''; const y=cNavDate.getFullYear(), m=cNavDate.getMonth();
    document.getElementById('month-name').innerText=new Intl.DateTimeFormat('es-ES',{month:'short',year:'numeric'}).format(cNavDate);
    const start=(new Date(y,m,1).getDay()+6)%7, days=new Date(y,m+1,0).getDate();
    for(let i=0;i<start;i++)cal.innerHTML+='<div></div>';
    for(let i=1;i<=days;i++){
        const dk=new Date(y,m,i).toLocaleDateString('es-ES');
        cal.innerHTML+=`<div class="cal-day ${logs[dk]?.length?'has-data':''} ${dk===selDateKey?'selected':''}" onclick="selDateKey='${dk}';update()">${i}</div>`;
    }
}

function showDay(){
    const d=logs[selDateKey]||[];
    document.getElementById('history-display').innerHTML=d.map(l=>{
        const r = BASE_DB[l.id]||customDB[l.id];
        return `<div><b>${l.h}</b> ${r.n} ${l.n?'- <i>'+l.n+'</i>':''}</div>`;
    }).join('')||'Sin registros';
}

function saveCustom(){
    const n=document.getElementById('cust-n').value, e=document.getElementById('cust-e').value||'☕'; 
    if(n){
        const id='c'+Date.now();
        customDB[id]={n,e};
        document.getElementById('cust-n').value='';
        renderCodex(); haptic();
    }
}

function renderCodex(){
    const g=document.getElementById('codex-grid');g.innerHTML=''; const full={...BASE_DB,...customDB};
    Object.keys(full).forEach(id=>{ 
        const r=full[id]; 
        g.innerHTML+=`<div class="neu-flat codex-item"><span>${r.e} ${r.n}</span><div style="display:flex;gap:4px;">${[0,1,2].map(i=>`<button class="neu-flat" style="padding:5px;font-size:9px;${activeTrinity[i]===id?'color:var(--lila-dark);font-weight:900;':''}" onclick="activeTrinity[${i}]='${id}';update();renderCodex()">T${i+1}</button>`).join('')}</div></div>`;
    });
}

function renderWrapped(){
    const l=document.getElementById('wrapped-list'), tDiv=document.getElementById('wrapped-total');
    l.innerHTML=''; let s={}, t=0;
    Object.values(logs).forEach(day=>day.forEach(rit=>{ s[rit.id]=(s[rit.id]||0)+1; t++; }));
    Object.keys(s).sort((a,b)=>s[b]-s[a]).forEach(id=>{
        const r=BASE_DB[id]||customDB[id]; 
        if(r) l.innerHTML+=`<div class="wrapped-item"><span>${r.e} ${r.n}</span><span>x${s[id]}</span></div>`;
    });
    tDiv.innerHTML=`<small>TOTAL</small><h1 style="color:var(--lila-dark);margin:0;font-size:40px;">${t}</h1>`;
}

function openOverlay(id){document.getElementById(id).style.display='flex';if(id==='codex')renderCodex();if(id==='wrapped')renderWrapped();}
function closeOverlay(id){document.getElementById(id).style.display='none';}
function changeMonth(d){cNavDate.setMonth(cNavDate.getMonth()+d);renderCal();}

update();
