<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>Velora RP</title>

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
border-bottom:2px solid #6d28d9;
}

.container{
max-width:750px;
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

input,textarea{
width:100%;
padding:10px;
margin:6px 0;
border:none;
border-radius:8px;
background:#0b0d12;
color:white;
outline:none;
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

.box{
background:#1a1f2d;
padding:10px;
margin:5px 0;
border-radius:8px;
text-align:center;
cursor:pointer;
}

.box.active{
background:#6d28d9;
}

.hidden{display:none;}

</style>
</head>

<body>

<header>
🏛️ Velora RP
<div style="font-size:12px;opacity:0.8;">نظام التوظيف الرسمي</div>
<div style="font-size:11px;opacity:0.6;">👑 الإدارة: RV</div>
</header>

<div class="container">

<!-- تقديم -->
<div class="card">
<h3>📄 التقديم</h3>

<input id="name" placeholder="الاسم">
<input id="age" placeholder="العمر">
<input id="discord" placeholder="Discord ID">
<input id="game" placeholder="Game ID">
<textarea id="exp" placeholder="الخبرات"></textarea>
<input id="hours" placeholder="ساعات التواجد">

<div class="box" onclick="selectDept(this,'وزارة الداخلية')">وزارة الداخلية</div>
<div class="box" onclick="selectDept(this,'وزارة الدفاع')">وزارة الدفاع</div>
<div class="box" onclick="selectDept(this,'وزارة الصحة')">وزارة الصحة</div>
<div class="box" onclick="selectDept(this,'وزارة العدل')">وزارة العدل</div>
<div class="box" onclick="selectDept(this,'قوات الطوارئ')">قوات الطوارئ</div>
<div class="box" onclick="selectDept(this,'أمن الدولة')">أمن الدولة</div>

<button onclick="send()">إرسال الطلب</button>

<p id="msg"></p>
</div>

<!-- لوحة المسؤول -->
<div class="card">
<button onclick="openAdmin()">🔐 لوحة المسؤول</button>
<div id="adminPanel" class="hidden"></div>
</div>

</div>

<script>

let dept="";
let applied=false;

/* اختيار قطاع */
function selectDept(el,name){
dept=name;
document.querySelectorAll(".box").forEach(b=>b.classList.remove("active"));
el.classList.add("active");
}

/* إرسال */
function send(){

if(applied){
alert("❌ قدمت مسبقاً");
return;
}

let data={
name:name.value,
age:age.value,
discord:discord.value,
game:game.value,
exp:exp.value,
hours:hours.value,
dept:dept,
status:"pending"
};

if(!data.name || !data.age || !data.discord || !data.game || !data.exp || !data.hours || !dept){
alert("عبي كل البيانات + اختر قطاع");
return;
}

let arr=JSON.parse(localStorage.getItem("apps")||"[]");
arr.push(data);
localStorage.setItem("apps",JSON.stringify(arr));

applied=true;

msg.innerText="✔ تم إرسال الطلب بنجاح";
}

/* لوحة المسؤول */
function openAdmin(){

let pass=prompt("ادخل الباسورد");

if(pass!="0008"){
alert("❌ خطأ");
return;
}

loadAdmin();
document.getElementById("adminPanel").classList.remove("hidden");

}

/* عرض الطلبات */
function loadAdmin(){

let arr=JSON.parse(localStorage.getItem("apps")||"[]");

let html="";

arr.forEach((a,i)=>{

html+=`
<div class="card">
<p>👤 الاسم: ${a.name}</p>
<p>🎂 العمر: ${a.age}</p>
<p>🎮 Game ID: ${a.game}</p>
<p>💬 Discord: ${a.discord}</p>
<p>🏛️ القطاع: ${a.dept}</p>
<p>📊 الحالة: ${a.status}</p>

<button onclick="accept(${i})">✔ قبول</button>
<button onclick="reject(${i})">✖ رفض</button>
<button onclick="copyDiscord('${a.discord}')">💬 نسخ ديسكورد</button>
</div>
`;
});

document.getElementById("adminPanel").innerHTML=html;
}

/* قبول */
function accept(i){
let arr=JSON.parse(localStorage.getItem("apps"));
arr[i].status="accepted";
localStorage.setItem("apps",JSON.stringify(arr));
loadAdmin();
}

/* رفض */
function reject(i){
let arr=JSON.parse(localStorage.getItem("apps"));
arr[i].status="rejected";
localStorage.setItem("apps",JSON.stringify(arr));
loadAdmin();
}

/* نسخ ديسكورد */
function copyDiscord(id){
navigator.clipboard.writeText(id);
alert("تم نسخ Discord ID");
}

</script>

</body>
</html>
