#VELORA RP
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Velora RP - </title>

<style>

*{
box-sizing:border-box;
}

body{
margin:0;
font-family:Tahoma, Arial;
background:#0f1116;
color:white;
unicode-bidi:plaintext;
direction:rtl;
}

header{
background:#141826;
padding:18px;
text-align:center;
border-bottom:2px solid #6d28d9;
}

.container{
max-width:850px;
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
font-family:inherit;
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
transition:0.2s;
}

.box.active{
background:#6d28d9;
}

.hidden{display:none;}

.copy{
background:#374151;
margin-top:5px;
font-size:12px;
padding:8px;
}

hr{opacity:0.2;}

pre{
white-space:pre-wrap;
background:#0b0d12;
padding:10px;
border-radius:8px;
}

</style>
</head>

<body>

<header>
🏛️ Velora RP 
<div style="font-size:12px;opacity:0.7;">نظام التوظيف الاحترافي</div>
</header>

<div class="container">

<!-- التقديم -->
<div class="card">

<h3>📄 التقديم</h3>

<input id="name" placeholder="الاسم">
<input id="age" placeholder="العمر">
<input id="discord" placeholder="Discord ID">
<input id="game" placeholder="Game ID">
<textarea id="exp" placeholder="الخبرات"></textarea>
<input id="hours" placeholder="ساعات التواجد">

<div class="box" onclick="pick(this,'الداخلية')">الداخلية</div>
<div class="box" onclick="pick(this,'الدفاع')">الدفاع</div>
<div class="box" onclick="pick(this,'الصحة')">الصحة</div>
<div class="box" onclick="pick(this,'العدل')">العدل</div>
<div class="box" onclick="pick(this,'الطوارئ')">الطوارئ</div>
<div class="box" onclick="pick(this,'أمن الدولة')">أمن الدولة</div>

<button onclick="send()">إرسال الطلب</button>

<p id="msg"></p>

</div>

<!-- لوحة المسؤول -->
<div class="card">

<button onclick="adminLogin()">🔐 لوحة المسؤول</button>

<div id="panel" class="hidden"></div>

</div>

</div>

<script>

let dept="";
let applied=false;

/* اختيار قطاع */
function pick(el,name){
dept=name;
document.querySelectorAll(".box").forEach(b=>b.classList.remove("active"));
el.classList.add("active");
}

/* إرسال */
function send(){

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
alert("❌ عبي كل البيانات واختر القطاع");
return;
}

if(applied){
alert("❌ قدمت مسبقًا");
return;
}

let arr=JSON.parse(localStorage.getItem("apps")||"[]");
arr.push(data);
localStorage.setItem("apps",JSON.stringify(arr));

applied=true;
msg.innerText="✔ تم إرسال الطلب";

}

/* دخول المسؤول */
function adminLogin(){

let pass=prompt("🔐 ادخل الباسورد");

if(pass!="0008"){
alert("❌ خطأ");
return;
}

load();
panel.classList.remove("hidden");

}

/* عرض الطلبات */
function load(){

let arr=JSON.parse(localStorage.getItem("apps")||"[]");

let html="";

arr.forEach((a,i)=>{

html+=`
<div class="card">

<p><b>👤 الاسم:</b> ${a.name}</p>
<button class="copy" onclick="copy('${a.name}')">نسخ الاسم</button>

<p><b>🎂 العمر:</b> ${a.age}</p>
<button class="copy" onclick="copy('${a.age}')">نسخ العمر</button>

<p><b>💬 Discord:</b> ${a.discord}</p>
<button class="copy" onclick="copy('${a.discord}')">نسخ ديسكورد</button>

<p><b>🎮 Game ID:</b> ${a.game}</p>
<button class="copy" onclick="copy('${a.game}')">نسخ Game ID</button>

<p><b>🏛️ القطاع:</b> ${a.dept}</p>

<p><b>📄 الخبرات:</b></p>
<pre>${a.exp}</pre>
<button class="copy" onclick="copy(\`${a.exp}\`)">نسخ الخبرات</button>

<p><b>⏱ الساعات:</b> ${a.hours}</p>

<p><b>📊 الحالة:</b> ${a.status}</p>

<hr>

<button onclick="accept(${i})">✔ قبول</button>
<button onclick="reject(${i})">✖ رفض</button>

</div>
`;
});

panel.innerHTML=html;
}

/* نسخ */
function copy(text){
navigator.clipboard.writeText(text);
alert("تم النسخ ✔");
}

/* قبول */
function accept(i){
let arr=JSON.parse(localStorage.getItem("apps"));
arr[i].status="accepted";
localStorage.setItem("apps",JSON.stringify(arr));
load();
}

/* رفض */
function reject(i){
let arr=JSON.parse(localStorage.getItem("apps"));
arr[i].status="rejected";
localStorage.setItem("apps",JSON.stringify(arr));
load();
}

</script>

</body>
</html>
