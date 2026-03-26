
<html>
<head>
<meta charset="UTF-8">
<title>Live Tracking</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>

<style>
body{margin:0;font-family:Arial;text-align:center;}
#map{height:80vh;}
</style>
</head>

<body>

<h2>📍 Live Tracking</h2>
<div id="map"></div>
<p id="status">Memuat...</p>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js"></script>

<script>

// 🔥 ISI CONFIG FIREBASE
const firebaseConfig = {
  apiKey: "ISI",
  authDomain: "ISI",
  databaseURL: "ISI"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// ambil ID dari URL
let id = location.hash.replace("#","");
if(!id){
  id = Math.random().toString(36).substr(2,9);
  location.hash = id;
}

// MAP
let map = L.map('map').setView([-6.2,106.8],13);
let marker;

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png')
.addTo(map);

// AUTO TRACKING (langsung jalan tanpa tombol)
if(navigator.geolocation){
  navigator.geolocation.watchPosition(pos=>{

    let lat = pos.coords.latitude;
    let lng = pos.coords.longitude;

    // kirim ke database
    db.ref("live/"+id).set({lat,lng});

    updateMap(lat,lng);

    document.getElementById("status").innerText =
      "📍 "+lat+" , "+lng;

  }, err=>{
    alert("Izin lokasi diperlukan!");
  },{
    enableHighAccuracy:true
  });
}

// update marker
function updateMap(lat,lng){
  if(marker){
    marker.setLatLng([lat,lng]);
  }else{
    marker = L.marker([lat,lng]).addTo(map);
  }
  map.setView([lat,lng],16);
}

// lihat lokasi (viewer)
db.ref("live/"+id).on("value",snap=>{
  let d = snap.val();
  if(d){
    updateMap(d.lat,d.lng);
  }
});

</script>

</body>
</html>
