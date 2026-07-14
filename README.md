K-Map Solver จำนวนค่า Digital logic
<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>K-Map Solver Pro — SOP / POS / Circuit / Equation Parser</title>
<style>
  :root{
    --bg-a: #eef8fb; --bg-b: #eafaf3;
    --surface: #ffffff; --surface-blue: #e7f4fc; --surface-green: #e8faf1;
    --line: #cfe6ee; --line-soft: #dcf0e8;
    --ink: #1d3345; --ink-dim: #6f8b98;
    --blue: #3fa0d6; --blue-deep: #256f9c;
    --green: #34c495; --green-deep: #1f9873;
    --warn: #d9822b; --warn-bg:#fdf1e2;
    --grid0-bg: #eef3f6; --grid0-fg:#8ba3ae;
    --grid1-bg: #d8f6e8; --grid1-fg:#1f9873;
    --gridx-bg: #e4e9fb; --gridx-fg:#5b64c9;
    --mono: "IBM Plex Mono", "SF Mono", Consolas, monospace;
    --sans: "IBM Plex Sans", "Segoe UI", sans-serif;
    --shadow: 0 2px 10px rgba(63,160,214,0.08);
  }
  *{box-sizing:border-box;}
  body{
    margin:0;
    background: linear-gradient(160deg, var(--bg-a) 0%, var(--bg-b) 100%);
    background-attachment: fixed;
    color:var(--ink); font-family:var(--sans);
    padding:20px 14px 60px;
    -webkit-tap-highlight-color: transparent;
  }
  .wrap{max-width:760px;margin:0 auto;}
  header{margin-bottom:22px;}
  .eyebrow{
    font-family:var(--mono); font-size:11px; letter-spacing:.14em;
    color:var(--blue-deep); text-transform:uppercase; margin-bottom:6px; font-weight:600;
  }
  h1{
    font-size:26px; margin:0 0 6px; font-weight:700; letter-spacing:-0.01em;
    background: linear-gradient(90deg, var(--blue-deep), var(--green-deep));
    -webkit-background-clip: text; background-clip: text; color: transparent;
  }
  .sub{color:var(--ink-dim); font-size:13.5px; line-height:1.55; max-width:58ch;}

  .card{
    background:var(--surface); border:1px solid var(--line); border-radius:14px;
    padding:16px; margin-bottom:16px; box-shadow: var(--shadow);
  }
  .card h2{
    font-size:12.5px; text-transform:uppercase; letter-spacing:.07em; color:var(--blue-deep);
    margin:0 0 12px; font-weight:700; font-family:var(--mono);
    display:flex; align-items:center; gap:8px;
  }
  .card h2 .dot{width:7px;height:7px;border-radius:50%;background:var(--green);display:inline-block;}

  .tabs{display:flex; gap:6px; margin-bottom:16px;}
  .tab{
    flex:1; padding:10px 8px; text-align:center; background:var(--surface);
    border:1px solid var(--line); border-radius:10px; font-size:13px;
    color:var(--ink-dim); cursor:pointer; font-family:var(--mono); font-weight:600;
    transition: all .15s ease;
  }
  .tab.active{
    background: linear-gradient(135deg, var(--blue), var(--green));
    border-color: transparent; color:#fff; box-shadow: var(--shadow);
  }

  .segwrap{display:flex; gap:6px; flex-wrap:wrap;}
  .seg{
    padding:8px 14px; background:var(--surface-blue); border:1px solid var(--line);
    border-radius:20px; font-size:12.5px; color:var(--blue-deep); cursor:pointer;
    font-family:var(--mono); font-weight:600;
  }
  .seg.active{
    background: linear-gradient(135deg, var(--blue), var(--green));
    border-color:transparent; color:#fff;
  }

  .eqinput-row{display:flex; gap:8px; flex-wrap:wrap;}
  input.eqbox{
    flex:1; min-width:220px;
    font-family:var(--mono); font-size:15px;
    padding:12px 14px; border-radius:10px;
    border:1.5px solid var(--line); background:var(--surface-blue);
    color:var(--ink); outline:none;
  }
  input.eqbox:focus{ border-color:var(--blue); background:var(--surface); }
  .eq-status{ font-family:var(--mono); font-size:12px; margin-top:8px; min-height:16px; }
  .eq-status.ok{ color:var(--green-deep); }
  .eq-status.err{ color:var(--warn); background:var(--warn-bg); padding:6px 10px; border-radius:6px; display:inline-block; }
  .eq-hint{font-size:11.5px; color:var(--ink-dim); margin-top:8px; line-height:1.5;}
  .eq-hint code{ background:var(--surface-blue); padding:1px 5px; border-radius:4px; font-family:var(--mono); }

  .kmap-scroll{overflow-x:auto; padding-bottom:4px;}
  table.kmap{ border-collapse:collapse; margin-top:6px; font-family:var(--mono); }
  table.kmap th{ font-size:12px; color:var(--ink-dim); padding:4px 8px; font-weight:600; white-space:nowrap; }
  table.kmap th.corner{ color:var(--ink-dim); font-size:10px; }
  table.kmap td{
    width:46px; height:42px; text-align:center; border:1px solid var(--line);
    cursor:pointer; user-select:none; font-size:16px; font-weight:700; border-radius:6px;
    transition:transform .08s ease, filter .1s ease;
  }
  td.v0{background:var(--grid0-bg); color:var(--grid0-fg);}
  td.v1{background:var(--grid1-bg); color:var(--grid1-fg);}
  td.vx{background:var(--gridx-bg); color:var(--gridx-fg);}
  td:active{transform:scale(0.94);}
  .hint{font-size:11.5px; color:var(--ink-dim); margin-top:8px; line-height:1.5;}

  .tt-scroll{overflow-x:auto;}
  table.tt{border-collapse:collapse; font-family:var(--mono); font-size:13px;}
  table.tt th, table.tt td{ border:1px solid var(--line); padding:6px 10px; text-align:center; }
  table.tt th{color:var(--ink-dim); font-weight:600; font-size:11px;}
  table.tt td.out{cursor:pointer; font-weight:700; width:50px; border-radius:6px;}
  table.tt td.out.v0{background:var(--grid0-bg); color:var(--grid0-fg);}
  table.tt td.out.v1{background:var(--grid1-bg); color:var(--grid1-fg);}
  table.tt td.out.vx{background:var(--gridx-bg); color:var(--gridx-fg);}
  table.tt td.idx{color:var(--ink-dim); font-size:11px;}

  .actions{display:flex; gap:8px; margin-top:14px; flex-wrap:wrap;}
  button.primary, button.ghost{
    font-family:var(--mono); font-size:12.5px; padding:11px 16px;
    border-radius:10px; cursor:pointer; border:1px solid var(--line);
  }
  button.primary{
    background: linear-gradient(135deg, var(--blue), var(--green));
    color:#fff; border-color:transparent; font-weight:700; box-shadow: var(--shadow);
  }
  button.ghost{ background:var(--surface-blue); color:var(--blue-deep); font-weight:600; }
  button.ghost:hover{ filter:brightness(0.97); }
  button:disabled{ opacity:0.5; cursor:default; }

  .live-badge{
    display:inline-flex; align-items:center; gap:6px;
    font-family:var(--mono); font-size:11px; color:var(--green-deep);
    background:var(--surface-green); padding:5px 10px; border-radius:20px;
    margin-left:auto;
  }
  .live-dot{width:6px;height:6px;border-radius:50%;background:var(--green); animation:pulse 1.6s infinite;}
  @keyframes pulse{ 0%,100%{opacity:1;} 50%{opacity:.35;} }
  .card h2.with-badge{ justify-content:space-between; }

  .eq-block{margin-bottom:14px;}
  .eq-label{font-family:var(--mono); font-size:11px; color:var(--ink-dim); margin-bottom:6px; text-transform:uppercase; letter-spacing:.06em;}
  .result-eq{
    font-family:var(--mono); font-size:18px; background: var(--surface-green);
    border:1px solid var(--line-soft); border-radius:10px; padding:14px 16px;
    color:var(--green-deep); line-height:1.6; overflow-x:auto; white-space:nowrap;
  }
  .result-eq.pos{ background:var(--surface-blue); color:var(--blue-deep); }
  .result-eq .z{color:var(--ink); font-weight:700;}
  .term-list{margin-top:10px; display:flex; flex-direction:column; gap:6px;}
  .term-row{
    display:flex; justify-content:space-between; align-items:center;
    font-family:var(--mono); font-size:12px; padding:8px 10px;
    background:var(--surface-blue); border-radius:8px; border-left:3px solid var(--blue);
  }
  .term-row.pos{ background:var(--surface-green); border-left-color:var(--green); }
  .term-row .cells{color:var(--ink-dim); font-size:10.5px;}
  .empty-msg{color:var(--ink-dim); font-size:13px; font-style:italic;}

  .circuit-scroll{ overflow-x:auto; background:var(--surface-blue); border-radius:10px; padding:10px; border:1px solid var(--line-soft); }
  .gate-legend{ margin-top:12px; display:flex; flex-direction:column; gap:6px; }
  .gate-legend-row{
    font-family:var(--mono); font-size:11.5px; padding:8px 10px; background:var(--surface);
    border-radius:8px; border:1px solid var(--line); display:flex; justify-content:space-between; gap:10px; flex-wrap:wrap;
  }
  .gate-legend-row b{ color:var(--blue-deep); }
  .gate-legend-row .out-tag{ color:var(--green-deep); font-weight:700; }

  footer{ text-align:center; color:var(--ink-dim); font-size:11px; margin-top:30px; font-family:var(--mono); }

  @media (max-width:420px){
    table.kmap td{width:38px; height:38px; font-size:14px;}
    .result-eq{font-size:15px;}
  }
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div class="eyebrow">Boolean Logic Toolkit</div>
    <h1>K-Map Solver Pro</h1>
    <div class="sub">พิมพ์สมการบูลีน หรือกรอกตาราง K-Map / Truth Table โดยตรง ระบบจะแปลงค่าให้กันอัตโนมัติทั้งสองทาง พร้อมลดรูป SOP และ POS และวาดวงจรลอจิกเกตให้ทันที — ทำงานสดทุกครั้งที่แก้ไข ไม่ต้องกดปุ่ม</div>
  </header>

  <div class="card">
    <h2><span class="dot"></span>จำนวนตัวแปร</h2>
    <div class="segwrap" id="varsel"></div>
  </div>

  <div class="card">
    <h2 class="with-badge"><span style="display:flex;align-items:center;gap:8px;"><span class="dot"></span>ป้อนสมการบูลีน (Equation → Table)</span></h2>
    <div class="eqinput-row">
      <input class="eqbox" id="eqInput" type="text" placeholder="เช่น A'B + AC'D  หรือ  (A+B)(A'+C)" autocomplete="off" autocapitalize="off" spellcheck="false">
      <button class="primary" id="applyEqBtn">แปลง → ใส่ในตาราง</button>
    </div>
    <div class="actions" style="margin-top:10px;">
      <button class="ghost" id="loadSopBtn">ดึงสมการ SOP ปัจจุบันมาที่ช่องนี้</button>
      <button class="ghost" id="loadPosBtn">ดึงสมการ POS ปัจจุบันมาที่ช่องนี้</button>
    </div>
    <div class="eq-status" id="eqStatus"></div>
    <div class="eq-hint">
      ไวยากรณ์ที่รองรับ: <code>'</code> หรือ <code>!</code> = NOT, <code>+</code> = OR, <code>.</code> หรือเขียนติดกัน = AND, <code>()</code> = วงเล็บ<br>
      ตัวอย่าง: <code>A'B'+AC</code>, <code>(A+B')(C+D)</code>, <code>!A.B + C</code>
    </div>
  </div>

  <div class="tabs">
    <div class="tab active" data-tab="kmap">กรอก K-Map</div>
    <div class="tab" data-tab="truth">กรอก Truth Table</div>
  </div>

  <div class="card" id="panel-kmap">
    <h2><span class="dot"></span>ตาราง Karnaugh Map — แตะเพื่อสลับ 0 → 1 → X</h2>
    <div class="kmap-scroll"><div id="kmapContainer"></div></div>
    <div class="hint">X = don't care (ใช้ช่วยลดรูปสมการเมื่อเป็นประโยชน์)</div>
  </div>

  <div class="card" id="panel-truth" style="display:none;">
    <h2><span class="dot"></span>Truth Table — แตะคอลัมน์ Output เพื่อสลับค่า</h2>
    <div class="tt-scroll"><div id="ttContainer"></div></div>
  </div>

  <div class="actions">
    <button class="ghost" id="clearBtn">ล้างค่าทั้งหมด</button>
    <button class="ghost" id="fillRandom">สุ่มตัวอย่าง</button>
    <span class="live-badge"><span class="live-dot"></span>คำนวณสดอัตโนมัติ</span>
  </div>

  <div class="card" id="resultCard">
    <h2><span class="dot"></span>ผลลัพธ์สมการ (ลดรูปแล้ว)</h2>
    <div class="segwrap" style="margin-bottom:14px;" id="dispSwitch">
      <div class="seg active" data-disp="both">แสดงทั้งคู่</div>
      <div class="seg" data-disp="sop">SOP เท่านั้น</div>
      <div class="seg" data-disp="pos">POS เท่านั้น</div>
    </div>

    <div class="eq-block" id="sopBlock">
      <div class="eq-label">Sum of Products (SOP) — ผลรวมของผลคูณ</div>
      <div class="result-eq" id="sopEq"></div>
      <div class="term-list" id="sopTermList"></div>
    </div>

    <div class="eq-block" id="posBlock">
      <div class="eq-label">Product of Sums (POS) — ผลคูณของผลรวม</div>
      <div class="result-eq pos" id="posEq"></div>
      <div class="term-list" id="posTermList"></div>
    </div>
  </div>

  <div class="card" id="circuitCard">
    <h2><span class="dot"></span>แผนภาพวงจรลอจิกเกต</h2>
    <div class="segwrap" style="margin-bottom:14px;" id="circuitSwitch">
      <div class="seg active" data-circuit="sop">สร้างจาก SOP (AND–OR)</div>
      <div class="seg" data-circuit="pos">สร้างจาก POS (OR–AND)</div>
    </div>
    <div class="circuit-scroll"><div id="circuitContainer"></div></div>
    <div class="gate-legend" id="gateLegend"></div>
  </div>

  <footer>เครื่องมือช่วยคำนวณ Boolean Algebra — ทำงานในเบราว์เซอร์ทั้งหมด ไม่ส่งข้อมูลออกไปที่ใด</footer>
</div>

<script>
(function(){
  let numVars = 4;
  let varNames = ['A','B','C','D','E'];
  let mode = 'kmap';
  let values = {};
  let dispMode = 'both';
  let circuitMode = 'sop';
  let lastSOP = null, lastPOS = null;

  const varselEl = document.getElementById('varsel');
  const kmapContainer = document.getElementById('kmapContainer');
  const ttContainer = document.getElementById('ttContainer');
  const panelKmap = document.getElementById('panel-kmap');
  const panelTruth = document.getElementById('panel-truth');
  const eqInput = document.getElementById('eqInput');
  const eqStatus = document.getElementById('eqStatus');

  function grayCode(bits){ const n=1<<bits; const seq=[]; for(let i=0;i<n;i++) seq.push(i^(i>>1)); return seq; }

  function initValues(){
    values = {};
    const total = 1<<numVars;
    for(let i=0;i<total;i++) values[i]=0;
  }

  function renderVarSelector(){
    varselEl.innerHTML='';
    for(let v=2; v<=5; v++){
      const b=document.createElement('div');
      b.className='seg'+(v===numVars?' active':'');
      b.textContent=v+' ตัวแปร';
      b.onclick=()=>{
        numVars=v; initValues(); renderVarSelector(); renderAll();
        eqStatus.textContent=''; eqStatus.className='eq-status';
        solveAndRender();
      };
      varselEl.appendChild(b);
    }
  }

  function splitBits(n){ const rowBits=Math.floor(n/2); const colBits=n-rowBits; return {rowBits,colBits}; }
  function cellClass(val){ if(val===1) return 'v1'; if(val===2) return 'vx'; return 'v0'; }
  function cellLabel(val){ return val===2?'X':String(val); }
  function nextVal(val){ return (val+1)%3; }

  function renderKmap(){
    const {rowBits, colBits} = splitBits(numVars);
    const rowGray = grayCode(rowBits);
    const colGray = grayCode(colBits);
    const rowVars = varNames.slice(0, rowBits);
    const colVars = varNames.slice(rowBits, numVars);

    function groupLabel(bitsVal, vars){
      let label='';
      for(let i=0;i<vars.length;i++){
        const bit=(bitsVal>>(vars.length-1-i))&1;
        label += bit===0 ? (vars[i]+"'") : vars[i];
      }
      return label || '—';
    }

    let html = '<table class="kmap"><thead><tr><th class="corner">'+rowVars.join('')+' \\ '+colVars.join('')+'</th>';
    colGray.forEach(cg=>{ html += '<th>'+groupLabel(cg, colVars)+'</th>'; });
    html += '</tr></thead><tbody>';
    rowGray.forEach(rg=>{
      html += '<tr><th>'+groupLabel(rg, rowVars)+'</th>';
      colGray.forEach(cg=>{
        const idx = (rg<<colBits)|cg;
        const val = values[idx];
        html += '<td class="'+cellClass(val)+'" data-idx="'+idx+'">'+cellLabel(val)+'</td>';
      });
      html += '</tr>';
    });
    html += '</tbody></table>';
    kmapContainer.innerHTML = html;
    kmapContainer.querySelectorAll('td[data-idx]').forEach(function(td){
      td.addEventListener('click', function(){
        const idx = parseInt(td.getAttribute('data-idx'), 10);
        values[idx] = nextVal(values[idx]);
        td.className = cellClass(values[idx]);
        td.textContent = cellLabel(values[idx]);
        renderTruthTable();
        solveAndRender();
      });
    });
  }

  function renderTruthTable(){
    const total = 1<<numVars;
    const vars = varNames.slice(0,numVars);
    let html = '<table class="tt"><thead><tr><th class="idx">#</th>';
    vars.forEach(function(v){ html += '<th>'+v+'</th>'; });
    html += '<th>Out</th></tr></thead><tbody>';
    for(let i=0;i<total;i++){
      html += '<tr><td class="idx">'+i+'</td>';
      for(let b=0;b<numVars;b++){ const bit=(i>>(numVars-1-b))&1; html += '<td>'+bit+'</td>'; }
      const val = values[i];
      html += '<td class="out '+cellClass(val)+'" data-idx="'+i+'">'+cellLabel(val)+'</td></tr>';
    }
    html += '</tbody></table>';
    ttContainer.innerHTML = html;
    ttContainer.querySelectorAll('td.out').forEach(function(td){
      td.addEventListener('click', function(){
        const idx = parseInt(td.getAttribute('data-idx'), 10);
        values[idx] = nextVal(values[idx]);
        td.className = 'out '+cellClass(values[idx]);
        td.textContent = cellLabel(values[idx]);
        renderKmap();
        solveAndRender();
      });
    });
  }

  function renderAll(){ renderKmap(); renderTruthTable(); }

  document.querySelectorAll('.tab').forEach(function(tab){
    tab.addEventListener('click', function(){
      document.querySelectorAll('.tab').forEach(function(t){ t.classList.remove('active'); });
      tab.classList.add('active');
      mode = tab.getAttribute('data-tab');
      if(mode==='kmap'){ panelKmap.style.display='block'; panelTruth.style.display='none'; renderKmap(); }
      else { panelKmap.style.display='none'; panelTruth.style.display='block'; renderTruthTable(); }
    });
  });

  // ---------- Quine-McCluskey core ----------
  function combineTerm(a, b, n){
    let diff=0, idx=-1;
    for(let i=0;i<n;i++){ if(a.bits[i]!==b.bits[i]){ diff++; idx=i; } }
    if(diff===1){ const bits=a.bits.slice(); bits[idx]='-'; return bits; }
    return null;
  }
  function toBits(n, val){ const arr=[]; for(let i=n-1;i>=0;i--) arr.push(((val>>i)&1).toString()); return arr; }

  function findCover(nv, targetIdxs, dontcareIdxs){
    if(targetIdxs.length===0) return {constant:'0', terms:[]};
    if(targetIdxs.length + dontcareIdxs.length === (1<<nv)) return {constant:'1', terms:[]};

    const allTerms = targetIdxs.concat(dontcareIdxs);
    let currentLevel = allTerms.map(function(m){ return { bits: toBits(nv,m), minterms: new Set([m]) }; });
    const primeImplicants = [];

    while(currentLevel.length>0){
      const used = new Array(currentLevel.length).fill(false);
      const nextLevelMap = new Map();
      for(let i=0;i<currentLevel.length;i++){
        for(let j=i+1;j<currentLevel.length;j++){
          const combined = combineTerm(currentLevel[i], currentLevel[j], nv);
          if(combined){
            used[i]=true; used[j]=true;
            const key = combined.join('');
            const mergedMinterms = new Set([...currentLevel[i].minterms, ...currentLevel[j].minterms]);
            if(!nextLevelMap.has(key)) nextLevelMap.set(key, {bits:combined, minterms:mergedMinterms});
            else { const ex=nextLevelMap.get(key); mergedMinterms.forEach(function(m){ ex.minterms.add(m); }); }
          }
        }
      }
      for(let i=0;i<currentLevel.length;i++){ if(!used[i]) primeImplicants.push(currentLevel[i]); }
      currentLevel = Array.from(nextLevelMap.values());
      if(nextLevelMap.size===0) break;
    }

    const piMap = new Map();
    primeImplicants.forEach(function(pi){ const key=pi.bits.join(''); if(!piMap.has(key)) piMap.set(key,pi); });
    const uniquePIs = Array.from(piMap.values());

    const coverTarget = new Set(targetIdxs);
    const piCovers = uniquePIs.map(function(pi){
      const covers = new Set();
      pi.minterms.forEach(function(m){ if(coverTarget.has(m)) covers.add(m); });
      return {pi:pi, covers:covers};
    }).filter(function(pc){ return pc.covers.size>0; });

    const chosen = [];
    const remaining = new Set(coverTarget);
    while(remaining.size>0){
      const mintermCoverCount = new Map();
      remaining.forEach(function(m){ mintermCoverCount.set(m,[]); });
      piCovers.forEach(function(pc,idx){ pc.covers.forEach(function(m){ if(remaining.has(m)) mintermCoverCount.get(m).push(idx); }); });

      let essentialIdx=-1;
      for(const entry of mintermCoverCount.entries()){ if(entry[1].length===1){ essentialIdx=entry[1][0]; break; } }

      let pickedIdx;
      if(essentialIdx!==-1){ pickedIdx=essentialIdx; }
      else{
        let bestIdx=-1,bestCount=-1;
        piCovers.forEach(function(pc,idx){ let cnt=0; pc.covers.forEach(function(m){ if(remaining.has(m)) cnt++; }); if(cnt>bestCount){bestCount=cnt;bestIdx=idx;} });
        pickedIdx=bestIdx;
      }
      if(pickedIdx===-1 || pickedIdx===undefined) break;

      chosen.push(piCovers[pickedIdx].pi);
      piCovers[pickedIdx].covers.forEach(function(m){ remaining.delete(m); });
      piCovers.splice(pickedIdx, 1);
    }

    return { constant: null, terms: chosen.map(c => ({ bits: c.bits, minterms: Array.from(c.minterms).filter(m => coverTarget.has(m)).sort((a,b)=>a-b) })) };
  }

  // ---------- Parser ----------
  function parseEquation(str, nv) {
    str = str.replace(/\s+/g, '').toUpperCase();
    if (!str) return null;
    
    const vars = varNames.slice(0, nv);
    const total = 1 << nv;
    const res = {};
    for (let i = 0; i < total; i++) res[i] = 0;

    // ไวยากรณ์ตรวจสอบอย่างง่าย
    try {
      for (let i = 0; i < total; i++) {
        let env = {};
        for (let b = 0; b < nv; b++) {
          env[vars[b]] = ((i >> (nv - 1 - b)) & 1) === 1;
        }
        let jsExpr = str;
        
        // แปลง ! เป็น !
        jsExpr = jsExpr.replace(/!/g, ' ! ');
        // แปลงตัวแปรเดี่ยวที่มี ' ตามหลังเป็นนิเสธ
        vars.forEach(v => {
          jsExpr = jsExpr.replace(new RegExp(v + "'", 'g'), `(!${v})`);
        });
        // แปลงผลคูณ (AND) แบบเขียนติดกัน
        vars.forEach(v => {
          jsExpr = jsExpr.replace(new RegExp(v, 'g'), ` ${v} `);
        });
        
        // เสริมเครื่องหมาย AND ระหว่างตัวแปรที่อยู่ชิดกัน
        jsExpr = jsExpr.trim().replace(/\s+/g, ' ');
        let tokens = jsExpr.split(' ');
        let spaced = [];
        for (let t = 0; t < tokens.length; t++) {
          spaced.push(tokens[t]);
          if (t < tokens.length - 1) {
            let curr = tokens[t];
            let next = tokens[t+1];
            let isCurrVarOrRightParen = vars.includes(curr) || curr.endsWith(')') || curr.endsWith("'");
            let isNextVarOrLeftParen = vars.includes(next) || next.startsWith('(') || next.startsWith('!');
            if (isCurrVarOrRightParen && isNextVarOrLeftParen) {
              spaced.push('&&');
            }
          }
        }
        jsExpr = spaced.join(' ');
        
        // แปลงสัญลักษณ์ตัวดำเนินการบูลีนเป็นจาวาสคริปต์
        jsExpr = jsExpr.replace(/\+/g, ' || ');
        jsExpr = jsExpr.replace(/\./g, ' && ');
        
        // เติมค่าตัวแปรลงใน Environment sandbox
        vars.forEach(v => {
          jsExpr = jsExpr.replace(new RegExp('\\b' + v + '\\b', 'g'), env[v] ? 'true' : 'false');
        });

        // ดำเนินการ Evaluate ประเมินผลสมการ
        let val = eval(jsExpr);
        res[i] = val ? 1 : 0;
      }
      return res;
    } catch (e) {
      return null;
    }
  }

  // ---------- Solve and Display ----------
  function makeTermString(bits, isPOS=false){
    const terms = [];
    for(let i=0; i<bits.length; i++){
      if(bits[i] === '-') continue;
      if(isPOS){
        terms.push(bits[i] === '1' ? (varNames[i] + "'") : varNames[i]);
      } else {
        terms.push(bits[i] === '0' ? (varNames[i] + "'") : varNames[i]);
      }
    }
    if(terms.length === 0) return isPOS ? '0' : '1';
    return isPOS ? ('(' + terms.join('+') + ')') : terms.join('');
  }

  function solveAndRender(){
    const ones = [];
    const dontcares = [];
    const zeros = [];
    const total = 1<<numVars;

    for(let i=0;i<total;i++){
      if(values[i]===1) ones.push(i);
      else if(values[i]===2) dontcares.push(i);
      else zeros.push(i);
    }

    const sopResult = findCover(numVars, ones, dontcares);
    const posResult = findCover(numVars, zeros, dontcares);

    // Render SOP
    const sopEqEl = document.getElementById('sopEq');
    const sopTermListEl = document.getElementById('sopTermList');
    sopTermListEl.innerHTML = '';
    
    if(sopResult.constant !== null){
      lastSOP = sopResult.constant;
      sopEqEl.innerHTML = '<span class="z">Z</span> = ' + sopResult.constant;
    } else {
      const termStrings = sopResult.terms.map(t => makeTermString(t.bits, false));
      lastSOP = termStrings.join(' + ');
      sopEqEl.innerHTML = '<span class="z">Z</span> = ' + termStrings.map(t => `<span>${t}</span>`).join(' + ');
      
      sopResult.terms.forEach((t, i) => {
        const tr = document.createElement('div');
        tr.className = 'term-row';
        tr.innerHTML = `<span><b>${termStrings[i]}</b></span><span class="cells">ครอบคลุมช่อง: m(${t.minterms.join(', ')})</span>`;
        sopTermListEl.appendChild(tr);
      });
    }

    // Render POS
    const posEqEl = document.getElementById('posEq');
    const posTermListEl = document.getElementById('posTermList');
    posTermListEl.innerHTML = '';

    if(posResult.constant !== null){
      lastPOS = posResult.constant === '0' ? '1' : '0'; // Dual
      posEqEl.innerHTML = '<span class="z">Z</span> = ' + lastPOS;
    } else {
      const termStrings = posResult.terms.map(t => makeTermString(t.bits, true));
      lastPOS = termStrings.join('');
      posEqEl.innerHTML = '<span class="z">Z</span> = ' + termStrings.map(t => `<span>${t}</span>`).join('');

      posResult.terms.forEach((t, i) => {
        const tr = document.createElement('div');
        tr.className = 'term-row pos';
        tr.innerHTML = `<span><b>${termStrings[i]}</b></span><span class="cells">ครอบคลุมช่อง: M(${t.minterms.join(', ')})</span>`;
        posTermListEl.appendChild(tr);
      });
    }

    renderCircuit(sopResult, posResult);
  }

  // ---------- Draw Logic Gates ----------
  function renderCircuit(sop, pos){
    const circuitContainer = document.getElementById('circuitContainer');
    const gateLegend = document.getElementById('gateLegend');
    circuitContainer.innerHTML = '';
    gateLegend.innerHTML = '';

    const isSOP = circuitMode === 'sop';
    const result = isSOP ? sop : pos;
    const targetTerms = result.terms || [];

    if(result.constant !== null){
      circuitContainer.innerHTML = `<div style="padding:20px; text-align:center; font-family:var(--mono);">Z = ${result.constant} (ต่อตรงไปที่สายลอจิกคงที่)</div>`;
      return;
    }

    const vars = varNames.slice(0, numVars);
    let html = `<svg width="100%" height="${120 + targetTerms.length * 60}" style="min-width:480px; background:#e7f4fc;" viewBox="0 0 600 ${120 + targetTerms.length * 60}">`;

    // วาดสายสัญญาณแนวตั้ง
    vars.forEach((v, i) => {
      const x = 40 + i * 40;
      html += `<line x1="${x}" y1="20" x2="${x}" y2="${100 + targetTerms.length * 60}" stroke="#cfe6ee" stroke-width="2"/>`;
      html += `<text x="${x-5}" y="15" font-family="var(--mono)" font-size="12" font-weight="bold" fill="#1d3345">${v}</text>`;
    });

    // แสดงเกตและอินพุตในตารางคำอธิบาย
    targetTerms.forEach((term, tIdx) => {
      const y = 60 + tIdx * 60;
      const termString = makeTermString(term.bits, !isSOP);
      
      // ลากเส้นเชื่อมจากตัวแปรไปหาลอจิกเกตแรก
      const inputsUsed = [];
      vars.forEach((v, vIdx) => {
        const bit = term.bits[vIdx];
        if(bit !== '-'){
          const x = 40 + vIdx * 40;
          html += `<circle cx="${x}" cy="${y}" r="4" fill="#3fa0d6"/>`;
          html += `<line x1="${x}" y1="${y}" x2="${240}" y2="${y}" stroke="#256f9c" stroke-width="1.5"/>`;
          inputsUsed.push(bit === '0' ? `${v}'` : v);
        }
      });

      // วาดกล่องตัวแทนเกตชั้นแรก (AND สำหรับ SOP / OR สำหรับ POS)
      html += `<rect x="240" y="${y-15}" width="60" height="30" rx="6" fill="${isSOP?'#34c495':'#3fa0d6'}" stroke="#fff" stroke-width="1.5"/>`;
      html += `<text x="255" y="${y+4}" font-family="var(--mono)" font-size="11" font-weight="bold" fill="#fff">${isSOP?'AND':'OR'}</text>`;
      // เส้นเอาต์พุตจากเกตย่อย
      html += `<line x1="300" y1="${y}" x2="${420}" y2="${y}" stroke="#1f9873" stroke-width="2"/>`;

      // ใส่ข้อมูล Legend
      const row = document.createElement('div');
      row.className = 'gate-legend-row';
      row.innerHTML = `<span><b>${isSOP?'AND':'OR'} Gate ${tIdx+1}</b> (อินพุต: ${inputsUsed.join(', ')})</span> <span class="out-tag">→ net_${tIdx+1}</span>`;
      gateLegend.appendChild(row);
    });

    // วาดเกตชั้นสุดท้าย (OR สำหรับ SOP / AND สำหรับ POS)
    const finalY = 60 + (targetTerms.length * 60) / 2;
    html += `<line x1="420" y1="50" x2="420" y2="${60 + targetTerms.length * 60}" stroke="#1f9873" stroke-width="2"/>`;
    html += `<line x1="420" y1="${finalY}" x2="460" y2="${finalY}" stroke="#1f9873" stroke-width="2"/>`;

    html += `<rect x="460" y="${finalY-20}" width="70" height="40" rx="8" fill="${isSOP?'#3fa0d6':'#34c495'}" stroke="#fff" stroke-width="1.5"/>`;
    html += `<text x="480" y="${finalY+5}" font-family="var(--mono)" font-size="13" font-weight="bold" fill="#fff">${isSOP?'OR':'AND'}</text>`;

    // เอาต์พุตสุดท้าย Z
    html += `<line x1="530" y1="${finalY}" x2="570" y2="${finalY}" stroke="#1d3345" stroke-width="3"/>`;
    html += `<text x="575" y="${finalY+5}" font-family="var(--mono)" font-size="15" font-weight="bold" fill="#1d3345">Z</text>`;

    html += '</svg>';
    circuitContainer.innerHTML = html;

    // คำอธิบายสุดท้าย
    const finalRow = document.createElement('div');
    finalRow.className = 'gate-legend-row';
    finalRow.innerHTML = `<span><b>${isSOP?'OR':'AND'} Gate (สัญญาณเอาต์พุตสุดท้าย)</b></span> <span class="out-tag" style="color:var(--blue-deep);">→ Z (Output)</span>`;
    gateLegend.appendChild(finalRow);
  }

  // ---------- Control Panels Switches ----------
  document.querySelectorAll('#dispSwitch .seg').forEach(seg=>{
    seg.addEventListener('click', ()=>{
      document.querySelectorAll('#dispSwitch .seg').forEach(s=>s.classList.remove('active'));
      seg.classList.add('active');
      dispMode = seg.getAttribute('data-disp');
      if(dispMode==='both'){
        document.getElementById('sopBlock').style.display='block';
        document.getElementById('posBlock').style.display='block';
      } else if(dispMode==='sop'){
        document.getElementById('sopBlock').style.display='block';
        document.getElementById('posBlock').style.display='none';
      } else {
        document.getElementById('sopBlock').style.display='none';
        document.getElementById('posBlock').style.display='block';
      }
    });
  });

  document.querySelectorAll('#circuitSwitch .seg').forEach(seg=>{
    seg.addEventListener('click', ()=>{
      document.querySelectorAll('#circuitSwitch .seg').forEach(s=>s.classList.remove('active'));
      seg.classList.add('active');
      circuitMode = seg.getAttribute('data-circuit');
      solveAndRender();
    });
  });

  // ---------- Event Listeners (ปุ่มการควบคุม) ----------
  document.getElementById('applyEqBtn').addEventListener('click', ()=>{
    const val = parseEquation(eqInput.value, numVars);
    if(val){
      values = val;
      eqStatus.textContent = '✓ แปลงสมการเรียบร้อย — กรอกลงตารางแล้ว (ตัวแปรที่ใช้: ' + varNames.slice(0,numVars).join(', ') + ')';
      eqStatus.className = 'eq-status ok';
      renderAll();
      solveAndRender();
    } else {
      eqStatus.textContent = '✗ สมการผิดพลาด! โปรดตรวจสอบตัวแปร (ใช้ได้เฉพาะ ' + varNames.slice(0,numVars).join(', ') + ') และสัญลักษณ์ทางคณิตศาสตร์';
      eqStatus.className = 'eq-status err';
    }
  });

  document.getElementById('loadSopBtn').addEventListener('click', ()=>{
    if(lastSOP) { eqInput.value = lastSOP; eqStatus.textContent = 'ดึงสมการ SOP ปัจจุบันมาแล้ว'; eqStatus.className = 'eq-status ok'; }
  });
  document.getElementById('loadPosBtn').addEventListener('click', ()=>{
    if(lastPOS) { eqInput.value = lastPOS; eqStatus.textContent = 'ดึงสมการ POS ปัจจุบันมาแล้ว'; eqStatus.className = 'eq-status ok'; }
  });

  document.getElementById('clearBtn').addEventListener('click', ()=>{
    initValues();
    eqInput.value = '';
    eqStatus.textContent = '';
    eqStatus.className = 'eq-status';
    renderAll();
    solveAndRender();
  });

  document.getElementById('fillRandom').addEventListener('click', ()=>{
    const total = 1<<numVars;
    for(let i=0;i<total;i++){
      values[i] = Math.floor(Math.random() * 3); // สุ่ม 0, 1, X
    }
    renderAll();
    solveAndRender();
  });

  // Start Application
  initValues();
  renderVarSelector();
  renderAll();
  solveAndRender();

})();
</script>
</body>
</html>
