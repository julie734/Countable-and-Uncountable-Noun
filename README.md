<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Spelling Catch Game</title>

<style>
body {
    margin: 0;
    font-family: Arial;
    text-align: center;
    background: linear-gradient(#ffe0b2, #ffcc80);
}

h1 {
    margin: 10px;
}

#gameArea {
    position: relative;
    width: 100%;
    height: 70vh;
    background: #fff3e0;
    overflow: hidden;
    border-top: 4px solid #ff9800;
}

/* CHARACTER */
#player {
    position: absolute;
    bottom: 10px;
    left: 50%;
    width: 60px;
    height: 60px;
    background: #2196f3;
    border-radius: 50%;
}

/* LETTERS */
.letter {
    position: absolute;
    font-size: 26px;
    font-weight: bold;
    color: #e65100;
}

#wordBox {
    font-size: 28px;
    margin: 10px;
    letter-spacing: 10px;
}

#info {
    font-size: 18px;
}

button {
    padding: 10px 20px;
    background: #ff9800;
    color: white;
    border: none;
    cursor: pointer;
}
</style>
</head>

<body>

<h1>🎮 Catch & Spell!</h1>

<div id="info">
Word: <span id="hint"></span> |
Time: <span id="time">30</span> |
Score: <span id="score">0</span>
</div>

<div id="wordBox"></div>

<button onclick="startGame()">Start Game</button>

<div id="gameArea">
    <div id="player"></div>
</div>

<script>
const words = ["clerk","juice","anything","treat","hot","dollar","togo"];
let currentWord = "";
let collected = "";
let score = 0;
let time = 30;

let gameInterval;
let dropInterval;

const player = document.getElementById("player");
let playerX = window.innerWidth / 2;

function startGame() {
    score = 0;
    time = 30;
    document.getElementById("score").innerText = score;
    document.getElementById("time").innerText = time;

    nextWord();

    clearInterval(gameInterval);
    clearInterval(dropInterval);

    gameInterval = setInterval(() => {
        time--;
        document.getElementById("time").innerText = time;
        if (time <= 0) endGame();
    }, 1000);

    dropInterval = setInterval(dropLetter, 800);
}

function nextWord() {
    currentWord = words[Math.floor(Math.random() * words.length)];
    collected = "";
    document.getElementById("hint").innerText = currentWord[0] + " _ _ _";
    updateWordBox();
}

function updateWordBox() {
    document.getElementById("wordBox").innerText = collected;
}

function dropLetter() {
    const gameArea = document.getElementById("gameArea");
    const letter = document.createElement("div");
    letter.classList.add("letter");

    const randomLetter = currentWord[Math.floor(Math.random() * currentWord.length)];
    letter.innerText = randomLetter;

    letter.style.left = Math.random() * (window.innerWidth - 30) + "px";
    letter.style.top = "0px";

    gameArea.appendChild(letter);

    let fall = setInterval(() => {
        letter.style.top = (letter.offsetTop + 5) + "px";

        // COLLISION DETECTION
        if (isColliding(letter, player)) {
            collected += randomLetter;
            updateWordBox();
            letter.remove();
            clearInterval(fall);

            if (collected === currentWord) {
                score += 10;
                document.getElementById("score").innerText = score;
                nextWord();
            }
        }

        if (letter.offsetTop > gameArea.clientHeight) {
            letter.remove();
            clearInterval(fall);
        }

    }, 30);
}

// COLLISION FUNCTION
function isColliding(a, b) {
    const aRect = a.getBoundingClientRect();
    const bRect = b.getBoundingClientRect();

    return !(
        aRect.top > bRect.bottom ||
        aRect.bottom < bRect.top ||
        aRect.right < bRect.left ||
        aRect.left > bRect.right
    );
}

// MOVE CHARACTER
document.addEventListener("keydown", (e) => {
    if (e.key === "ArrowLeft") {
        playerX -= 20;
    } else if (e.key === "ArrowRight") {
        playerX += 20;
    }

    player.style.left = playerX + "px";
});

function endGame() {
    clearInterval(gameInterval);
    clearInterval(dropInterval);
    alert("Game Over! Score: " + score);
}
</script>

</body>
</html>
