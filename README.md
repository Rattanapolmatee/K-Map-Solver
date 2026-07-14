K-Map Solve Auto Calculate All Process เครื่องมือคำนวณค่า Digital logic แบบอัตโนมัติครบวงจร
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
        if(pickedIdx===-1) break;
      }
      const pc = piCovers[pickedIdx];
      if(chosen.indexOf(pc)===-1) chosen.push(pc);
      pc.covers.forEach(function(m){ remaining.delete(m); });
    }

    const terms = chosen.map(function(c){ return { bits:c.pi.bits, cells: Array.from(c.covers).sort(function(a,b){return a-b;}) }; });
    terms.sort(function(a,b){ return a.cells[0]-b.cells[0]; });
    return {constant:null, terms:terms};
  }

  function bitsToLiteralsAND(bits){ return bits.map(function(b,i){ return b==='-'?null:{var:varNames[i], neg:b==='0'}; }).filter(Boolean); }
  function bitsToLiteralsOR(bits){ return bits.map(function(b,i){ return b==='-'?null:{var:varNames[i], neg:b==='1'}; }).filter(Boolean); }
  function litStr(l){ return l.var + (l.neg? "&#772;" : ""); }

  function getSOP(){
    const target=[], dontcare=[];
    for(let i=0;i<(1<<numVars);i++){ if(values[i]===1) target.push(i); else if(values[i]===2) dontcare.push(i); }
    const cover = findCover(numVars, target, dontcare);
    if(cover.constant!==null) return {constant:cover.constant, terms:[]};
    const terms = cover.terms.map(function(t){ return { literals: bitsToLiteralsAND(t.bits), cells:t.cells }; });
    return {constant:null, terms:terms};
  }

  function getPOS(){
    const target=[], dontcare=[];
    for(let i=0;i<(1<<numVars);i++){ if(values[i]===0) target.push(i); else if(values[i]===2) dontcare.push(i); }
    const cover = findCover(numVars, target, dontcare);
    if(cover.constant!==null){
      const flipped = cover.constant==='0' ? '1' : '0';
      return {constant:flipped, terms:[]};
    }
    const terms = cover.terms.map(function(t){ return { literals: bitsToLiteralsOR(t.bits), cells:t.cells }; });
    return {constant:null, terms:terms};
  }

  function renderSOP(sop){
    const eqEl = document.getElementById('sopEq');
    const listEl = document.getElementById('sopTermList');
    if(sop.constant!==null){
      eqEl.innerHTML = '<span class="z">Z = </span>' + (sop.constant==='1'?'1 (Always True)':'0 (Always False)');
      listEl.innerHTML = '<div class="empty-msg">ไม่มีเทอม (ค่าคงที่)</div>';
      return;
    }
    const exprHtml = sop.terms.map(function(t){ return t.literals.map(litStr).join(''); }).join(' + ');
    eqEl.innerHTML = '<span class="z">Z = </span>' + exprHtml;
    listEl.innerHTML = sop.terms.map(function(t){
      return '<div class="term-row"><span>'+t.literals.map(litStr).join('')+'</span><span class="cells">m('+t.cells.join(',')+')</span></div>';
    }).join('');
  }

  function renderPOS(pos){
    const eqEl = document.getElementById('posEq');
    const listEl = document.getElementById('posTermList');
    if(pos.constant!==null){
      eqEl.innerHTML = '<span class="z">Z = </span>' + (pos.constant==='1'?'1 (Always True)':'0 (Always False)');
      listEl.innerHTML = '<div class="empty-msg">ไม่มีเทอม (ค่าคงที่)</div>';
      return;
    }
    const exprHtml = pos.terms.map(function(t){ return '('+t.literals.map(litStr).join(' + ')+')'; }).join('');
    eqEl.innerHTML = '<span class="z">Z = </span>' + exprHtml;
    listEl.innerHTML = pos.terms.map(function(t){
      return '<div class="term-row pos"><span>('+t.literals.map(litStr).join(' + ')+')</span><span class="cells">M('+t.cells.join(',')+')</span></div>';
    }).join('');
  }

  function applyDispMode(){
    const sopBlock = document.getElementById('sopBlock');
    const posBlock = document.getElementById('posBlock');
    if(dispMode==='both'){ sopBlock.style.display='block'; posBlock.style.display='block'; }
    else if(dispMode==='sop'){ sopBlock.style.display='block'; posBlock.style.display='none'; }
    else { sopBlock.style.display='none'; posBlock.style.display='block'; }
  }

  document.querySelectorAll('#dispSwitch .seg').forEach(function(seg){
    seg.addEventListener('click', function(){
      document.querySelectorAll('#dispSwitch .seg').forEach(function(s){ s.classList.remove('active'); });
      seg.classList.add('active');
      dispMode = seg.getAttribute('data-disp');
      applyDispMode();
    });
  });

  document.querySelectorAll('#circuitSwitch .seg').forEach(function(seg){
    seg.addEventListener('click', function(){
      document.querySelectorAll('#circuitSwitch .seg').forEach(function(s){ s.classList.remove('active'); });
      seg.classList.add('active');
      circuitMode = seg.getAttribute('data-circuit');
      drawCircuit();
    });
  });

  // ---------- Circuit diagram ----------
  function gateShape(type, x, y, w, h){
    if(type==='AND'){
      const flatW = w - h/2;
      return '<path d="M '+x+' '+y+' H '+(x+flatW)+' A '+(h/2)+' '+(h/2)+' 0 0 1 '+(x+flatW)+' '+(y+h)+' H '+x+' Z" fill="#ffffff" stroke="var(--gate-stroke)" stroke-width="2"/>';
    }
    if(type==='OR'){
      return '<path d="M '+x+' '+y+' C '+(x+w*0.35)+' '+y+', '+(x+w*0.7)+' '+(y+h*0.12)+', '+(x+w)+' '+(y+h*0.5)+
        ' C '+(x+w*0.7)+' '+(y+h*0.88)+', '+(x+w*0.35)+' '+(y+h)+', '+x+' '+(y+h)+
        ' Q '+(x+w*0.22)+' '+(y+h*0.5)+', '+x+' '+y+' Z" fill="#ffffff" stroke="var(--gate-stroke)" stroke-width="2"/>';
    }
    return '';
  }
  function notShape(x,y,size){
    const cx = x+size*1.15, cy=y, r=4.5;
    return '<path d="M '+x+' '+(y-size/2)+' L '+x+' '+(y+size/2)+' L '+(x+size*1.15)+' '+y+' Z" fill="#ffffff" stroke="var(--gate-stroke)" stroke-width="2"/>'+
      '<circle cx="'+(cx+r)+'" cy="'+cy+'" r="'+r+'" fill="#ffffff" stroke="var(--gate-stroke)" stroke-width="2"/>';
  }

  function buildCircuit(termsInput, constant, exprMode){
    const rowHeight = 46, marginTop = 30, leftX = 16, notX = 80, notSize = 22;
    const afterNotX = notX + notSize*1.15 + 9 + 14;
    const busX = 260, gateColX = 340, gateW = 68;
    const busX2 = gateColX + gateW + 46, combinerX = busX2 + 40, combinerW = 68;
    const outLineX = combinerX + combinerW + 60, labelX = outLineX + 14;
    let svgW = labelX + 40;

    if(constant!==null){
      const y = marginTop + rowHeight;
      const svg =
        '<rect x="40" y="'+(y-18)+'" width="120" height="36" rx="8" fill="#ffffff" stroke="var(--gate-stroke)" stroke-width="2"/>'+
        '<text x="100" y="'+(y+5)+'" text-anchor="middle" font-family="var(--mono)" font-size="13" fill="var(--ink)">logic '+constant+'</text>'+
        '<line x1="160" y1="'+y+'" x2="'+outLineX+'" y2="'+y+'" stroke="var(--wire)" stroke-width="2"/>'+
        '<text x="'+labelX+'" y="'+(y+5)+'" font-family="var(--mono)" font-size="14" font-weight="700" fill="var(--green-deep)">Z</text>';
      return { svg:svg, width:svgW, height:y+40, legend:[{label:'ค่าคงที่', type:constant==='1'?'VCC (Logic High)':'GND (Logic Low)', inputs:'—', output:'Z'}] };
    }

    const litKey = function(l){ return l.var + (l.neg?'-1':'-0'); };
    const litMap = new Map();
    termsInput.forEach(function(t){
      t.literals.forEach(function(l){
        const k = litKey(l);
        if(!litMap.has(k)) litMap.set(k, {var:l.var, neg:l.neg, users:0});
      });
    });
    const litList = Array.from(litMap.values()).sort(function(a,b){ return a.var===b.var ? (a.neg-b.neg) : a.var.localeCompare(b.var); });
    litList.forEach(function(l,i){ l.y = marginTop + i*rowHeight; });
    const litLookup = new Map();
    litList.forEach(function(l){ litLookup.set(litKey(l), l); });
    termsInput.forEach(function(t){ t.literals.forEach(function(l){ litLookup.get(litKey(l)).users++; }); });

    const gateTerms = [], passthroughTerms = [];
    termsInput.forEach(function(t,ti){
      const copy = {literals:t.literals, cells:t.cells, idx:ti};
      if(t.literals.length===1) passthroughTerms.push(copy);
      else gateTerms.push(copy);
    });

    const gateInfos = [];
    gateTerms.forEach(function(t,gi){
      const ys = t.literals.map(function(l){ return litLookup.get(litKey(l)).y; });
      const avgY = ys.reduce(function(a,b){return a+b;},0)/ys.length;
      const h = Math.max(t.literals.length*22, 40);
      const gy = avgY - h/2;
      gateInfos.push({ term:t, x:gateColX, y:gy, w:gateW, h:h, outY: gy+h/2, gateNum: gi+1 });
    });

    passthroughTerms.forEach(function(t){
      const l = litLookup.get(litKey(t.literals[0]));
      t.outY = l.y; t.outXStart = busX;
    });

    let allOutputs = gateInfos.map(function(g){ return { outXStart: g.x+g.w, outY:g.outY, isGate:true, term:g.term, gateNum:g.gateNum }; })
      .concat(passthroughTerms.map(function(t){ return { outXStart:t.outXStart, outY:t.outY, isGate:false, term:t }; }));
    allOutputs.sort(function(a,b){ return termsInput.indexOf(a.term) - termsInput.indexOf(b.term); });

    const needCombiner = allOutputs.length > 1;
    let combinerInfo = null;
    if(needCombiner){
      const ch = Math.max(allOutputs.length*24, 44);
      const avgOutY = allOutputs.reduce(function(a,o){return a+o.outY;},0)/allOutputs.length;
      combinerInfo = { x:combinerX, y:avgOutY-ch/2, w:combinerW, h:ch, outY: avgOutY };
    }

    const totalRows = Math.max(litList.length, gateInfos.length, allOutputs.length);
    const svgH = marginTop*2 + totalRows*rowHeight + 30;
    let svg = '';

    litList.forEach(function(l){
      svg += '<text x="'+leftX+'" y="'+(l.y+4)+'" font-family="var(--mono)" font-size="13" font-weight="700" fill="var(--ink)">'+l.var+'</text>';
      svg += '<line x1="'+(leftX+16)+'" y1="'+l.y+'" x2="'+(l.neg?notX:busX)+'" y2="'+l.y+'" stroke="var(--wire)" stroke-width="2"/>';
      if(l.neg){
        svg += notShape(notX, l.y, notSize);
        svg += '<line x1="'+afterNotX+'" y1="'+l.y+'" x2="'+busX+'" y2="'+l.y+'" stroke="var(--wire)" stroke-width="2"/>';
        svg += '<text x="'+(notX+2)+'" y="'+(l.y-notSize/2-6)+'" font-family="var(--mono)" font-size="9.5" fill="var(--ink-dim)">NOT</text>';
      }
      if(l.users>1){ svg += '<circle cx="'+busX+'" cy="'+l.y+'" r="3.5" fill="var(--wire)"/>'; }
      svg += '<text x="'+(busX+6)+'" y="'+(l.y-8)+'" font-family="var(--mono)" font-size="10" fill="var(--ink-dim)">'+l.var+(l.neg?'&#772;':'')+'</text>';
    });

    const legend = [];
    const gateTypeName = exprMode==='sop' ? 'AND' : 'OR';
    gateInfos.forEach(function(g){
      svg += gateShape(gateTypeName, g.x, g.y, g.w, g.h);
      svg += '<text x="'+(g.x+g.w/2)+'" y="'+(g.y+g.h+14)+'" text-anchor="middle" font-family="var(--mono)" font-size="10" fill="var(--blue-deep)" font-weight="700">'+gateTypeName+g.gateNum+'</text>';
      const n = g.term.literals.length;
      g.term.literals.forEach(function(l,k){
        const litY = litLookup.get(litKey(l)).y;
        const pinY = g.y + (k+1)*(g.h/(n+1));
        svg += '<polyline points="'+busX+','+litY+' '+busX+','+pinY+' '+g.x+','+pinY+'" fill="none" stroke="var(--wire)" stroke-width="2"/>';
      });
      legend.push({
        label: gateTypeName+g.gateNum,
        type: gateTypeName+' Gate ('+n+'-input)',
        inputs: g.term.literals.map(function(l){ return l.var+(l.neg?"'":""); }).join(', '),
        output: 'net_'+gateTypeName.toLowerCase()+g.gateNum
      });
    });

    if(needCombiner){
      const combTypeName = exprMode==='sop' ? 'OR' : 'AND';
      svg += gateShape(combTypeName, combinerInfo.x, combinerInfo.y, combinerInfo.w, combinerInfo.h);
      svg += '<text x="'+(combinerInfo.x+combinerInfo.w/2)+'" y="'+(combinerInfo.y+combinerInfo.h+14)+'" text-anchor="middle" font-family="var(--mono)" font-size="10" fill="var(--green-deep)" font-weight="700">'+combTypeName+'_OUT</text>';
      allOutputs.forEach(function(o,k){
        const pinY = combinerInfo.y + (k+1)*(combinerInfo.h/(allOutputs.length+1));
        svg += '<polyline points="'+o.outXStart+','+o.outY+' '+busX2+','+o.outY+' '+busX2+','+pinY+' '+combinerInfo.x+','+pinY+'" fill="none" stroke="var(--wire)" stroke-width="2"/>';
      });
      const finalY = combinerInfo.y + combinerInfo.h/2;
      svg += '<line x1="'+(combinerInfo.x+combinerInfo.w)+'" y1="'+finalY+'" x2="'+outLineX+'" y2="'+finalY+'" stroke="var(--wire)" stroke-width="2.5"/>';
      svg += '<text x="'+labelX+'" y="'+(finalY+5)+'" font-family="var(--mono)" font-size="14" font-weight="700" fill="var(--green-deep)">Z</text>';
      legend.push({
        label: combTypeName+'_OUT',
        type: combTypeName+' Gate ('+allOutputs.length+'-input, ตัวรวมสัญญาณสุดท้าย)',
        inputs: gateInfos.map(function(g){return gateTypeName+g.gateNum;}).concat(passthroughTerms.map(function(t){ return t.literals[0].var+(t.literals[0].neg?"'":""); })).join(', '),
        output: 'Z (Output)'
      });
    } else if(allOutputs.length===1){
      const o = allOutputs[0];
      svg += '<line x1="'+o.outXStart+'" y1="'+o.outY+'" x2="'+outLineX+'" y2="'+o.outY+'" stroke="var(--wire)" stroke-width="2.5"/>';
      svg += '<text x="'+labelX+'" y="'+(o.outY+5)+'" font-family="var(--mono)" font-size="14" font-weight="700" fill="var(--green-deep)">Z</text>';
    }

    return { svg:svg, width:svgW, height:Math.max(svgH,120), legend:legend };
  }

  function drawCircuit(){
    if(!lastSOP || !lastPOS) return;
    const container = document.getElementById('circuitContainer');
    const legendEl = document.getElementById('gateLegend');
    let terms, constant, exprMode;
    if(circuitMode==='sop'){ exprMode='sop'; constant=lastSOP.constant; terms=lastSOP.terms; }
    else { exprMode='pos'; constant=lastPOS.constant; terms=lastPOS.terms; }

    const built = buildCircuit(terms, constant, exprMode);
    const strokeColor = exprMode==='sop' ? '#3fa0d6' : '#34c495';
    const svgStyle = '--gate-stroke:'+strokeColor+'; --wire:#7ea9b8; --ink:#1d3345; --ink-dim:#6f8b98; --blue-deep:#256f9c; --green-deep:#1f9873;';
    container.innerHTML = '<svg style="'+svgStyle+'" width="'+built.width+'" height="'+built.height+'" viewBox="0 0 '+built.width+' '+built.height+'" xmlns="http://www.w3.org/2000/svg">'+built.svg+'</svg>';

    if(built.legend.length){
      legendEl.innerHTML = built.legend.map(function(g){
        return '<div class="gate-legend-row"><span><b>'+g.label+'</b> — '+g.type+'</span><span>in: '+g.inputs+' → <span class="out-tag">'+g.output+'</span></span></div>';
      }).join('');
    } else { legendEl.innerHTML = ''; }
  }

  function solveAndRender(){
    lastSOP = getSOP();
    lastPOS = getPOS();
    renderSOP(lastSOP);
    renderPOS(lastPOS);
    applyDispMode();
    drawCircuit();
  }

  document.getElementById('clearBtn').addEventListener('click', function(){
    initValues(); renderAll(); solveAndRender();
    eqStatus.textContent=''; eqStatus.className='eq-status';
  });

  document.getElementById('fillRandom').addEventListener('click', function(){
    const total = 1<<numVars;
    for(let i=0;i<total;i++){ const r=Math.random(); values[i] = r<0.5?0:(r<0.9?1:2); }
    renderAll(); solveAndRender();
    eqStatus.textContent=''; eqStatus.className='eq-status';
  });

  // ---------- Boolean expression parser ----------
  function tokenize(str){
    const tokens = [];
    let i=0;
    while(i<str.length){
      const c = str[i];
      if(/\s/.test(c)){ i++; continue; }
      if(c==='('){ tokens.push({t:'('}); i++; continue; }
      if(c===')'){ tokens.push({t:')'}); i++; continue; }
      if(c==='+'){ tokens.push({t:'+'}); i++; continue; }
      if(c==='.'||c==='*'){ tokens.push({t:'.'}); i++; continue; }
      if(c==='!'||c==='~'){ tokens.push({t:'!'}); i++; continue; }
      if(c==="'"){ tokens.push({t:"'"}); i++; continue; }
      if(/[A-Za-z]/.test(c)){ tokens.push({t:'VAR', name:c.toUpperCase()}); i++; continue; }
      throw new Error("อักขระที่ไม่รู้จัก: '"+c+"'");
    }
    return tokens;
  }

  function makeParser(tokens){
    let pos = 0;
    function peek(){ return tokens[pos]; }
    function advance(){ return tokens[pos++]; }
    function parseExpr(){
      let node = parseTerm();
      while(peek() && peek().t==='+'){ advance(); const right=parseTerm(); node={op:'OR', left:node, right:right}; }
      return node;
    }
    function parseTerm(){
      let node = parseFactor();
      while(peek() && (peek().t==='.' || peek().t==='VAR' || peek().t==='(' || peek().t==='!')){
        if(peek().t==='.') advance();
        const right = parseFactor();
        node = {op:'AND', left:node, right:right};
      }
      return node;
    }
    function parseFactor(){
      if(peek() && peek().t==='!'){ advance(); const operand=parseFactor(); return {op:'NOT', operand:operand}; }
      let node = parsePrimary();
      while(peek() && peek().t==="'"){ advance(); node = {op:'NOT', operand:node}; }
      return node;
    }
    function parsePrimary(){
      const tok = peek();
      if(!tok) throw new Error('นิพจน์ไม่สมบูรณ์ (พิมพ์ไม่จบ)');
      if(tok.t==='VAR'){ advance(); return {op:'VAR', name:tok.name}; }
      if(tok.t==='('){
        advance();
        const node = parseExpr();
        if(!peek() || peek().t!==')') throw new Error('วงเล็บไม่ครบ (ขาด ")" )');
        advance();
        return node;
      }
      throw new Error('รูปแบบนิพจน์ไม่ถูกต้องใกล้ตำแหน่งที่ '+(pos+1));
    }
    const tree = parseExpr();
    if(pos !== tokens.length) throw new Error('มีอักขระเกินหลังนิพจน์ที่ถูกต้อง');
    return tree;
  }

  function evalNode(node, assign){
    switch(node.op){
      case 'VAR': {
        if(!(node.name in assign)) throw new Error("ตัวแปร '"+node.name+"' ไม่อยู่ในชุดตัวแปรปัจจุบัน");
        return assign[node.name];
      }
      case 'NOT': return evalNode(node.operand, assign) ? 0 : 1;
      case 'AND': return (evalNode(node.left, assign) && evalNode(node.right, assign)) ? 1 : 0;
      case 'OR': return (evalNode(node.left, assign) || evalNode(node.right, assign)) ? 1 : 0;
    }
  }

  function collectVars(node, set){
    if(node.op==='VAR'){ set.add(node.name); return; }
    if(node.op==='NOT'){ collectVars(node.operand, set); return; }
    collectVars(node.left, set); collectVars(node.right, set);
  }

  function applyEquation(){
    const raw = eqInput.value.trim();
    if(!raw){ eqStatus.className='eq-status err'; eqStatus.textContent='กรุณาพิมพ์สมการก่อน'; return; }
    try{
      const tokens = tokenize(raw);
      const tree = makeParser(tokens);
      const usedVars = new Set();
      collectVars(tree, usedVars);
      const activeVars = varNames.slice(0, numVars);
      const invalid = Array.from(usedVars).filter(function(v){ return activeVars.indexOf(v)===-1; });
      if(invalid.length){
        eqStatus.className='eq-status err';
        eqStatus.textContent = 'ตัวแปร '+invalid.join(', ')+' ไม่อยู่ในชุดตัวแปรปัจจุบัน (ใช้ได้: '+activeVars.join(', ')+') — ลองเพิ่มจำนวนตัวแปร หรือแก้สมการ';
        return;
      }
      const total = 1<<numVars;
      for(let i=0;i<total;i++){
        const assign = {};
        for(let b=0;b<numVars;b++){ assign[varNames[b]] = (i>>(numVars-1-b))&1; }
        values[i] = evalNode(tree, assign);
      }
      renderAll();
      solveAndRender();
      eqStatus.className='eq-status ok';
      eqStatus.textContent = '✓ แปลงสมการเรียบร้อย — กรอกลงตารางแล้ว ('+ (usedVars.size ? 'ตัวแปรที่ใช้: '+Array.from(usedVars).sort().join(', ') : 'ไม่มีตัวแปร') +')';
    } catch(e){
      eqStatus.className='eq-status err';
      eqStatus.textContent = '✗ '+e.message;
    }
  }

  document.getElementById('applyEqBtn').addEventListener('click', applyEquation);
  eqInput.addEventListener('keydown', function(e){ if(e.key==='Enter'){ applyEquation(); } });

  function stripTags(html){
    return html.replace(/&#772;/g, "'").replace(/<[^>]+>/g,'');
  }

  document.getElementById('loadSopBtn').addEventListener('click', function(){
    if(!lastSOP){ solveAndRender(); }
    if(lastSOP.constant!==null){ eqInput.value = lastSOP.constant; }
    else { eqInput.value = lastSOP.terms.map(function(t){ return t.literals.map(function(l){ return l.var+(l.neg?"'":""); }).join(''); }).join('+'); }
    eqStatus.className='eq-status ok';
    eqStatus.textContent='ดึงสมการ SOP มาไว้ในช่องแล้ว (แก้ไขแล้วกด "แปลง → ใส่ในตาราง" ได้)';
  });
  document.getElementById('loadPosBtn').addEventListener('click', function(){
    if(!lastPOS){ solveAndRender(); }
    if(lastPOS.constant!==null){ eqInput.value = lastPOS.constant; }
    else { eqInput.value = lastPOS.terms.map(function(t){ return '('+t.literals.map(function(l){ return l.var+(l.neg?"'":""); }).join('+')+')'; }).join(''); }
    eqStatus.className='eq-status ok';
    eqStatus.textContent='ดึงสมการ POS มาไว้ในช่องแล้ว (แก้ไขแล้วกด "แปลง → ใส่ในตาราง" ได้)';
  });

  // init
  initValues();
  renderVarSelector();
  renderAll();
  solveAndRender();
})();
</script>
</body>
</html>
