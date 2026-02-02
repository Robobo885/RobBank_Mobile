[index.html](https://github.com/user-attachments/files/25019769/index.html)
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>RobBank Pro — Mobile App</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Inter',sans-serif;background:radial-gradient(circle at top,#0f2027,#000);color:#fff;overflow-x:hidden;max-width:420px;margin:0 auto;transition: background 0.5s;}
header{display:flex;justify-content:space-between;align-items:center;padding:20px;position:sticky;top:0;background:rgba(0,0,0,0.6);backdrop-filter:blur(12px);z-index:10;animation:slideDown 0.8s ease forwards;}
.logo{font-size:24px;font-weight:800;opacity:0;animation:fadeIn 1s forwards 0.5s;}
section{display:none;padding:20px;gap:20px;}
.hero,#dashboard,#login,#settings{display:grid;}
h1{font-size:24px;margin-bottom:15px;opacity:0;animation:fadeIn 1s forwards 0.6s;}
p{color:#ccc;margin-bottom:20px;opacity:0;animation:fadeIn 1s forwards 0.8s;}
input{width:100%;padding:12px;border-radius:10px;border:none;margin-bottom:10px;}
.btn{padding:12px 20px;border-radius:25px;border:none;background:linear-gradient(135deg,#00e5ff,#7c4dff);color:#000;font-weight:600;cursor:pointer;transition: transform 0.3s, box-shadow 0.3s;}
.btn:hover{transform:scale(1.05);box-shadow:0 10px 25px rgba(0,229,255,0.4);}
.card-visual{position:relative;width:100%;height:160px;border-radius:20px;background:linear-gradient(135deg,#1a237e,#000);margin-bottom:20px;animation:float 4s ease-in-out infinite;}
.chip{width:50px;height:30px;background:linear-gradient(135deg,gold,orange);border-radius:6px;margin:20px;}
.number,.name{position:absolute;left:20px;color:#ccc;}
.number{bottom:50px;letter-spacing:2px;}
.name{bottom:20px;font-size:14px;}
.feature{background:rgba(255,255,255,0.05);padding:20px;border-radius:20px;backdrop-filter:blur(10px);margin-bottom:20px;opacity:0;transform:translateY(20px);animation:fadeUp 0.8s forwards;}
#history{font-size:14px;color:#ccc;max-height:150px;overflow-y:auto;}
footer{padding:80px 20px 100px;text-align:center;color:#666;font-size:14px;}
.bottom-nav{position:fixed;bottom:0;left:0;right:0;max-width:420px;margin:0 auto;display:flex;justify-content:space-around;padding:15px 0;background:rgba(0,0,0,0.85);backdrop-filter:blur(15px);border-top:1px solid rgba(255,255,255,0.1);}
.bottom-nav button{background:none;border:none;color:#aaa;font-size:13px;display:flex;flex-direction:column;align-items:center;gap:6px;cursor:pointer;transition: color 0.3s;}
.bottom-nav button.active{color:#00e5ff;}
canvas{border-radius:20px; background: rgba(255,255,255,0.05); backdrop-filter:blur(10px); padding:10px;}
.dark{background:radial-gradient(circle at top,#000,#0f2027);}
@keyframes float{0%,100%{transform:translateY(0);}50%{transform:translateY(-15px);}}
@keyframes fadeIn{to{opacity:1;}}
@keyframes fadeUp{to{opacity:1;transform:translateY(0);}}
@keyframes slideDown{from{transform:translateY(-50px);opacity:0;}to{transform:translateY(0);opacity:1;}}
</style>
</head>
<body>
<header><div class="logo">RobBank Pro</div></header>

<section class="hero" id="hero">
<h1>Банк будущего</h1>
<p>Управляй деньгами, инвестируй и следи за расходами</p>
<div class="card-visual"><div class="chip"></div><div class="number">5412 **** **** 2049</div><div class="name">ROB BANK</div></div>
<button class="btn" id="openAccount">Открыть счёт</button>
</section>

<section id="login">
<div class="feature">
<h3>Вход</h3>
<input id="user" placeholder="Логин">
<input id="pass" type="password" placeholder="Пароль">
<button class="btn" id="loginBtn">Войти</button>
</div>
</section>

<section id="dashboard">
<div class="feature">
<h3>Баланс</h3>
<p id="balanceText">12 500 ₽</p>
<button class="btn" id="addMoney">+ Пополнить</button>
</div>
<div class="feature">
<h3>Перевод</h3>
<input id="to" placeholder="Кому">
<input id="amount" type="number" placeholder="Сумма">
<button class="btn" id="sendMoney">Отправить</button>
</div>
<div class="feature">
<h3>История операций</h3>
<ul id="history"></ul>
</div>
<div class="feature">
<h3>График расходов/доходов</h3>
<canvas id="financeChart" height="160"></canvas>
</div>
</section>

<section id="settings">
<div class="feature">
<h3>Настройки</h3>
<button class="btn" id="darkMode">Тёмная/Светлая тема</button>
<button class="btn" id="resetApp">Сбросить данные</button>
</div>
</section>

<footer>© 2026 RobBank Pro. Official banking interface.</footer>
<div class="bottom-nav">
<button class="active" onclick="showSection('hero')">🏠<span>Главная</span></button>
<button onclick="showSection('dashboard')">💳<span>Банк</span></button>
<button onclick="showSection('login')">👤<span>Профиль</span></button>
<button onclick="showSection('settings')">⚙️<span>Настройки</span></button>
</div>

<script>
// ДАННЫЕ
let balance = Number(localStorage.getItem('balance'))||12500;
let operations = JSON.parse(localStorage.getItem('operations'))||[];
const balanceText=document.getElementById('balanceText');
const history=document.getElementById('history');

function saveData(){localStorage.setItem('balance',balance);localStorage.setItem('operations',JSON.stringify(operations));}
function renderHistory(){history.innerHTML='';operations.slice().reverse().forEach(op=>{history.innerHTML+=`<li>${op}</li>`;});}
function updateBalance(){if(balanceText)balanceText.textContent=balance.toLocaleString()+' ₽';}
updateBalance();renderHistory();

// LOGIN
document.getElementById('loginBtn').addEventListener('click',()=>{const user=document.getElementById('user').value;const pass=document.getElementById('pass').value;if(user&&pass){showSection('dashboard');document.querySelector('#hero h1').textContent='Добро пожаловать, '+user;document.querySelector('#hero p').innerHTML='Ваш баланс: <b>'+balance+' ₽</b>';}else alert('Введите логин и пароль');});

// ДЕНЬГИ
document.getElementById('addMoney').addEventListener('click',()=>{balance+=1000;updateBalance();operations.push('Пополнение: 1 000 ₽');saveData();renderHistory();updateChart();});
document.getElementById('sendMoney').addEventListener('click',()=>{const amount=Number(document.getElementById('amount').value);const to=document.getElementById('to').value;if(amount>0&&amount<=balance&&to){balance-=amount;updateBalance();operations.push(`Перевод ${amount} ₽ → ${to}`);saveData();renderHistory();updateChart();}else alert('Ошибка перевода');});
document.getElementById('openAccount').addEventListener('click',()=>{alert('Счёт открыт');showSection('login');});

// НАСТРОЙКИ
document.getElementById('darkMode').addEventListener('click',()=>{document.body.classList.toggle('dark');});
document.getElementById('resetApp').addEventListener('click',()=>{if(confirm('Сбросить все данные?')){localStorage.clear();balance=12500;operations=[];updateBalance();renderHistory();updateChart();showSection('hero');}});

// НАВИГАЦИЯ
function showSection(id){document.querySelectorAll('section').forEach(s=>s.style.display='none');const el=document.getElementById(id);if(el)el.style.display='grid';document.querySelectorAll('.bottom-nav button').forEach(b=>b.classList.remove('active'));}
showSection('hero');

// ГРАФИК
const ctx=document.getElementById('financeChart').getContext('2d');
let financeChart=new Chart(ctx,{type:'line',data:{labels:operations.map((_,i)=>i+1),datasets:[{label:'Баланс',data:operations.map(()=>balance),backgroundColor:'rgba(0,229,255,0.2)',borderColor:'#00e5ff',borderWidth:2,tension:0.4}]},options:{responsive:true,plugins:{legend:{display:true}},scales:{y:{beginAtZero:false}}}});
function updateChart(){financeChart.data.labels=operations.map((_,i)=>i+1);financeChart.data.datasets[0].data=operations.map(()=>balance);financeChart.update();}
</script>
</body>
</html>
