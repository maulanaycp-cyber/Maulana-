
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Slot Mahjong Ultra HD</title>

<style>
body{
background:radial-gradient(circle,#0b3d2e,#000);
color:white;
text-align:center;
font-family:Arial;
}

h1{color:gold;}

#game{
background:#145a32;
padding:20px;
border-radius:20px;
display:inline-block;
box-shadow:0 0 20px #000;
}

.reel{
display:inline-block;
width:70px;
height:90px;
margin:5px;
border-radius:12px;
font-size:28px;
line-height:90px;
background:white;
color:black;
transition:0.2s;
}

.spin{animation:spin 0.1s infinite;}
@keyframes spin{
0%{transform:translateY(-10px);}
50%{transform:translateY(10px);}
100%{transform:translateY(-10px);}
}

.win{box-shadow:0 0 20px gold; transform:scale(1.1);}
.wild{background:gold;}
.scatter{background:purple;color:white;}

#spinBtn{
width:80px;height:80px;
border-radius:50%;
background:gold;
font-size:18px;
border:none;
margin-top:10px;
}

#info{
background:red;
padding:10px;
border-radius:10px;
margin-top:10px;
}

</style>
</head>

<body>

<h1>🎰 SLOT ULTRA HD</h1>

<div id="game">
<div id="reels"></div>

<div id="info">MENANG: 0</div>

<button id="spinBtn" onclick="spin()">SPIN</button>
<button onclick="autoSpin()">AUTO</button>
<button onclick="stopAuto()">STOP</button>
<button onclick="turbo()">TURBO</button>
</div>

<p>💰 Saldo: <span id="coin"></span></p>
<p>🎁 Free Spin: <span id="free"></span></p>
<p>🏆 Level: <span id="level"></span> | XP: <span id="xp"></span></p>

<audio id="spinS" src="https://www.soundjay.com/button/sounds/button-16.mp3"></audio>
<audio id="winS" src="https://www.soundjay.com/button/sounds/button-3.mp3"></audio>

<script>

const simbol=["一","二","三","四","五","六"];
const wild="WILD";
const scatter="SCATTER";

let coin=+localStorage.coin||500;
let free=+localStorage.free||0;
let level=+localStorage.level||1;
let xp=+localStorage.xp||0;

let auto=null;
let speed=1000;

function save(){
localStorage.coin=coin;
localStorage.free=free;
localStorage.level=level;
localStorage.xp=xp;
}

function init(){
let r=document.getElementById("reels");
r.innerHTML="";
for(let i=0;i<5;i++){
let d=document.createElement("div");
d.className="reel";
d.id="r"+i;
d.innerText="❓";
r.appendChild(d);
}
update();
}

function update(){
document.getElementById("coin").innerText=coin;
document.getElementById("free").innerText=free;
document.getElementById("level").innerText=level;
document.getElementById("xp").innerText=xp;
}

function rand(){
let x=Math.random();
if(x<0.08)return wild;
if(x<0.18)return scatter;
return simbol[Math.floor(Math.random()*simbol.length)];
}

function spin(){

let bet=10;

if(coin<bet && free===0){
info("Saldo habis!");
return;
}

spinS.play();

if(free>0){free--;} else {coin-=bet;}

let hasil=[];
let scat=0;

for(let i=0;i<5;i++){
let el=document.getElementById("r"+i);
el.classList.add("spin");

setTimeout(()=>{
let s=rand();
hasil[i]=s;

el.className="reel";

if(s===wild){
el.classList.add("wild");
el.innerText="🃏";
}
else if(s===scatter){
el.classList.add("scatter");
el.innerText="🎁";
scat++;
}
else{
el.innerText=s;
}

if(i===4){
cek(hasil,scat,bet);
save();
}
},i*150);
}
}

function cek(h,scat,bet){

let total=0;

if(scat>=3){
free+=10;
info("🎁 FREE SPIN +10");
}

for(let start=0;start<3;start++){
let base=h[start];
let count=1;

for(let i=start+1;i<5;i++){
if(h[i]===base || h[i]==="WILD"){
count++;
}else break;
}

if(base!=="SCATTER" && count>=3){
total+=bet*count*2;

for(let j=0;j<count;j++){
document.getElementById("r"+(start+j)).classList.add("win");
}
}
}

if(total>0){

if(Math.random()<0.03){
total*=5;
info("💎 MEGA JACKPOT "+total);
}else{
info("🎉 MENANG "+total);
}

coin+=total;
xp+=total;

if(xp>100){
level++;
xp=0;
info("🏆 LEVEL UP!");
}

winS.play();
}

update();
}

function autoSpin(){
if(auto)return;
auto=setInterval(spin,speed);
}

function stopAuto(){
clearInterval(auto);
auto=null;
}

function turbo(){
speed=400;
info("⚡ TURBO ON");
}

function info(t){
document.getElementById("info").innerText=t;
}

init();

</script>

</body>
</html>
