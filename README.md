# - albadaa rp
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>AlBadaa RP - التقديم</title>

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

button:hover{
background:#6d28d9;
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
}

.box.active{
background:#6d28d9;
border:1px solid #a78bfa;
}

.hidden{display:none;}

</style>
</head>

<body>

<header>
🏛️ AlBadaa RP - التقديم الرسمي
<div style="font-size:12px;opacity:0.7;margin-top:5px;">
👑 المسؤول: RV
</div>
</header>

<div class="container">

<div class="card">
<button onclick="login()">تسجيل دخول Discord</button>
</div>

<div class="card">
<h3>اختر الجهة</h3>

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

<button onclick="send()">إرسال الطلب</button>

<p id="msg"></p>

</div>

</div>

<script>

/* ================= FIREBASE ================= */
const firebaseConfig = {
apiKey: "YOUR_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_ID"
};

firebase.initializeApp(firebaseConfig);

const db = firebase.firestore();

/* ================= بيانات ================= */
let dept="";
let userApplied=false;

/* ================= تسجيل دخول وهمي (مؤقت) ================= */
/* لأن Discord OAuth يحتاج سيرفر */
function login(){
localStorage.setItem("logged","true");
alert("تم تسجيل الدخول (محاكاة)");
}

/* ================= اختيار القطاع ================= */
function selectDept(el,name){

if(userApplied){
alert("لا يمكنك اختيار قطاع بعد التقديم");
return;
}

dept=name;

// إزالة اللون من الكل
document.querySelectorAll(".box").forEach(b=>{
b.classList.remove("active");
});

// تلوين المختار
el.classList.add("active");

}

/* ================= إرسال طلب ================= */
async function send(){

if(userApplied){
alert("لقد قدمت مسبقاً ❌");
return;
}

if(!dept){
alert("اختر القطاع أولاً");
return;
}

let data={
name:document.getElementById("name").value,
age:document.getElementById("age").value,
discord:document.getElementById("discord").value,
gameId:document.getElementById("gameId").value,
exp:document.getElementById("exp").value,
hours:document.getElementById("hours").value,
dept:dept,
status:"pending"
};

await db.collection("applications").add(data);

/* قفل التقديم */
userApplied=true;
localStorage.setItem("applied","true");

document.getElementById("msg").innerText="✔ تم إرسال الطلب بنجاح";

}

/* ================= منع التكرار ================= */
window.onload=()=>{
if(localStorage.getItem("applied")){
userApplied=true;
document.getElementById("msg").innerText="لقد قدمت مسبقاً";
}
}

</script>

</body>
</html>
