# - albadaa rp

<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>AlBadaa RP - بوابة التوظيف</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
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
font-size:20px;
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
}

.box:hover{
background:#232a3d;
}

.hidden{display:none;}

</style>
</head>

<body>

<header>
🏛️ AlBadaa RP - بوابة التوظيف
<div style="font-size:12px;opacity:0.7;margin-top:4px;">
👑 المسؤول: RV
</div>
</header>

<div class="container">

<!-- تسجيل دخول -->
<div class="card">
<button onclick="login()">تسجيل دخول Discord</button>
</div>

<!-- الجهات -->
<div class="card">
<h3>اختر الجهة</h3>

<div class="box" onclick="dept='وزارة الداخلية'">وزارة الداخلية</div>
<div class="box" onclick="dept='وزارة الدفاع'">وزارة الدفاع</div>
<div class="box" onclick="dept='وزارة الصحة'">وزارة الصحة</div>
<div class="box" onclick="dept='وزارة العدل'">وزارة العدل</div>
<div class="box" onclick="dept='قوات الطوارئ'">قوات الطوارئ</div>
<div class="box" onclick="dept='أمن الدولة'">أمن الدولة</div>

</div>

<!-- التقديم -->
<div class="card">

<input id="name" placeholder="الاسم">
<input id="age" placeholder="العمر">
<input id="discord" placeholder="Discord ID">
<input id="gameId" placeholder="Game ID داخل السيرفر">
<textarea id="exp" placeholder="الخبرات"></textarea>
<input id="hours" placeholder="عدد ساعات التواجد يومياً">

<button onclick="send()">إرسال الطلب</button>

<p id="msg"></p>

</div>

<!-- لوحة المسؤول -->
<div class="card hidden" id="adminPanel">
<h3>👑 لوحة المسؤول</h3>
<div id="apps"></div>
</div>

</div>

<script>

/* ================= FIREBASE ================= */
const firebaseConfig = {
apiKey: "YOUR_API_KEY",
authDomain: "YOUR_AUTH",
projectId: "YOUR_PROJECT"
};

firebase.initializeApp(firebaseConfig);

const db = firebase.firestore();
const auth = firebase.auth();

/* ================= DISCORD ================= */
const WEBHOOK_URL = "YOUR_DISCORD_WEBHOOK";

/* ================= المسؤولين ================= */
const ADMIN_IDS = [
"1274697642533978184"
];

let dept="";
let currentDiscordId="";

/* ================= تسجيل دخول ================= */
function login(){
const provider = new firebase.auth.OAuthProvider('discord.com');
auth.signInWithPopup(provider);
}

/* جلب المستخدم */
auth.onAuthStateChanged(user=>{
if(user){
currentDiscordId = user.providerData[0].uid;
loadApplications();
}
});

/* ================= تحقق مسؤول ================= */
function isAdmin(id){
return ADMIN_IDS.includes(id);
}

/* ================= إرسال طلب ================= */
async function send(){

if(!auth.currentUser){
alert("سجل دخول أول");
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
status:"pending",
discordId:currentDiscordId,
server:"AlBadaa RP"
};

await db.collection("applications").add(data);

document.getElementById("msg").innerText="✔ تم إرسال الطلب";
}

/* ================= تحميل الطلبات ================= */
async function loadApplications(){

if(!isAdmin(currentDiscordId)) return;

document.getElementById("adminPanel").classList.remove("hidden");

let snap = await db.collection("applications").get();

document.getElementById("apps").innerHTML="";

snap.forEach(doc=>{

let d = doc.data();

document.getElementById("apps").innerHTML += `
<div class="card">

<p>👤 ${d.name}</p>
<p>🏛️ ${d.dept}</p>
<p>🎮 ${d.gameId}</p>
<p>💬 ${d.discord}</p>
<p>📊 ${d.status}</p>

<button onclick="accept('${doc.id}')">✔ قبول</button>
<button onclick="reject('${doc.id}')">✖ رفض</button>

</div>
`;
});

}

/* ================= إشعار ديسكورد ================= */
function notify(data,msg){

fetch(WEBHOOK_URL,{
method:"POST",
headers:{"Content-Type":"application/json"},
body:JSON.stringify({
content:`
📢 إشعار طلب

👤 ${data.name}
🏛️ ${data.dept}
🎮 ${data.gameId}

📊 ${msg}
`
})
});

}

/* ================= قبول / رفض ================= */
async function accept(id){

if(!isAdmin(currentDiscordId)) return;

let doc=await db.collection("applications").doc(id).get();
let data=doc.data();

await db.collection("applications").doc(id).update({
status:"accepted"
});

notify(data,"✔ تم قبول طلبك");

loadApplications();
}

async function reject(id){

if(!isAdmin(currentDiscordId)) return;

let doc=await db.collection("applications").doc(id).get();
let data=doc.data();

await db.collection("applications").doc(id).update({
status:"rejected"
});

notify(data,"✖ تم رفض طلبك");

loadApplications();
}

</script>

</body>
</html>
