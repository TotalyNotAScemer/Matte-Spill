<!DOCTYPE html>
<html lang="no">
<head>
<meta charset="UTF-8">
<title>Matte Spill</title>

<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js"></script>

<style>

body{
font-family:Arial;
background:linear-gradient(135deg,#4facfe,#00f2fe);
height:100vh;
display:flex;
justify-content:center;
align-items:center;
gap:30px;
margin:0;
}

#start,#game,#leaderboard{
background:white;
padding:30px;
border-radius:15px;
box-shadow:0 10px 25px rgba(0,0,0,0.2);
}

#game{
display:none;
text-align:center;
width:300px;
}

#leaderboard{
display:none;
width:200px;
text-align:center;
}

#oppgave{
font-size:28px;
margin:20px 0;
}

input{
padding:10px;
font-size:18px;
width:150px;
margin:5px;
}

</style>
</head>

<body>

<div id="start">

<h2>Start Spill</h2>

<input id="klasse" placeholder="Klasse"><br>
<input id="navn" placeholder="Navn"><br>

<button onclick="start()">Start</button>

</div>

<div id="leaderboard">

<h3>🏫 Klasse Leaderboard</h3>

<p id="best"></p>

</div>

<div id="game">

<h1>🧠 Matte Spill</h1>

<p id="player"></p>

<div id="oppgave"></div>

<input id="svar" type="number">

<p>Runde: <span id="runde">1</span></p>

</div>

<script>

// LIM INN DIN FIREBASE CONFIG HER
const firebaseConfig = {
apiKey: "DIN_KEY",
authDomain: "DIN_APP.firebaseapp.com",
databaseURL: "https://DIN_APP-default-rtdb.firebaseio.com",
projectId: "DIN_APP",
storageBucket: "DIN_APP.appspot.com",
messagingSenderId: "123456",
appId: "APP_ID"
}

const app = firebase.initializeApp(firebaseConfig)
const db = firebase.database()

let klasse=""
let navn=""
let runde=1
let riktig=0

function start(){

klasse=document.getElementById("klasse").value.trim()
navn=document.getElementById("navn").value.trim()

if(!klasse||!navn){
alert("Skriv klasse og navn")
return
}

document.getElementById("start").style.display="none"
document.getElementById("game").style.display="block"
document.getElementById("leaderboard").style.display="block"

document.getElementById("player").innerText=klasse+" - "+navn

liveLeaderboard()

nyOppgave()

}

function nyOppgave(){

let a=Math.floor(Math.random()*5)+1
let b=Math.floor(Math.random()*5)+1

if(runde>20){

a=Math.floor(Math.random()*5)+1
b=Math.floor(Math.random()*5)+1

riktig=a*b

document.getElementById("oppgave").innerText=a+" x "+b

}else{

riktig=a+b

document.getElementById("oppgave").innerText=a+" + "+b

}

document.getElementById("svar").value=""

}

document.getElementById("svar").addEventListener("input",function(){

if(this.value==="") return

let svar=Number(this.value)

if(svar===riktig){

runde++
document.getElementById("runde").innerText=runde

}else{

lagreScore()

alert("Feil! Riktig var "+riktig)

runde=1
document.getElementById("runde").innerText=1

}

nyOppgave()

})

function lagreScore(){

db.ref("leaderboard/"+klasse+"/"+navn).set({
score:runde
})

}

function liveLeaderboard(){

db.ref("leaderboard/"+klasse).on("value",snap=>{

let data=snap.val()

if(!data) return

let bestName=""
let bestScore=0

for(let n in data){

if(data[n].score>bestScore){

bestScore=data[n].score
bestName=n

}

}

document.getElementById("best").innerText=
bestName+" - "+bestScore

})

}

</script>

</body>
</html>
