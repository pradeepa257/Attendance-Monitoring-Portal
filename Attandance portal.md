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
      font-family: Inter, system-ui, Arial;
      margin:0;
      background:var(--bg);
      color:#111;
      line-height:1.45;
    }
    header{
      background:linear-gradient(90deg,var(--brand),#075a40);
      color:#fff;padding:22px 20px;
    }
    .wrap{max-width:1100px;margin:18px auto;padding:0 16px}
    h1{margin:0 0 6px 0;font-size:20px}
    .lead{color:#e6f6ee}
    .btn{
      background:var(--brand);
      color:#fff;
      padding:8px 12px;
      border-radius:8px;
      border:none;
      cursor:pointer;
    }
    .card{
      background:#fff;
      border-radius:10px;
      padding:16px;
      margin-bottom:16px;
      box-shadow:0 4px 18px rgba(20,20,40,0.05)
    }
    textarea,input,select{
      width:100%;
      padding:8px;
      border-radius:6px;
      border:1px solid #e1e6ea
    }
    table{
      width:100%;
      border-collapse:collapse;
      margin-top:12px
    }
    th,td{
      padding:8px;
      border-bottom:1px solid #eef2f6;
      text-align:left
    }
    th{background:#fbfcfd}
    footer{
      padding:16px;
      text-align:center;
      color:#6b7280;
      font-size:13px
    }
    .header-logo{
      width:470px;
      height:180px;
      display:block;
      margin:0 auto;
    }
  </style>
</head>

<body>

<header>
  <div class="wrap">
    <h1>Student Attendance Monitoring Portal</h1>
    <p class="lead">
      Maintain daily attendance without manual registers — fully browser based.
    </p>
    <img src="C:\Users\Dell\Downloads\image.png" class="header-logo" alt="Logo">
  </div>
</header>

<main class="wrap">

  <!-- Student Entry -->
  <section class="card">
    <h2>1) Add Students</h2>
    <label>Enter students (one per line: roll,name)</label>
    <textarea id="quickAdd" rows="4"
      placeholder="101,Anita Sharma&#10;102,Rahul Verma"></textarea>
    <br><br>
    <button class="btn" id="importBtn">Add Students</button>
    <button class="btn" id="clearBtn" style="background:#fff;color:#0b6e4f;border:1px solid #cfe5da">
      Clear All Data
    </button>
  </section>

  <!-- Attendance -->
  <section class="card">
    <h2>2) Mark Attendance</h2>
    <label>Select Date</label>
    <input type="date" id="attDate">
    <div id="studentsSection"></div>
    <br>
    <button class="btn" id="saveBtn">Save Attendance</button>
  </section>

  <!-- Reports -->
  <section class="card">
    <h2>3) Attendance Report</h2>
    <p><strong>Total Students:</strong> <span id="totalStudents">0</span></p>
    <p><strong>Overall Attendance %:</strong> <span id="overallPercentage">—</span></p>
    <canvas id="attendanceChart" height="180"></canvas>
  </section>

  <!-- About -->
  <section class="card">
    <h2>About</h2>
    <ul>
      <li>Manual student entry</li>
      <li>Daily attendance marking</li>
      <li>Percentage calculation</li>
      <li>Graphical report</li>
      <li>No CSV, no server, fully offline</li>
    </ul>
  </section>

</main>

<footer>
  <p>Submitted by: <strong>Batch no:2 251FD01002Pradeepa, 251FD01044Dheeraj, 251FD01054Waseem, 251FD01065Prasanthi</strong></p>
</footer>

<script>
  const STUD_KEY = 'students';
  const DATA_KEY = 'attendance';

  const quickAdd = document.getElementById('quickAdd');
  const studentsSection = document.getElementById('studentsSection');
  const totalStudents = document.getElementById('totalStudents');
  const overallPercentage = document.getElementById('overallPercentage');
  const attDate = document.getElementById('attDate');

  let chart;

  function readData(){
    return {
      students: JSON.parse(localStorage.getItem(STUD_KEY) || '[]'),
      attendance: JSON.parse(localStorage.getItem(DATA_KEY) || '{}')
    };
  }

  function writeData(students, attendance){
    localStorage.setItem(STUD_KEY, JSON.stringify(students));
    localStorage.setItem(DATA_KEY, JSON.stringify(attendance));
  }

  document.getElementById('importBtn').onclick = () => {
    const lines = quickAdd.value.trim().split('\n');
    let {students, attendance} = readData();

    lines.forEach(line => {
      const [roll, name] = line.split(',');
      if(roll && name){
        students.push({roll: roll.trim(), name: name.trim()});
      }
    });

    writeData(students, attendance);
    quickAdd.value = '';
    renderStudents();
    updateStats();
  };

  document.getElementById('clearBtn').onclick = () => {
    if(confirm('Clear all data?')){
      localStorage.clear();
      renderStudents();
      updateStats();
    }
  };

  document.getElementById('saveBtn').onclick = () => {
    const date = attDate.value;
    if(!date) return alert('Select date');

    let {students, attendance} = readData();
    attendance[date] = {};

    document.querySelectorAll('.markSel').forEach(sel => {
      if(sel.value){
        attendance[date][sel.dataset.roll] = sel.value;
      }
    });

    writeData(students, attendance);
    alert('Attendance saved');
    updateStats();
  };

  function renderStudents(){
    const {students, attendance} = readData();
    totalStudents.textContent = students.length;

    if(!students.length){
      studentsSection.innerHTML = '<p>No students added.</p>';
      return;
    }

    let html = '<table><tr><th>Roll</th><th>Name</th><th>Status</th></tr>';
    students.forEach(s => {
      html += `
        <tr>
          <td>${s.roll}</td>
          <td>${s.name}</td>
          <td>
            <select class="markSel" data-roll="${s.roll}">
              <option value="">--</option>
              <option value="P">Present</option>
              <option value="A">Absent</option>
            </select>
          </td>
        </tr>`;
    });
    html += '</table>';
    studentsSection.innerHTML = html;
  }

  function updateStats(){
    const {students, attendance} = readData();
    let p = 0, m = 0;

    Object.values(attendance).forEach(day => {
      Object.values(day).forEach(v => {
        if(v === 'P') p++;
        if(v === 'P' || v === 'A') m++;
      });
    });

    overallPercentage.textContent = m ? Math.round((p/m)*100) + '%' : '—';
    renderChart(p, m - p);
  }

  function renderChart(present, absent){
    const ctx = document.getElementById('attendanceChart');
    if(chart) chart.destroy();
    chart = new Chart(ctx,{
      type:'bar',
      data:{
        labels:['Present','Absent'],
        datasets:[{data:[present,absent]}]
      },
      options:{responsive:true}
    });
  }

  attDate.value = new Date().toISOString().slice(0,10);
  renderStudents();
  updateStats();
</script>

</body>
</html>
