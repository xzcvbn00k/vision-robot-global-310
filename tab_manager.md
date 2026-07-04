# 可拖拽标签页界面



## 说明

实现一个可拖拽排序的标签页管理器，能新增、关闭、拖拽重排标签页。类似浏览器标签页行为。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head><meta charset="UTF-8"><title>标签页管理</title>

<style>

body{font-family:Arial;margin:0;background:#1e1e1e;color:#fff}

.tab-bar{display:flex;background:#252526;border-bottom:2px solid #007acc;padding:0 10px;overflow-x:auto}

.tab{display:flex;align-items:center;padding:8px 15px;background:#2d2d2d;border-right:1px solid #1e1e1e;cursor:grab;white-space:nowrap;min-width:80px;user-select:none;font-size:14px}

.tab.active{background:#1e1e1e;border-bottom:2px solid #007acc}

.tab .close{margin-left:8px;cursor:pointer;font-size:14px;color:#999}

.tab .close:hover{color:#fff}

.content{height:300px;display:flex;align-items:center;justify-content:center;font-size:60px}

.add-tab{padding:8px 15px;background:#0e639c;color:white;border:none;cursor:pointer;font-size:18px}

.add-tab:hover{background:#1177bb}

</style></head>

<body>

<div style="display:flex;align-items:center">

  <div class="tab-bar" id="tabBar"></div>

  <button class="add-tab" onclick="addTab()" title="新建标签">+</button>

</div>

<div class="content" id="content">📄</div>



<script>

let tabs = [

  { title: "页面 1", emoji: "🏠" },

  { title: "页面 2", emoji: "📧" },

  { title: "页面 3", emoji: "⚙️" }

];

let activeTab = 0;

let dragIdx = -1;



function render() {

  let bar = document.getElementById("tabBar");

  bar.innerHTML = "";

  tabs.forEach((t, i) => {

    let tab = document.createElement("div");

    tab.className = "tab" + (i === activeTab ? " active" : "");

    tab.draggable = true;

    tab.innerHTML = t.emoji + " " + t.title + '<span class="close" data-idx="'+i+'">×</span>';



    tab.addEventListener("click", e => {

      if (e.target.classList.contains("close")) return;

      activeTab = i;

      render();

    });



    tab.addEventListener("dragstart", e => {

      dragIdx = i;

      tab.style.opacity = "0.5";

    });

    tab.addEventListener("dragend", () => { tab.style.opacity = "1"; dragIdx = -1; });

    tab.addEventListener("dragover", e => e.preventDefault());

    tab.addEventListener("drop", e => {

      e.preventDefault();

      if (dragIdx >= 0 && dragIdx !== i) {

        let moved = tabs.splice(dragIdx, 1)[0];

        tabs.splice(i, 0, moved);

      }

      render();

    });



    bar.appendChild(tab);

  });



  // 关闭按钮

  document.querySelectorAll(".close").forEach(btn => {

    btn.addEventListener("click", e => {

      e.stopPropagation();

      let idx = parseInt(btn.dataset.idx);

      tabs.splice(idx, 1);

      if (activeTab >= tabs.length) activeTab = Math.max(0, tabs.length - 1);

      if (tabs.length === 0) { tabs = [{title:"新标签",emoji:"📄"}]; activeTab = 0; }

      render();

    });

  });



  document.getElementById("content").textContent = tabs[activeTab]?.emoji || "📄";

}



function addTab() {

  let name = prompt("标签名称:", "页面 " + (tabs.length + 1));

  if (name) {

    tabs.push({ title: name, emoji: "📄" });

    activeTab = tabs.length - 1;

    render();

  }

}



render();

</script></body></html>

```



## 教学重点

- 标签页管理系统是 IDE/浏览器的基础组件

- `e.target.closest(".close")` 判断点击的目标元素

- 关闭标签后需处理 `activeTab` 越界

- `e.stopPropagation()` 防止关闭按钮触发标签切换

- 拖拽排序的核心保持不变：`splice(取出) → splice(插入)`

