# 拖拽排序清单



## 说明

通过拖拽重新排列待办事项的顺序，顶部到最重要，底部到最不重要。学习在列表项之间拖放排序的实现技巧。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head>

<meta charset="UTF-8">

<title>拖拽排序清单</title>

<style>

  body { font-family: Arial; max-width: 500px; margin: 20px auto; }

  h2 { text-align: center; }

  .todo-list { list-style: none; padding: 0; }

  .todo-list li {

    padding: 15px; margin: 5px 0;

    background: #f5f5f5; border: 2px solid #ddd; border-radius: 8px;

    cursor: grab; display: flex; align-items: center; gap: 10px; user-select: none;

  }

  .todo-list li:hover { background: #e8f5e9; }

  .todo-list li.dragging { opacity: 0.5; background: #c8e6c9; }

  .handle { font-size: 20px; color: #999; cursor: grab; }

  .input-row { display: flex; gap: 10px; margin: 15px 0; }

  .input-row input { flex: 1; padding: 10px; border: 2px solid #ddd; border-radius: 8px; font-size: 16px; }

  .input-row button { padding: 10px 20px; background: #2196F3; color: white; border: none; border-radius: 8px; cursor: pointer; }

</style>

</head>

<body>

<h2>📋 拖拽排序待办清单</h2>

<div class="input-row">

  <input id="todoInput" placeholder="输入新任务..." onkeyup="if(event.key==='Enter') addTodo()">

  <button onclick="addTodo()">添加</button>

</div>

<ul class="todo-list" id="todoList"></ul>



<script>

let items = ["学习 HTML 拖拽 API", "完成数学作业", "跑步 30 分钟"];

let dragIndex = -1;



function render() {

  let ul = document.getElementById("todoList");

  ul.innerHTML = "";

  items.forEach((text, idx) => {

    let li = document.createElement("li");

    li.draggable = true;

    li.innerHTML = `<span class="handle">☰</span> ${text}`;



    li.addEventListener("dragstart", e => {

      dragIndex = idx;

      li.classList.add("dragging");

      e.dataTransfer.effectAllowed = "move";

    });



    li.addEventListener("dragend", () => {

      li.classList.remove("dragging");

      dragIndex = -1;

    });



    li.addEventListener("dragover", e => {

      e.preventDefault();

      e.dataTransfer.dropEffect = "move";

    });



    li.addEventListener("drop", e => {

      e.preventDefault();

      if (dragIndex >= 0 && dragIndex !== idx) {

        // 取出拖拽项并插入到目标位置

        let moved = items.splice(dragIndex, 1)[0];

        items.splice(idx, 0, moved);

        render();

      }

    });



    // 双击编辑

    li.addEventListener("dblclick", () => {

      let newText = prompt("编辑任务:", text);

      if (newText && newText.trim()) {

        items[idx] = newText.trim();

        render();

      }

    });



    ul.appendChild(li);

  });

}



function addTodo() {

  let input = document.getElementById("todoInput");

  let text = input.value.trim();

  if (text) {

    items.push(text);

    input.value = "";

    render();

  }

}



render();

</script>

</body>

</html>

```



## 教学重点

- 列表拖放排序的核心是 `splice(index, 1)` 取出一项再 `splice(target, 0, item)` 插入

- `dragstart` 记录原始索引，`drop` 在目标位置插入

- `dragover` 上设置 `dropEffect` 改变鼠标图标

- 双击编辑 (`dblclick`) 是常见的交互模式

- `prompt()` 快速实现编辑，生产环境宜用内联编辑框

