# Attendence-management-system
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Professional Attendance System</title>
<style>
body { margin:0; font-family: Arial, sans-serif; background:#f0f2f5; }
.hidden { display:none; }

/* INTRO PAGE */
#introPage {
  display:flex; flex-direction:column; align-items:center; justify-content:center;
  height:100vh; text-align:center; background:linear-gradient(135deg,#007bff,#00c6ff);
  color:white; padding:20px;
}
#introPage h1 { font-size:2.5em; margin-bottom:15px; }
#introPage p { max-width:600px; font-size:1.1em; line-height:1.5; }
#getStartedBtn {
  margin-top:30px; padding:12px 25px; font-size:16px; border:none;
  background:white; color:#007bff; border-radius:8px; cursor:pointer; font-weight:bold;
}
#getStartedBtn:hover { background:#f9f9f9; }

/* LOGIN / SIGNUP CONTAINER */
.container {
  max-width:400px; margin:50px auto; background:#fff; padding:30px;
  border-radius:12px; box-shadow:0 8px 20px rgba(0,0,0,0.15);
}
h2 { text-align:center; color:#333; }
input,select {
  width:100%; padding:10px; margin:10px 0;
  border-radius:6px; border:1px solid #ccc; box-sizing:border-box;
}
button {
  padding:10px; width:100%; border:none; border-radius:6px; cursor:pointer;
  font-weight:bold; background:#007bff; color:white;
}
button:hover { opacity:0.9; }
a { color:#007bff; text-decoration:none; }
a:hover { text-decoration:underline; }

/* ATTENDANCE PAGE */
.attendance-container {
  max-width:1000px; margin:30px auto; background:#fff; padding:20px;
  border-radius:12px; box-shadow:0 8px 20px rgba(0,0,0,0.1);
}
table { width:100%; border-collapse:collapse; margin-top:20px; }
th,td { padding:12px; text-align:center; border-bottom:1px solid #ddd; }
th { background:#007bff; color:white; }
tr:nth-child(even){ background:#f9f9f9; }
tr:hover { background:#f1f1f1; }
.present{ color:green; font-weight:bold; }
.absent{ color:red; font-weight:bold; }

.btn-present{ background:#28a745; color:white; border-radius:5px; padding:6px 12px; margin:2px; cursor:pointer; }
.btn-absent{ background:#dc3545; color:white; border-radius:5px; padding:6px 12px; margin:2px; cursor:pointer; }
.disabled-btn{ background:#ccc; color:#666; cursor:not-allowed; padding:6px 12px; border-radius:5px; }

.filter-box{ display:flex; flex-wrap:wrap; justify-content:center; gap:10px; margin-top:15px; }
.filter-box input,.filter-box select{ width:auto; flex:1; min-width:120px; }

.summary{ display:flex; justify-content:space-around; margin-top:15px; font-weight:bold; }
.logout-btn{ background:#ff4d4d; color:white; margin-top:20px; }

@media(max-width:700px){
  .filter-box{ flex-direction:column; align-items:stretch; }
  .summary{ flex-direction:column; align-items:center; gap:10px; }
}
</style>
</head>
<body>

<!-- INTRO PAGE (Default visible) -->
<div id="introPage">
  <h1>Attendance Management System</h1>
  <p>
    Welcome! This system helps institutions manage student attendance efficiently.  
    You can mark attendance, filter records by date/department, and view summaries.  
    Please click below to continue.
  </p>
  <button id="getStartedBtn" onclick="showLogin()">Get Started</button>
</div>

<!-- SIGNUP PAGE (Hidden by default) -->
<div id="signupPage" class="container hidden">
  <h2>Create Account</h2>
  <input type="text" id="campus" placeholder="Campus Name">
  <input type="email" id="signupEmail" placeholder="Email">
  <input type="password" id="signupPassword" placeholder="Password">
  <input type="password" id="confirmPassword" placeholder="Confirm Password">
  <button onclick="signup()">Sign Up</button>
  <p style="text-align:center;">Already have an account? <a href="#" onclick="showLogin()">Login</a></p>
  <p id="signupMsg" style="color:red; text-align:center;"></p>
</div>

<!-- LOGIN PAGE (Hidden by default) -->
<div id="loginPage" class="container hidden">
  <h2>Login</h2>
  <input type="email" id="loginEmail" placeholder="Email">
  <input type="password" id="loginPassword" placeholder="Password">
  <button onclick="login()">Login</button>
  <p style="text-align:center;">Don't have an account? <a href="#" onclick="showSignup()">Sign Up</a></p>
  <p id="loginMsg" style="color:red; text-align:center;"></p>
</div>

<!-- ATTENDANCE PAGE (Hidden by default) -->
<div id="attendancePage" class="attendance-container hidden">
  <h2>Attendance Management System</h2>

  <div class="filter-box">
    <label>Date: <input type="date" id="dateFilter"></label>
    <select id="deptFilter">
      <option value="">All Departments</option>
      <option value="CSE">CSE</option>
      <option value="EEE">EEE</option>
      <option value="CIVIL">CIVIL</option>
    </select>
    <input type="text" id="rollFilter" placeholder="Search Roll">
    <input type="text" id="nameFilter" placeholder="Search Name">
  </div>

  <table id="attendanceTable">
    <thead>
      <tr>
        <th>Department</th>
        <th>Roll</th>
        <th>Name</th>
        <th>Status</th>
        <th>Mark</th>
      </tr>
    </thead>
    <tbody>
      <tr><td>CSE</td><td>101</td><td>Akash</td><td class="status">-</td><td class="buttons"></td></tr>
      <tr><td>EEE</td><td>102</td><td>Rahim</td><td class="status">-</td><td class="buttons"></td></tr>
      <tr><td>CIVIL</td><td>103</td><td>Karim</td><td class="status">-</td><td class="buttons"></td></tr>
    </tbody>
  </table>

  <div class="summary">
    <div>Total Present: <span id="totalPresent">0</span></div>
    <div>Total Absent: <span id="totalAbsent">0</span></div>
  </div>

  <button class="logout-btn" onclick="logout()">Logout</button>
</div>

<script>
// Show only one section at a time
function hideAll(){
  document.getElementById("introPage").classList.add("hidden");
  document.getElementById("loginPage").classList.add("hidden");
  document.getElementById("signupPage").classList.add("hidden");
  document.getElementById("attendancePage").classList.add("hidden");
}

// Show Login
function showLogin(){
  hideAll();
  document.getElementById("loginPage").classList.remove("hidden");
}

// Show Signup
function showSignup(){
  hideAll();
  document.getElementById("signupPage").classList.remove("hidden");
}

// Signup
function signup(){
  const campus=document.getElementById('campus').value;
  const email=document.getElementById('signupEmail').value;
  const password=document.getElementById('signupPassword').value;
  const confirmPassword=document.getElementById('confirmPassword').value;
  const msg=document.getElementById('signupMsg');

  if(!campus||!email||!password||!confirmPassword){ msg.textContent="All fields required!"; return; }
  if(password!==confirmPassword){ msg.textContent="Passwords do not match!"; return; }

  let users=JSON.parse(localStorage.getItem('users')||"[]");
  if(users.find(u=>u.email===email)){ msg.textContent="Email already registered!"; return; }

  users.push({campus,email,password});
  localStorage.setItem('users',JSON.stringify(users));
  msg.style.color="green"; msg.textContent="Account created! Redirecting...";
  setTimeout(showLogin,1500);
}

// Login
function login(){
  const email=document.getElementById('loginEmail').value;
  const password=document.getElementById('loginPassword').value;
  const msg=document.getElementById('loginMsg');

  let users=JSON.parse(localStorage.getItem('users')||"[]");
  let user=users.find(u=>u.email===email && u.password===password);

  if(user){
    hideAll();
    document.getElementById('attendancePage').classList.remove('hidden');
    loadButtons();
    updateSummary();
  } else msg.textContent="Invalid Email or Password!";
}

// Logout
function logout(){
  hideAll();
  showLogin();
  document.getElementById('loginEmail').value="";
  document.getElementById('loginPassword').value="";
}

// ATTENDANCE SYSTEM
const today=new Date().toISOString().split("T")[0];
const dateInput=document.getElementById("dateFilter");
dateInput.value=today;

function loadButtons(){
  let selectedDate=dateInput.value;
  let rows=document.querySelectorAll("#attendanceTable tbody tr");

  rows.forEach(row=>{
    let btnCell=row.querySelector(".buttons");
    btnCell.innerHTML="";
    let key="attendance_"+selectedDate;
    let stored=JSON.parse(localStorage.getItem(key)||"{}");
    let roll=row.cells[1].textContent;

    if(selectedDate===today){
      btnCell.innerHTML=`
        <button class="btn-present" onclick="mark(this,'Present')">Present</button>
        <button class="btn-absent" onclick="mark(this,'Absent')">Absent</button>
      `;
    } else {
      btnCell.innerHTML=`<button class="disabled-btn" disabled>Present</button><button class="disabled-btn" disabled>Absent</button>`;
    }

    if(stored[roll]){
      let statusCell=row.querySelector(".status");
      statusCell.textContent=stored[roll];
      statusCell.className="status "+(stored[roll]==="Present"?"present":"absent");
    }
  });
  updateSummary();
}

function mark(button,status){
  let row=button.closest("tr");
  let statusCell=row.querySelector(".status");
  statusCell.textContent=status;
  statusCell.className="status "+(status==="Present"?"present":"absent");

  let key="attendance_"+dateInput.value;
  let stored=JSON.parse(localStorage.getItem(key)||"{}");
  let roll=row.cells[1].textContent;
  stored[roll]=status;
  localStorage.setItem(key,JSON.stringify(stored));
  updateSummary();
}

// Filter
document.querySelectorAll("#deptFilter,#rollFilter,#nameFilter").forEach(el=>{el.addEventListener("input",filterTable);});
dateInput.addEventListener("change",()=>{loadButtons(); filterTable();});

function filterTable(){
  let dept=document.getElementById("deptFilter").value.toUpperCase();
  let roll=document.getElementById("rollFilter").value.toUpperCase();
  let name=document.getElementById("nameFilter").value.toUpperCase();

  let rows=document.querySelectorAll("#attendanceTable tbody tr");
  rows.forEach(row=>{
    let deptCell=row.cells[0].textContent.toUpperCase();
    let rollCell=row.cells[1].textContent.toUpperCase();
    let nameCell=row.cells[2].textContent.toUpperCase();

    if((dept===""||deptCell===dept)&&(roll===""||rollCell.includes(roll))&&(name===""||nameCell.includes(name))) row.style.display="";
    else row.style.display="none";
  });
}

// Summary
function updateSummary(){
  let totalP=0,totalA=0;
  let rows=document.querySelectorAll("#attendanceTable tbody tr");
  rows.forEach(row=>{
    let status=row.querySelector(".status").textContent;
    if(status==="Present") totalP++;
    else if(status==="Absent") totalA++;
  });
  document.getElementById("totalPresent").textContent=totalP;
  document.getElementById("totalAbsent").textContent=totalA;
}
</script>

</body>
</html>
