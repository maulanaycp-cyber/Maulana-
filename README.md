
<html>
<head>
<meta charset="UTF-8">
<title>Track by Number (Legal)</title>

<style>
body{font-family:Arial;text-align:center;padding:20px;}
input,button{padding:10px;margin:5px;}
</style>
</head>

<body>

<h2>📍 Lacak Lokasi</h2>

<input id="nomor" placeholder="Masukkan nomor (08xxxx)">
<button onclick="kirim()">KIRIM LINK TRACKING</button>

<p id="status"></p>

<script>

// buat link tracking unik
function buatLink(){
let id = Math.random().toString(36).substr(2,9);
return location.origin + "/tracker.html#" + id;
}

function kirim(){
let nomor = document.getElementById("nomor").value;

if(!nomor){
alert("Masukkan nomor dulu!");
return;
}

let link = buatLink();

// buka WhatsApp otomatis
let wa = "https://wa.me/"+nomor+"?text=Klik link ini untuk share lokasi kamu: "+link;

window.open(wa);

document.getElementById("status").innerText =
"Link tracking dikirim ke "+nomor;
}

</script>

</body>
</html>
