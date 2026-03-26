
<html>
<head>
<meta charset="UTF-8">
<title>Tracker Pro Max</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>

<style>
body{margin:0;font-family:Arial;text-align:center;}
#map{height:65vh;}
input,button{padding:10px;margin:5px;}
</style>
</head>

<body>

<h2>🌐 Tracker Pro Max</h2>

<input id="email" placeholder="Email">
<input id="pass" type="password" placeholder="Password">
<button onclick="register()">REGISTER</button>
<button onclick="login()">LOGIN</button>

<div id="map"></div>

<button onclick="sos()">🚨 SOS</button>

<p id="status"></p>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js"></script>

<script>

// 🔥 GANTI DENGAN CONFIG FIREBASE KAMU
const firebaseConfig = {
  apiKey: "ISI",
  authDomain: "ISI",
  databaseURL: "ISI"
};

firebase.initializeApp(firebaseConfig);

const auth = firebase.auth();
const db = firebase.database();

let userId="";
let map=L.map('map').setView([-6.2,106.8],13);
let markers={};
let lines={};

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// 🔐 REGISTER
function register(){
let email=emailEl.value;
let pass=passEl.value;

auth.createUserWithEmailAndPassword(email,pass)
.then(()=>status("Register berhasil"))
.catch(e=>alert(e.message));
}

// 🔐 LOGIN
function login(){
let email=emailEl.value;
let pass=passEl.value;

auth.signInWithEmailAndPassword(email,pass)
.then(user=>{
userId=user.user.uid;
status("Login berhasil");
startTracking();
})
.catch(e=>alert(e.message));
}

// 📍 AUTO TRACKING
function startTracking(){
navigator.geolocation.watchPosition(pos=>{

let lat=pos.coords.latitude;
let lng=pos.coords.longitude;

cekZona(lat,lng);

db.ref("users/"+userId).set({
lat,lng,
sos:false,
lastUpdate:Date.now()
});

db.ref("history/"+userId).push({lat,lng});

},{enableHighAccuracy:true});
}

// 🚧 GEOFENCE
const home={lat:-6.2,lng:106.8,radius:0.01};

function cekZona(lat,lng){
let jarak=Math.sqrt(
Math.pow(lat-home.lat,2)+Math.pow(lng-home.lng,2)
);

if(jarak>home.radius){
kirimNotif("⚠️ Keluar zona!");
}
}

// 🚨 SOS
function sos(){
db.ref("users/"+userId+"/sos").set(true);
kirimNotif("🚨 SOS terkirim!");
}

// 🔔 NOTIF
Notification.requestPermission();

function kirimNotif(pesan){
if(Notification.permission==="granted"){
new Notification(pesan);
}
}

// 👥 UPDATE USER REALTIME
db.ref("users").on("value",snap=>{
let data=snap.val();

for(let u in data){
let d=data[u];

if(markers[u]){
markers[u].setLatLng([d.lat,d.lng]);
}else{
markers[u]=L.marker([d.lat,d.lng]).addTo(map);
}

// notif SOS
if(d.sos){
kirimNotif("🚨 Ada user SOS!");
}
}
});

// 🧭 HISTORY
db.ref("history").on("value",snap=>{
let data=snap.val();

for(let u in data){
let coords=[];

for(let k in data[u]){
coords.push([data[u][k].lat,data[u][k].lng]);
}

if(lines[u]){
lines[u].setLatLngs(coords);
}else{
lines[u]=L.polyline(coords).addTo(map);
}
}
});

// 🟢 ONLINE/OFFLINE
setInterval(()=>{
db.ref("users").once("value",snap=>{
let data=snap.val();

for(let u in data){
let last=data[u].lastUpdate;

if(Date.now()-last>10000){
console.log(u+" OFFLINE");
}
}
});
},5000);

// UI helper
const emailEl=document.getElementById("email");
const passEl=document.getElementById("pass");

function status(t){
document.getElementById("status").innerText=t;
}

</script>
</body>
</html>
