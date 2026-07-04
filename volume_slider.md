# 拖动进度条与响铃



## 说明

拖动音量进度条控制播放的"铃声"大小和数值显示。经典的 slider 交互模式在游戏/工具应用中非常常见。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head>

<meta charset="UTF-8">

<title>滑动音量控制</title>

<style>

  body { font-family: Arial; text-align: center; margin: 40px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; min-height: 100vh; }

  .card { background: rgba(255,255,255,0.15); border-radius: 20px; padding: 30px; max-width: 400px; margin: 0 auto; backdrop-filter: blur(10px); }

  .icon { font-size: 60px; margin: 15px; }

  .slider-wrap { position: relative; height: 50px; display: flex; align-items: center; }

  .track { width: 100%; height: 8px; background: rgba(255,255,255,0.3); border-radius: 4px; position: relative; cursor: pointer; }

  .fill { height: 100%; background: #00e676; border-radius: 4px; transition: width 0.05s; }

  .thumb { width: 30px; height: 30px; background: white; border-radius: 50%; position: absolute; top: -11px; cursor: grab; box-shadow: 0 2px 8px rgba(0,0,0,0.3); transform: translateX(-50%); }

  .value { font-size: 40px; font-weight: bold; margin: 10px; }

  .bar { display: flex; height: 100px; align-items: flex-end; gap: 4px; justify-content: center; }

  .bar-seg { width: 12px; background: white; border-radius: 3px; transition: height 0.1s; opacity: 0.6; }

  .bar-seg.active { opacity: 1; }

</style>

</head>

<body>

<div class="card">

  <div class="icon" id="icon">🔔</div>

  <div class="value" id="value">50</div>

  <div class="slider-wrap" id="sliderWrap">

    <div class="track">

      <div class="fill" id="fill" style="width:50%"></div>

    </div>

    <div class="thumb" id="thumb" style="left:50%"></div>

  </div>

  <div class="bar" id="bars"></div>

</div>



<script>

const thumb = document.getElementById("thumb");

const fill = document.getElementById("fill");

const sliderWrap = document.getElementById("sliderWrap");

const valueEl = document.getElementById("value");

const iconEl = document.getElementById("icon");



let current = 50;

let dragging = false;



function updateValue(x) {

  let rect = sliderWrap.getBoundingClientRect();

  let percent = Math.max(0, Math.min(100, ((x - rect.left) / rect.width) * 100));

  percent = Math.round(percent);

  current = percent;

  fill.style.width = percent + "%";

  thumb.style.left = percent + "%";

  valueEl.textContent = percent;

  updateIcon();

}



function updateIcon() {

  if (current === 0) iconEl.textContent = "🔇";

  else if (current < 33) iconEl.textContent = "🔈";

  else if (current < 66) iconEl.textContent = "🔉";

  else iconEl.textContent = "🔊";

}



thumb.addEventListener("mousedown", e => { dragging = true; e.preventDefault(); });

document.addEventListener("mousemove", e => { if (dragging) updateValue(e.clientX); });

document.addEventListener("mouseup", () => { dragging = false; });



sliderWrap.addEventListener("click", e => updateValue(e.clientX));



// 触屏支持

thumb.addEventListener("touchstart", e => { dragging = true; });

document.addEventListener("touchmove", e => { if (dragging) updateValue(e.touches[0].clientX); });

document.addEventListener("touchend", () => { dragging = false; });



updateValue(sliderWrap.getBoundingClientRect().left + sliderWrap.getBoundingClientRect().width * 0.5);

</script>

</body>

</html>

```



## 教学重点

- 自定义滑块三要素：轨道(track) + 填充(fill) + 拇指(thumb)

- 百分比计算：`(mouseX - trackLeft) / trackWidth * 100`

- `dragging` 标志位控制 mousemove 是否生效

- 触摸事件 `touchstart/touchmove/touchend` 兼容移动端

- 渐变背景 + 毛玻璃 `backdrop-filter` 实现现代 UI

