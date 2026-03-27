<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>러시아어 동물 미로 게임</title>
<style>
    body {
        background:#f0f0f0;
        font-family: Arial;
        text-align:center;
    }
    #game {
        margin:auto;
        margin-top:20px;
        width:640px;
        height:480px;
        position:relative;
        background:#1d1d1d;
        border:4px solid #444;
        box-shadow:0 0 10px rgba(0,0,0,0.5);
        image-rendering: pixelated;
    }
    .cell {
        width:32px; height:32px;
        position:absolute;
    }
    .wall {
        background:#3c3c3c;
        border:2px solid #2a2a2a;
        box-sizing:border-box;
    }
    .player {
        font-size:28px;
        line-height:32px;
        text-align:center;
    }

    /* 🔑 픽셀 열쇠 (더 깔끔한 버전) */
    .keyPixel {
        background:gold;
        border:2px solid #c49000;
        border-radius:4px;
        box-shadow: inset 0 0 4px rgba(0,0,0,0.3);
    }

    /* 🚪 픽셀 문 (더 깔끔한 버전) */
    .doorPixel {
        background:#8b5732;
        border:3px solid #503018;
        box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
        border-radius:2px;
    }

    #questionBox {
        width:420px;
        margin:auto;
        margin-top:15px;
        background:white;
        padding:15px;
        border-radius:12px;
        display:none;
        box-shadow:0 2px 8px rgba(0,0,0,0.15);
    }
    #keyboard button {
        margin:3px;
        padding:10px;
        font-size:18px;
        width:40px;
        border-radius:6px;
        border:1px solid #ccc;
        background:#f8f8f8;
    }
    #moveBtns button {
        margin:5px;
        padding:10px;
        font-size:20px;
        width:60px;
        border-radius:8px;
    }
</style>
</head>
<body>

<h2>🐰 러시아어 단어 미로 게임 (업그레이드 버전)</h2>

<div id="game"></div>

<!-- 방향키 -->
<div id="moveBtns">
    <button onclick="move('up')">⬆️</button><br>
    <button onclick="move('left')">⬅️</button>
    <button onclick="move('down')">⬇️</button>
    <button onclick="move('right')">➡️</button>
</div>

<!-- 문제 박스 -->
<div id="questionBox">
    <div id="questionText" style="font-size:18px;margin-bottom:10px;"></div>
    <input id="answerInput" style="width:80%; font-size:20px; text-align:center; padding:6px;">
    <div id="keyboard" style="margin-top:10px;"></div>
    <button onclick="submitAnswer()" style="margin-top:8px; font-size:18px; padding:8px 20px;">제출하기</button>
    <p id="hintText" style="color:red; margin-top:10px;"></p>
</div>

<script>
// ------------------- 단어 데이터 ----------------------
const words = [
    {kor:"말", rus:"лошадь"},
    {kor:"원숭이", rus:"обезьяна"},
    {kor:"사자", rus:"лев"},
    {kor:"개", rus:"собака"},
    {kor:"고양이", rus:"кошка"},
    {kor:"돼지", rus:"свинья"},
    {kor:"호랑이", rus:"тигр"},
    {kor:"코끼리", rus:"слон"},
    {kor:"소", rus:"корова"},
    {kor:"토끼", rus:"заяц"}
];

// 강세 제거
function normalize(s){
    return s.replace(/[\u0301]/g, "").replace(/ё/g,"е");
}

// ------------------- 새 미로 구성 -----------------------
// 더 깔끔하고 재미있게 리메이크된 미로
const maze = [
"11111111111111111111",
"1P0000010000000000001",
"101101011011111011101",
"100001000000001000001",
"101111011111101011101",
"100000010000001000001",
"101110011011101011101",
"100000000000000000001",
"1D1111111111111111111"
];

const ROW = maze.length;
const COL = maze[0].length;

const game = document.getElementById("game");
let playerX = 1, playerY = 1;

let collected = 0;
const totalKeys = 10;

// ------------------- 빈칸 찾기 & 열쇠 배치 -----------------------
let emptyCells = [];
for (let y=0; y<ROW; y++){
    for (let x=0; x<COL; x++){
        if (maze[y][x] === "0") emptyCells.push({x,y});
    }
}
shuffle(emptyCells);
let keyPositions = emptyCells.slice(0,10);

// 단어 연결
keyPositions.forEach((cell, i)=> cell.word = words[i]);

// 문 찾기
let doorX=0, doorY=0;
for (let y=0;y<ROW;y++){
    for (let x=0;x<COL;x++){
        if (maze[y][x]==="D"){doorX=x; doorY=y;}
    }
}

// ------------------- 화면 렌더링 -----------------------
function render(){
    game.innerHTML = "";
    for (let y=0;y<ROW;y++){
        for (let x=0;x<COL;x++){
            let div=document.createElement("div");
            div.className="cell";
            div.style.left=x*32+"px";
            div.style.top=y*32+"px";

            if (maze[y][x]==="1") div.classList.add("wall");

            if (x===playerX && y===playerY){
                div.classList.add("player");
                div.textContent="😊";
            }

            keyPositions.forEach(k=>{
                if(!k.collected && k.x===x && k.y===y)
                    div.classList.add("keyPixel");
            });

            if (x===doorX && y===doorY)
                div.classList.add("doorPixel");

            game.appendChild(div);
        }
    }
}
render();

// ------------------- 이동 처리 -----------------------
function move(dir){
    let nx = playerX, ny = playerY;
    if (dir==="up") ny--;
    if (dir==="down") ny++;
    if (dir==="left") nx--;
    if (dir==="right") nx++;

    if (maze[ny][nx]==="1") return;

    playerX=nx; playerY=ny;

    // 열쇠
    keyPositions.forEach(k=>{
        if (!k.collected && k.x===nx && k.y===ny){
            openQuestion(k);
        }
    });

    // 문
    if (nx===doorX && ny===doorY){
        if (collected === totalKeys){
            alert("👏 축하합니다! 모든 열쇠를 모아 탈출했습니다!");
        } else {
            alert("아직 열쇠가 부족해요!");
        }
    }

    render();
}

// ------------------- 문제창 -----------------------
let currentKey=null;

function openQuestion(k){
    currentKey=k;
    document.getElementById("questionText").innerHTML=
        `<b>${k.word.kor}</b> 을(를) 러시아어로 입력하세요`;
    document.getElementById("answerInput").value="";
    document.getElementById("hintText").textContent="";
    document.getElementById("questionBox").style.display="block";
}

function submitAnswer(){
    let input = normalize(document.getElementById("answerInput").value.trim());
    let correct = normalize(currentKey.word.rus);

    if (input === correct){
        currentKey.collected=true;
        collected++;
        document.getElementById("questionBox").style.display="none";
        render();
        alert("✔ 정답! 열쇠를 획득했습니다! ("+collected+"/"+totalKeys+")");
    } else {
        document.getElementById("hintText").textContent =
            "힌트: 첫 글자는 '" + currentKey.word.rus[0] + "'";
    }
}

// ------------------- 러시아어 키보드 -----------------------
const rusKeys = [
"й","ц","у","к","е","н","г","ш","щ","з","х",
"ф","ы","в","а","п","р","о","л","д","ж","э",
"я","ч","с","м","и","т","ь","б","ю"
];

const keyboard = document.getElementById("keyboard");
rusKeys.forEach(k=>{
    let b=document.createElement("button");
    b.textContent=k;
    b.onclick=()=> document.getElementById("answerInput").value+=k;
    keyboard.appendChild(b);
});
let back=document.createElement("button");
back.textContent="⌫";
back.onclick=()=>{
    let inp=document.getElementById("answerInput");
    inp.value=inp.value.slice(0,-1);
};
keyboard.appendChild(back);

// ------------------- 유틸 -----------------------
function shuffle(arr){
    for (let i=arr.length-1;i>0;i--){
        let j=Math.floor(Math.random()*(i+1));
        [arr[i],arr[j]]=[arr[j],arr[i]];
    }
}
</script>

</body>
</html>
