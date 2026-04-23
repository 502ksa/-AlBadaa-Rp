<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>AlBadaa RP - التوظيف</title>

<style>

*{box-sizing:border-box;}

body{
margin:0;
font-family:tahoma;
background:#0f1116;
color:white;
}

header{
background:#141826;
padding:18px;
text-align:center;
font-weight:bold;
border-bottom:2px solid #6d28d9;
}

.container{
max-width:900px;
margin:auto;
padding:15px;
}

.card{
background:#161a23;
padding:15px;
margin:10px 0;
border-radius:10px;
border:1px solid #22283a;
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
outline:none;
}

input.error,textarea.error{
border:1px solid red;
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
}

.box.active{
background:#6d28d9;
}

.hidden{display:none;}

</style>
</head>

<body>

<header>
🏛️ AlBadaa RP - التوظيف
<div style="font-size:12px;">👑 المسؤول: RV</div>
</header>

<div class="container">

<div class="card">
<button onclick="login()">تسجيل دخول</button>
<button onclick="openAdmin()">لوحة المسؤول</button>
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

const ADMIN_PASSWORD = "123456"; // 🔐 غيره

let dept="";
let logged=false;
let applied=false;

/* تسجيل */
function login(){
logged=true;
localStorage.setItem("login","1");
alert("تم تسجيل الدخول ✔");
}

/* لوحة المسؤول */
function openAdmin(){

let pass=prompt("ادخل الرمز السري");

if(pass===ADMIN_PASSWORD){
admin.classList.remove("hidden");
load();
}else{
alert("❌ رمز خاطئ");
}

}

/* استرجاع */
window.onload=()=>{
if(localStorage.getItem("login")) logged=true;
if(localStorage.getItem("applied")) applied=true;
};

/* اختيار */
function selectDept(el,name){
if(applied) return;

dept=name;

document.querySelectorAll(".box").forEach(b=>b.classList.remove("active"));
el.classList.add("active");

check();
}

/* تحقق ذكي */
function check(){

let valid=true;

let fields=["name","age","discord","gameId","exp","hours"];

fields.forEach(id=>{
let el=document.getElementById(id);
if(!el.value.trim()){
el.classList.add("error");
valid=false;
}else{
el.classList.remove("error");
}
});

if(!dept) valid=false;

sendBtn.disabled=!valid;
}

/* مراقبة */
document.querySelectorAll("input,textarea").forEach(i=>{
i.addEventListener("input",check);
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

}

/* لوحة المسؤول */
function load(){

let apps=JSON.parse(localStorage.getItem("apps")||"[]");

let html="";

apps.forEach((a,i)=>{

html+=`
<div class="card">
<p>👤 ${a.name}</p>
<p>🏛️ ${a.dept}</p>
<p>📊 ${a.status}</p>

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
