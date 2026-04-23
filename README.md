<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>AlBadaa RP - التوظيف</title>

<style>

*{
box-sizing:border-box;
}

body{
margin:0;
font-family:tahoma;
background:#0f1116;
color:white;
}

/* إصلاح مشكلة الضغط */
body::before{
content:"";
position:fixed;
top:0;
left:0;
width:100%;
height:100%;
z-index:-1;
}

.container{
position:relative;
z-index:5;
max-width:900px;
margin:auto;
padding:15px;
}

header{
background:#141826;
padding:18px;
text-align:center;
font-weight:bold;
border-bottom:2px solid #6d28d9;
}

.card{
background:#161a23;
padding:15px;
margin:10px 0;
border-radius:10px;
border:1px solid #22283a;
position:relative;
z-index:10;
}

button{
width:100%;
padding:12px;
margin-top:8px;
border:none;
border-radius:8px;
background:#5b21b6;
color:white;
font-weight:bold;
cursor:pointer;
position:relative;
z-index:20;
pointer-events:auto;
}

button:disabled{
background:#333;
cursor:not-allowed;
}

input,textarea{
width:100%;
padding:10px;
margin:6px 0;
border-radius:8px;
border:none;
background:#0b0d12;
color:white;
}

.box{
background:#1a1f2d;
padding:12px;
margin:6px 0;
border-radius:8px;
text-align:center;
cursor:pointer;
border:1px solid #2a3145;
transition:0.2s;
user-select:none;
position:relative;
z-index:20;
pointer-events:auto;
}

.box:hover{
background:#232a3d;
}

.box.active{
background:#6d28d9;
border:1px solid #a78bfa;
}

.hidden{display:none;}

.small{
font-size:12px;
opacity:0.7;
}

</style>
</head>

<body>

<header>
🏛️ AlBadaa RP - التوظيف
<div class="small">👑 المسؤول: RV</div>
</header>

<div class="container">

<div class="card">
<button onclick="login()">تسجيل دخول</button>
<p class="small">الدخول محفوظ تلقائي</p>
</div>

<div class="card">
<h3>اختر القطاع</h3>

<div class="box" onclick="selectDept(this,'وزارة الداخلية')">وزارة الداخلية</div>
<div class="box" onclick="selectDept(this,'وزارة الدفاع')">وزارة الدفاع</div>
<div class="box" onclick="selectDept(this,'وزارة الصحة')">وزارة الصحة</div>
<div class="box" onclick="selectDept(this,'وزارة العدل')">وزارة العدل</div>
<div class="box" onclick="selectDept(this,'قوات الطوارئ')">قوات الطوارئ</div>
<div class="box" onclick="selectDept(this,'أمن الدولة')">أمن الدولة</div>

</div>

<div class="card">

<input id="name" placeholder="الاسم">
<input id="age" placeholder="العمر">
<input id="discord" placeholder="Discord ID">
<input id="gameId" placeholder="Game ID">
<textarea id="exp" placeholder="الخبرات"></textarea>
<input id="hours" placeholder="ساعات التواجد">

<button id="sendBtn" onclick="send()" disabled>إرسال الطلب</button>

<p id="msg"></p>

</div>

<div class="card hidden" id="admin">
<h3>👑 لوحة المسؤول</h3>
<div id="list"></div>
</div>

</div>

<script>

let dept="";
let logged=false;
let applied=false;

/* تسجيل */
function login(){
logged=true;
localStorage.setItem("login","1");
alert("تم تسجيل الدخول ✔");
}

/* استرجاع */
window.onload=()=>{
if(localStorage.getItem("login")) logged=true;
if(localStorage.getItem("applied")) applied=true;
load();
};

/* اختيار */
function selectDept(el,name){

if(applied) return;

dept=name;

document.querySelectorAll(".box").forEach(b=>b.classList.remove("active"));
el.classList.add("active");

check();
}

/* تحقق */
function check(){

let filled=
name.value &&
age.value &&
discord.value &&
gameId.value &&
exp.value &&
hours.value &&
dept;

sendBtn.disabled=!filled;
}

/* مراقبة */
document.querySelectorAll("input,textarea").forEach(i=>{
i.oninput=check;
});

/* إرسال */
function send(){

if(!logged){
alert("سجل دخول أول");
return;
}

if(applied){
alert("قدمت مسبقاً ❌");
return;
}

let data={
name:name.value,
age:age.value,
discord:discord.value,
gameId:gameId.value,
exp:exp.value,
hours:hours.value,
dept:dept,
status:"pending"
};

let apps=JSON.parse(localStorage.getItem("apps")||"[]");
apps.push(data);
localStorage.setItem("apps",JSON.stringify(apps));

applied=true;
localStorage.setItem("applied","1");

msg.innerText="✔ تم إرسال الطلب بنجاح";

sendBtn.disabled=true;

load();
}

/* لوحة المسؤول */
function load(){

let apps=JSON.parse(localStorage.getItem("apps")||"[]");

if(logged){
admin.classList.remove("hidden");
}

let html="";

apps.forEach((a,i)=>{

html+=`
<div class="card">
<p>👤 ${a.name}</p>
<p>🏛️ ${a.dept}</p>
<p>📊 الحالة: ${a.status}</p>

<button onclick="accept(${i})">✔ قبول</button>
<button onclick="reject(${i})">✖ رفض</button>
</div>
`;
});

list.innerHTML=html;
}

function accept(i){
let apps=JSON.parse(localStorage.getItem("apps"));
apps[i].status="accepted";
localStorage.setItem("apps",JSON.stringify(apps));
load();
}

function reject(i){
let apps=JSON.parse(localStorage.getItem("apps"));
apps[i].status="rejected";
localStorage.setItem("apps",JSON.stringify(apps));
load();
}

</script>

</body>
</html>
