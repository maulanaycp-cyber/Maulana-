
<html>
<head>
<meta charset="UTF-8">
<title>Live Share Location</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>

<style>
body{margin:0;font-family:Arial;text-align:center;}
#map{height:75vh;}
button,input{padding:10px;margin:5px;}
</style>
</head>

<body>

<h2>📍 Share Lokasi Real-Time</h2>

<div id="map"></div>

<button onclick="start()">START</button>
<button onclick="stop()">STOP</button>

<br>
<input id="link" placeholder="Link share muncul di sini" style="width:80%">

<p id="status">Belum aktif</p>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js"></script>

<script>

// 🔥 CONFIG FIREBASE (ISI SENDIRI)
const firebaseConfig = {
  apiKey: "ISI",
  authDomain: "ISI",
  databaseURL: "ISI"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// ambil ID unik dari URL
let id = location.hash.replace("#","");

if(!id){
    id = Math.random().toString(36).substr(2,9);
    location.hash = id;
}

// tampilkan link
document.getElementById("link").value = location.href;

let map = L.map('map').setView([-6.2,106.8],13);
let marker;
let watchId;

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// START TRACKING
function start(){
watchId = navigator.geolocation.watchPosition(pos=>{

let lat = pos.coords.latitude;
let lng = pos.coords.longitude;

// kirim ke database
db.ref("share/"+id).set({lat,lng});

// tampilkan di map sendiri
updateMarker(lat,lng);

}, err=>{
alert("Izin lokasi ditolak!");
});
}

// STOP
function stop(){
navigator.geolocation.clearWatch(watchId);
}

// UPDATE MARKER
function updateMarker(lat,lng){
if(marker){
marker.setLatLng([lat,lng]);
}else{
marker = L.marker([lat,lng]).addTo(map);
}
map.setView([lat,lng],16);
}

// LIHAT DATA (buat orang lain)
db.ref("share/"+id).on("value",snap=>{
let data = snap.val();

if(data){
updateMarker(data.lat,data.lng);

document.getElementById("status").innerText =
"📍 "+data.lat+" , "+data.lng;
}
});

</script>

</body>
</html>
