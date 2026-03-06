<!DOCTYPE html>
<html lang="no">
<head>
<meta charset="UTF-8">
<title>Matte Spill – Skriv navn først</title>
<style>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(to bottom right, #a1c4fd, #c2e9fb);
  margin: 0;
  display: flex;
  height: 100vh;
}

#sidebar {
  width: 250px;
  background: #ffffffcc;
  padding: 20px;
  box-shadow: 5px 0 15px rgba(0,0,0,0.2);
  overflow-y: auto;
}

#sidebar h2 {
  text-align: center;
  color: #1e3799;
}

#board-list {
  list-style: none;
  padding-left: 0;
}

#board-list li {
  font-size: 18px;
  margin: 5px 0;
  color: #0a3d62;
}

#game-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding-top: 50px;
}

h1 {
  font-size: 48px;
  color: #0a3d62;
  text-shadow: 2px 2px #d6e6ff;
  margin-bottom: 5px;
}

#name-prompt {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#name-prompt input {
  font-size: 24px;
  padding: 8px;
  margin-top: 10px;
  border-radius: 8px;
  border: 2px solid #1e3799;
}

#name-prompt button {
  font-size: 20px;
  padding: 6px 12px;
  margin-top: 10px;
  border-radius: 8px;
  border: none;
  background-color: #1e3799;
  color: white;
  cursor: pointer;
}

#round, #score {
  font-size: 24px;
  color: #1e3799;
  margin-bottom: 10px;
}

#question-box {
  background: #ffffffaa;
  padding: 40px 60px;
  border-radius: 15px;
  box-shadow: 0 10px 20px rgba(0,0,0,0.2);
  font-size: 36px;
  margin-bottom: 20px;
  min-width: 200px;
  text-align: center;
}

input.answer {
  font-size: 28px;
  padding: 10px 15px;
  width: 150px;
  text-align: center;
  border: 2px solid #1e3799;
  border-radius: 10px;
  outline: none;
  transition: 0.2s;
}

input.answer:focus {
  border-color: #3c40c6;
  box-shadow: 0 0 10px #3c40c6aa;
}

#result {
  font-size: 24px;
  height: 30px;
  margin-top: 20px;
  color: #0a3d62;
  font-weight: bold;
}
</style>
</head>
<body>

<div id="sidebar">
  <h2>Leaderboard</h2>
  <ol id="board-list"></ol>
</div>

<div id="game-area">
  <h1>🎮 Matte Spill</h1>

  <div id="name-prompt">
    <label for="player-name">Skriv inn navnet ditt:</label>
    <input type="text" id="player-name" placeholder="Navn">
    <button onclick="startGame()">Start Spill</button>
  </div>

  <div id="round" style="display:none;">Runde: 1</div>
  <div id="score" style="display:none;">Poeng: 0</div>
  <div id="question-box" style="display:none;"></div>
  <input type="number" id="answer" class="answer" placeholder="Skriv svaret" style="display:none;">
  <div id="result"></div>
</div>

<script>
let round = 1;
let score = 0;
let num1, num2, correct;
let maxMultiplier = 5;
let gameOver = false;
let playerName = "";

// Leaderboard fra localStorage
let leaderboard = JSON.parse(localStorage.getItem("mathLeaderboard")) || [];

function renderLeaderboard() {
  leaderboard.sort((a,b)=>b.score-a.score);
  if(leaderboard.length>10) leaderboard = leaderboard.slice(0,10);
  let listHTML = "";
  for(let p of leaderboard){
    listHTML += `<li>${p.name}: ${p.score} poeng</li>`;
  }
  document.getElementById("board-list").innerHTML = listHTML;
}

function newQuestion(){
  if(gameOver) return;
  if(round <= 20){
    num1 = Math.floor(Math.random()*5)+1;
    num2 = Math.floor(Math.random()*5)+1;
    correct = num1 + num2;
    document.getElementById("question-box").textContent = `${num1} + ${num2} = ?`;
  } else {
    num1 = Math.floor(Math.random()*maxMultiplier)+1;
    num2 = Math.floor(Math.random()*maxMultiplier)+1;
    correct = num1 * num2;
    document.getElementById("question-box").textContent = `${num1} × ${num2} = ?`;
  }
  document.getElementById("round").textContent = `Runde: ${round}`;
  document.getElementById("score").textContent = `Poeng: ${score}`;
  document.getElementById("answer").value = "";
  document.getElementById("answer").focus();
}

function saveScore(){
  let existing = leaderboard.findIndex(p=>p.name===playerName);
  if(existing>-1){
    if(score>leaderboard[existing].score) leaderboard[existing].score = score;
  } else {
    leaderboard.push({name: playerName, score: score});
  }
  localStorage.setItem("mathLeaderboard", JSON.stringify(leaderboard));
  renderLeaderboard();
}

function restartGame(){
  round = 1;
  score = 0;
  maxMultiplier = 5;
  gameOver = false;
  newQuestion();
}

function startGame(){
  playerName = document.getElementById("player-name").value.trim();
  if(!playerName) playerName = "Anon";
  document.getElementById("name-prompt").style.display = "none";
  document.getElementById("round").style.display = "block";
  document.getElementById("score").style.display = "block";
  document.getElementById("question-box").style.display = "block";
  document.getElementById("answer").style.display = "block";
  newQuestion();
}

document.getElementById("answer").addEventListener("input", function(){
  if(gameOver) return;
  let user = Number(this.value);
  if(user===correct){
    score++;
    round++;
    if(round>20 && score%5===0) maxMultiplier = Math.min(maxMultiplier+1,12);
    document.getElementById("result").textContent = "✅ Riktig!";
    setTimeout(()=>{
      document.getElementById("result").textContent = "";
      newQuestion();
    },500);
  } else if(this.value!==""){
    gameOver = true;
    document.getElementById("result").textContent = `❌ Feil! Spillet er over, poeng: ${score}`;
    saveScore();
    setTimeout(()=>{
      restartGame();
      document.getElementById("result").textContent = "";
    },1500);
  }
});

renderLeaderboard();
</script>
</body>
</html>
