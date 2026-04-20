<!DOCTYPE html>
<html>
<head>
<title>Otaku Tiers</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{margin:0;font-family:Arial;background:#0b0f1a;color:white;max-width:420px;margin:auto}
.header{display:flex;justify-content:space-between;align-items:center;padding:10px}
.logo{font-weight:bold;color:#ff4d4d}
.tabs{display:flex;overflow-x:auto;gap:8px;padding:5px}
.tab{padding:6px 10px;background:#1a2133;border-radius:6px;white-space:nowrap;cursor:pointer}
.active{background:#ff4d4d}
.search input{width:90%;margin:10px auto;display:block;padding:8px;border-radius:8px;border:none;background:#1a2133;color:white}
.card{background:#141a2b;margin:10px;padding:10px;border-radius:10px}
.player{display:flex;gap:10px;align-items:center}
.player img{width:40px;border-radius:6px}
.rank{font-size:12px;color:#aaa}
.top1{border:2px solid gold}.top2{border:2px solid silver}.top3{border:2px solid #cd7f32}
.popup{position:fixed;top:0;left:0;width:100%;height:100%;background:#000c;display:none;justify-content:center;align-items:center}
.popupBox{background:#141a2b;padding:15px;border-radius:10px;width:80%}
.adminPage{display:none;padding:10px}
button{padding:8px;border:none;border-radius:8px;background:#ff4d4d;color:white;margin-top:5px;width:100%}
</style>
</head>

<body>

<!-- HEADER -->
<div class="header">
  <h2>🏆 OTAKU TIERS</h2>
  <div class="logo">MC</div>
</div>

<!-- GAMEMODES -->
<div class="tabs" id="tabs"></div>

<!-- SEARCH -->
<div class="search">
<input id="search" placeholder="Search player...">
</div>

<!-- LIST -->
<div id="list"></div>

<!-- ADMIN PAGE -->
<div class="adminPage" id="adminPage">
  <h3>Admin Panel</h3>
  <input id="pass" placeholder="Enter password">
  <button onclick="login()">Login</button>

  <div id="adminTools" style="display:none">
    <input id="name" placeholder="Player name">
    <input id="region" placeholder="Region">
    <input id="mode" placeholder="Gamemode (Crystal/Sword/etc)">
    <input id="tier" placeholder="Tier (HT1/LT1)">
    <button onclick="addPlayer()">Add Player</button>
  </div>
</div>

<!-- PROFILE -->
<div class="popup" id="popup">
  <div class="popupBox" id="profile"></div>
</div>

<script>

const ADMIN_PASS="OtakuTier$Prime";

let gamemodes=["Overall","Crystal","Sword","Axe","SMP","Mace"];
let currentMode="Overall";

let players=JSON.parse(localStorage.getItem("players"))||[
{name:"Ayaxis_Shark",region:"NA",modes:{Crystal:"HT1",Sword:"HT1"}},
{name:"NoobieChips",region:"NA",modes:{Crystal:"HT1",Axe:"LT1"}},
{name:"UnitedPlayzZ",region:"EU",modes:{Sword:"HT2",Crystal:"HT1"}}
];

// SCORE SYSTEM
function getPoints(t){
if(t=="HT1") return 40;
if(t=="HT2") return 30;
if(t=="LT1") return 15;
return 0;
}

// CALC SCORE
function calc(p){
let s=0;
Object.values(p.modes).forEach(t=>s+=getPoints(t));
return s;
}

// RANK
function rank(s){
if(s>=200)return"Grandmaster";
if(s>=150)return"Master";
if(s>=100)return"Ace";
if(s>=60)return"Specialist";
if(s>=30)return"Beginner";
return"Newbie";
}

// RENDER TABS
function renderTabs(){
let html="";
gamemodes.forEach(m=>{
html+=`<div class="tab ${m==currentMode?"active":""}" onclick="switchMode('${m}')">${m}</div>`;
});
document.getElementById("tabs").innerHTML=html;
}
renderTabs();

// SWITCH MODE
function switchMode(m){
currentMode=m;
render();
renderTabs();
}

// RENDER PLAYERS
function render(){
let list=players.map(p=>{
let score=calc(p);
return {...p,score};
}).sort((a,b)=>b.score-a.score);

let html="";
list.forEach((p,i)=>{
if(currentMode!="Overall" && !p.modes[currentMode]) return;

let top=i==0?"top1":i==1?"top2":i==2?"top3":"";

html+=`
<div class="card ${top}" onclick="openProfile('${p.name}')">
<div class="player">
<img src="https://mc-heads.net/avatar/${p.name}">
<div>
<b>#${i+1} ${p.name}</b><br>
<span class="rank">${p.region} • ${rank(p.score)} • ${p.score}</span>
</div>
</div>
</div>`;
});

document.getElementById("list").innerHTML=html;
}
render();

// SEARCH
document.getElementById("search").onkeyup=function(){
let v=this.value.toLowerCase();
document.querySelectorAll(".card").forEach(c=>{
c.style.display=c.innerText.toLowerCase().includes(v)?"block":"none";
});
};

// PROFILE
function openProfile(name){
let p=players.find(x=>x.name==name);
let html=`<h3>${p.name}</h3><p>${p.region}</p><p>${rank(calc(p))}</p>`;
Object.entries(p.modes).forEach(([m,t])=>{
html+=`<p>${m}: ${t}</p>`;
});
html+=`<button onclick="closePop()">Close</button>`;
document.getElementById("profile").innerHTML=html;
document.getElementById("popup").style.display="flex";
}
function closePop(){document.getElementById("popup").style.display="none";}

// ADMIN
function login(){
if(document.getElementById("pass").value==ADMIN_PASS){
document.getElementById("adminTools").style.display="block";
}
}

// ADD PLAYER
function addPlayer(){
let name=document.getElementById("name").value;
let region=document.getElementById("region").value;
let mode=document.getElementById("mode").value;
let tier=document.getElementById("tier").value;

let p=players.find(x=>x.name==name);
if(!p){
p={name,region,modes:{}};
players.push(p);
}
p.modes[mode]=tier;

localStorage.setItem("players",JSON.stringify(players));
render();
}

// OPEN ADMIN PAGE (type in URL #admin)
if(location.hash=="#admin"){
document.getElementById("adminPage").style.display="block";
document.getElementById("list").style.display="none";
}

</script>

</body>
</html>
