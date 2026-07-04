# 拖拽拼句子游戏



## 说明

将打乱的词语拖到横线上，排列成正确的句子。帮助小学生学习句子结构。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head>

<meta charset="UTF-8">

<title>拼句子游戏</title>

<style>

  body { font-family: "Microsoft YaHei", Arial; text-align: center; margin: 30px; background: #fff9c4; }

  h2 { color: #f57f17; }

  .question { font-size: 20px; margin: 15px; color: #555; }

  .words { display: flex; flex-wrap: wrap; justify-content: center; gap: 12px; margin: 20px; }

  .word {

    padding: 12px 20px; background: #fff; border: 2px solid #ff9800;

    border-radius: 25px; font-size: 20px; cursor: grab; user-select: none; font-weight: bold;

  }

  .sentence-line {

    display: flex; justify-content: center; gap: 8px; margin: 30px;

    min-height: 55px; padding: 10px; background: white; border-radius: 12px; flex-wrap: wrap;

  }

  .sentence-line .word { border-color: #4CAF50; background: #e8f5e9; cursor: default; }

  #result { font-size: 24px; min-height: 40px; margin: 10px; }

  .correct { background: #81c784 !important; color: white !important; }

</style>

</head>

<body>

<h2>📝 拖拽拼句子</h2>

<div class="question">提示: <span id="hint"></span></div>

<div class="words" id="wordBank"></div>

<div class="sentence-line" id="sentenceArea"></div>

<div id="result"></div>

<button onclick="nextSentence()" style="padding:10px 30px;border:none;border-radius:20px;background:#ff9800;color:white;font-size:18px;cursor:pointer;margin:10px;">下一题</button>



<script>

const sentences = [

  { answer: "我今天去公园玩", hint: "我今天的活动" },

  { answer: "小猫爱吃鱼", hint: "猫喜欢吃什么" },

  { answer: "太阳从东方升起", hint: "太阳从哪里出来" },

  { answer: "春天花儿开了", hint: "春天的景色" },

];



let current = 0;

let userWords = [];

let availableWords = [];



function shuffle(arr) {

  return [...arr].sort(() => Math.random() - 0.5);

}



function loadSentence() {

  let s = sentences[current];

  availableWords = shuffle(s.answer.split("").map((c,i) => c));

  userWords = [];

  document.getElementById("hint").textContent = s.hint;

  document.getElementById("result").textContent = "";

  render();

}



function render() {

  // 词库区

  let bank = document.getElementById("wordBank");

  bank.innerHTML = "";

  availableWords.forEach((w, idx) => {

    if (w === "") return;

    let div = document.createElement("div");

    div.className = "word";

    div.textContent = w;

    div.draggable = true;

    div.dataset.idx = idx;

    div.addEventListener("dragstart", e =>

      e.dataTransfer.setData("text/plain", JSON.stringify({idx, word: w}))

    );

    bank.appendChild(div);

  });



  // 句子区

  let area = document.getElementById("sentenceArea");

  area.innerHTML = "";

  userWords.forEach((w, i) => {

    let div = document.createElement("div");

    div.className = "word";

    div.textContent = w;

    div.addEventListener("click", () => {

      availableWords.push(userWords.splice(i, 1)[0]);

      render();

    });

    area.appendChild(div);

  });



  // 句子区的 drop

  area.addEventListener("dragover", e => e.preventDefault());

  area.addEventListener("drop", e => {

    e.preventDefault();

    let data = JSON.parse(e.dataTransfer.getData("text/plain"));

    if (availableWords[data.idx] !== undefined) {

      userWords.push(data.word);

      availableWords.splice(data.idx, 1);

      render();

      checkResult();

    }

  });

}



function checkResult() {

  let s = sentences[current].answer;

  let userStr = userWords.join("");

  if (userStr === s) {

    document.getElementById("result").innerHTML = "✅ 正确！";

    // 高亮所有词

    document.querySelectorAll(".sentence-line .word").forEach(d =>

      d.classList.add("correct")

    );

  } else if (userStr.length >= s.length) {

    document.getElementById("result").innerHTML = "❌ 不太对，请点击字移除重新排列";

  }

}



function nextSentence() {

  current = (current + 1) % sentences.length;

  loadSentence();

}



loadSentence();

</script>

</body>

</html>

```



## 教学重点

- 将句子拆成单个汉字而非词，训练识字能力

- 点击已放置的字可移除（click 事件放回 `availableWords`）

- `drop` 事件只在句子区处理，词库区本身不接收 drop

- `shuffle` 用 `sort(() => Math.random() - 0.5)` 简单洗牌

- 检查结果 `userWords.join("") === answer` 精确匹配

