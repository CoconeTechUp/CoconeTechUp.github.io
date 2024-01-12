---
layout: post
title: "Canvas API"
comments: true
excerpt: "Canvas API 사용기"
date: 2024-01-11
tags: [html5, canavs api]
thumbnail: https://github.com/sssodahae/20240111/assets/68799501/6a51e50a-256b-44a4-8798-0ae5ec878002
---

### 들어가며...

안녕하세요! 2024년 첫 포스팅을 하게되었네요.
무슨 내용의 포스팅을 적어야하나 고민 하던 중.. 저의 일상 속에서 주제를 찾았습니다.
저는 모바일 게임 유목민입니다. 여러가지 게임을 하다보면 광고를 많이 보게되는데요.
(한 게임에서만 광고를 20번 이상 보고 있습니다...광고 너무 많아..😂)
그 중에서도 플레이어블 광고가 흥미로웠고! 가끔 광고시간을 채웠는데도 나가지 않고 플레이하고 있는 저를 볼 수 있었어요.

플레이어 광고란, 사용자가 앱설치 전에 직접 플레이할 수 있게 만든 광고를 말합니다.
모바일 게임을 한번쯤 해보셨다면 겪어보지 않으셨을지 않을까 싶어요!
플레이어블 광고는 웹에서 동작할 수 있게 html5를 이용하여 만들고, css, 자바스크립트 등을 이용해 만듭니다.
저는 그 중에서 Canvas API가 눈에 띄었는데, 오늘은 **Canvas API 사용기**를 포스팅하려고 합니다! (서론이 너무 길었죠..? 드디어 본격적..)

### Canvas API

Canvas API란 HTML 'canvas' 요소를 통해 제공되며, 이를 사용하여 스크립트를 통해 동적으로 이미지, 그림, 애니메이션 등을 그릴 수 있습니다.
기본적으로 Canvas API는 2D 그래픽을 그릴 때 사용합니다!

### 그럼 한번 사용해보자

이러니 저러니 해도 부딧혀봐야겠죠.
가장 고전적인 벽돌꺠기 게임을 한번 만들어보겠습니다.

```
<canvas id="canvas" width="600" height="600"></canvas>
<script>
    var canvas = document.getElementById('gameCanvas');
    var context = canvas.getContext('2d');
</script>
```

게임판을 만들기 위해서는 캔버스를 명시해줍니다 그리고 스크립트 안에서 canvas와 context를 가져옵니다.

```
<style>
    canvas {
      border: 1px solid #000;
      display: block;
      margin: 20px auto;
    }
</style>
```

캔버스가 눈에 안보여서 라인을 따주는 스타일을 입혔습니다.

캔버스 안에 원과 사각형을 만드는 방법은 아래와 같아요! 원은 arc, 사각형은 rect 를 사용합니다.

```
// 캔버스에 원 그리기 함수
function drawCircle() {
    context.beginPath();
    context.arc(300, 200, 10, 0, 2 * Math.PI); // x, y, radius
    context.fillStyle = "blue"; // color
    context.fill();
    context.closePath();
}
// 캔버스에 직사각형 그리기 함수
function drawRectangle() {
    context.beginPath();
    context.rect(30, 30, 20, 20); //x, y, width, height
    context.fillStyle = "red";
    context.fill();
    context.closePath();
}
```

원과 사각형만 만드는 방법을 알았으니 이제 배치를 해보겠습니다.

```
// 공 속성
var ball = {
    x: canvas.width / 2,
    y: canvas.height,
    radius: 10,
    color: 'blue',
    movex: 2,
    movey: -2
};
// 캔버스에 원 그리기 함수
function drawCircle(x, y, radius, color) {
    context.beginPath();
    context.arc(x, y, radius, 0, 2 * Math.PI);
    context.fillStyle = color;
    context.fill();
    context.closePath();
}
```

우선 공을 만들어볼게요 공속성을 명시해주고 위 함수들에 인자값을 받아서 실행하도록 수정해줍니다.
x와 y좌표는 중간 아래쪽에서 나오게 명시해주었습니다.

```
// 게임 루프
function gameLoop() {
    // 화면 초기화
    context.clearRect(0, 0, canvas.width, canvas.height);

    // 공 그리기
    drawCircle(ball.x, ball.y, ball.radius, ball.color);
    // x가 width값보다 크면 마이너스 || 0보다도 작으면 안되니 플러스 되게
    if(ball.x + ball.movex > canvas.width || ball.x + ball.movex < 0) {
        ball.movex = -ball.movex;
    }
    if(ball.y + ball.movey > canvas.height || ball.y + ball.movey < 0) {
        ball.movey = -ball.movey;
    }

    ball.x += ball.movex;
    ball.y += ball.movey;

    // 반복 호출
    requestAnimationFrame(gameLoop);
}
```

공이 움직일 수 있도록 requestAnimationFrame()을 사용해줍니다 계속해서 함수를 호출 해주는 거예요.
그리고 화살 x와 y 좌표에 move값만큼 계속 더해줍니다.
공이 화면가장자리에 닿는 순간 다른 방향으로 튈 수 있도록 if문도 더해주었어요.
주석에 적힌것처럼 x가 width값보다 크면 마이너스가 필요했고, 0보다 작으면 화면 밖을 넘어가서 플러스되게끔 해주었습니다.
y는 height을 기준으로 넣어주면 됩니다.

여기까지오면..! 통통 튀는 공모양이 레이아웃 안에서 움직이게 됩니다.

공을 튕겨줄 판자를 만들어볼게요.

```
// player
var player = {
    x: canvas.width / 2,
    y: canvas.height - 30,
    width: 100,
    height: 20,
    radius: 10,
    color: 'pink',
};

// 이벤트 리스너 등록
document.addEventListener('mousemove', handleMouseMove);

function handleMouseMove(event) {
    player.x = event.clientX - canvas.offsetLeft;
    // 레이아웃을 벗어나지 않게 x좌표 컨트롤
    if(player.x >= canvas.width - player.width) {
        player.x = canvas.width - player.width;
    }
    if(player.x <= 0) {
        player.x = 0;
    }
}
// 캔버스에 직사각형 그리기 함수
function drawRectangle(x, y, width, height, color) {
    context.beginPath();
    context.rect(x, y, width, height);
    context.fillStyle = color;
    context.fill();
    context.closePath();
}
function gameLoop() {
    ...(중략)
    drawRectangle(player.x, player.y, player.width, player.height, player.color);
    ...(중략)
}
```

판자를 만들기위해 player라는 속성을 명시해주고
이벤트 리스너를 통해 좌우로만 움직일 수 있도록 x좌표만 마우스에 따라 값을 주었어요.
그리고 판자가 레이아웃을 벗어나지 않도록 했습니다.

이제 벽돌을 만들어 봅니다.

```
// 벽돌
var brick = {
    x: 0,
    y: 0,
    width: 100,
    height: 30,
    color: 'red',
    rowCount: 4, // 행 개수
    columnCount : 5, // 열 개수
    padding : 10,
    marginleft : 30,
    marginTop : 30,
};
// 벽돌 그리기
function drawBrick() {
    for(let i=0; i<brick.columnCount; i++) {
        for(let j=0; j<brick.rowCount; j++) {
            var x = i * (brick.width + brick.padding) + brick.marginleft;
            var y = j * (brick.height + brick.padding) + brick.marginTop;
            context.beginPath();
            context.rect(x, y, brick.width, brick.height);
            context.fillStyle = brick.color;
            context.fill();
            context.closePath();
        }
    }
}
```

벽돌을 2차배열을 통해 만들어줍니다. 벽돌사이의 간격을 위해 padding과 margin을 계산해서 x,y좌표를 주었습니다.
여기까지 완성시키면 나름 그럴듯한? 레이아웃 화면이 완성됩니다!

벽돌과 충돌했을 때 공을 움직이고, 벽돌을 없애주는 코드입니다.
벽돌들의 좌표와 상태값을 2차배열로 만들고
위 벽돌 그리기 함수를 호출 할 때 좌표를 저장합니다.
충돌 시 상태값을 0으로 수정해주어 벽돌그리기 시 그려지지않게 해주었습니다.

```
var bricks = [];
for (let i = 0; i < brick.columnCount; i++) {
    bricks[i] = [];
    for (let j = 0; j < brick.rowCount; j++) {
        bricks[i][j] = { x: 0, y: 0, status: 1 };
    }
}
// 벽돌 그리기
function drawBrick() {
    for(let i=0; i<brick.columnCount; i++) {
        for(let j=0; j<brick.rowCount; j++) {
            // 존재하는 것들만 그려준다
            if(bricks[i][j].status == 1) {
                var x = i * (brick.width + brick.padding) + brick.marginleft;
                var y = j * (brick.height + brick.padding) + brick.marginTop;
                bricks[i][j].x = x;
                bricks[i][j].y = y;
                ...(중략)
            }
        }
    }
}
// 충돌 확인
function isCollision() {
    for (var i = 0; i < brick.columnCount; i++) {
        for (var j = 0; j < brick.rowCount; j++) {
            var br = bricks[i][j]; // 벽돌
            // 벽돌이 존재하는 것만 충돌확인, 공 y 축 수정
            if (br.status == 1) {
            // 공 x좌표 > 벽돌 x좌표
            // 공 x좌표 < 벽돌 X좌표 + width
            // 공 y좌표 > 벽돌 y좌표
            // 공 y좌표 < 벽돌 y좌표 + height
            if(ball.x > br.x &&
            ball.x < br.x + brick.width &&
            ball.y > br.y &&
            ball.y < br.y + brick.height) {
                    ball.movey = -ball.movey; // y축 마이너스로
                    br.status = 0; //  벽돌없애기
                }
            }
        }
    }
}
```

여기까지면 공은 움직이고 판자도 좌우로 움직이고, 벽돌도 공에 맞으면 사라지는 화면이 만들어집니다.
이제 판자에 부딪히지 않으면 게임이 종료, 부딪히면 공이 튀어나갈 수 있게 바꾸기만 하면 게임이 얼추 만들어지겠네요.
벽돌과 마찬가지로 공의 x, 판자의 x width값을 비교하고 공의 y, 판자의 y height 값을 비교해 공의 y값을 수정해줍니다.
공의 y값이 캔버스의 height 커지면 게임이 종료됩니다.

```
// 게임 루프
function gameLoop() {
    // 화면 초기화
    context.clearRect(0, 0, canvas.width, canvas.height);

    // 공 그리기
    drawCircle(ball.x, ball.y, ball.radius, ball.color);
    // 판자 그리기
    drawRectangle(player.x, player.y, player.width, player.height, player.color);
    // 벽돌 그리기
    drawBrick();
    // 충돌 감지
    isCollision();

    // 판자를 넘어서 가장 아랫면에 닿았을 때 게임 종료
    if(ball.y + ball.movey + ball.radius > canvas.height) {
        // 값을 초기화 시켜준다
        ball.x = canvas.width / 2;
        ball.y = canvas.height - 50;
        alert("게임오버");
        // 페이지 리로드
        document.location.reload();
    }

    // x가 width값보다 크면 - || 0보다도 작으면 안됨
    if(ball.x + ball.movex > canvas.width || ball.x + ball.movex < 0) {
      ball.movex = -ball.movex;
    }
    if(ball.y + ball.movey > canvas.height || ball.y + ball.movey < 0) {
      ball.movey = -ball.movey;
    }

    // 판자에 부딪히면
    if(ball.x + ball.movex > player.x &&
        ball.x < player.x + player.width &&
        ball.y + ball.movey > player.y &&
        ball.y < player.y + player.height) {
        ball.movey = -ball.movey;
    }

    ball.x += ball.movex;
    ball.y += ball.movey;

    // 반복 호출
    requestAnimationFrame(gameLoop);
}

// 게임 루프 시작
gameLoop();
```

클리어가 되는 부분은 벽돌 그리는 부분에 추가를 해주었어요.

```
// 벽돌 그리기
function drawBrick() {
    var clear = true;
    for(let i=0; i<brick.columnCount; i++) {
        for(let j=0; j<brick.rowCount; j++) {
            // 존재하는 것들만 그려준다
            if(bricks[i][j].status == 1) {
                // 하나라도 그린다면 클리어 아님
                clear = false;
                var x = i * (brick.width + brick.padding) + brick.marginleft;
                var y = j * (brick.height + brick.padding) + brick.marginTop;
                bricks[i][j].x = x;
                bricks[i][j].y = y;
                ...(중략)
            }
        }
    }
    if(clear) {
        alert("축하드립니다 게임클리어~");
        document.location.reload();
    }
}
```

게임은 완성되었는데 공이 너무 느리고 게임성이 없네요 ㅎㅎ
판자에 부딪힐 때마다 속도가 증가하게끔 하고 점수를 카운트 했습니다.

```
// 점수
var score = 0;
// 점수 그리기
function drawScore(){
    context.beginPath();
    context.font = '15px serif';
    context.fillStyle = "black";
    context.fillText(`SCORE : ${score}`, 20, 25);
}
// 충돌 확인
function isCollision() {
    ...(중략)
    ball.movey = -ball.movey; // y축 마이너스로
    br.status = 0; //  벽돌없애기
    score ++; // 점수 늘리기
    ...(중략)
}
// 게임 루프
function gameLoop() {
    ...(중략)
    // 점수 그리기
    drawScore();
    ...(중략)
    // 판자에 부딪히면
    if(ball.x + ball.movex > player.x &&
        ball.x < player.x + player.width &&
        ball.y + ball.movey > player.y &&
        ball.y < player.y + player.height) {
        // 속도늘리기
        ball.movey ++;
        ball.movey = -ball.movey;
    }
    ...(중략)
}
```

완성된 게임입니다! [🕹️게임하러가기🕹️](https://codepen.io/rejqqtye-the-decoder/pen/eYXBYNO)

### 마치며

그동안 한번도 사용해보진 못한 'canvas'요소를 사용해보니 완전 새로운 경험이였습니다! 이런 것도 있구나 하고 많이 알게 되었네요.
html5 사용해본 적이 있으니 사실 조금 쉽게 보기도 했는데, 전혀 그렇지 않았고 많이 헤맸습니다..ㅎㅎ
게임은 생각외로 어려우니 다들 한번 시도해보시길~! 허접하고 버그가 나도 너그러이 이해해주세요😂
