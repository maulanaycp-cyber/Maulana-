
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Slot Real Grid</title>

<style>
body{
background:#0b3d2e;
text-align:center;
font-family:Arial;
color:white;
}

#game{
background:#2ecc71;
padding:15px;
border-radius:15px;
display:inline-block;
}

.grid{
display:grid;
grid-template-columns: repeat(5, 60px);
grid-gap:5px;
margin-bottom:10px;
}

.tile{
width:60px;
height:70px;
background:white;
border-radius:10px;
font-size:22px;
display:flex;
align-items:center;
justify-content:center;
color:black;
}

.wild{background:gold;}
.scatter{background:purple;color:white;}

.drop{
animation:drop 0.3s ease;
}

@keyframes drop{
from{transform:translateY(-80px);}
to{transform:translateY(0);}
}

#win{
background:red;
padding:10px;
border-radius:10px;
margin-top:10px;
font-weight:bold;
}

#spinBtn{
position:relative;
margin-top:10px;
width:70px;
height:70px;
border-radius:50%;
background:gold;
border:none;
font-size:18px;
}

</style>
</head>

<body>

<h2>🎰 SLOT REAL GRID</h2>

<div id="game">
<div id="grid" class="grid"></div>

<div id="win">MENANG 0</div>

<button id="spinBtn" onclick="spin()">SPIN</button>
</div>

<p>💰 Saldo: <span id="coin">100</span></p>

<script>

const simbol=["一","二","三","四","五","六"];
const wild="WILD";
const scatter="SCATTER";

let coin=100;

function init(){
let grid=document.getElementById("grid");
grid.innerHTML="";

for(let i=0;i<15;i++){
let d=document.createElement("div");
d.className="tile";
d.id="t"+i;
d.innerText="❓";
grid.appendChild(d);
}
}

function rand(){
let r=Math.random();
if(r<0.1)return wild;
if(r<0.2)return scatter;
return simbol[Math.floor(Math.random()*simbol.length)];
}

function spin(){

if(coin<=0){
alert("Saldo habis!");
return;
}

coin-=5;

let hasil=[];
let win=0;

for(let col=0;col<5;col++){

for(let row=0;row<3;row++){

let i = col + row*5;

setTimeout(()=>{
let s=rand();
hasil[i]=s;

let el=document.getElementById("t"+i);
el.className="tile drop";

if(s===wild){
el.classList.add("wild");
el.innerText="🃏";
}
else if(s===scatter){
el.classList.add("scatter");
el.innerText="🎁";
}
else{
el.innerText=s;
}

}, col*200); // delay per kolom
}
}

// cek menang simple
setTimeout(()=>{

if(hasil[0]===hasil[1] && hasil[1]===hasil[2]){
win=30;
}

coin+=win;

document.getElementById("coin").innerText=coin;
document.getElementById("win").innerText="MENANG "+win;

},1200);
}

init();

</script>

</body>
</html>
