<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Student Attendance Monitoring Portal</title>

  <!-- Chart.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <style>
    :root{
      --brand:#0b6e4f;
      --muted:#6b7280;
      --bg:#f4f6f8;
      --card:#fff;
    }
    *{box-sizing:border-box}
    body{
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      margin:0;
      background:var(--bg);
      color:#111;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      line-height:1.45;
    }
    a{color:var(--brand); text-decoration:none}
    header{
      background:linear-gradient(90deg,var(--brand),#075a40);
      color:#fff;padding:22px 20px;
      box-shadow:0 6px 18px rgba(10,20,20,0.08);
    }
    .wrap{max-width:1100px;margin:18px auto;padding:0 16px}
    .hero{
      display:flex;align-items:center;gap:24px;flex-wrap:wrap;
    }
    .hero .intro{flex:1;min-width:260px}
    h1{margin:0 0 6px 0;font-size:20px}
    .lead{color:#e6f6ee; margin-bottom:8px}
    .small{font-size:13px;color:var(--muted)}
    .btn{background:var(--brand);color:#fff;padding:8px 12px;border-radius:8px;border:none;cursor:pointer}
    .card{background:var(--card);border-radius:10px;padding:16px;margin-bottom:16px;box-shadow:0 4px 18px rgba(20,20,40,0.05)}
    .row{display:flex;gap:12px;flex-wrap:wrap}
    .col{flex:1;min-width:220px}
    .controls{display:flex;gap:12px;align-items:end;flex-wrap:wrap}
    label{font-size:13px;color:var(--muted);display:block;margin-bottom:6px}
    input[type=file]{display:block}
    textarea{width:100%;resize:vertical}
    input[type=text],input[type=date],select,textarea{padding:8px;border-radius:6px;border:1px solid #e1e6ea}
    table{width:100%;border-collapse:collapse;margin-top:12px}
    th,td{padding:8px;border-bottom:1px solid #eef2f6;text-align:left}
    th{background:#fbfcfd}
    .stats{display:flex;gap:12px;flex-wrap:wrap}
    .stat{background:#fafdf9;border:1px solid #e6f2ea;padding:12px;border-radius:8px;min-width:160px}
    .legend{display:flex;gap:12px;align-items:center}
    .dot{width:12px;height:12px;border-radius:50%;display:inline-block}
    .present{background:#0b6e4f}
    .absent{background:#ef4444}
    .neutral{background:#9ca3af}
    .right{margin-left:auto}
    .export{display:flex;gap:8px;flex-wrap:wrap}
    @media (max-width:820px){
      .hero{flex-direction:column;align-items:flex-start}
      .controls{align-items:stretch}
    }
    footer{padding:16px;text-align:center;color:var(--muted);font-size:13px}
    pre.sample{background:#0b0b0b;color:#fff;padding:12px;border-radius:8px;overflow:auto}
    .muted-note{font-size:13px;color:#666;margin-top:6px}
    /* small logo tweak */
    .header-logo{display:block; margin:0 auto 12px auto; width:200px; max-width:45%; border-radius:8px;}
  </style>
</head>
<body>
  <header>
    <div class="wrap hero">
      <div class="intro">
        <h1>Student Attendance Monitoring Portal</h1>
        <div class="lead">Maintain daily attendance without manual registers — upload lists, mark attendance, view monthly & overall percentages, export reports. Fully in-browser.</div>
        <div style="margin-top:10px">
          <a class="btn" href="#app">Open App</a>
          <a style="margin-left:8px;color:#fff;background:rgba(255,255,255,0.12);padding:8px 12px;border-radius:8px" href="#about">About</a>
        </div>
      </div>

      <!-- Logo column: centered logo above Quick Features -->
      <div style="min-width:240px; text-align:center;">
        <!--
          Use one of the following src options:
          1) Local absolute Windows path (works when opening file locally on your machine):
             src="C:\Users\Dell\Downloads\image.png"
          2) Recommended: move image.png to same folder as this HTML and use:
             src="image.png"
        -->
        <img
  src="C:\Users\Dell\Downloads\image.png"
  alt="Logo"
  class="header-logo"
  style="width:470px; height:180px;"
>

    </div>
  </header>

  <main class="wrap" id="app" style="margin-top:18px">
    <!-- Upload / Setup -->
    <section class="card" id="uploadCard">
      <h2>1) Upload Student List</h2>
      <div class="row" style="align-items:flex-start">
        <div class="col">
          <label>CSV file (columns: id,roll,name)</label>
          <input id="fileInput" type="file" accept=".csv" />
          <div class="muted-note">Example CSV rows: <code>1,101,Anita Sharma</code></div>
        </div>

        <div class="col" style="min-width:260px">
          <label>Or quick-add (one per line: roll,name)</label>
          <textarea id="quickAdd" placeholder="101,Anita Sharma\n102,Rahul Verma" style="height:86px;padding:8px;border-radius:6px;border:1px solid #e1e6ea"></textarea>
        </div>

        <div style="display:flex;flex-direction:column;gap:8px;align-items:flex-start;min-width:180px">
          <button class="btn" id="importBtn">Import</button>
          <button class="btn" id="sampleBtn" style="background:#0b5b40">Load Sample</button>
          <button class="btn" id="clearBtn" style="background:#fff;color:var(--brand);border:1px solid #dbeae2">Clear All Data</button>
        </div>
      </div>
      <div class="small" style="margin-top:8px">Imported students persist in your browser via localStorage. No server required.</div>
    </section>

    <!-- Attendance marking -->
    <section class="card">
      <h2>2) Mark Daily Attendance</h2>
      <div class="controls">
        <div>
          <label>Select Date</label>
          <input type="date" id="attDate" />
        </div>
        <div>
          <label>View Month</label>
          <select id="monthSel"></select>
        </div>
        <div class="right">
          <button class="btn" id="saveBtn">Save Attendance</button>
        </div>
      </div>

      <div id="studentsSection" style="margin-top:12px"></div>
    </section>

    <!-- Stats & Graph -->
    <section class="card">
      <h2>3) Reports & Export</h2>
      <div class="row" style="align-items:flex-start">
        <div class="col stats" style="flex-basis:280px;flex-direction:column;gap:12px;">
          <div class="stat">
            <div class="small">Selected Month Present %</div>
            <div id="monthPercentage" style="font-size:20px;font-weight:700">—</div>
          </div>
          <div class="stat">
            <div class="small">Overall Attendance % (all days)</div>
            <div id="overallPercentage" style="font-size:20px;font-weight:700">—</div>
          </div>
          <div class="stat">
            <div class="small">Total Students</div>
            <div id="totalStudents" style="font-size:20px;font-weight:700">0</div>
          </div>
        </div>

        <div class="col" style="min-width:300px">
          <canvas id="attendanceChart" height="160"></canvas>
        </div>
      </div>

      <div style="display:flex;justify-content:space-between;align-items:center;margin-top:12px;flex-wrap:wrap">
        <div class="legend" style="gap:10px">
          <div><span class="dot present"></span> Present</div>
          <div><span class="dot absent"></span> Absent</div>
          <div><span class="dot neutral"></span> No record</div>
        </div>

        <div class="export">
          <button class="btn" id="exportCSV">Export CSV (All)</button>
          <button class="btn" id="downloadReport">Download Month Report (CSV)</button>
        </div>
      </div>
    </section>

    <!-- About / details -->
    <section class="card" id="about">
      <h2>About</h2>
      <p style="margin-top:0">Purpose: <strong>To maintain daily attendance without manual registers.</strong></p>
      <div style="margin-top:8px">
        <strong>Details:</strong>
        <ul>
          <li>Upload student list</li>
          <li>Mark present/absent daily</li>
          <li>Monthly & overall percentage calculation</li>
          <li>Graphical attendance report</li>
          <li>Export attendance sheet</li>
          <li>Works on browser, fully responsive</li>
        </ul>
      </div>
      <p class="small">This portal stores data locally in your browser. For multi-user or centralized storage, connect the app to a server or cloud database (e.g., Firebase, Supabase).</p>
    </section>

    <section class="card">
      <h3>Sample CSV (save as <code>students.csv</code>)</h3>
      <pre class="sample">id,roll,name
1,101,Anita Sharma
2,102,Rahul Verma
3,103,Priya Singh
4,104,Arjun Mehta
5,105,Keerthi Reddy
6,106,Sai Teja
7,107,Nisha Patel
8,108,Rohan Gupta
9,109,Pooja Agarwal
10,110,Manish Kumar
11,111,Sanjana Iyer
12,112,Amit Verma
13,113,Harika Chowdary
14,114,Deepak Rao
15,115,Sravani Reddy
16,116,Viraj Shetty
17,117,Divya Agarwal
18,118,Karthik Nair
19,119,Swetha Rani
20,120,Ashwin Rao</pre>
    </section>
  </main>

  <!-- App script -->
  <script>
    // Storage keys
    const STUD_KEY = 'att_students_v1';
    const DATA_KEY = 'att_data_v1'; // { 'YYYY-MM-DD': {roll: 'P'|'A'} }

    // DOM refs
    const qs = s => document.querySelector(s);
    const qsa = s => Array.from(document.querySelectorAll(s));

    const fileInput = qs('#fileInput');
    const importBtn = qs('#importBtn');
    const quickAdd = qs('#quickAdd');
    const clearBtn = qs('#clearBtn');
    const sampleBtn = qs('#sampleBtn');
    const studentsSection = qs('#studentsSection');
    const attDate = qs('#attDate');
    const monthSel = qs('#monthSel');
    const totalStudents = qs('#totalStudents');
    const monthPercentage = qs('#monthPercentage');
    const overallPercentage = qs('#overallPercentage');
    const saveBtn = qs('#saveBtn');
    const exportCSV = qs('#exportCSV');
    const downloadReport = qs('#downloadReport');

    let chart;

    // Storage helpers
    function readStorage(){
      const studs = JSON.parse(localStorage.getItem(STUD_KEY) || '[]');
      const data = JSON.parse(localStorage.getItem(DATA_KEY) || '{}');
      return {studs,data};
    }
    function writeStorage(studs,data){
      localStorage.setItem(STUD_KEY, JSON.stringify(studs));
      localStorage.setItem(DATA_KEY, JSON.stringify(data));
    }

    // CSV parsing (simple) — skips header if present
    function parseCSV(text){
      const lines = text.split(/\r?\n/).map(l=>l.trim()).filter(Boolean);
      if(!lines.length) return [];
      // detect header like "id,roll,name" or presence of the strings id/roll/name
      const firstLow = lines[0].toLowerCase();
      if(firstLow.includes('id') && firstLow.includes('roll')) lines.shift();

      const rows = lines.map(line=>{
        const parts = line.split(',').map(p=>p.trim());
        if(parts.length>=3) return {id:parts[0], roll:parts[1], name:parts.slice(2).join(',')};
        if(parts.length==2) return {id:parts[0], roll:parts[0], name:parts[1]};
        return null;
      }).filter(Boolean);
      return rows;
    }

    function importFromFile(file){
      const reader = new FileReader();
      reader.onload = ()=>{
        const text = reader.result;
        const rows = parseCSV(text);
        saveStudents(rows, true);
      };
      reader.onerror = ()=> alert('Failed to read file');
      reader.readAsText(file);
    }

    function saveStudents(rows, showAlert){
      if(!rows || !rows.length){ if(showAlert) alert('No rows found to import'); return; }
      let {studs,data} = readStorage();
      const map = new Map(studs.map(s=>[String(s.roll),s]));
      rows.forEach(r=>{
        const roll = String((r.roll || '').trim());
        if(!roll) return;
        map.set(roll, {id: r.id || roll, roll, name: r.name || ('Student '+roll)});
      });
      studs = Array.from(map.values()).sort((a,b)=>a.roll.localeCompare(b.roll, undefined, {numeric:true}));
      writeStorage(studs,data);
      renderStudents();
      populateMonthSelect();
      if(showAlert) alert('Imported ' + rows.length + ' students');
    }

    // Attach events
    importBtn.addEventListener('click', ()=>{
      const quick = quickAdd.value.trim();
      if(quick){
        const lines = quick.split(/\r?\n/).map(l=>l.trim()).filter(Boolean);
        const rows = lines.map(line=>{
          const parts = line.split(',').map(p=>p.trim());
          if(parts.length>=2) return {id:parts[0], roll:parts[0], name:parts.slice(1).join(',')};
          return null;
        }).filter(Boolean);
        saveStudents(rows, true);
        quickAdd.value='';
      }
      const f = fileInput.files[0];
      if(f) importFromFile(f);
    });

    sampleBtn.addEventListener('click', ()=>{
      const sampleRows = [
        {id:'1',roll:'101',name:'Anita Sharma'},
        {id:'2',roll:'102',name:'Rahul Verma'},
        {id:'3',roll:'103',name:'Priya Singh'},
        {id:'4',roll:'104',name:'Arjun Mehta'},
        {id:'5',roll:'105',name:'Keerthi Reddy'},
        {id:'6',roll:'106',name:'Sai Teja'},
        {id:'7',roll:'107',name:'Nisha Patel'},
        {id:'8',roll:'108',name:'Rohan Gupta'},
        {id:'9',roll:'109',name:'Pooja Agarwal'},
        {id:'10',roll:'110',name:'Manish Kumar'}
      ];
      saveStudents(sampleRows, true);
    });

    clearBtn.addEventListener('click', ()=>{
      if(!confirm('Clear all students and attendance?')) return;
      localStorage.removeItem(STUD_KEY);
      localStorage.removeItem(DATA_KEY);
      renderStudents();
      populateMonthSelect();
      updateStats();
    });

    // Render students attendance UI
    function renderStudents(){
      const {studs,data} = readStorage();
      totalStudents.textContent = studs.length;
      if(!studs.length){
        studentsSection.innerHTML = '<div class="small">No students found. Upload CSV or quick-add to begin (or click Load Sample).</div>';
        return;
      }
      const date = attDate.value || new Date().toISOString().slice(0,10);
      const dayData = data[date] || {};
      let html = '<table><thead><tr><th>Roll</th><th>Name</th><th>Mark</th></tr></thead><tbody>';
      studs.forEach(s=>{
        const val = (dayData && dayData[s.roll]) || '';
        html += `<tr data-roll="${escapeHtmlAttr(s.roll)}"><td>${escapeHtml(s.roll)}</td><td>${escapeHtml(s.name)}</td><td>
          <select class="markSel" aria-label="Mark attendance for ${escapeHtmlAttr(s.name)}"><option value="">--</option><option value="P" ${val==='P'?'selected':''}>Present</option><option value="A" ${val==='A'?'selected':''}>Absent</option></select>
        </td></tr>`;
      });
      html += '</tbody></table>';
      studentsSection.innerHTML = html;
      qsa('.markSel').forEach(sel=>sel.addEventListener('change', ()=>{/* optional auto-save */}));
    }

    function escapeHtml(t){ return (t+'').replace(/[&<>"']/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":"&#39;"})[c]); }
    function escapeHtmlAttr(t){ return String(t).replace(/"/g,'&quot;'); }

    // Date handling
    function setDefaultDate(){
      attDate.value = new Date().toISOString().slice(0,10);
    }
    attDate.addEventListener('change', ()=>{ renderStudents(); updateStats(); });

    // Save attendance for selected date
    saveBtn.addEventListener('click', ()=>{
      const date = attDate.value || new Date().toISOString().slice(0,10);
      const {studs,data} = readStorage();
      if(!studs.length) return alert('No students to save');
      const rows = qsa('tr[data-roll]');
      data[date] = data[date] || {};
      rows.forEach(r=>{
        const roll = r.getAttribute('data-roll');
        const sel = r.querySelector('.markSel');
        const v = sel.value;
        if(v) data[date][roll] = v;
        else delete data[date][roll];
      });
      writeStorage(studs,data);
      alert('Attendance saved for ' + date);
      populateMonthSelect();
      updateStats();
    });

    // Month selector (populates options — listener attached once in init)
    function populateMonthSelect(){
      const {data} = readStorage();
      const months = new Set();
      Object.keys(data).forEach(d=>{
        const m = d.slice(0,7); // YYYY-MM
        months.add(m);
      });
      const arr = Array.from(months).sort().reverse();
      monthSel.innerHTML = '';
      if(!arr.length) {
        const opt = document.createElement('option'); opt.value=''; opt.textContent='-- No months --';
        monthSel.appendChild(opt);
        monthSel.value = '';
        return;
      }
      arr.forEach(m=>{
        const opt = document.createElement('option'); opt.value=m; opt.textContent=m; monthSel.appendChild(opt);
      });
      // keep previously selected month if still present, otherwise choose newest
      if(!arr.includes(monthSel.value)) monthSel.value = arr[0];
    }

    // Stats calculations & chart
    function updateStats(){
      const {studs,data} = readStorage();
      const total = studs.length || 0;
      totalStudents.textContent = total;
      // overall percentage across all dates
      let presentCount = 0, totalMarked = 0;
      Object.values(data).forEach(dayObj=>{
        Object.values(dayObj).forEach(v=>{
          if(v==='P') presentCount++;
          if(v==='P' || v==='A') totalMarked++;
        });
      });
      const overall = totalMarked? Math.round((presentCount/totalMarked)*10000)/100 : 0;
      overallPercentage.textContent = totalMarked? overall + '%' : '—';

      // month percentage
      const selMonth = monthSel.value; // YYYY-MM
      if(!selMonth){ monthPercentage.textContent='—'; renderChart([],[]); return; }
      // compute per-day present% for that month
      const days = Object.keys(data).filter(d=>d.startsWith(selMonth+'-')).sort();
      const dayLabels = [], dayPct = [];
      days.forEach(d=>{
        const dayObj = data[d] || {};
        let p=0,m=0;
        studs.forEach(s=>{
          const v = dayObj[s.roll];
          if(v==='P') p++; if(v==='P' || v==='A') m++;
        });
        const pct = m? Math.round((p/m)*10000)/100 : 0;
        dayLabels.push(d.slice(8)); // day number
        dayPct.push(pct);
      });
      const monthOverall = dayPct.length? Math.round((dayPct.reduce((a,b)=>a+b,0)/dayPct.length)*100)/100 : '—';
      monthPercentage.textContent = dayPct.length? (monthOverall + '%') : '—';
      renderChart(dayLabels, dayPct);
    }

    function renderChart(labels, dataArr){
      const ctx = qs('#attendanceChart').getContext('2d');
      if(chart) chart.destroy();
      chart = new Chart(ctx,{
        type:'bar',
        data:{labels:labels, datasets:[{label:'Present % (day)', data:dataArr, borderRadius:6}]},
        options:{plugins:{legend:{display:false}},scales:{y:{beginAtZero:true,max:100}},responsive:true,maintainAspectRatio:false}
      });
    }

    // Export CSV of full data
    exportCSV.addEventListener('click', ()=>{
      const {studs,data} = readStorage();
      if(!studs.length) return alert('No students');
      let csv = 'date,roll,name,status\n';
      Object.keys(data).sort().forEach(d=>{
        const dayObj = data[d] || {};
        studs.forEach(s=>{
          const st = dayObj[s.roll] || '';
          csv += d + ',' + escapeCsv(s.roll) + ',' + escapeCsv(s.name) + ',' + st + '\n';
        });
      });
      downloadBlob(csv, 'attendance_all.csv');
    });

    // Download month report
    downloadReport.addEventListener('click', ()=>{
      const {studs,data} = readStorage();
      const selMonth = monthSel.value;
      if(!selMonth) return alert('No month selected');
      let csv = 'date,roll,name,status\n';
      Object.keys(data).sort().forEach(d=>{
        if(!d.startsWith(selMonth+'-')) return;
        const dayObj = data[d] || {};
        studs.forEach(s=>{
          const st = dayObj[s.roll] || '';
          csv += d + ',' + escapeCsv(s.roll) + ',' + escapeCsv(s.name) + ',' + st + '\n';
        });
      });
      downloadBlob(csv, 'attendance_' + selMonth + '.csv');
    });

    function escapeCsv(t){ if(t==null) return ''; return '"' + String(t).replace(/"/g,'""') + '"'; }
    function downloadBlob(text, filename){
      const blob = new Blob([text], {type:'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href=url; a.download=filename; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    }

    // init with a few sample students if empty
    function ensureDefaults(){
      const {studs} = readStorage();
      if(!studs.length){
        const sample = [
          {id:'1', roll:'101', name:'Anita Sharma'},
          {id:'2', roll:'102', name:'Rahul Verma'},
          {id:'3', roll:'103', name:'Priya Singh'}
        ];
        writeStorage(sample, {});
      }
    }

    // allow clicking row to toggle mark
    studentsSection.addEventListener('click', (e)=>{
      const tr = e.target.closest('tr[data-roll]');
      if(!tr) return;
      const sel = tr.querySelector('.markSel');
      if(!sel) return;
      // if user clicked the select itself, don't toggle here
      if(e.target.tagName === 'SELECT') return;
      const cur = sel.value;
      sel.value = cur===''? 'P' : cur==='P'? 'A' : '';
    });

    // bootstrap
    function init(){
      ensureDefaults();
      setDefaultDate();
      renderStudents();
      populateMonthSelect();
      updateStats();

      // attach month change listener once
      monthSel.addEventListener('change', updateStats);

      quickAdd.addEventListener('keydown', (e)=>{ if(e.key==='Enter' && (e.ctrlKey||e.metaKey)) importBtn.click(); });
      fileInput.addEventListener('change', ()=>{ if(fileInput.files[0]) importFromFile(fileInput.files[0]); });
    }

    init();
  </script>
<footer>
  <p>Submitted by: <strong>Pradeepa, Dheeraj, Waseem, Prasanthi</strong></p>
</footer>

</body>
</html>
