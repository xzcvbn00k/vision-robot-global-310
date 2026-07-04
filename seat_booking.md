# 拖拽选座系统



## 说明

模拟电影院/教室选座。可拖拽人物到座位上，已占座位不能再放。学习网格布局和位置锁定。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head><meta charset="UTF-8"><title>选座系统</title>

<style>

body{font-family:Arial;text-align:center;background:#1a1a2e;color:white;padding:20px}

.stage{background:#333;padding:10px;margin:20px auto;width:fit-content;border-radius:8px;color:#aaa}

.seats{display:grid;grid-template-columns:repeat(6,60px);gap:8px;justify-content:center;margin:20px}

.seat{width:60px;height:60px;background:#16213e;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:24px;cursor:pointer}

.seat.occupied{background:#0f3460}

.seat.over{background:#e94560;border:2px solid white}

.people-bar{display:flex;justify-content:center;gap:15px;margin:20px}

.person{font-size:36px;cursor:grab;padding:8px;background:#533483;border-radius:12px;width:50px;height:50px;display:flex;align-items:center;justify-content:center}

#details{font-size:18px;min-height:24px}

</style></head>

<body>

<h2>🎬 选座系统</h2>

<div class="stage">===== 银 幕 =====</div>

<div class="seats" id="seats"></div>

<div id="details">从下方拖入观众选座</div>

<div class="people-bar" id="peopleBar"></div>



<script>

const ROWS = 5, COLS = 6;

let seats = Array(ROWS).fill(null).map(() => Array(COLS).fill(null));

const people = ["👦","👧","👨","👩","👴","👵","🧑","👶"];

let targetRow = -1, targetCol = -1;



function render() {

  let seatsDiv = document.getElementById("seats");

  seatsDiv.style.gridTemplateColumns = `repeat(${COLS}, 60px)`;

  seatsDiv.innerHTML = "";

  for (let r = 0; r < ROWS; r++) {

    for (let c = 0; c < COLS; c++) {

      let seat = document.createElement("div");

      seat.className = "seat" + (seats[r][c] ? " occupied" : "");

      seat.textContent = seats[r][c] || (r+1) + String.fromCharCode(65+c);

      if (!seat.textContent.match(/[A-Z]/)) seat.textContent = seats[r][c];

      seat.dataset.row = r; seat.dataset.col = c;



      seat.addEventListener("dragover", e => {

        e.preventDefault();

        if (!seats[r][c]) seat.classList.add("over");

      });

      seat.addEventListener("dragleave", () => seat.classList.remove("over"));

      seat.addEventListener("drop", e => {

        e.preventDefault(); seat.classList.remove("over");

        if (seats[r][c]) return;

        let person = e.dataTransfer.getData("text");

        seats[r][c] = person;

        render();

      });



      // 点击移除已占座位

      seat.addEventListener("click", () => {

        if (seats[r][c]) {

          seats[r][c] = null;

          render();

        }

      });



      seatsDiv.appendChild(seat);

    }

  }

}



let peopleDiv = document.getElementById("peopleBar");

people.forEach(p => {

  let div = document.createElement("div");

  div.className = "person"; div.textContent = p; div.draggable = true;

  div.addEventListener("dragstart", e => e.dataTransfer.setData("text", p));

  peopleDiv.appendChild(div);

});



render();

</script></body></html>

```

