# albadaa RP
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>AlBadaa RP - نظام التوظيف</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

<style>

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
border:1px solid #a78bfa;
}

.hidden{display:none;}

.small{font-size:12px;opacity:0.7;}

</style>
</head>

<body>

<header>
🏛️ AlBadaa RP - نظام التوظيف
<div class="small">👑 المسؤول: RV</div>
</header>

<div class="container">

<!-- تسجيل دخول -->
<div class="card">
<button onclick="login()">تسجيل دخول Discord</button>
<p class="small">الدخول يتم حفظه تلقائيًا</p>
</div>

<!-- القطاعات -->
<div class="card">
<h3>اختر القطاع</h3>

<div class="box" onclick="selectDept(this,'وزارة الداخلية')">وزارة الداخلية</div>
<div class="box" onclick="selectDept(this,'وزارة الدفاع')">وزارة الدفاع</div>
<div class="box" onclick="selectDept(this,'وزارة الصحة')">وزارة الصحة</div>
<div class="box" onclick="selectDept(this,'وزارة العدل')">وزارة العدل</div>
<div class="box" onclick="selectDept(this,'قوات الطوارئ')">قوات الطوارئ</div>
<div class="box" onclick="selectDept(this,'أمن الدولة')">أمن الدولة</div>

</div>

<!-- التقديم -->
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

<!-- لوحة المسؤول -->
<div class="card hidden" id="adminPanel">
<h3>👑 لوحة المسؤول</h3>
<div id="apps"></div>
</div>

</div>

<script>

/* 🔥 Firebase */
const firebaseConfig = {
apiKey:"YOUR_KEY",
authDomain:"YOUR_DOMAIN",
projectId:"YOUR_ID"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

/* 🔔 Discord Webhook */
const WEBHOOK="YOUR_DISCORD_WEBHOOK";

/* 👑 المسؤولين */
const ADMIN_IDS=[
"1274697642533978184"
];

let dept="";
let applied=false;
let logged=false;

/* 🔐 تسجيل دخول (محفوظ) */
function login(){
localStorage.setItem("logged","true");
logged=true;
alert("تم تسجيل الدخول ✔");
}

/* 🔄 استرجاع الحالة */
window.onload=()=>{
if(localStorage.getItem("logged")){
logged=true;
}

if(localStorage.getItem("applied")){
applied=true;
document.getElementById("msg").innerText="لقد قدمت مسبقاً";
}
}

/* 🎯 اختيار قطاع */
function selectDept(el,name){

if(applied) return;

dept=name;

document.querySelectorAll(".box").forEach(b=>{
b.classList.remove("active");
});

el.classList.add("active");

checkForm();
}

/* 🧠 تحقق ذكي */
function checkForm(){

if(applied) return;

let name=document.getElementById("name").value.trim();
let age=document.getElementById("age").value.trim();
let discord=document.getElementById("discord").value.trim();
let gameId=document.getElementById("gameId").value.trim();
let exp=document.getElementById("exp").value.trim();
let hours=document.getElementById("hours").value.trim();

document.getElementById("sendBtn").disabled=
!(name && age && discord && gameId && exp && hours && dept);

}

/* 📩 إرسال */
async function send(){

if(!logged){
alert("سجل دخول أول");
return;
}

if(applied){
alert("❌ قدمت مسبقاً");
return;
}

let name=document.getElementById("name").value.trim();
let age=document.getElementById("age").value.trim();
let discord=document.getElementById("discord").value.trim();
let gameId=document.getElementById("gameId").value.trim();
let exp=document.getElementById("exp").value.trim();
let hours=document.getElementById("hours").value.trim();

if(!name || !age || !discord || !gameId || !exp || !hours || !dept){
alert("❌ أكمل البيانات");
return;
}

let data={
name,age,discord,gameId,exp,hours,dept,
status:"pending"
};

await db.collection("applications").add(data);

/* قفل التقديم */
applied=true;
localStorage.setItem("applied","true");

document.getElementById("msg").innerText="✔ تم إرسال الطلب بنجاح";

document.getElementById("sendBtn").disabled=true;

/* Discord */
fetch(WEBHOOK,{
method:"POST",
headers:{"Content-Type":"application/json"},
body:JSON.stringify({
content:`📩 طلب جديد
👤 ${name}
🏛️ ${dept}
🎮 ${gameId}
💬 ${discord}`
})
});

}

/* 👑 لوحة المسؤول */
async function loadAdmin(){

let snap=await db.collection("applications").get();

let html="";

snap.forEach(doc=>{

let d=doc.data();

html+=`
<div class="card">
<p>👤 ${d.name}</p>
<p>🏛️ ${d.dept}</p>
<p>📊 ${d.status}</p>

<button onclick="accept('${doc.id}')">✔ قبول</button>
<button onclick="reject('${doc.id}')">✖ رفض</button>
</div>
`;
});

document.getElementById("apps").innerHTML=html;

}

/* ✔ قبول */
function accept(id){
db.collection("applications").doc(id).update({status:"accepted"});
loadAdmin();
}

/* ✖ رفض */
function reject(id){
db.collection("applications").doc(id).update({status:"rejected"});
loadAdmin();
}

loadAdmin();

</script>

</body>
</html>
