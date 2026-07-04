# 拖拽裁剪游戏（图片拼贴）



## 说明

将大图分割成 9 块，玩家通过拖拽重新排列成正确图像。用 Canvas 实现图片分割。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head>

<meta charset="UTF-8">

<title>图片拼图游戏</title>

<style>

  body { font-family: Arial; text-align: center; margin: 20px; }

  canvas { border: 2px solid #333; cursor: default; }

  .info { margin: 10px; font-size: 18px; }

  button { padding: 10px 20px; margin: 5px; border: none; border-radius: 8px; background: #4CAF50; color: white; cursor: pointer; font-size: 16px; }

</style>

</head>

<body>

<h2>🖼️ 图片拼图游戏</h2>

<div class="info" id="info">点击两个格子交换位置</div>

<canvas id="canvas" width="300" height="300"></canvas>

<br>

<button onclick="shuffle()">打乱</button>



<script>

const SIZE = 3;

const CELL = 100;

const canvas = document.getElementById("canvas");

const ctx = canvas.getContext("2d");



let grid = [0,1,2,3,4,5,6,7,8];

let pieces = [];

let selected = -1;



// 创建渐变色图片（可替换为真实图片）

function createImage() {

  let imgCanvas = document.createElement("canvas");

  imgCanvas.width = CELL * SIZE;

  imgCanvas.height = CELL * SIZE;

  let ictx = imgCanvas.getContext("2d");



  // 画一个彩色网格图

  let colors = ["#ff6b6b","#feca57","#48dbfb","#ff9ff3",

                "#54a0ff","#5f27cd","#01a3a4","#f368e0","#ff6348"];

  ictx.fillStyle = "#fff"; ictx.fillRect(0,0,300,300);

  colors.forEach((c,i) => {

    let x = (i % SIZE) * CELL, y = Math.floor(i / SIZE) * CELL;

    ictx.fillStyle = c; ictx.fillRect(x+3, y+3, CELL-6, CELL-6);

    ictx.fillStyle = "#fff"; ictx.font = "30px Arial";

    ictx.textAlign = "center"; ictx.fillText(i+1, x+CELL/2, y+CELL/2+10);

  });



  // 分割成小块

  for (let i = 0; i < 9; i++) {

    let sx = (i % SIZE) * CELL, sy = Math.floor(i / SIZE) * CELL;

    let pc = document.createElement("canvas");

    pc.width = CELL; pc.height = CELL;

    pc.getContext("2d").drawImage(imgCanvas, sx, sy, CELL, CELL, 0, 0, CELL, CELL);

    pieces.push(pc);

  }

}



function draw() {

  ctx.clearRect(0, 0, 300, 300);

  for (let i = 0; i < 9; i++) {

    let row = Math.floor(i / SIZE), col = i % SIZE;

    ctx.drawImage(pieces[grid[i]], col*CELL, row*CELL);

    // 高亮选中

    if (i === selected) {

      ctx.strokeStyle = "#ff0"; ctx.lineWidth = 3;

      ctx.strokeRect(col*CELL, row*CELL, CELL, CELL);

    }

    // 已归位的绿色边框

    if (grid[i] === i) {

      ctx.strokeStyle = "rgba(0,255,0,0.5)"; ctx.lineWidth = 2;

      ctx.strokeRect(col*CELL, row*CELL, CELL, CELL);

    }

  }

}



canvas.addEventListener("click", e => {

  let rect = canvas.getBoundingClientRect();

  let col = Math.floor((e.clientX - rect.left) / CELL);

  let row = Math.floor((e.clientY - rect.top) / CELL);

  let idx = row * SIZE + col;



  if (selected < 0) {

    selected = idx;

  } else {

    // 交换

    [grid[selected], grid[idx]] = [grid[idx], grid[selected]];

    selected = -1;

  }

  draw();

  checkWin();

});



function shuffle() {

  for (let i = grid.length - 1; i > 0; i--) {

    let j = Math.floor(Math.random() * (i + 1));

    [grid[i], grid[j]] = [grid[j], grid[i]];

  }

  selected = -1;

  draw();

}



function checkWin() {

  if (grid.every((v,i) => v === i)) {

    setTimeout(() => alert("🎉 拼图完成！"), 200);

  }

}



createImage();

draw();

</script>

</body>

</html>

```



## 教学重点

- Canvas `drawImage()` 九参数用法：(src, sx, sy, sw, sh, dx, dy, dw, dh) 实现图片裁剪

- 点击交换而非拖拽（另一种交互方式）

- `pieces` 数组存储预裁剪的小画布，提高性能

- 已归位的格子给绿色边框作为提示

- 用渐变色画示例图替代真实图片加载，避免跨域问题

