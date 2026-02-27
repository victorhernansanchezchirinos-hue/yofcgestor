<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Gestor Incidencias – YOFC PERÚ</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet"/>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#07101f;--surface:#0f1f35;--border:#1a3353;--blue:#2563eb;--blue-dim:#1e3a5f;
  --text:#e2e8f0;--muted:#64748b;--faint:#1e2d45;
  --red:#dc2626;--orange:#ea580c;--yellow:#ca8a04;--green:#16a34a;
}
html,body{height:100%;font-family:'Syne',sans-serif;background:var(--bg);color:var(--text);overflow-x:hidden}
::-webkit-scrollbar{width:5px}::-webkit-scrollbar-track{background:var(--surface)}::-webkit-scrollbar-thumb{background:var(--blue-dim);border-radius:3px}
#app{display:flex;flex-direction:column;height:100vh}
#loader{position:fixed;inset:0;background:var(--bg);z-index:9999;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:18px;transition:opacity .4s}
#loader.hide{opacity:0;pointer-events:none}
.loader-logo{display:flex;align-items:center;gap:14px}
.loader-icon{width:50px;height:50px;border-radius:12px;background:linear-gradient(135deg,var(--blue),#1d4ed8);display:flex;align-items:center;justify-content:center;font-size:26px}
.loader-title{font-weight:800;font-size:20px}
.loader-sub{color:#60a5fa;font-size:10px;letter-spacing:3px;text-transform:uppercase}
.loader-bar-wrap{width:240px;height:3px;background:var(--border);border-radius:2px;overflow:hidden}
.loader-bar{height:100%;background:linear-gradient(90deg,var(--blue),#60a5fa);animation:load 1.6s ease-in-out forwards}
@keyframes load{0%{width:0}60%{width:70%}100%{width:100%}}
.loader-msg{color:var(--muted);font-size:11px;font-family:'DM Mono',monospace}
#loader-error{display:none;background:#dc262222;border:1px solid #dc262244;border-radius:8px;padding:12px 18px;color:#f87171;font-size:11px;max-width:400px;text-align:center;line-height:1.7}
header{background:linear-gradient(90deg,#0a1628,#0f1f35);border-bottom:1px solid var(--border);padding:11px 22px;display:flex;align-items:center;justify-content:space-between;flex-shrink:0}
.logo{display:flex;align-items:center;gap:10px}
.logo-icon{width:32px;height:32px;border-radius:7px;background:linear-gradient(135deg,var(--blue),#1d4ed8);display:flex;align-items:center;justify-content:center;font-size:16px}
.logo-title{font-weight:800;font-size:15px}
.logo-sub{color:#60a5fa;font-size:9px;letter-spacing:3px;text-transform:uppercase;margin-top:1px}
.hdr-right{display:flex;align-items:center;gap:9px}
.sync-pill{display:flex;align-items:center;gap:6px;font-size:10px;padding:5px 11px;border-radius:20px;border:1px solid var(--border);background:var(--faint)}
.sdot{width:7px;height:7px;border-radius:50%;background:var(--muted)}
.sdot.ok{background:var(--green);box-shadow:0 0 6px var(--green)}
.sdot.spin{background:var(--yellow);animation:blink 1s infinite}
.sdot.err{background:var(--red)}
@keyframes blink{0%,100%{opacity:1}50%{opacity:.2}}
.btn{background:var(--blue);color:#fff;border:none;border-radius:7px;padding:6px 13px;font-size:11px;font-weight:700;cursor:pointer;font-family:'Syne',sans-serif;transition:all .2s;white-space:nowrap}
.btn:hover{background:#1d4ed8}
.btn:disabled{opacity:.5;cursor:default}
#kpis{display:grid;grid-template-columns:repeat(4,1fr);gap:9px;padding:10px 22px;background:var(--surface);border-bottom:1px solid var(--border);flex-shrink:0}
.kpi{background:var(--bg);border:1px solid var(--border);border-radius:9px;padding:10px 14px;display:flex;align-items:center;gap:11px}
.kpi-icon{font-size:19px}
.kpi-lbl{color:var(--muted);font-size:9px;font-weight:700;letter-spacing:1.5px;text-transform:uppercase}
.kpi-val{font-size:24px;font-weight:800;line-height:1}
#tabs{padding:8px 22px;background:var(--surface);border-bottom:1px solid var(--border);display:flex;gap:7px;flex-shrink:0}
.tab{background:transparent;border:1px solid var(--border);color:var(--muted);border-radius:7px;padding:6px 13px;font-size:11px;font-weight:700;cursor:pointer;font-family:'Syne',sans-serif;transition:all .2s;display:flex;align-items:center;gap:5px}
.tab:hover{border-color:var(--blue);color:var(--text)}
.tab.active{background:var(--blue);border-color:var(--blue);color:#fff}
.tbadge{border-radius:8px;padding:1px 6px;font-size:9px;background:rgba(255,255,255,.18);color:#fff}
.tab:not(.active) .tbadge{background:var(--blue-dim);color:#60a5fa}
#content{flex:1;overflow:hidden}
.view{display:none;height:100%;overflow-y:auto;padding:16px 22px}
.view.active{display:block}
#view-mapa{display:none;height:100%}
#view-mapa.active{display:flex}
.snote{background:#1e3a5f33;border:1px solid var(--blue-dim);border-radius:7px;padding:8px 13px;color:#60a5fa;font-size:10px;margin-bottom:11px}
.filters{background:var(--surface);border:1px solid var(--border);border-radius:9px;padding:10px 14px;display:flex;gap:8px;flex-wrap:wrap;align-items:center;margin-bottom:11px}
.fsel,.finput{background:var(--faint);border:1px solid var(--border);border-radius:7px;color:var(--text);font-size:11px;padding:6px 10px;font-family:'Syne',sans-serif;outline:none}
.fsel:focus,.finput:focus{border-color:var(--blue)}
.finput{width:190px}
.table-wrap{background:var(--surface);border:1px solid var(--border);border-radius:11px;overflow:hidden}
.t-head{display:grid;padding:9px 13px;background:var(--bg);border-bottom:1px solid var(--border);grid-template-columns:38px 90px 52px 130px 180px 1fr 90px 100px;gap:8px}
.t-head span{color:#60a5fa;font-size:9px;font-weight:700;letter-spacing:1px;text-transform:uppercase}
.t-row{display:grid;padding:9px 13px;grid-template-columns:38px 90px 52px 130px 180px 1fr 90px 100px;gap:8px;border-bottom:1px solid #1a334322;align-items:center;transition:background .15s}
.t-row:hover{background:var(--faint)}
.t-row:nth-child(odd){background:var(--bg)}
.t-row:nth-child(odd):hover{background:var(--faint)}
.t-row>*{font-size:11px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.mono{font-family:'DM Mono',monospace}
.nc{display:flex;flex-direction:column;gap:1px}
.nc-id{color:#60a5fa;font-weight:700;font-size:11px;font-family:'DM Mono',monospace}
.nc-zona{color:var(--muted);font-size:9px;overflow:hidden;text-overflow:ellipsis}
.badge{display:inline-flex;align-items:center;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:700;font-family:'DM Mono',monospace;white-space:nowrap}
.empty{padding:40px;text-align:center;color:var(--muted);font-size:12px}
.ng{display:grid;grid-template-columns:repeat(auto-fill,minmax(262px,1fr));gap:11px}
.ncard{background:var(--surface);border:1px solid var(--border);border-radius:9px;padding:13px;display:flex;flex-direction:column;gap:7px;transition:border-color .2s}
.ncard:hover{border-color:var(--blue)}
.ncard-top{display:flex;justify-content:space-between;align-items:center}
.ncard-name{font-weight:700;font-size:12px}
.ncard-info{color:var(--muted);font-size:10px;display:flex;flex-direction:column;gap:2px}
#map-wrap{flex:1;position:relative}
#lmap{width:100%;height:100%}
#msidebar{width:330px;flex-shrink:0;background:var(--surface);display:flex;flex-direction:column;overflow:hidden;border-left:1px solid var(--border)}
#msidebar-hdr{padding:12px 15px;border-bottom:1px solid var(--border);font-weight:800;font-size:12px;flex-shrink:0}
#mfilters{padding:9px 12px;border-bottom:1px solid var(--border);display:flex;flex-direction:column;gap:6px;flex-shrink:0}
#mfilters select,#mfilters input{background:var(--faint);border:1px solid var(--border);border-radius:7px;color:var(--text);font-size:11px;padding:6px 10px;font-family:'Syne',sans-serif;outline:none;width:100%}
#mlist{flex:1;overflow-y:auto;padding:8px 9px;display:flex;flex-direction:column;gap:6px}
.mc{background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:10px 12px;cursor:pointer;transition:all .2s}
.mc:hover,.mc.sel{border-color:var(--blue);background:var(--blue-dim)}
.mc-top{display:flex;justify-content:space-between;align-items:center;margin-bottom:4px}
.mc-id{font-family:'DM Mono',monospace;color:#60a5fa;font-size:10px;font-weight:600}
.mc-tipo{font-size:11px;font-weight:600;margin-bottom:2px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.mc-meta{font-size:10px;color:var(--muted);display:flex;gap:6px;flex-wrap:wrap}
#mlegend{padding:8px 12px;border-top:1px solid var(--border);display:flex;gap:9px;flex-wrap:wrap;flex-shrink:0}
.leg{display:flex;align-items:center;gap:4px;font-size:10px;color:var(--muted)}
.leg-dot{width:8px;height:8px;border-radius:50%}
.leaflet-popup-content-wrapper{background:#0f1f35!important;color:#e2e8f0!important;border:1px solid #1a3353!important;border-radius:10px!important;box-shadow:0 8px 32px rgba(0,0,0,.6)!important}
.leaflet-popup-content{font-family:'Syne',sans-serif!important;font-size:12px!important;margin:11px 13px!important}
.leaflet-popup-tip{background:#0f1f35!important}
.leaflet-popup-close-button{color:#60a5fa!important}
.pp-title{font-weight:800;font-size:13px;margin-bottom:7px;color:#fff}
.pp-row{display:flex;gap:8px;margin-bottom:3px;align-items:flex-start}
.pp-k{color:#64748b;font-size:9px;text-transform:uppercase;letter-spacing:1px;min-width:68px;margin-top:1px;flex-shrink:0}
.pp-v{color:#e2e8f0;font-size:11px;font-family:'DM Mono',monospace;word-break:break-word}
</style>
</head>
<body>
<div id="app">

<div id="loader">
  <div class="loader-logo">
    <div class="loader-icon">📡</div>
    <div><div class="loader-title">GESTOR DE INCIDENCIAS</div><div class="loader-sub">YOFC PERÚ S.A.C.</div></div>
  </div>
  <div class="loader-bar-wrap"><div class="loader-bar"></div></div>
  <div class="loader-msg" id="loader-msg">Conectando con Google Sheets…</div>
  <div id="loader-error">
    ⚠️ No se pudo cargar desde Google Sheets.<br/>
    Verifica que el archivo esté publicado en la web.<br/><br/>
    <button class="btn" id="btn-demo-fallback">Continuar con datos de demo</button>
  </div>
</div>

<header>
  <div class="logo">
    <div class="logo-icon">📡</div>
    <div><div class="logo-title">GESTOR DE INCIDENCIAS</div><div class="logo-sub">YOFC PERÚ S.A.C.</div></div>
  </div>
  <div class="hdr-right">
    <div class="sync-pill"><div class="sdot" id="sdot"></div><span id="slbl">Cargando…</span></div>
    <button class="btn" id="btn-refresh">🔄 Actualizar</button>
    <div style="font-family:'DM Mono',monospace;color:var(--muted);font-size:10px" id="hdr-date"></div>
  </div>
</header>

<div id="kpis">
  <div class="kpi"><div class="kpi-icon">📋</div><div><div class="kpi-lbl">Total</div><div class="kpi-val" id="k-total" style="color:#60a5fa">—</div></div></div>
  <div class="kpi"><div class="kpi-icon">🔴</div><div><div class="kpi-lbl">Críticas</div><div class="kpi-val" id="k-crit" style="color:#dc2626">—</div></div></div>
  <div class="kpi"><div class="kpi-icon">🟠</div><div><div class="kpi-lbl">Abiertas</div><div class="kpi-val" id="k-open" style="color:#ea580c">—</div></div></div>
  <div class="kpi"><div class="kpi-icon">📍</div><div><div class="kpi-lbl">Zonas</div><div class="kpi-val" id="k-zones" style="color:#a78bfa">—</div></div></div>
</div>

<div id="tabs">
  <button class="tab active" data-view="lista">📋 Incidencias <span class="tbadge" id="b-lista">0</span></button>
  <button class="tab" data-view="mapa">🗺️ Mapa <span class="tbadge" id="b-mapa">0</span></button>
  <button class="tab" data-view="nodos">🗄️ Nodos <span class="tbadge" id="b-nodos">0</span></button>
</div>

<div id="content">

  <div class="view active" id="view-lista">
    <div class="snote">☁️ Datos en vivo desde Google Sheets — edita tu hoja y pulsa <strong>🔄 Actualizar</strong></div>
    <div class="filters">
      <input class="finput" id="l-buscar" type="text" placeholder="🔍 Buscar nodo o descripción..."/>
      <select class="fsel" id="l-prio"><option value="">Toda prioridad</option></select>
      <select class="fsel" id="l-tipo"><option value="">Todo tipo</option></select>
      <select class="fsel" id="l-zona"><option value="">Todas las zonas</option></select>
      <select class="fsel" id="l-estado"><option value="">Todos los estados</option></select>
    </div>
    <div class="table-wrap">
      <div class="t-head">
        <span>N°</span><span>Fecha</span><span>Hora</span><span>Nodo</span>
        <span>Tipo</span><span>Descripción</span><span>Prioridad</span><span>Estado</span>
      </div>
      <div id="tbody"><div class="empty">⏳ Cargando datos…</div></div>
    </div>
  </div>

  <div class="view" id="view-mapa">
    <div id="map-wrap"><div id="lmap"></div></div>
    <div id="msidebar">
      <div id="msidebar-hdr">🗺️ Mapa de Incidencias
        <div style="color:var(--muted);font-size:10px;font-weight:400;margin-top:2px">Clic en tarjeta o marcador</div>
      </div>
      <div id="mfilters">
        <select id="m-prio"><option value="">Toda prioridad</option></select>
        <select id="m-tipo"><option value="">Todo tipo</option></select>
        <select id="m-zona"><option value="">Todas las zonas</option></select>
        <input id="m-buscar" placeholder="🔍 Buscar nodo…"/>
      </div>
      <div id="mlist"><div class="empty">⏳ Cargando…</div></div>
      <div id="mlegend">
        <div class="leg"><div class="leg-dot" style="background:#dc2626"></div>CRÍTICA</div>
        <div class="leg"><div class="leg-dot" style="background:#ea580c"></div>ALTA</div>
        <div class="leg"><div class="leg-dot" style="background:#ca8a04"></div>MEDIA</div>
        <div class="leg"><div class="leg-dot" style="background:#16a34a"></div>BAJA</div>
      </div>
    </div>
  </div>

  <div class="view" id="view-nodos">
    <div style="background:var(--surface);border:1px solid var(--border);border-radius:9px;padding:11px 15px;margin-bottom:13px">
      <div style="font-weight:700;font-size:12px;margin-bottom:2px">Base de Datos de Nodos</div>
      <div style="color:var(--muted);font-size:10px">Cargado desde Google Sheets. Edita y pulsa 🔄 Actualizar.</div>
    </div>
    <div class="ng" id="ngrid"><div class="empty">⏳ Cargando…</div></div>
  </div>

</div>
</div>

<script>
// ══ URLs Google Sheets ══════════════════════════════════════════════
const URL_INC   = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRGTHjRFoyBamibzZT4h8TuBxgSgU9tzH1rNYSnRWRkGymsg1hiP0Y44BsjaJKVyg/pub?gid=1492328946&single=true&output=csv';
const URL_NODOS = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRGTHjRFoyBamibzZT4h8TuBxgSgU9tzH1rNYSnRWRkGymsg1hiP0Y44BsjaJKVyg/pub?gid=1751286472&single=true&output=csv';

// ══ Estado global ═══════════════════════════════════════════════════
let NODOS=[], INCS=[], mapReady=false, map=null, markers={};
const PC={CRÍTICA:'#dc2626',ALTA:'#ea580c',MEDIA:'#ca8a04',BAJA:'#16a34a'};
const EC={Abierta:'#dc2626','En Proceso':'#2563eb',Cerrada:'#16a34a'};

// ══ Helpers ══════════════════════════════════════════════════════════
function getNodo(id){
  if(!id) return null;
  const s = String(id).trim();
  return NODOS.find(n=>n.NODO_ID===s)
      || NODOS.find(n=>n.NODO_ID===s.split('_')[0])
      || NODOS.find(n=>n.NODO_ID.replace(/^A/,'')===s.replace(/^A/,'').split('_')[0])
      || null;
}
function getZona(n){ return n?(n.ZONA||''):''; }
function cleanDate(s){ return s?String(s).trim().split(' ')[0].split('T')[0]:''; }
function mkBadge(label,cmap){
  const c=cmap[label]||'#94a3b8';
  return `<span class="badge" style="background:${c}22;color:${c};border:1px solid ${c}44">${label||'—'}</span>`;
}

// ══ CSV parser ═══════════════════════════════════════════════════════
function splitLine(line){
  const v=[]; let cur='',q=false;
  for(let i=0;i<line.length;i++){
    const c=line[i];
    if(c==='"'){if(q&&line[i+1]==='"'){cur+='"';i++;}else q=!q;}
    else if(c===','&&!q){v.push(cur);cur='';}
    else cur+=c;
  }
  v.push(cur);
  return v.map(x=>x.replace(/^"|"$/g,'').trim());
}

function parseCSV(text){
  const lines=text.replace(/\r\n/g,'\n').replace(/\r/g,'\n')
    .split('\n').map(l=>l.trim()).filter(Boolean);
  if(lines.length<2) return [];

  // find header row: first cell must be a known key
  const KEYS=['ID','NODO_ID','FECHA'];
  let hi=0;
  for(let i=0;i<Math.min(lines.length,8);i++){
    const first=splitLine(lines[i])[0].toUpperCase();
    if(KEYS.includes(first)){hi=i;break;}
  }

  const headers=splitLine(lines[hi]).map(h=>h.toUpperCase());
  const rows=[];
  for(let i=hi+1;i<lines.length;i++){
    const vals=splitLine(lines[i]);
    if(!vals[0]||vals[0].startsWith('⚠')) continue;
    const obj={};
    headers.forEach((h,j)=>{obj[h]=vals[j]||'';});
    if(Object.values(obj).every(v=>!v)) continue;
    rows.push(obj);
  }
  return rows;
}

// ══ Fetch con proxy CORS ══════════════════════════════════════════════
async function fetchCSV(url){
  const proxies=[
    u=>`https://corsproxy.io/?${encodeURIComponent(u)}`,
    u=>`https://api.allorigins.win/raw?url=${encodeURIComponent(u)}`,
    u=>`https://thingproxy.freeboard.io/fetch/${u}`,
  ];
  for(const mk of proxies){
    try{
      const r=await fetch(mk(url),{cache:'no-cache'});
      if(!r.ok) continue;
      const buf=await r.arrayBuffer();
      let t=new TextDecoder('utf-8').decode(buf);
      t=t.replace(/^\uFEFF/,'');
      if(t&&t.includes(',')) return t;
    }catch(e){continue;}
  }
  throw new Error('Sin conexión CORS');
}

// ══ Sync status ═══════════════════════════════════════════════════════
function syncStatus(state,label){
  document.getElementById('sdot').className='sdot '+state;
  document.getElementById('slbl').textContent=label;
}

// ══ Cargar Sheets ══════════════════════════════════════════════════════
async function loadSheets(){
  syncStatus('spin','Conectando…');
  try{
    const [csvI,csvN]=await Promise.all([fetchCSV(URL_INC),fetchCSV(URL_NODOS)]);
    const inc=parseCSV(csvI), nod=parseCSV(csvN);
    if(!inc.length||!nod.length) throw new Error('Datos vacíos');
    INCS=inc; NODOS=nod;
    syncStatus('ok','Google Sheets · '+new Date().toLocaleTimeString('es-PE',{hour:'2-digit',minute:'2-digit'}));
    return true;
  }catch(e){
    syncStatus('err','Error de conexión');
    return false;
  }
}

// ══ Poblar selects dinámicamente ═══════════════════════════════════════
function fillSel(id,vals,current){
  const s=document.getElementById(id);
  if(!s) return;
  while(s.options.length>1) s.remove(1);
  vals.forEach(v=>{const o=document.createElement('option');o.value=v;o.textContent=v;s.appendChild(o);});
  if(current&&vals.includes(current)) s.value=current;
}

function populateSelects(){
  const zonas=[...new Set(NODOS.map(n=>getZona(n)).filter(Boolean))].sort();
  const tipos=[...new Set(INCS.map(i=>i.TIPO).filter(Boolean))].sort();
  const PORD=['CRÍTICA','ALTA','MEDIA','BAJA'];
  const prios=PORD.filter(p=>[...new Set(INCS.map(i=>i.PRIORIDAD))].includes(p));
  const estados=[...new Set(INCS.map(i=>i.ESTADO).filter(Boolean))].sort();

  ['l-zona','m-zona'].forEach(id=>fillSel(id,zonas,document.getElementById(id)?.value));
  ['l-tipo','m-tipo'].forEach(id=>fillSel(id,tipos,document.getElementById(id)?.value));
  ['l-prio','m-prio'].forEach(id=>fillSel(id,prios,document.getElementById(id)?.value));
  fillSel('l-estado',estados,document.getElementById('l-estado')?.value);
}

// ══ KPIs ════════════════════════════════════════════════════════════════
function renderKpis(){
  document.getElementById('k-total').textContent=INCS.length;
  document.getElementById('k-crit').textContent=INCS.filter(i=>i.PRIORIDAD==='CRÍTICA').length;
  document.getElementById('k-open').textContent=INCS.filter(i=>i.ESTADO==='Abierta').length;
  document.getElementById('k-zones').textContent=new Set(INCS.map(i=>getZona(getNodo(i.NODO_ID))).filter(Boolean)).size;
  document.getElementById('b-lista').textContent=INCS.length;
  document.getElementById('b-mapa').textContent=INCS.length;
  document.getElementById('b-nodos').textContent=NODOS.length;
}

// ══ Filtrar ═════════════════════════════════════════════════════════════
function getFiltered(pfx){
  const b=(document.getElementById(pfx+'-buscar')?.value||'').toLowerCase();
  const z=document.getElementById(pfx+'-zona')?.value||'';
  const p=document.getElementById(pfx+'-prio')?.value||'';
  const t=document.getElementById(pfx+'-tipo')?.value||'';
  const e=pfx==='l'?(document.getElementById('l-estado')?.value||''):'';
  return INCS.filter(inc=>{
    const n=getNodo(inc.NODO_ID);
    if(p&&inc.PRIORIDAD!==p) return false;
    if(t&&inc.TIPO!==t) return false;
    if(z&&getZona(n)!==z) return false;
    if(e&&inc.ESTADO!==e) return false;
    if(b){
      const hay=[inc.NODO_ID,inc.TIPO,inc.DESCRIPCION,inc.ESTADO,n?.NOMBRE_NODO,getZona(n)].join(' ').toLowerCase();
      if(!hay.includes(b)) return false;
    }
    return true;
  });
}

// ══ Tabla lista ═══════════════════════════════════════════════════════
function renderTabla(){
  const f=getFiltered('l');
  document.getElementById('b-lista').textContent=f.length;
  const body=document.getElementById('tbody');
  if(!f.length){body.innerHTML='<div class="empty">Sin resultados con este filtro.</div>';return;}
  body.innerHTML='';
  f.forEach((inc,i)=>{
    const n=getNodo(inc.NODO_ID);
    const row=document.createElement('div'); row.className='t-row';
    row.innerHTML=`
      <span class="mono" style="color:var(--muted)">${i+1}</span>
      <span class="mono" style="color:#94a3b8;font-size:10px">${cleanDate(inc.FECHA)}</span>
      <span class="mono" style="color:var(--muted);font-size:10px">${inc.HORA||''}</span>
      <div class="nc"><span class="nc-id">${inc.NODO_ID}</span><span class="nc-zona">${getZona(n)}</span></div>
      <span>${inc.TIPO||'—'}</span>
      <span style="color:#94a3b8" title="${inc.DESCRIPCION||''}">${inc.DESCRIPCION||'—'}</span>
      ${mkBadge(inc.PRIORIDAD,PC)}
      ${mkBadge(inc.ESTADO,EC)}
    `;
    body.appendChild(row);
  });
}

// ══ Nodos cards ═══════════════════════════════════════════════════════
function renderNodos(){
  const g=document.getElementById('ngrid'); g.innerHTML='';
  if(!NODOS.length){g.innerHTML='<div class="empty">Sin nodos</div>';return;}
  NODOS.forEach(n=>{
    const eC=n.ESTADO==='Activo'?'#16a34a':n.ESTADO==='Inactivo'?'#dc2626':'#ea580c';
    const c=document.createElement('div'); c.className='ncard';
    c.innerHTML=`
      <div class="ncard-top">
        <span style="font-family:'DM Mono',monospace;color:#60a5fa;font-size:11px;font-weight:600">${n.NODO_ID}</span>
        <span class="badge" style="background:${eC}22;color:${eC};border:1px solid ${eC}44">${n.ESTADO||'—'}</span>
      </div>
      <div class="ncard-name">${n.NOMBRE_NODO||n.NODO_ID}</div>
      <div class="ncard-info">
        <span>📍 ${getZona(n)||'—'}${n.PROVINCIA?' – '+n.PROVINCIA:''}</span>
        <span>👤 ${n.SUPERVISOR||'—'}</span>
        <span style="font-family:'DM Mono',monospace;font-size:9px;color:#374151">${n.LATITUD||''}, ${n.LONGITUD||''}</span>
      </div>
    `;
    g.appendChild(c);
  });
}

// ══ Mapa ════════════════════════════════════════════════════════════════
function initMap(){
  if(mapReady) return; mapReady=true;
  map=L.map('lmap',{center:[-9.8,-77.5],zoom:8});
  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',{
    attribution:'© OpenStreetMap © CARTO',subdomains:'abcd',maxZoom:19
  }).addTo(map);
  setTimeout(()=>{map.invalidateSize();renderMarkers();},150);
}

function makeIcon(prio){
  const c=PC[prio]||'#60a5fa', l=(prio||'?')[0];
  const svg=`<svg xmlns="http://www.w3.org/2000/svg" width="28" height="36">
    <path d="M14 0C6.27 0 0 6.27 0 14c0 9.63 14 22 14 22s14-12.37 14-22C28 6.27 21.73 0 14 0z"
      fill="${c}" stroke="rgba(255,255,255,.4)" stroke-width="1.5"/>
    <circle cx="14" cy="14" r="7" fill="rgba(0,0,0,.3)"/>
    <text x="14" y="18" text-anchor="middle" font-size="8" fill="#fff" font-weight="bold" font-family="Arial">${l}</text>
  </svg>`;
  return L.divIcon({html:`<img src="data:image/svg+xml,${encodeURIComponent(svg)}" width="28" height="36"/>`,iconSize:[28,36],iconAnchor:[14,36],popupAnchor:[0,-38],className:''});
}

function popupHTML(inc,n){
  const pC=PC[inc.PRIORIDAD]||'#fff', eC=EC[inc.ESTADO]||'#94a3b8';
  return `<div class="pp-title">${inc.NODO_ID}</div>
    <div class="pp-row"><span class="pp-k">Nodo</span><span class="pp-v">${n?.NOMBRE_NODO||'—'}</span></div>
    <div class="pp-row"><span class="pp-k">Zona</span><span class="pp-v">${getZona(n)||'—'}</span></div>
    <div class="pp-row"><span class="pp-k">Tipo</span><span class="pp-v">${inc.TIPO||'—'}</span></div>
    <div style="color:#94a3b8;font-size:10px;margin:4px 0 6px;line-height:1.4">${inc.DESCRIPCION||''}</div>
    <div class="pp-row"><span class="pp-k">Prioridad</span><span class="pp-v" style="color:${pC};font-weight:700">${inc.PRIORIDAD||'—'}</span></div>
    <div class="pp-row"><span class="pp-k">Estado</span><span class="pp-v" style="color:${eC};font-weight:700">${inc.ESTADO||'—'}</span></div>
    <div class="pp-row"><span class="pp-k">Fecha</span><span class="pp-v">${cleanDate(inc.FECHA)} ${inc.HORA||''}</span></div>
    <div class="pp-row"><span class="pp-k">Supervisor</span><span class="pp-v">${n?.SUPERVISOR||'—'}</span></div>
    <div class="pp-row"><span class="pp-k">Coords</span><span class="pp-v">${n?.LATITUD||''}, ${n?.LONGITUD||''}</span></div>`;
}

function renderMarkers(){
  if(!map) return;
  Object.values(markers).forEach(m=>map.removeLayer(m));
  markers={};
  const f=getFiltered('m');
  const bounds=[];
  f.forEach((inc,idx)=>{
    const n=getNodo(inc.NODO_ID);
    if(!n) return;
    const lat=parseFloat(n.LATITUD), lng=parseFloat(n.LONGITUD);
    if(isNaN(lat)||isNaN(lng)) return;
    bounds.push([lat,lng]);
    const key=inc.ID||String(idx);
    const m=L.marker([lat,lng],{icon:makeIcon(inc.PRIORIDAD)}).addTo(map)
      .bindPopup(popupHTML(inc,n),{maxWidth:280});
    m.on('click',()=>hlCard(key));
    markers[key]=m;
  });
  if(bounds.length>0) map.fitBounds(L.latLngBounds(bounds),{padding:[30,30],maxZoom:11,animate:true});
  renderMapList(f);
}

function renderMapList(f){
  const c=document.getElementById('mlist'); c.innerHTML='';
  if(!f?.length){c.innerHTML='<div class="empty" style="font-size:11px">Sin resultados</div>';return;}
  f.forEach((inc,idx)=>{
    const n=getNodo(inc.NODO_ID);
    const pC=PC[inc.PRIORIDAD]||'#fff', eC=EC[inc.ESTADO]||'#94a3b8';
    const key=inc.ID||String(idx);
    const card=document.createElement('div'); card.className='mc'; card.id=`mc-${key}`;
    card.innerHTML=`
      <div class="mc-top">
        <span class="mc-id">${inc.NODO_ID}</span>
        <span class="badge" style="background:${pC}22;color:${pC};border:1px solid ${pC}44">${inc.PRIORIDAD||'—'}</span>
      </div>
      <div class="mc-tipo">${inc.TIPO||'—'}</div>
      <div class="mc-meta">
        <span>📍 ${getZona(n)||'—'}</span>
        <span>👤 ${n?.SUPERVISOR||'—'}</span>
        <span style="color:${eC}">${inc.ESTADO||'—'}</span>
      </div>`;
    card.addEventListener('click',()=>{
      const lat=parseFloat(n?.LATITUD||0), lng=parseFloat(n?.LONGITUD||0);
      if(map&&!isNaN(lat)&&lat!==0){map.setView([lat,lng],13,{animate:true});markers[key]?.openPopup();hlCard(key);}
    });
    c.appendChild(card);
  });
}

function hlCard(key){
  document.querySelectorAll('.mc').forEach(c=>c.classList.remove('sel'));
  const el=document.getElementById(`mc-${key}`);
  if(el){el.classList.add('sel');el.scrollIntoView({behavior:'smooth',block:'nearest'});}
}

// ══ Render completo ═══════════════════════════════════════════════════
function renderAll(){
  populateSelects();
  renderKpis();
  renderTabla();
  renderNodos();
  if(mapReady) renderMarkers();
}

// ══ Eventos ═══════════════════════════════════════════════════════════
document.querySelectorAll('.tab').forEach(t=>{
  t.addEventListener('click',()=>{
    document.querySelectorAll('.tab').forEach(x=>x.classList.remove('active'));
    document.querySelectorAll('.view').forEach(x=>x.classList.remove('active'));
    t.classList.add('active');
    document.getElementById('view-'+t.dataset.view).classList.add('active');
    if(t.dataset.view==='mapa') setTimeout(()=>{initMap();if(mapReady)renderMarkers();},50);
  });
});

['l-buscar','l-prio','l-tipo','l-zona','l-estado'].forEach(id=>{
  const el=document.getElementById(id);
  el.addEventListener('input',renderTabla);
  el.addEventListener('change',renderTabla);
});

['m-prio','m-tipo','m-zona','m-buscar'].forEach(id=>{
  const el=document.getElementById(id);
  el.addEventListener('input',()=>{if(mapReady)renderMarkers();});
  el.addEventListener('change',()=>{if(mapReady)renderMarkers();});
});

document.getElementById('btn-refresh').addEventListener('click',async()=>{
  const btn=document.getElementById('btn-refresh');
  btn.textContent='⏳'; btn.disabled=true;
  const ok=await loadSheets();
  if(ok) renderAll();
  btn.textContent='🔄 Actualizar'; btn.disabled=false;
});

document.getElementById('btn-demo-fallback').addEventListener('click',()=>{
  hidLoader(); renderAll();
});

function hidLoader(){
  const l=document.getElementById('loader');
  l.classList.add('hide');
  setTimeout(()=>l.style.display='none',420);
}

// ══ Inicio ════════════════════════════════════════════════════════════
document.getElementById('hdr-date').textContent=
  new Date().toLocaleDateString('es-PE',{weekday:'short',year:'numeric',month:'short',day:'numeric'});

(async()=>{
  const ok=await loadSheets();
  if(ok){hidLoader();renderAll();}
  else{
    document.getElementById('loader-msg').textContent='No se pudo conectar';
    document.getElementById('loader-error').style.display='block';
  }
})();
</script>
</body>
</html>
