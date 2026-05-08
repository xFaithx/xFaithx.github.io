<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Lavender: Chronos Trinity Mobile</title>
    <style>
        :root { --bg: #e0e0f0; --lila-soft: #dcd6f7; --lila-dark: #6c5ce7; --shadow-light: #ffffff; --shadow-dark: #b8b8d1; --text: #444466; }
        body.dark-mode { --bg: #1e1e2f; --lila-soft: #3d3d5c; --lila-dark: #a696c8; --shadow-light: #2a2a40; --shadow-dark: #0f0f1a; --text: #c0c0d0; }
        
        /* Ajuste para móviles: evitar scroll lateral y centrar contenido */
        body { margin:0; padding:10px; background:#cfcfe2; font-family:system-ui, sans-serif; color:var(--text); display:flex; justify-content:center; min-height:100vh; box-sizing: border-box; overflow-x: hidden; }
        
        .app { 
            width: 100%; 
            max-width: 380px; 
            background: var(--bg); 
            border-radius: 35px; 
            padding: 20px; 
            box-sizing: border-box; 
            display: flex; 
            flex-direction: column; 
            gap: 12px; 
            box-shadow: 10px 10px 20px var(--shadow-dark), -8px -8px 20px var(--shadow-light);
            margin-top: 5px; /* Pequeño margen por si el texto del servidor aparece arriba */
        }

        /* Estilo Neumórfico Adaptativo */
        .neu-inset { background:var(--bg); border-radius:15px; box-shadow:inset 4px 4px 8px var(--shadow-dark), inset -4px -4px 8px var(--shadow-light); border:none; padding:10px; }
        .neu-flat { background:var(--bg); border-radius:15px; border:none; box-shadow:4px 4px 10px var(--shadow-dark), -4px -4px 10px var(--shadow-light); cursor:pointer; color:var(--text); padding:12px; font-size: 11px; font-weight: bold; }
        .neu-flat:active { box-shadow:inset 2px 2px 5px var(--shadow-dark), inset -2px -2px 5px var(--shadow-light); }

        .header h1 { margin:0; font-size:22px; color:var(--lila-dark); font-weight:900; text-align:center; }
        
        /* Trinity y Display principal */
        .trinity-container { display:flex; align-items:center; justify-content:space-between; margin:5px 0; }
        .coffee-display { text-align:center; flex-grow:1; }
        .coffee-icon-main { font-size:50px; animation:steam 4s ease-in-out infinite; }
        @keyframes steam { 0%, 100% { transform:translateY(0); } 50% { transform:translateY(-5px); } }

        /* Calendario Ajustado */
        .grid-7 { display:grid; grid-template-columns:repeat(7, 1fr); gap:2px; text-align:center; font-size:10px; }
        .cal-day { padding:8px 0; border-radius:5px; }
        .cal-day.has-data { color:var(--lila-dark); font-weight:900; text-decoration:underline; }
        .cal-day.selected { background: var(--lila-soft); }

        /* Overlays para móviles */
        .overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:var(--bg); z-index:1000; flex-direction:column; padding:20px; box-sizing:border-box; overflow-y: auto; }
    </style>
</head>
<body>
    <div class="app">
        <div class="header">
            <h1>Lavender</h1>
        </div>

        <div class="neu-inset" style="min-height: 60px; display: flex; flex-direction: column; justify-content: center; text-align: center; padding: 10px;">
            <span id="quote" style="font-size: 10px; font-style: italic;">"Cargando..."</span>
            <small id="philosopher" style="color:var(--lila-dark); font-weight:bold; font-size:9px; margin-top:5px;"></small>
        </div>

        <div class="trinity-container">
            <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="prevT()">‹</button>
            <div class="coffee-display">
                <div id="main-icon" class="coffee-icon-main">☕</div>
                <div id="main-name" style="font-size:10px; font-weight:900; color:var(--lila-dark); margin-top:5px;">ESPRESSO</div>
            </div>
            <button class="neu-flat" style="width:40px; height:40px; border-radius:50%;" onclick="nextT()">›</button>
        </div>

        <div style="text-align:center; font-weight:900; font-size:11px; color:var(--lila-dark);">
            <span id="count">0</span> rituales hoy
        </div>

        <input type="text" id="note" class="neu-inset" style="font-size:11px;" placeholder="Nota del ritual...">

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
            <button class="neu-flat" onclick="undo()">UNDO</button>
            <button class="neu-flat" style="color:var(--lila-dark);" onclick="add()">REGISTRAR</button>
        </div>

        <div class="neu-inset" style="padding:10px;">
            <div style="display:flex; justify-content:space-between; font-size:11px; font-weight:bold; margin-bottom:8px;">
                <span id="month-name">Mes</span>
                <div>
                    <span onclick="changeMonth(-1)" style="cursor:pointer; margin-right:10px;">‹</span>
                    <span onclick="changeMonth(1)" style="cursor:pointer;">›</span>
                </div>
            </div>
            <div id="calendar" class="grid-7"></div>
        </div>

        <div style="display:grid; grid-template-columns:1fr 1fr; gap:10px;">
            <button class="neu-flat" onclick="openOverlay('codex')">GRIMORIO</button>
            <button class="neu-flat" onclick="openOverlay('wrapped')">WRAPPED</button>
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
