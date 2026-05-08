
<html lang="es">
<head>
    <meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lavender: Chronos Trinity V2</title>
    <style>
        :root { --bg: #e0e0f0; --lila-soft: #dcd6f7; --lila-dark: #6c5ce7; --shadow-light: #ffffff; --shadow-dark: #b8b8d1; --text: #444466; }
        body.dark-mode { --bg: #1e1e2f; --lila-soft: #3d3d5c; --lila-dark: #a696c8; --shadow-light: #2a2a40; --shadow-dark: #0f0f1a; --text: #c0c0d0; }
        body { margin:0; background:#cfcfe2; font-family:system-ui, -apple-system, sans-serif; color:var(--text); display:flex; justify-content:center; align-items:center; min-height:100vh; transition:0.4s; overflow:hidden; }
        .app { width:360px; background:var(--bg); border-radius:50px; padding:25px; box-sizing:border-box; display:flex; flex-direction:column; gap:15px; box-shadow:15px 15px 30px var(--shadow-dark), -12px -12px 30px var(--shadow-light); }
        .neu-inset { background:var(--bg); border-radius:20px; box-shadow:inset 6px 6px 12px var(--shadow-dark), inset -6px -6px 12px var(--shadow-light); }
        .neu-flat { background:var(--bg); border-radius:20px; border:none; box-shadow:6px 6px 12px var(--shadow-dark), -6px -6px 12px var(--shadow-light); cursor:pointer; transition:0.2s; color:var(--text); outline:none; }
        .neu-flat:active { box-shadow:inset 4px 4px 8px var(--shadow-dark), inset -4px -4px 8px var(--shadow-light); }
        .header { display:flex; justify-content:space-between; align-items:center; }
        .header h1 { margin:0; font-size:26px; color:var(--lila-dark); font-weight:900; letter-spacing:-1px; }
        .trinity-container { display:flex; align-items:center; justify-content:space-between; margin:10px 0; }
        .coffee-display { flex-grow:1; display:flex; flex-direction:column; align-items:center; justify-content:center; }
        .coffee-icon-main { font-size:65px; color:var(--text); text-shadow:2px 2px 4px var(--shadow-dark), -2px -2px 4px var(--shadow-light); animation:steam 4s ease-in-out infinite; filter:opacity(0.9); }
        @keyframes steam { 0%, 100% { transform:translateY(0) scale(1); filter:blur(0px); } 50% { transform:translateY(-8px) scale(1.02); filter:blur(1px); } }
        .coffee-name { font-size:11px; font-weight:900; color:var(--lila-dark); letter-spacing:2px; margin-top:10px; text-transform:uppercase; }
        .art-row { display:grid; grid-template-columns:1fr 1.1fr; gap:12px; align-items:center; }
        .art-img { width:100%; aspect-ratio:1; border-radius:18px; background:url('https://upload.wikimedia.org/wikipedia/en/d/dd/The_Persistence_of_Memory.jpg') center/cover; box-shadow:4px 4px 10px var(--shadow-dark); }
        .controls-row { display:flex; gap:10px; align-items:center; }
        .note-input { flex-grow:1; border:none; padding:12px; border-radius:15px; outline:none; background:var(--bg); font-size:11px; color:var(--text); }
        .cal-box { padding:15px; min-height:180px; }
        .cal-nav { display:flex; justify-content:space-between; align-items:center; margin-bottom:10px; font-weight:900; font-size:12px; color:var(--lila-dark); }
        .grid-7 { display:grid; grid-template-columns:repeat(7, 1fr); gap:4px; text-align:center; font-size:9px; }
        .cal-day { padding:6px 0; border-radius:8px; cursor:pointer; }
        .cal-day.has-data { color:var(--lila-dark); font-weight:900; text-decoration:underline; }
        .cal-day.selected { border:1px solid var(--lila-dark); }
        .overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:var(--bg); z-index:100; flex-direction:column; padding:30px; box-sizing:border-box; }
        .codex-item { padding:12px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center; }
        .wrapped-item { display:flex; justify-content:space-between; padding:10px; border-bottom:1px solid var(--shadow-dark); font-weight:bold; font-size:11px; }
    </style>
</head>
<body>
    <div class="app">
        <div class="header"><h1>Lavender</h1><button class="neu-flat" style="width:38px; height:38px; border-radius:50%;" onclick="toggleTheme()">🌓</button></div>
        <div class="art-row">
            <div class="art-img"></div>
            <div class="neu-inset" style="padding:10px; min-height:80px; display:flex; flex-direction:column; justify-content:center;">
                <span id="quote" style="font-size:9px; font-style:italic;">"..."</span>
                <small id="philosopher" style="color:var(--lila-dark); margin-top:5px; font-weight:bold; font-size:8px; text-align:right;"></small>
            </div>
        </div>
        <div class="trinity-container">
            <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="prevT()">‹</button>
            <div class="coffee-display"><div id="main-icon" class="coffee-icon-main">☕</div><div id="main-name" class="coffee-name">ESPRESSO</div></div>
            <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="nextT()">›</button>
        </div>
        <div style="text-align:center; font-weight:900; font-size:12px; color:var(--lila-dark);"><span id="date-label">Hoy</span>: <span id="count">0</span> rituales</div>
        <div class="controls-row">
            <button class="neu-flat" style="padding:12px; font-size:9px; font-weight:bold; width:55px;" onclick="undo()">UNDO</button>
            <input type="text" id="note" class="neu-inset note-input" placeholder="Nota...">
            <button class="neu-flat" style="padding:12px; font-size:10px; font-weight:bold; color:var(--lila-dark);" onclick="add()">REGISTRAR</button>
        </div>
        <div class="neu-inset cal-box">
            <div class="cal-nav"><button class="neu-flat" style="padding:5px 10px;" onclick="changeMonth(-1)">‹</button><span id="month-name">Mes</span><button class="neu-flat" style="padding:5px 10px;" onclick="changeMonth(1)">›</button></div>
            <div id="calendar" class="grid-7"></div>
            <div id="history-display" style="font-size:9px; margin-top:8px; border-top:1px dashed var(--shadow-dark); padding-top:5px; max-height:60px; overflow-y:auto; line-height:1.4;"></div>
        </div>
        <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px;">
            <button class="neu-flat" style="padding:12px; font-size:10px; font-weight:bold;" onclick="openOverlay('codex')">GRIMORIO</button>
            <button class="neu-flat" style="padding:12px; font-size:10px; font-weight:bold;" onclick="openOverlay('wrapped')">WRAPPED</button>
        </div>
    </div>

    <div id="codex" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark)">Grimorio</h2>
        <div class="neu-inset" style="padding:12px; margin-bottom:15px; display:flex; flex-direction:column; gap:8px;">
            <div style="display:flex; gap:8px;"><input type="text" id="cust-e" class="neu-inset note-input" style="width:45px; text-align:center;" placeholder="☕"><input type="text" id="cust-n" class="neu-inset note-input" placeholder="Nombre"></div>
            <input type="text" id="cust-r" class="neu-inset note-input" placeholder="Receta..."><button onclick="saveCustom()" class="neu-flat" style="padding:10px; font-weight:bold; color:var(--lila-dark);">CREAR</button>
        </div>
        <div id="codex-grid" style="overflow-y:auto; flex-grow:1;"></div>
        <button onclick="closeOverlay('codex')" class="neu-flat" style="padding:15px; margin-top:10px; font-weight:bold;">CERRAR</button>
    </div>
    <div id="wrapped" class="overlay">
        <h2 style="text-align:center; color:var(--lila-dark)">Wrapped</h2>
        <div id="wrapped-list" class="neu-inset" style="padding:15px; margin-bottom:15px; flex-grow:1; overflow-y:auto;"></div>
        <div id="wrapped-peak" class="neu-inset" style="padding:10px; text-align:center; margin-bottom:10px; font-size:11px; font-weight:bold;"></div>
        <div id="wrapped-total" class="neu-inset" style="padding:20px; text-align:center;"></div>
        <button onclick="closeOverlay('wrapped')" class="neu-flat" style="padding:15px; margin-top:10px;">VOLVER</button>
    </div>

    <script>
        const FRASES = { 
            0: [{q:"La existencia es una distracción.", p:"Kafka"}, {q:"Existir es una imperfección.", p:"Cioran"}, {q:"El vacío es libertad.", p:"Sartre"}, {q:"La inacción es reflexión.", p:"Platón"}, {q:"Nacer es comenzar a morir.", p:"Heidegger"}, {q:"Solo sé que no sé nada.", p:"Sócrates"}], 
            1: [{q:"Pienso, luego existo.", p:"Descartes"}, {q:"Sapere Aude.", p:"Kant"}, {q:"La duda es el origen.", p:"Aristóteles"}, {q:"El hombre es medida.", p:"Protágoras"}, {q:"Todo fluye.", p:"Heráclito"}, {q:"La razón es reina.", p:"Spinoza"}], 
            2: [{q:"Somos lo que hacemos.", p:"Aristóteles"}, {q:"Amor Fati.", p:"Nietzsche"}, {q:"La vida es voluntad.", p:"Schopenhauer"}, {q:"El deber es ley.", p:"Kant"}, {q:"Persistencia es clave.", p:"Dalí"}, {q:"El ahora es eterno.", p:"Hegel"}], 
            3: [{q:"Parece imposible.", p:"Mandela"}, {q:"Libertad: responsabilidad.", p:"Sartre"}, {q:"Crea tu destino.", p:"Camus"}, {q:"El poder es acción.", p:"Foucault"}, {q:"Vence al miedo.", p:"Marco Aurelio"}, {q:"El fuego purifica.", p:"Zenón"}], 
            4: [{q:"Todo es real.", p:"Picasso"}, {q:"Realidad es lienzo.", p:"Dalí"}, {q:"Supera al hombre.", p:"Nietzsche"}, {q:"Danza en el caos.", p:"Zarathustra"}, {q:"Eterno retorno.", p:"Nietzsche"}, {q:"La fuerza es vida.", p:"Heráclito"}] 
        };
        const BASE_DB = {'r1':{n:'Espresso',e:'☕'},'r2':{n:'Cortado',e:'☕🥛'},'r3':{n:'Americano',e:'☕💧'},'r4':{n:'Cappuccino',e:'☕☁️'},'r5':{n:'Latte',e:'☕🍶'},'r6':{n:'Cold Brew',e:'⏳☕'},'r7':{n:'Moka',e:'☕🍫'}};
        
        let logs=JSON.parse(localStorage.getItem('lav_tr_logs'))||{}, 
            customDB=JSON.parse(localStorage.getItem('lav_tr_cust'))||{}, 
            activeTrinity=JSON.parse(localStorage.getItem('lav_tr_act'))||['r1','r2','r3'], 
            cNavDate=new Date(), 
            selDateKey=new Date().toLocaleDateString('es-ES'), 
            cIdx=0;

        function haptic(){if(navigator.vibrate)navigator.vibrate(25);}
        function toggleTheme(){haptic();document.body.classList.toggle('dark-mode');}

        function update(){
            localStorage.setItem('lav_tr_logs',JSON.stringify(logs));
            localStorage.setItem('lav_tr_cust',JSON.stringify(customDB));
            localStorage.setItem('lav_tr_act',JSON.stringify(activeTrinity));
            
            const day=logs[selDateKey]||[], count=day.length; 
            document.getElementById('count').innerText=count;
            document.getElementById('date-label').innerText=selDateKey===new Date().toLocaleDateString('es-ES')?"Hoy":selDateKey;
            
            const level=Math.min(count,4), pick=FRASES[level][Math.floor(Math.random()*6)];
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
            logs[selDateKey].push({
                id,
                h: now.toLocaleTimeString('es-ES',{hour:'2-digit',minute:'2-digit'}),
                t: now.getHours(),
                n: document.getElementById('note').value || ""
            }); 
            document.getElementById('note').value=''; 
            update();
        }

        function undo(){if(logs[selDateKey]?.length>0){haptic();logs[selDateKey].pop();update();}}

        function renderCal(){
            const cal=document.getElementById('calendar');cal.innerHTML=''; const y=cNavDate.getFullYear(), m=cNavDate.getMonth();
            document.getElementById('month-name').innerText=new Intl.DateTimeFormat('es-ES',{month:'long',year:'numeric'}).format(cNavDate);
            const start=(new Date(y,m,1).getDay()+6)%7, days=new Date(y,m+1,0).getDate();
            for(let i=0;i<start;i++)cal.innerHTML+='<div></div>';
            for(let i=1;i<=days;i++){
                const dk=new Date(y,m,i).toLocaleDateString('es-ES');
                cal.innerHTML+=`<div class="cal-day ${logs[dk]?.length?'has-data':''} ${dk===selDateKey?'selected':''}" onclick="selDateKey='${dk}';update()">${i}</div>`;
            }
        }

        function showDay(){
            const d=logs[selDateKey]||[];
            document.getElementById('history-display').innerHTML=d.length?d.map(l=>{
                const r = BASE_DB[l.id]||customDB[l.id];
                return `<div style="margin-bottom:3px;"><b>${l.h}</b> ${r.e} ${r.n} ${l.n? ' - <i>'+l.n+'</i>' : ''}</div>`;
            }).join(''):'Sin registros';
        }

        function saveCustom(){const n=document.getElementById('cust-n').value,e=document.getElementById('cust-e').value||'☕'; if(n){const id='c'+Date.now();customDB[id]={n,e,r:document.getElementById('cust-r').value};document.getElementById('cust-n').value='';renderCodex();haptic();}}

        function renderCodex(){
            const g=document.getElementById('codex-grid');g.innerHTML=''; const full={...BASE_DB,...customDB};
            Object.keys(full).forEach(id=>{ 
                const r=full[id]; 
                g.innerHTML+=`<div class="neu-flat codex-item"><div style="text-align:left;font-size:11px;"><strong>${r.e} ${r.n}</strong></div><div style="display:flex;gap:5px;">${[0,1,2].map(i=>`<button class="neu-flat" style="padding:6px;font-size:9px;width:28px;${activeTrinity[i]===id?'color:var(--lila-dark);font-weight:900;':''}" onclick="activeTrinity[${i}]='${id}';update();renderCodex()">T${i+1}</button>`).join('')}</div></div>`;
            });
        }

        function renderWrapped(){
            const l=document.getElementById('wrapped-list'), tDiv=document.getElementById('wrapped-total'), pDiv=document.getElementById('wrapped-peak');
            l.innerHTML=''; let s={}, t=0, hCounts={};
            
            Object.values(logs).forEach(day=>{
                day.forEach(rit=>{
                    s[rit.id]=(s[rit.id]||0)+1;
                    hCounts[rit.t]=(hCounts[rit.t]||0)+1;
                    t++;
                });
            });

            Object.keys(s).sort((a,b)=>s[b]-s[a]).forEach(id=>{
                const r=BASE_DB[id]||customDB[id]; 
                if(r) l.innerHTML+=`<div class="wrapped-item"><span>${r.e} ${r.n}</span><span>x${s[id]}</span></div>`;
            });

            let peakH = Object.entries(hCounts).sort((a,b)=>b[1]-a[1])[0];
            pDiv.innerHTML = peakH ? `HORA PICO: ${peakH[0]}:00 hs` : "SIN DATOS";
            tDiv.innerHTML=`<small>TOTAL RITUALES</small><h1 style="color:var(--lila-dark);margin:0;font-size:60px;">${t}</h1>`;
        }

        function openOverlay(id){document.getElementById(id).style.display='flex';if(id==='codex')renderCodex();if(id==='wrapped')renderWrapped();}
        function closeOverlay(id){document.getElementById(id).style.display='none';}
        function changeMonth(d){cNavDate.setMonth(cNavDate.getMonth()+d);renderCal();}

        update();
    </script>
</body>
</html>
