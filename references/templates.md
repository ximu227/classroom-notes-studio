# 课堂笔记工作室 · 3 种模板 HTML/CSS 完整规范

## 通用技术栈（所有模板共用）

每个模板生成**独立的完整 HTML 文件**。

### 基础模板 head（3 种模板共用）

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{title}</title>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="katex/katex.min.css">
<script src="katex/katex.min.js"></script>
<style>
  body { font-family:'Times New Roman','Microsoft YaHei',serif; margin:0; }
  .katex { font-size: 1em; }
  .en { font-style: italic; }
</style>
</head>
```

> **重要**：KaTeX 必须本地引用（`katex/katex.min.css` 和 `katex/katex.min.js`），CDN 版在 Playwright file:// 页面中渲染不生效。HTML 文件需与 `katex/` 目录同级。

### 通用渲染脚本（放在 </body> 前，所有模板共用）

```javascript
(function(){
  // 公式占位符列表
  var formulaQueue = [];

  // 解析要点内容：将 $...$ 替换为公式占位 span
  function renderPoint(text) {
    // 处理加粗 **text**
    text = text.replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>');
    // 处理行内公式 $...$
    text = text.replace(/\$([^$]+)\$/g, function(m, tex) {
      var id = 'formula_' + formulaQueue.length;
      formulaQueue.push({id: id, tex: tex});
      return '<span id="' + id + '" class="formula-placeholder"></span>';
    });
    // 处理块级公式 $$...$$
    text = text.replace(/\$\$([^$]+)\$\$/g, function(m, tex) {
      var id = 'formula_' + formulaQueue.length;
      formulaQueue.push({id: id, tex: tex, display: true});
      return '<div id="' + id + '" class="formula-placeholder text-center my-2"></div>';
    });
    return text;
  }

  // 批量渲染所有公式
  function flushKatex() {
    formulaQueue.forEach(function(item) {
      var el = document.getElementById(item.id);
      if (el && typeof katex !== 'undefined') {
        try {
          el.innerHTML = katex.renderToString(item.tex, {
            throwOnError: false,
            displayMode: item.display || false
          });
        } catch(e) {
          el.textContent = item.tex;
        }
      } else if (el) {
        el.textContent = item.tex;
      }
    });
  }

  // 英文斜体：遍历文本节点，仅将 [a-zA-Z0-9]+ 包裹为 .en
  function wrapEnglish(root) {
    var walker = document.createTreeWalker(root, NodeFilter.SHOW_TEXT, null);
    var nodes = [];
    while (walker.nextNode()) nodes.push(walker.currentNode);
    nodes.forEach(function(node) {
      if (node.parentElement && node.parentElement.closest('.katex')) return;
      var text = node.textContent;
      if (!/[a-zA-Z0-9]/.test(text)) return;
      var span = document.createElement('span');
      span.innerHTML = text.replace(/([a-zA-Z0-9]+)/g, '<span class="en">$1</span>');
      node.parentNode.replaceChild(span, node);
    });
  }

  // 数据注入后调用：
  // 1. 用 renderPoint() 处理每个 points 字符串，写入 DOM
  // 2. 调用 flushKatex() 渲染公式
  // 3. 调用 wrapEnglish(document.body) 实现英文斜体
})();
```

**要点内容写法**：直接写纯文本（含 `**加粗**` 和 `$公式$`），通过 `renderPoint()` 解析为 HTML。不要手动写 `<strong>` 或公式占位符。

**数据注入方式**：在 HTML 中用 `<script>var data = {...};</script>` 注入 NoteData JSON，然后用 JS 遍历 sections 递归渲染。

---

## 1. 提纲笔记 (outline)

### 布局
```
┌─────────────────────────────────────────────┐
│ [蓝紫粉渐变背景]                              │
│  [π 提纲笔记]  结构清晰，层次分明，适合复习    │
│                              [书本SVG装饰]   │
│  ┌───────────────────────────────────────┐  │
│  │ 第1讲  一元一次方程的应用               │  │
│  │ ▓▓▓▓▓ 渐变装饰条                       │  │
│  │                                       │  │
│  │  ①(粉圆) 一、有理数基础复习             │  │
│  │    ● 数轴上表示数a的点...              │  │
│  │    ▌(色条) 绝对值                      │  │
│  │      ● 绝对值的几何意义...             │  │
│  │                                       │  │
│  │  ②(蓝圆) 二、一元一次方程含参问题       │  │
│  │    ● 先求已知方程解...                 │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
```

### 章节颜色循环（3 色）
```javascript
const OUTLINE_COLORS = [
  { numGrad:'linear-gradient(135deg,#F472B6,#EC4899)', dot:'#F472B6', tagBg:'#FCE7F3', tagColor:'#BE185D' },
  { numGrad:'linear-gradient(135deg,#60A5FA,#3B82F6)', dot:'#3B82F6', tagBg:'#DBEAFE', tagColor:'#1D4ED8' },
  { numGrad:'linear-gradient(135deg,#34D399,#10B981)', dot:'#10B981', tagBg:'#D1FAE5', tagColor:'#047857' }
];
```
第 i 个 level 1 章节使用 `OUTLINE_COLORS[i % 3]`。

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-8 relative overflow-hidden" style="background:linear-gradient(135deg,#EEF2FF 0%,#F5F3FF 50%,#FDF4FF 100%);">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-6">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#8B5CF6,#7C3AED);">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M4 9h16"/><path d="M4 9v2a8 8 0 0 0 16 0V9"/><path d="M9 9v10"/><path d="M15 9v10"/>
        </svg>
        提纲笔记
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">结构清晰，层次分明，适合复习</div>
    </div>

    <!-- 右上角装饰 SVG -->
    <div class="absolute top-6 right-8 opacity-80">
      <svg width="72" height="72" viewBox="0 0 72 72" fill="none">
        <path d="M14 18c0-3 2-5 5-5h16v38H19c-3 0-5-2-5-5V18z" fill="#A78BFA" stroke="#7C3AED" stroke-width="2"/>
        <path d="M58 18c0-3-2-5-5-5H37v38h16c3 0 5-2 5-5V18z" fill="#C4B5FD" stroke="#7C3AED" stroke-width="2"/>
        <path d="M37 13v38" stroke="#7C3AED" stroke-width="2"/>
        <path d="M20 22h10M20 28h10M42 22h10M42 28h10" stroke="#7C3AED" stroke-width="1.5" stroke-linecap="round" opacity="0.5"/>
      </svg>
    </div>

    <!-- 内容卡片 -->
    <div class="bg-white rounded-3xl shadow-lg p-8 pt-10 relative z-10">
      <h1 class="text-4xl font-bold text-gray-800 mb-2" id="note-title"></h1>
      <div class="h-1.5 w-64 rounded-full mb-8" style="background:linear-gradient(to right,#8B5CF6,#A78BFA);"></div>
      <div id="sections-container"></div>
    </div>
  </div>
</div>

<script>
var data = {/* NoteData JSON */};
document.getElementById('note-title').textContent = data.title;

function renderSection(section, colorIndex, depth) {
  var color = OUTLINE_COLORS[colorIndex % 3];
  var div = document.createElement('div');
  div.className = depth === 0 ? 'mb-7' : 'mb-4 ml-' + (depth === 1 ? '12' : '8');

  if (section.level === 1) {
    // level 1: 编号圆 + 大标题
    var header = document.createElement('div');
    header.className = 'flex items-center gap-3 mb-4';
    header.innerHTML = '<span class="flex items-center justify-center w-10 h-10 rounded-full text-white text-lg font-bold shadow-md" style="background:' + color.numGrad + '">' + (colorIndex + 1) + '</span>' +
      '<h2 class="text-2xl font-bold text-gray-800">' + section.heading + '</h2>';
    div.appendChild(header);
  } else if (section.level === 2) {
    // level 2: 色条 + 标题
    var header2 = document.createElement('div');
    header2.className = 'flex items-center gap-2.5 mb-3 mt-5';
    header2.innerHTML = '<span class="w-1.5 h-6 rounded-full" style="background:' + color.dot + '"></span>' +
      '<h3 class="text-xl font-bold text-gray-700">' + section.heading + '</h3>';
    div.appendChild(header2);
  } else {
    // level 3: 小圆点 + 标题
    var header3 = document.createElement('div');
    header3.className = 'flex items-center gap-2 mb-2 mt-3';
    header3.innerHTML = '<span class="w-2 h-2 rounded-full" style="background:' + color.dot + '"></span>' +
      '<h4 class="text-base font-bold text-gray-600">' + section.heading + '</h4>';
    div.appendChild(header3);
  }

  // points
  var pointsDiv = document.createElement('div');
  pointsDiv.className = (section.level === 1 ? 'ml-12' : 'ml-4') + ' space-y-3';
  section.points.forEach(function(p) {
    var pointDiv = document.createElement('div');
    pointDiv.className = 'flex items-start gap-2.5';
    pointDiv.innerHTML = '<span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:' + color.dot + ';"></span>' +
      '<div class="text-base text-gray-700 leading-relaxed">' + renderPoint(p) + '</div>';
    pointsDiv.appendChild(pointDiv);
  });
  div.appendChild(pointsDiv);

  // children
  if (section.children) {
    section.children.forEach(function(child) {
      div.appendChild(renderSection(child, colorIndex, depth + 1));
    });
  }
  return div;
}

var container = document.getElementById('sections-container');
data.sections.forEach(function(sec, i) {
  container.appendChild(renderSection(sec, i, 0));
});

flushKatex();
wrapEnglish(document.body);
</script>
</body>
```

---

## 2. 康奈尔笔记 (cornell)

### 布局
```
┌─────────────────────────────────────────┐
│ [暖黄背景]                                │
│ [π 康奈尔笔记]  左侧线索词右侧详细内容     │
│                         [折线SVG装饰]    │
│  ┌─────────────────────────────────────┐│
│  │ ○  第1讲 一元一次方程的应用    日期... ││
│  │ ○  ───────────红色分割线───────────  ││
│  │ ○  ┌────────┬────────────────────┐  ││
│  │ ○  │ 线索词  │ 详细内容             │  ││
│  │ ○  ├────────┼────────────────────┤  ││
│  │ ○  │[绝对值] │ ● 数轴上表示数a...   │  ││
│  │ ○  │[分类]   │ ● 整数和分数...      │  ││
│  │ ○  └────────┴────────────────────┘  ││
│  │    ┌────────┬────────────────────┐  ││
│  │    │ 总结    │ 本节课核心是...      │  ││
│  │    └────────┴────────────────────┘  ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
```

### 线索词标签颜色（6 色循环）
```javascript
const CORNELL_TAG_COLORS = [
  {bg:'#FCE7F3', color:'#BE185D'},  // 粉
  {bg:'#EDE9FE', color:'#5B21B6'},  // 紫
  {bg:'#F5D0FE', color:'#86198F'},  // 紫红
  {bg:'#D1FAE5', color:'#065F46'},  // 绿
  {bg:'#A7F3D0', color:'#065F46'},  // 浅绿
  {bg:'#CFFAFE', color:'#155E75'},  // 青
];
```

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-8 relative overflow-hidden" style="background:#FFF8EC;">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-6">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#FBBF24,#F59E0B);">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M4 9h16"/><path d="M4 9v2a8 8 0 0 0 16 0V9"/><path d="M9 9v10"/><path d="M15 9v10"/>
        </svg>
        康奈尔笔记
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">左侧线索词，右侧详细内容，方便复习回顾</div>
    </div>

    <!-- 右上角装饰 -->
    <div class="absolute top-8 right-10">
      <svg width="60" height="40" viewBox="0 0 60 40" fill="none">
        <path d="M10 30 L15 10 L20 25 L25 5 L30 20 L35 15 L40 28 L45 8 L50 22" stroke="#FCD34D" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
      </svg>
    </div>

    <!-- 活页本卡片 -->
    <div class="bg-white rounded-3xl shadow-lg relative pl-10">
      <!-- 左侧圆环 -->
      <div class="absolute left-3 top-0 bottom-0 flex flex-col justify-around py-8">
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
        <div class="w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100"></div>
      </div>

      <div class="p-7 pl-4">
        <!-- 表头 -->
        <div class="flex items-end justify-between mb-3 pb-3" style="border-bottom:3px solid #F87171;">
          <h1 class="text-3xl font-bold text-gray-800" id="note-title"></h1>
          <div class="text-right text-sm text-gray-600 space-y-1">
            <div>日期：<span id="note-date"></span></div>
            <div>课题：<span id="note-subject"></span></div>
          </div>
        </div>

        <!-- 表格 -->
        <table class="w-full border-collapse">
          <thead>
            <tr>
              <th class="w-[180px] px-4 py-3 text-center text-lg font-bold rounded-l-xl" style="background:#FEF3C7;color:#92400E;">线索词</th>
              <th class="px-4 py-3 text-center text-lg font-bold rounded-r-xl" style="background:#DBEAFE;color:#1D4ED8;">详细内容</th>
            </tr>
          </thead>
          <tbody id="cornell-rows"></tbody>
        </table>

        <!-- 总结栏 -->
        <div class="mt-4 flex rounded-2xl overflow-hidden" style="background:#FFF1F2;">
          <div class="w-[180px] flex items-center justify-center py-4 shrink-0" style="background:#FECDD3;">
            <span class="text-lg font-bold" style="color:#9F1239;">总结</span>
          </div>
          <div class="flex-1 p-4 text-base text-gray-700 leading-relaxed" id="note-summary"></div>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
var data = {/* NoteData JSON */};
document.getElementById('note-title').textContent = data.title;
document.getElementById('note-date').textContent = data.date || '';
document.getElementById('note-subject').textContent = data.subject;
document.getElementById('note-summary').innerHTML = renderPoint(data.summary);

// 展平所有 sections 的 points 为行，cues 作为线索词
var rows = [];
data.sections.forEach(function(sec) {
  var cues = (sec.cues || []).join(' / ') || sec.heading;
  // 收集本级 points
  sec.points.forEach(function(p) {
    rows.push({keyword: cues, content: p});
  });
  // 收集子节点 points
  function collectChildren(children) {
    if (!children) return;
    children.forEach(function(child) {
      child.points.forEach(function(p) {
        rows.push({keyword: child.heading, content: p});
      });
      collectChildren(child.children);
    });
  }
  collectChildren(sec.children);
});

var tbody = document.getElementById('cornell-rows');
rows.forEach(function(row, i) {
  var tagColor = CORNELL_TAG_COLORS[i % 6];
  var tr = document.createElement('tr');
  tr.className = 'border-b border-gray-200';
  tr.innerHTML = '<td class="px-4 py-3 align-middle">' +
    '<span class="block text-center text-base font-bold py-1.5 rounded-lg" style="background:' + tagColor.bg + ';color:' + tagColor.color + ';">' + row.keyword + '</span></td>' +
    '<td class="px-4 py-3 align-middle"><div class="flex items-start gap-2">' +
    '<span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:#F472B6;"></span>' +
    '<div class="text-base text-gray-700 leading-relaxed">' + renderPoint(row.content) + '</div></div></td>';
  tbody.appendChild(tr);
});

flushKatex();
wrapEnglish(document.body);
</script>
</body>
```

---

## 3. 知识点笔记 (knowledge)

### 布局
```
┌─────────────────────────────────────────┐
│ [绿渐变背景]                              │
│ [π 知识点笔记]  系统整理，突出重点         │
│                          [堆叠SVG装饰]   │
│  ┌─────────────────────────────────────┐│
│  │ 第1讲 一元一次方程的应用 · 知识点梳理  ││
│  │                                     ││
│  │ ┌──────────────┐ ┌──────────────┐  ││
│  │ │①(红) 绝对值  │ │②(蓝) 分类比较│  ││
│  │ │  数轴上...   │ │  · 有理数... │  ││
│  │ │              │ │  · 比较大小.. │  ││
│  │ └──────────────┘ └──────────────┘  ││
│  │ ┌──────────────┐ ┌──────────────┐  ││
│  │ │③(绿) 方程的解│ │④(紫) 解题步骤│  ││
│  │ │  使方程...   │ │  1)审题...   │  ││
│  │ │              │ │  2)列方程... │  ││
│  │ └──────────────┘ └──────────────┘  ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
```

### 卡片颜色（4 色）
```javascript
const KNOWLEDGE_CARD_COLORS = [
  {bg:'#FFF1F2', border:'#FECDD3', numGrad:'linear-gradient(135deg,#FB7185,#F43F5E)', titleColor:'#9F1239', dot:'#F43F5E'},
  {bg:'#EFF6FF', border:'#BFDBFE', numGrad:'linear-gradient(135deg,#60A5FA,#3B82F6)', titleColor:'#1E40AF', dot:'#3B82F6'},
  {bg:'#ECFDF5', border:'#A7F3D0', numGrad:'linear-gradient(135deg,#34D399,#10B981)', titleColor:'#065F46', dot:'#10B981'},
  {bg:'#F5F3FF', border:'#C4B5FD', numGrad:'linear-gradient(135deg,#A78BFA,#7C3AED)', titleColor:'#5B21B6', dot:'#7C3AED'}
];
```

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-8 relative overflow-hidden" style="background:linear-gradient(135deg,#ECFDF5 0%,#F0FDF4 50%,#F7FEE7 100%);">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-6">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#34D399,#10B981);">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M4 9h16"/><path d="M4 9v2a8 8 0 0 0 16 0V9"/><path d="M9 9v10"/><path d="M15 9v10"/>
        </svg>
        知识点笔记
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">系统整理，突出重点，便于记忆</div>
    </div>

    <!-- 右上角装饰 -->
    <div class="absolute top-6 right-8">
      <svg width="72" height="64" viewBox="0 0 72 64" fill="none">
        <rect x="8" y="36" width="52" height="14" rx="2" fill="#A7F3D0" stroke="#10B981" stroke-width="2"/>
        <rect x="12" y="22" width="52" height="14" rx="2" fill="#6EE7B7" stroke="#10B981" stroke-width="2"/>
        <rect x="16" y="8" width="52" height="14" rx="2" fill="#34D399" stroke="#059669" stroke-width="2"/>
        <rect x="20" y="12" width="8" height="6" rx="1" fill="white" opacity="0.6"/>
        <rect x="16" y="26" width="8" height="6" rx="1" fill="white" opacity="0.6"/>
        <rect x="12" y="40" width="8" height="6" rx="1" fill="white" opacity="0.6"/>
      </svg>
    </div>

    <!-- 内容卡片 -->
    <div class="bg-white rounded-3xl shadow-lg p-8 relative z-10">
      <h1 class="text-3xl font-bold text-gray-800 mb-6" id="note-title"></h1>
      <div class="grid grid-cols-2 gap-5" id="cards-container"></div>
    </div>
  </div>
</div>

<script>
var data = {/* NoteData JSON */};
document.getElementById('note-title').textContent = data.title + ' · 知识点梳理';

// 取 level 1 章节作为卡片（最多4张）
var cards = data.sections.filter(function(s) { return s.level === 1; }).slice(0, 4);

var container = document.getElementById('cards-container');
cards.forEach(function(sec, i) {
  var color = KNOWLEDGE_CARD_COLORS[i % 4];
  var card = document.createElement('div');
  card.className = 'rounded-2xl p-5';
  card.style.background = color.bg;
  card.style.border = '2px solid ' + color.border;

  // 收集所有 points（本级 + 子节点）
  var allPoints = sec.points.slice();
  function collectChildren(children) {
    if (!children) return;
    children.forEach(function(child) {
      allPoints = allPoints.concat(child.points);
      collectChildren(child.children);
    });
  }
  collectChildren(sec.children);

  var pointsHtml = allPoints.map(function(p) {
    return '<div class="flex items-start gap-2">' +
      '<span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:' + color.dot + ';"></span>' +
      '<div class="text-base text-gray-700 leading-relaxed">' + renderPoint(p) + '</div></div>';
  }).join('');

  card.innerHTML = '<div class="flex items-center gap-3 mb-4">' +
    '<span class="flex items-center justify-center w-9 h-9 rounded-full text-white text-base font-bold shadow" style="background:' + color.numGrad + ';">' + (i + 1) + '</span>' +
    '<h3 class="text-xl font-bold" style="color:' + color.titleColor + ';">' + sec.heading + '</h3></div>' +
    '<div class="space-y-3 pl-1">' + pointsHtml + '</div>';
  container.appendChild(card);
});

flushKatex();
wrapEnglish(document.body);
</script>
</body>
```

---

## 输出图片规格
- 宽度：1080px（render_notes.py 的 `--width` 参数）
- 高度：自适应内容（全页截图 `full_page=True`）
- 格式：PNG
- 文件名：`{title}_{模板名}.png`

## 渲染等待
- 所有模板默认 1500ms
- 公式较多时增加 `--wait 2500`
- KaTeX 本地引用，无需等待 CDN 加载
