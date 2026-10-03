<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Neon Catch</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    -webkit-tap-highlight-color: transparent;
}

body {
    overflow: hidden;
    font-family: Arial, sans-serif;
    background: #050816;
    color: white;
    touch-action: none;
}

#game {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    background:
        radial-gradient(circle at 50% 20%, #172554 0%, transparent 40%),
        linear-gradient(180deg, #020617, #08111f);
}

/* Background stars */

.star {
    position: absolute;
    width: 2px;
    height: 2px;
    background: white;
    border-radius: 50%;
    opacity: .6;
    animation: twinkle 2s infinite alternate;
}

@keyframes twinkle {
    from { opacity: .2; }
    to { opacity: .9; }
}

/* Header */

#hud {
    position: absolute;
    top: 18px;
    left: 15px;
    right: 15px;
    z-index: 10;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.stat {
    min-width: 85px;
    padding: 10px 14px;
    border-radius: 15px;
    background: rgba(15,23,42,.75);
    border: 1px solid rgba(255,255,255,.12);
    backdrop-filter: blur(10px);
    text-align: center;
    box-shadow: 0 8px 25px rgba(0,0,0,.25);
}

.stat small {
    display: block;
    color: #94a3b8;
    font-size: 10px;
    text-transform: uppercase;
    letter-spacing: 1px;
}

.stat strong {
    font-size: 20px;
}

#pauseBtn {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    border: 1px solid rgba(255,255,255,.15);
    background: rgba(15,23,42,.8);
    color: white;
    font-size: 18px;
}

/* Player */

#player {
    position: absolute;
    bottom: 35px;
    left: 50%;
    transform: translateX(-50%);
    width: 95px;
    height: 35px;
    border-radius: 50%;
    background: linear-gradient(90deg,#06b6d4,#8b5cf6,#ec4899);
    box-shadow:
        0 0 15px #06b6d4,
        0 0 35px #8b5cf6,
        0 12px 30px rgba(0,0,0,.5);
}

#player::before {
    content: "";
    position: absolute;
    width: 55px;
    height: 12px;
    left: 20px;
    top: -5px;
    border-radius: 50%;
    background: #fff;
    opacity: .7;
    filter: blur(7px);
}

/* Falling objects */

.object {
    position: absolute;
    width: 32px;
    height: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 23px;
    z-index: 5;
}

.gem {
    filter: drop-shadow(0 0 10px #22d3ee);
}

.bomb {
    filter: drop-shadow(0 0 10px #ef4444);
}

/* Combo */

#combo {
    position: absolute;
    top: 105px;
    left: 50%;
    transform: translateX(-50%);
    font-weight: bold;
    font-size: 18px;
    color: #facc15;
    text-shadow: 0 0 12px #facc15;
    opacity: 0;
    transition: .2s;
}

/* Start screen */

.overlay {
    position: absolute;
    inset: 0;
    z-index: 20;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 25px;
    background: rgba(2,6,23,.82);
    backdrop-filter: blur(12px);
}

.panel {
    width: min(390px,100%);
    padding: 35px 25px;
    text-align: center;
    border-radius: 28px;
    background: rgba(15,23,42,.9);
    border: 1px solid rgba(255,255,255,.12);
    box-shadow: 0 25px 80px rgba(0,0,0,.55);
}

.logo {
    font-size: 45px;
    margin-bottom: 8px;
}

h1 {
    font-size: 38px;
    background: linear-gradient(90deg,#22d3ee,#8b5cf6,#ec4899);
    -webkit-background-clip: text;
    color: transparent;
}

.subtitle {
    margin: 10px 0 25px;
    color: #94a3b8;
    line-height: 1.5;
}

button.main {
    width: 100%;
    padding: 15px;
    border: none;
    border-radius: 16px;
    font-size: 17px;
    font-weight: bold;
    color: white;
    background: linear-gradient(90deg,#06b6d4,#7c3aed);
    box-shadow: 0 10px 30px rgba(124,58,237,.3);
}

button.main:active {
    transform: scale(.97);
}

.instructions {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin: 20px 0;
}

.tip {
    padding: 12px;
    border-radius: 14px;
    background: rgba(255,255,255,.05);
    font-size: 12px;
    color: #cbd5e1;
}

.hidden {
    display: none !important;
}

/* Game over */

.finalScore {
    font-size: 48px;
    margin: 10px 0;
    color: #22d3ee;
}

.best {
    color: #94a3b8;
    margin-bottom: 25px;
}

/* Floating score */

.floatScore {
    position: absolute;
    color: #facc15;
    font-weight: bold;
    font-size: 18px;
    pointer-events: none;
    animation: floatUp .7s forwards;
    z-index: 30;
}

@keyframes floatUp {
    to {
        transform: translateY(-45px);
        opacity: 0;
    }
}
</style>
</head>

<body>

<div id="game">

    <div id="stars"></div>

    <div id="hud">
        <div class="stat">
            <small>Score</small>
            <strong id="score">0</strong>
        </div>

        <div class="stat">
            <small>Lives</small>
            <strong id="lives">❤️❤️❤️</strong>
        </div>

        <div class="stat">
            <small>Level</small>
            <strong id="level">1</strong>
        </div>

        <button id="pauseBtn">Ⅱ</button>
    </div>

    <div id="combo">COMBO x1</div>

    <div id="player"></div>

    <!-- Start Screen -->

    <div class="overlay" id="startScreen">
        <div class="panel">

            <div class="logo">💎</div>

            <h1>NEON CATCH</h1>

            <p class="subtitle">
                Catch the gems.<br>
                Avoid the bombs.<br>
                Build the biggest combo.
            </p>

            <div class="instructions">
                <div class="tip">💎 Catch gems<br>+10 points</div>
                <div class="tip">💣 Avoid bombs<br>-1 life</div>
                <div class="tip">🔥 Combos<br>More points</div>
                <div class="tip">⚡ Speed<br>Gets harder</div>
            </div>

            <button class="main" id="startBtn">
                START GAME
            </button>

        </div>
    </div>

    <!-- Game Over -->

    <div class="overlay hidden" id="gameOver">

        <div class="panel">

            <div class="logo">🏆</div>

            <h1>GAME OVER</h1>

            <div class="finalScore" id="finalScore">0</div>

            <div class="best">
                Best Score: <span id="bestScore">0</span>
            </div>

            <button class="main" id="restartBtn">
                PLAY AGAIN
            </button>

        </div>

    </div>

</div>

<script>

const game = document.getElementById("game");
const player = document.getElementById("player");

const scoreEl = document.getElementById("score");
const livesEl = document.getElementById("lives");
const levelEl = document.getElementById("level");
const comboEl = document.getElementById("combo");

const startScreen = document.getElementById("startScreen");
const gameOver = document.getElementById("gameOver");

const finalScore = document.getElementById("finalScore");
const bestScore = document.getElementById("bestScore");

const startBtn = document.getElementById("startBtn");
const restartBtn = document.getElementById("restartBtn");
const pauseBtn = document.getElementById("pauseBtn");

let score = 0;
let lives = 3;
let level = 1;
let combo = 0;

let running = false;
let paused = false;

let objects = [];
let spawnTimer;
let gameLoop;

let playerX = window.innerWidth / 2;

const best = Number(localStorage.getItem("neonCatchBest") || 0);
bestScore.textContent = best;


/* Stars */

for(let i = 0; i < 80; i++){

    const star = document.createElement("div");

    star.className = "star";

    star.style.left = Math.random() * 100 + "%";
    star.style.top = Math.random() * 100 + "%";

    star.style.animationDelay = Math.random() * 2 + "s";

    document.getElementById("stars").appendChild(star);
}


/* Sound */

function sound(type){

    try{

        const ctx = new (window.AudioContext || window.webkitAudioContext)();

        const osc = ctx.createOscillator();
        const gain = ctx.createGain();

        osc.connect(gain);
        gain.connect(ctx.destination);

        if(type === "catch"){
            osc.frequency.value = 700;
        }

        if(type === "bomb"){
            osc.frequency.value = 120;
        }

        if(type === "gameover"){
            osc.frequency.value = 80;
        }

        gain.gain.value = .06;

        osc.start();

        osc.stop(ctx.currentTime + .08);

    }catch(e){}

}


/* Start */

function startGame(){

    score = 0;
    lives = 3;
    level = 1;
    combo = 0;

    running = true;
    paused = false;

    scoreEl.textContent = score;
    levelEl.textContent = level;

    updateLives();

    startScreen.classList.add("hidden");
    gameOver.classList.add("hidden");

    clearObjects();

    clearInterval(spawnTimer);

    spawnTimer = setInterval(spawnObject, 650);

    requestAnimationFrame(loop);

}


/* Spawn */

function spawnObject(){

    if(!running || paused) return;

    const obj = document.createElement("div");

    const isBomb = Math.random() < Math.min(.18 + level * .015,.35);

    obj.className = "object " + (isBomb ? "bomb" : "gem");

    obj.innerHTML = isBomb ? "💣" : "💎";

    const x = Math.random() * (window.innerWidth - 40);

    obj.style.left = x + "px";
    obj.style.top = "-40px";

    game.appendChild(obj);

    objects.push({

        el: obj,
        x: x,
        y: -40,
        speed: 2.5 + Math.random() * 2 + level * .25,
        bomb: isBomb

    });

}


/* Main loop */

function loop(){

    if(!running) return;

    if(!paused){

        for(let i = objects.length - 1; i >= 0; i--){

            const o = objects[i];

            o.y += o.speed;

            o.el.style.top = o.y + "px";

            if(checkCollision(o)){

                collect(o);

                continue;

            }

            if(o.y > window.innerHeight){

                if(!o.bomb){

                    combo = 0;

                }

                o.el.remove();

                objects.splice(i,1);

            }

        }

    }

    requestAnimationFrame(loop);

}


/* Collision */

function checkCollision(o){

    const p = player.getBoundingClientRect();
    const r = o.el.getBoundingClientRect();

    return (

        r.bottom >= p.top &&
        r.top <= p.bottom &&
        r.right >= p.left &&
        r.left <= p.right

    );

}


/* Collect */

function collect(o){

    if(o.bomb){

        lives--;

        combo = 0;

        sound("bomb");

        updateLives();

        flash("#ef4444");

        if(lives <= 0){

            endGame();

        }

    }else{

        combo++;

        const points = 10 + Math.min(combo,10) * 2;

        score += points;

        sound("catch");

        showFloatScore(o.x,o.y,"+" + points);

        if(combo >= 2){

            comboEl.textContent = "🔥 COMBO x" + combo;

            comboEl.style.opacity = "1";

        }

        level = Math.floor(score / 100) + 1;

        levelEl.textContent = level;

    }

    o.el.remove();

    const index = objects.indexOf(o);

    if(index !== -1){

        objects.splice(index,1);

    }

    scoreEl.textContent = score;

}


/* Floating points */

function showFloatScore(x,y,text){

    const el = document.createElement("div");

    el.className = "floatScore";

    el.textContent = text;

    el.style.left = x + "px";
    el.style.top = y + "px";

    game.appendChild(el);

    setTimeout(() => el.remove(),700);

}


/* Lives */

function updateLives(){

    livesEl.textContent = "❤️".repeat(lives) + "🖤".repeat(3-lives);

}


/* Screen flash */

function flash(color){

    const f = document.createElement("div");

    f.style.position = "absolute";
    f.style.inset = "0";
    f.style.background = color;
    f.style.opacity = ".15";
    f.style.zIndex = "15";
    f.style.pointerEvents = "none";

    game.appendChild(f);

    setTimeout(() => f.remove(),150);

}


/* End */

function endGame(){

    running = false;

    clearInterval(spawnTimer);

    sound("gameover");

    finalScore.textContent = score;

    let currentBest = Number(localStorage.getItem("neonCatchBest") || 0);

    if(score > currentBest){

        localStorage.setItem("neonCatchBest",score);

        currentBest = score;

    }

    bestScore.textContent = currentBest;

    gameOver.classList.remove("hidden");

}


/* Clear */

function clearObjects(){

    objects.forEach(o => o.el.remove());

    objects = [];

}


/* Pause */

pauseBtn.addEventListener("click", () => {

    if(!running) return;

    paused = !paused;

    pauseBtn.textContent = paused ? "▶" : "Ⅱ";

});


/* Touch controls */

function movePlayer(clientX){

    const width = 95;

    playerX = Math.max(
        width / 2,
        Math.min(window.innerWidth - width / 2, clientX)
    );

    player.style.left = playerX + "px";

}

game.addEventListener("touchmove", e => {

    if(!running || paused) return;

    movePlayer(e.touches[0].clientX);

},{passive:true});


game.addEventListener("mousemove", e => {

    if(!running || paused) return;

    movePlayer(e.clientX);

});


/* Buttons */

startBtn.addEventListener("click",startGame);

restartBtn.addEventListener("click",startGame);


/* Keyboard support */

document.addEventListener("keydown", e => {

    if(!running) return;

    if(e.key === "ArrowLeft"){

        movePlayer(playerX - 40);

    }

    if(e.key === "ArrowRight"){

        movePlayer(playerX + 40);

    }

});


/* Resize */

window.addEventListener("resize", () => {

    movePlayer(playerX);

});

</script>

</body>
</html>
