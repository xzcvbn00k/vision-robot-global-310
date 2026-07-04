# 拖放故事书制作器



## 说明

通过拖放角色、道具到场景区域创建属于自己的故事页面。创意写作与编程结合。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head><meta charset="UTF-8"><title>故事书制作器</title>

<style>

body{font-family:Arial;margin:0;display:flex;height:100vh;background:#2c3e50;color:white}

.sidebar{width:200px;background:#34495e;padding:15px;overflow-y:auto}

.sticker{padding:10px;margin:5px 0;background:#3d566e;border-radius:8px;cursor:grab;text-align:center;font-size:36px;user-select:none}

.stage{flex:1;background:#ecf0f1;position:relative;overflow:hidden}

.stage-item{position:absolute;font-size:40px;cursor:move;user-select:none}

.stage-item:hover{outline:2px solid #3498db;border-radius:4px}

#title{position:absolute;top:10px;left:50%;transform:translateX(-50%);font-size:28px;color:#2c3e50}

.toolbar{position:absolute;bottom:10px;left:50%;transform:translateX(-50%);display:flex;gap:10px}

.toolbar button{padding:10px 20px;border:none;border-radius:8px;cursor:pointer;font-size:16px;color:white}

</style></head>

<body>

<div class="sidebar" id="sidebar"></div>

<div class="stage" id="stage">

  <div id="title" contenteditable="true">我的故事书</div>

  <div class="toolbar">

    <button style="background:#e74c3c" onclick="clearStage()">清空</button>

    <button style="background:#2ecc71" onclick="addText()">加文字</button>

  </div>

</div>



<script>

const stickers = ["👦","👧","🐶","🐱","🐰","🌳","☀️","☁️","🏠","🚗","🌟","❤️","🌸","🍎","🎈","📚"];

const stage = document.getElementById("stage");



// 渲染侧边栏贴纸

stickers.forEach(s => {

  let div = document.createElement("div");

  div.className = "sticker"; div.textContent = s; div.draggable = true;

  div.addEventListener("dragstart", e => e.dataTransfer.setData("text", s));

  document.getElementById("sidebar").appendChild(div);

});



// 添加到舞台

stage.addEventListener("dragover", e => e.preventDefault());

stage.addEventListener("drop", e => {

  e.preventDefault();

  let emoji = e.dataTransfer.getData("text");

  let rect = stage.getBoundingClientRect();

  addItem(emoji, e.clientX - rect.left - 20, e.clientY - rect.top - 20);

});



function addItem(emoji, x, y) {

  let div = document.createElement("div");

  div.className = "stage-item"; div.textContent = emoji;

  div.style.left = x + "px"; div.style.top = y + "px";



  let ox, oy;

  div.addEventListener("mousedown", e => {

    ox = e.clientX - div.offsetLeft; oy = e.clientY - div.offsetTop;

    function move(ev) { div.style.left = (ev.clientX - ox) + "px"; div.style.top = (ev.clientY - oy) + "px"; }

    function up() { document.removeEventListener("mousemove", move); document.removeEventListener("mouseup", up); }

    document.addEventListener("mousemove", move);

    document.addEventListener("mouseup", up);

  });



  div.addEventListener("dblclick", () => {

    let size = prompt("设置大小(px):", "40");

    if (size) div.style.fontSize = size + "px";

  });



  div.addEventListener("contextmenu", e => { e.preventDefault(); div.remove(); });

  stage.appendChild(div);

}



function addText() {

  let div = document.createElement("div");

  div.className = "stage-item";

  div.textContent = "双击编辑文字";

  div.style.fontSize = "24px";

  div.style.color = "#2c3e50";

  div.style.left = "100px"; div.style.top = "300px";

  div.contentEditable = true;

  stage.appendChild(div);

}



function clearStage() {

  document.querySelectorAll(".stage-item").forEach(el => el.remove());

}

</script></body></html>

```

