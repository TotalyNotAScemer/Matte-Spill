<!DOCTYPE html>
<html lang="no">
<head>
<meta charset="UTF-8">
<title>Matte Spill</title>

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

/* fjern piler */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button{
-webkit-appearance:none;
margin:0;
}

input[type=number]{
-moz-appearance:textfield;
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

<h3>🥇 Beste i klassen</h3>
<p id="best">Ingen score enda</p>

</div>

<div id="game">

<h1>🧠 Matte Spill</h1>

<p id="player"></p>

<div id="oppgave"></div>

<input id="svar" type="number" placeholder="Svar">

<p>Runde: <span id="runde">1</span></p>

</div>

<script>

let klasse=""
let navn=""
let runde=1
let riktig=0
let timer=null

function start(){

klasse=document.getElementById("klasse").value.trim()
navn=document.getElementById("navn").value.trim()

if(!klasse || !navn){
return
}

document.getElementById("start").style.display="none"
document.getElementById("game").style.display="block"
document.getElementById("leaderboard").style.display="block"

document.getElementById("player").innerText=klasse+" - "+navn

visScore()
nyOppgave()

}

function nyOppgave(){

let a
let b
let symbol="+"

if(runde<=5){
a=Math.floor(Math.random()*3)+1
b=Math.floor(Math.random()*3)+1
}
else if(runde<=10){
a=Math.floor(Math.random()*5)+1
b=Math.floor(Math.random()*5)+1
}
else if(runde<=20){
a=Math.floor(Math.random()*10)+1
b=Math.floor(Math.random()*10)+1
}
else{

symbol="x"

let max=5

a=Math.floor(Math.random()*max)+1
b=Math.floor(Math.random()*max)+1

}

if(symbol==="+") riktig=a+b
else riktig=a*b

document.getElementById("oppgave").innerText=a+" "+symbol+" "+b
document.getElementById("svar").value=""

}

document.getElementById("svar").addEventListener("input",function(){

clearTimeout(timer)

let felt=this

timer=setTimeout(function(){

if(felt.value==="") return

let svar=Number(felt.value)

if(svar===riktig){

runde++
document.getElementById("runde").innerText=runde

}else{

lagreScore()

runde=1
document.getElementById("runde").innerText=1

}

nyOppgave()

},400)

})

function lagreScore(){

let lagret=JSON.parse(localStorage.getItem("bestScore")) || {}

if(!lagret[klasse] || runde > lagret[klasse].score){

lagret[klasse]={
navn:navn,
score:runde
}

}

localStorage.setItem("bestScore",JSON.stringify(lagret))

visScore()

}

function visScore(){

let lagret=JSON.parse(localStorage.getItem("bestScore")) || {}

let best=lagret[klasse]

if(best){

document.getElementById("best").innerText=
best.navn+" - "+best.score

}else{

document.getElementById("best").innerText="Ingen score enda"

}

}

</script>

</body>
</html>
