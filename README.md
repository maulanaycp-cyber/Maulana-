
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Slot Ultra</title>

<style>
body {
    background: radial-gradient(circle,#222,#000);
    color:white;
    text-align:center;
    font-family:Arial;
}

h1 { color: gold; }

.reel {
    display:inline-block;
    width:80px;
    height:80px;
    border:3px solid gold;
    border-radius:10px;
    font-size:40px;
    line-height:80px;
    margin:5px;
    background:black;
}

.spin { animation:spin 0.1s infinite; }

@keyframes spin {
    0%{transform:translateY(-5px);}
    50%{transform:translateY(5px);}
    100%{transform:translateY(-5px);}
}

button,select {
    padding:10px;
    margin:5px;
    border-radius:8px;
}
</style>
</head>

<body>

<h1>🎰 SLOT ULTRA</h1>

<div id="slot"></div>

<button onclick="spin()">SPIN</button>
<button onclick="autoSpin()">AUTO</button>
<button onclick="stopAuto()">STOP</button>
<button onclick="turbo()">TURBO</button>

<br><br>

BET:
<select id="bet">
<option value="5">5</option>
<option value="10">10</option>
<option value="20">20</option>
<option value="50">50</option>
</select>

<p>💰 Saldo: <span id="coin"></span></p>
<p>🎁 Free Spin: <span id="free"></span></p>
<p>🏆 Level: <span id="level"></span> | XP: <span id="xp"></span></p>

<p id="info"></p>

<audio id="spinS" src="https://www.soundjay.com/button/sounds/button-16.mp3"></audio>
<audio id="winS" src="https://www.soundjay.com/button/sounds/button-3.mp3"></audio>

<script>

const normal=["🍒","🍋","🍉","⭐","🔔","💎"];
const wild="🃏";
const scatter="🎁";

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
let slot=document.getElementById("slot");
slot.innerHTML="";
for(let i=0;i<5;i++){
let d=document.createElement("div");
d.className="reel";
d.id="r"+i;
d.innerText="❓";
slot.appendChild(d);
}
update();
}

function update(){
coin=Math.max(coin,0);
document.getElementById("coin").innerText=coin;
document.getElementById("free").innerText=free;
document.getElementById("level").innerText=level;
document.getElementById("xp").innerText=xp;
}

function rand(){
let r=Math.random();
if(r<0.08) return wild;
if(r<0.18) return scatter;
return normal[Math.floor(Math.random()*normal.length)];
}

function spin(){

let bet=+betEl.value;

if(coin<bet && free===0){
info("Saldo habis!");
return;
}

spinS.play();

if(free>0){ free--; } else { coin-=bet; }

let hasil=[];
let scat=0;

for(let i=0;i<5;i++){
let el=r(i);
el.classList.add("spin");

setTimeout(()=>{
let s=rand();
hasil[i]=s;
if(s===scatter) scat++;

el.classList.remove("spin");
el.innerText=s;

if(i===4){
hitung(hasil,bet,scat);
save();
}
},i*150);
}
}

function hitung(h,bet,scat){

if(scat>=3){
free+=10;
info("🎁 FREE SPIN +10");
}

let totalWin=0;

// multi payline simple (cek dari kiri)
for(let start=0;start<3;start++){
let base=h[start];
let count=1;

for(let i=start+1;i<5;i++){
if(h[i]===base || h[i]===wild){
count++;
}else break;
}

if(base!==scatter && count>=3){
let win=bet*count*2;
totalWin+=win;
}
}

if(totalWin>0){

// jackpot chance
if(Math.random()<0.03){
totalWin*=5;
info("💎 MEGA JACKPOT "+totalWin);
}else{
info("🎉 MENANG "+totalWin);
}

coin+=totalWin;
winS.play();

// XP naik
xp+=totalWin;
if(xp>100){
level++;
xp=0;
info("🏆 LEVEL UP!");
}
}

update();
}

function autoSpin(){
if(auto) return;
auto=setInterval(spin,speed);
}

function stopAuto(){
clearInterval(auto);
auto=null;
}

function turbo(){
speed=500;
info("⚡ TURBO ON");
}

function info(t){
document.getElementById("info").innerText=t;
}

function r(i){ return document.getElementById("r"+i); }
const betEl=document.getElementById("bet");
const spinS=document.getElementById("spinS");
const winS=document.getElementById("winS");

init();

</script>

</body>
</html>
