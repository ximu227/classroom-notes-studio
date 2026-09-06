# 课堂笔记工作室 · 5 种模板 HTML/CSS 完整规范

## 通用技术栈（所有模板共用）

每个模板生成**独立的完整 HTML 文件**。

### 基础模板 head（提纲/思维导图/康奈尔/知识点 共用）

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{title}</title>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
<script src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
<style>
  body { font-family:'Times New Roman','Microsoft YaHei',serif; margin:0; }
  .katex { font-size: 1em; }
</style>
</head>
```

### Excalidraw 模板 head（额外引入 React + Excalidraw）

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{title}</title>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
<script src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/react@18.2.0/umd/react.production.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/react-dom@18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@excalidraw/excalidraw@0.14.2/dist/excalidraw.production.min.js"></script>
<style>
  body { font-family:'Times New Roman','Microsoft YaHei',serif; margin:0; }
  #excalidraw-container { width:100%; height:70vh; min-height:500px; border-radius:1rem; overflow:hidden; position:relative; }
  #excalidraw-root { width:100%; height:100%; }
  .excalidraw-loading { position:absolute; inset:0; display:flex; flex-direction:column; align-items:center; justify-content:center; background:#fdf6e3; z-index:10; }
  .excalidraw-loading.hide { display:none; }
</style>
</head>
```

### 通用公式渲染脚本（放在 </body> 前）

要点内容中的 LaTeX 公式用 `<span id="formula-xxx"></span>` 占位，然后通过 JS 渲染：

```javascript
(function(){
  function renderKatex(id, tex) {
    var el = document.getElementById(id);
    if (el && typeof katex !== 'undefined') {
      try { el.innerHTML = katex.renderToString(tex, {throwOnError:false}); }
      catch(e){ el.textContent = tex; }
    } else if (el) { el.textContent = tex; }
  }
  // 为每个公式调用 renderKatex('id', 'latex_code')
})();
```

**要点内容写法**：直接写 HTML，关键词用 `<span class="px-2 py-0.5 rounded-md font-semibold" style="background:...;color:...">关键词：</span>` 包裹，公式用 `<span id="f1"></span>` 占位。

---

## 1. 提纲笔记 (outline)

### 布局
```
┌─────────────────────────────────────────────┐
│ [蓝紫粉渐变背景]                              │
│  [1 提纲笔记]  结构清晰，层次分明，适合复习    │
│                              [书本SVG装饰]   │
│  ┌───────────────────────────────────────┐  │
│  │ 第1讲  一元一次方程的应用               │  │
│  │ ▓▓▓▓▓ 渐变装饰条                       │  │
│  │                                       │  │
│  │  ①(粉圆) 一、有理数基础复习             │  │
│  │    ● [绝对值：标签] 数轴上表示数a...    │  │
│  │    ● [有理数分类：标签] 整数和分数...   │  │
│  │                                       │  │
│  │  ②(蓝圆) 二、一元一次方程含参问题       │  │
│  │    ● [核心思路：标签] 先求已知方程解... │  │
│  │                                       │  │
│  │  ③(绿圆) 三、一元一次方程实际应用       │  │
│  │    ● [列方程核心：标签] 找等量关系...   │  │
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
第 i 章使用 `OUTLINE_COLORS[i % 3]`。

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-8 relative overflow-hidden" style="background:linear-gradient(135deg,#EEF2FF 0%,#F5F3FF 50%,#FDF4FF 100%);">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-6">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#8B5CF6,#7C3AED);">
        <span class="flex items-center justify-center w-7 h-7 rounded-full bg-white/25 text-base font-bold">1</span>
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
      <h1 class="text-4xl font-bold text-gray-800 mb-2">{title}</h1>
      <div class="h-1.5 w-64 rounded-full mb-8" style="background:linear-gradient(to right,#8B5CF6,#A78BFA);"></div>

      {#each sections}
      <div class="mb-7">
        <div class="flex items-center gap-3 mb-4">
          <span class="flex items-center justify-center w-10 h-10 rounded-full text-white text-lg font-bold shadow-md" style="background:{color.numGrad}">{index}</span>
          <h2 class="text-2xl font-bold text-gray-800">{heading}</h2>
        </div>
        <div class="ml-12 space-y-3">
          {#each points}
          <div class="flex items-start gap-2.5">
            <span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:{color.dot};"></span>
            <div class="text-base text-gray-700 leading-relaxed">
              {#if keyword}<span class="px-2 py-0.5 rounded-md font-semibold" style="background:{color.tagBg};color:{color.tagColor};">{keyword}：</span>{/if}
              {content}
            </div>
          </div>
          {/each}
        </div>
      </div>
      {/each}

    </div>
  </div>
</div>
</body>
```

---

## 2. 思维导图 (mindmap)

### 布局
```
┌─────────────────────────────────────────┐
│ [暖黄背景 #FFF8EC]                       │
│ [2 思维导图笔记]  图文结合，逻辑清晰       │
│                          [灯泡SVG装饰]   │
│  ┌─────────────────────────────────────┐│
│  │  [SVG贝塞尔曲线连接线]               ││
│  │                                     ││
│  │ [红分支卡片]      [蓝分支卡片]       ││
│  │ 一、有理数基础      二、含参问题      ││
│  │ · 绝对值意义        · 核心思路       ││
│  │ · 有理数分类        · 方程的解       ││
│  │ · 比较大小                          ││
│  │                                     ││
│  │         [紫色椭圆中心]               ││
│  │         第1讲 一元一次方程            ││
│  │              的应用                  ││
│  │                                     ││
│  │      [绿分支卡片]                    ││
│  │      三、实际应用                    ││
│  │      · 等量关系                      ││
│  │      · 验证解                        ││
│  │                 [黄色便签] 化实际     ││
│  │                 问题为方程是关键！    ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
```

### 分支颜色
- 左分支（红）：bg `#FFF1F2`，border `#FDA4AF`，标题 bg `#FECDD3` color `#9F1239`，子项 bg `#FEE2E2`
- 右分支（蓝）：bg `#EFF6FF`，border `#93C5FD`，标题 bg `#BFDBFE` color `#1E40AF`，子项 bg `#DBEAFE`
- 底部分支（绿）：bg `#ECFDF5`，border `#6EE7B7`，标题 bg `#A7F3D0` color `#065F46`，子项 bg `#D1FAE5`

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-8 relative overflow-hidden" style="background:#FFF8EC;">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-6">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#FBBF24,#F59E0B);">
        <span class="flex items-center justify-center w-7 h-7 rounded-full bg-white/25 text-base font-bold">2</span>
        思维导图笔记
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">图文结合，逻辑清晰，适合梳理知识</div>
    </div>

    <!-- 右上角装饰 -->
    <div class="absolute top-6 right-8">
      <svg width="56" height="64" viewBox="0 0 56 64" fill="none">
        <path d="M28 4C17 4 8 12 8 22c0 6 3 10 6 13v5h28v-5c3-3 6-7 6-13 0-10-9-18-20-18z" fill="#FDE68A" stroke="#F59E0B" stroke-width="2"/>
        <rect x="20" y="44" width="16" height="4" rx="1" fill="#F59E0B"/>
        <rect x="22" y="49" width="12" height="3" rx="1" fill="#F59E0B"/>
        <rect x="24" y="53" width="8" height="3" rx="1" fill="#F59E0B"/>
        <path d="M8 14l-4-2M48 14l4-2M4 24H0M56 24h-4M10 34l-3 3M46 34l3 3" stroke="#F59E0B" stroke-width="2" stroke-linecap="round"/>
      </svg>
    </div>

    <!-- 思维导图画布 -->
    <div class="relative bg-white/60 rounded-3xl p-6" style="min-height:560px;">

      <!-- 连接线 SVG -->
      <svg class="absolute inset-0 w-full h-full pointer-events-none" style="z-index:1;" viewBox="0 0 900 540" preserveAspectRatio="none">
        <path d="M 450 270 Q 320 200 230 160" stroke="#F472B6" stroke-width="4" fill="none" stroke-linecap="round"/>
        <path d="M 450 270 Q 580 200 670 160" stroke="#60A5FA" stroke-width="4" fill="none" stroke-linecap="round"/>
        <path d="M 450 270 Q 450 370 450 430" stroke="#34D399" stroke-width="4" fill="none" stroke-linecap="round"/>
      </svg>

      <!-- 中心节点 -->
      <div class="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 z-10">
        <div class="px-8 py-6 rounded-[50%] text-white text-center shadow-xl" style="background:linear-gradient(135deg,#8B5CF6,#7C3AED); min-width:200px; min-height:140px; display:flex; flex-direction:column; justify-content:center; align-items:center;">
          <div class="text-2xl font-bold">第1讲</div>
          <div class="text-xl font-bold mt-1">一元一次方程</div>
          <div class="text-xl font-bold">的应用</div>
        </div>
      </div>

      <!-- 左分支（红） -->
      <div class="absolute left-4 top-8 z-10" style="max-width:260px;">
        <div class="rounded-2xl p-4 shadow-md" style="background:#FFF1F2; border:3px solid #FDA4AF;">
          <div class="text-lg font-bold text-center mb-3 px-3 py-1.5 rounded-xl" style="background:#FECDD3;color:#9F1239;">{branch1.title}</div>
          <div class="space-y-2">
            {#each branch1.items}<div class="text-center text-base py-2 rounded-xl" style="background:#FEE2E2;color:#9F1239;">{item}</div>{/each}
          </div>
        </div>
      </div>

      <!-- 右分支（蓝） -->
      <div class="absolute right-4 top-8 z-10" style="max-width:260px;">
        <div class="rounded-2xl p-4 shadow-md" style="background:#EFF6FF; border:3px solid #93C5FD;">
          <div class="text-lg font-bold text-center mb-3 px-3 py-1.5 rounded-xl" style="background:#BFDBFE;color:#1E40AF;">{branch2.title}</div>
          <div class="space-y-2">
            {#each branch2.items}<div class="text-center text-base py-2 rounded-xl" style="background:#DBEAFE;color:#1E40AF;">{item}</div>{/each}
          </div>
        </div>
      </div>

      <!-- 底部分支（绿） -->
      <div class="absolute left-1/2 -translate-x-1/2 bottom-6 z-10" style="max-width:340px;">
        <div class="rounded-2xl p-4 shadow-md" style="background:#ECFDF5; border:3px solid #6EE7B7;">
          <div class="text-lg font-bold text-center mb-3 px-3 py-1.5 rounded-xl" style="background:#A7F3D0;color:#065F46;">{branch3.title}</div>
          <div class="space-y-2">
            {#each branch3.items}<div class="text-center text-base py-2 rounded-xl" style="background:#D1FAE5;color:#065F46;">{item}</div>{/each}
          </div>
        </div>
      </div>

      <!-- 右下角便签 -->
      {#if note}
      <div class="absolute right-6 bottom-8 z-20" style="transform:rotate(6deg);">
        <div class="px-5 py-4 shadow-lg" style="background:#FEF3C7; width:160px; min-height:130px; display:flex; flex-direction:column; justify-content:center; align-items:center;">
          <div class="text-lg font-bold text-center leading-snug" style="color:#92400E;">{note}</div>
          <div class="w-16 h-1 rounded-full mt-2" style="background:#FCD34D;"></div>
        </div>
      </div>
      {/if}

    </div>
  </div>
</div>
</body>
```

---

## 3. 康奈尔笔记 (cornell)

### 布局
```
┌─────────────────────────────────────────┐
│ [暖黄背景]                                │
│ [3 康奈尔笔记]  左侧关键词右侧详细内容     │
│                         [折线SVG装饰]    │
│  ┌─────────────────────────────────────┐│
│  │ ○  第1讲 一元一次方程的应用    日期... ││
│  │ ○  ───────────红色分割线───────────  ││
│  │ ○  ┌────────┬────────────────────┐  ││
│  │ ○  │ 关键词  │ 详细内容             │  ││
│  │ ○  ├────────┼────────────────────┤  ││
│  │ ○  │[绝对值] │ ● 数轴上表示数a...   │  ││
│  │ ○  │[分类]   │ ● 整数和分数...      │  ││
│  │ ○  │[比较]   │ ● 负数<0<正数...     │  ││
│  │ ○  └────────┴────────────────────┘  ││
│  │    ┌────────┬────────────────────┐  ││
│  │    │ 总结    │ 本节课核心是...      │  ││
│  │    └────────┴────────────────────┘  ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
```

### 关键词标签颜色（6 色循环）
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
        <span class="flex items-center justify-center w-7 h-7 rounded-full bg-white/25 text-base font-bold">3</span>
        康奈尔笔记
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">左侧关键词，右侧详细内容，方便复习回顾</div>
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
          <h1 class="text-3xl font-bold text-gray-800">{title}</h1>
          <div class="text-right text-sm text-gray-600 space-y-1">
            <div>日期：<span>{date}</span></div>
            <div>课题：<span>{subject}</span></div>
          </div>
        </div>

        <!-- 表格 -->
        <table class="w-full border-collapse">
          <thead>
            <tr>
              <th class="w-[180px] px-4 py-3 text-center text-lg font-bold rounded-l-xl" style="background:#FEF3C7;color:#92400E;">关键词</th>
              <th class="px-4 py-3 text-center text-lg font-bold rounded-r-xl" style="background:#DBEAFE;color:#1D4ED8;">详细内容</th>
            </tr>
          </thead>
          <tbody>
            {#each rows}
            <tr class="border-b border-gray-200">
              <td class="px-4 py-3 align-middle">
                <span class="block text-center text-base font-bold py-1.5 rounded-lg" style="background:{tagColor.bg};color:{tagColor.color};">{keyword}</span>
              </td>
              <td class="px-4 py-3 align-middle">
                <div class="flex items-start gap-2">
                  <span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:#F472B6;"></span>
                  <div class="text-base text-gray-700 leading-relaxed">{content}</div>
                </div>
              </td>
            </tr>
            {/each}
          </tbody>
        </table>

        <!-- 总结栏 -->
        <div class="mt-4 flex rounded-2xl overflow-hidden" style="background:#FFF1F2;">
          <div class="w-[180px] flex items-center justify-center py-4 shrink-0" style="background:#FECDD3;">
            <span class="text-lg font-bold" style="color:#9F1239;">总结</span>
          </div>
          <div class="flex-1 p-4 text-base text-gray-700 leading-relaxed">{summary}</div>
        </div>
      </div>
    </div>
  </div>
</div>
</body>
```

---

## 4. 知识点笔记 (knowledge)

### 布局
```
┌─────────────────────────────────────────┐
│ [绿渐变背景]                              │
│ [4 知识点笔记]  系统整理，突出重点         │
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
        <span class="flex items-center justify-center w-7 h-7 rounded-full bg-white/25 text-base font-bold">4</span>
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
      <h1 class="text-3xl font-bold text-gray-800 mb-6">{title} · 知识点梳理</h1>

      <div class="grid grid-cols-2 gap-5">
        {#each cards}
        <div class="rounded-2xl p-5" style="background:{color.bg}; border:2px solid {color.border};">
          <div class="flex items-center gap-3 mb-4">
            <span class="flex items-center justify-center w-9 h-9 rounded-full text-white text-base font-bold shadow" style="background:{color.numGrad};">{index}</span>
            <h3 class="text-xl font-bold" style="color:{color.titleColor};">{title}</h3>
          </div>

          {#if type === 'text'}
          <div class="text-base text-gray-700 leading-relaxed pl-1">{content}</div>
          {/if}

          {#if type === 'list'}
          <div class="space-y-3 pl-1">
            {#each items}
            <div class="flex items-start gap-2">
              <span class="mt-2 w-2.5 h-2.5 rounded-full shrink-0" style="background:{color.dot};"></span>
              <div class="text-base text-gray-700 leading-relaxed">{item}</div>
            </div>
            {/each}
          </div>
          {/if}

          {#if type === 'steps'}
          <div class="space-y-2.5 pl-1">
            {#each steps}
            <div class="flex items-start gap-2.5">
              <span class="flex items-center justify-center w-6 h-6 rounded-full text-white text-xs font-bold shrink-0 mt-0.5" style="background:#7C3AED;">{stepIndex}</span>
              <div class="text-base text-gray-700 leading-relaxed">{step}</div>
            </div>
            {/each}
          </div>
          {/if}
        </div>
        {/each}
      </div>

    </div>
  </div>
</div>
</body>
```

---

## 5. Excalidraw 手绘导图 (excalidraw)

### 布局
```
┌─────────────────────────────────────────┐
│ [米黄渐变背景]                            │
│ [5 Excalidraw手绘导图]  可交互编辑        │
│  ┌─────────────────────────────────────┐│
│  │                                     ││
│  │   [Excalidraw 交互式画布]            ││
│  │   中心椭圆 + 3个分支矩形 + 箭头       ││
│  │   黄色便签                           ││
│  │                                     ││
│  └─────────────────────────────────────┘│
│  提示：可拖拽移动画布、滚轮缩放...         │
└─────────────────────────────────────────┘
```

### HTML 结构（body 部分）

```html
<body class="min-h-screen bg-gray-100">
<div class="max-w-5xl mx-auto px-4 py-8">
  <div class="rounded-3xl p-6 relative overflow-hidden" style="background:linear-gradient(135deg,#fdf6e3 0%,#fef3c7 100%);">

    <!-- 标签头 -->
    <div class="flex items-center gap-3 mb-4">
      <div class="flex items-center gap-2 px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md" style="background:linear-gradient(135deg,#f59e0b,#d97706);">
        <span class="flex items-center justify-center w-7 h-7 rounded-full bg-white/25 text-base font-bold">5</span>
        Excalidraw 手绘导图
      </div>
      <div class="px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm">开源手绘风格白板，可交互编辑</div>
    </div>

    <!-- Excalidraw 容器 -->
    <div id="excalidraw-container">
      <div class="excalidraw-loading" id="excalidraw-loading">
        <div style="font-size:18px;color:#7c3aed;margin-bottom:8px;">正在加载 Excalidraw 手绘思维导图…</div>
        <div style="font-size:13px;color:#999;" id="excalidraw-status">初始化</div>
      </div>
      <div id="excalidraw-root"></div>
    </div>

    <p class="text-center text-xs text-gray-400 mt-3">提示：可拖拽移动画布、滚轮缩放、点击元素编辑，左上角菜单支持导出图片</p>
  </div>
</div>

<script>
(function(){
  function setStatus(s) { var el = document.getElementById('excalidraw-status'); if(el) el.textContent = s; }

  var _seed = 2000, _idx = 100;
  function nextSeed() { return ++_seed; }
  function nextIndex() { return "b" + (++_idx); }
  function uid() { return "ex_" + Math.random().toString(36).slice(2, 11); }

  function baseEl(type, x, y, w, h, opts) {
    return {
      id: opts.id || uid(), type, x, y, width: w, height: h, angle: 0,
      strokeColor: opts.strokeColor || "#1e1e1e",
      backgroundColor: opts.backgroundColor || "transparent",
      fillStyle: opts.fillStyle || "solid",
      strokeWidth: opts.strokeWidth || 2,
      strokeStyle: opts.strokeStyle || "solid",
      roughness: opts.roughness != null ? opts.roughness : 1,
      opacity: opts.opacity != null ? opts.opacity : 100,
      groupIds: opts.groupIds || [], frameId: null,
      index: opts.index || nextIndex(), seed: opts.seed || nextSeed(),
      version: 1, versionNonce: nextSeed(), isDeleted: false,
      boundElements: opts.boundElements || null, updated: 1, link: null, locked: false
    };
  }
  function makeRect(x, y, w, h, opts) {
    var el = baseEl("rectangle", x, y, w, h, opts);
    if (opts.round !== false) el.roundness = { type: 3 };
    return el;
  }
  function makeEllipse(x, y, w, h, opts) { return baseEl("ellipse", x, y, w, h, opts); }
  function makeText(x, y, text, opts) {
    var fontSize = opts.fontSize || 16;
    var lines = text.split("\n");
    var maxLen = 0;
    for (var i = 0; i < lines.length; i++) { if (lines[i].length > maxLen) maxLen = lines[i].length; }
    var w = opts.width || maxLen * fontSize * 0.55 + 20;
    var h = lines.length * fontSize * 1.3 + 10;
    var el = baseEl("text", x, y, w, h, opts);
    el.text = text; el.fontSize = fontSize; el.fontFamily = opts.fontFamily || 1;
    el.textAlign = opts.textAlign || "left"; el.verticalAlign = opts.verticalAlign || "top";
    el.containerId = opts.containerId || null; el.originalType = "text";
    el.lineHeight = 1.25; el.baseline = fontSize;
    return el;
  }
  function makeArrow(x1, y1, x2, y2, opts) {
    var dx = x2 - x1, dy = y2 - y1;
    var el = baseEl("arrow", x1, y1, Math.max(Math.abs(dx), 1), Math.max(Math.abs(dy), 1), opts);
    el.points = [[0, 0], [dx, dy]]; el.lastCommittedPoint = null;
    el.startBinding = opts.startBinding || null; el.endBinding = opts.endBinding || null;
    el.startArrowhead = null; el.endArrowhead = "arrow"; el.elbowed = false;
    return el;
  }

  function buildElements() {
    setStatus("构造场景数据");
    var elements = [];

    // 中心节点
    var center = makeEllipse(400, 260, 200, 140, {
      strokeColor: "#7c3aed", backgroundColor: "#ddd6fe", fillStyle: "solid", strokeWidth: 3
    });
    var centerText = makeText(420, 290, "{centerTitle}", {
      fontSize: 19, textAlign: "center", verticalAlign: "middle",
      containerId: center.id, width: 160, strokeColor: "#4c1d95"
    });
    center.boundElements = [{ type: "text", id: centerText.id }];

    // 分支1（左上红）
    var b1 = makeRect(30, 40, 280, 230, {
      strokeColor: "#e03131", backgroundColor: "#ffe3e3", fillStyle: "solid", strokeWidth: 2.5
    });
    var b1Text = makeText(50, 60, "{branch1Text}", {
      fontSize: 15, containerId: b1.id, width: 240, strokeColor: "#5c0000"
    });
    b1.boundElements = [{ type: "text", id: b1Text.id }];

    // 分支2（右上蓝）
    var b2 = makeRect(690, 40, 280, 190, {
      strokeColor: "#1971c2", backgroundColor: "#d0ebff", fillStyle: "solid", strokeWidth: 2.5
    });
    var b2Text = makeText(710, 60, "{branch2Text}", {
      fontSize: 15, containerId: b2.id, width: 240, strokeColor: "#003a66"
    });
    b2.boundElements = [{ type: "text", id: b2Text.id }];

    // 分支3（底部绿）
    var b3 = makeRect(350, 520, 300, 170, {
      strokeColor: "#2f9e44", backgroundColor: "#d3f9d8", fillStyle: "solid", strokeWidth: 2.5
    });
    var b3Text = makeText(370, 540, "{branch3Text}", {
      fontSize: 15, containerId: b3.id, width: 260, strokeColor: "#0b3d14"
    });
    b3.boundElements = [{ type: "text", id: b3Text.id }];

    // 便签（黄色）
    var note = makeRect(770, 480, 170, 130, {
      strokeColor: "#f08c00", backgroundColor: "#ffec99", fillStyle: "solid",
      strokeWidth: 2, roughness: 2, round: false
    });
    var noteText = makeText(785, 500, "{noteText}", {
      fontSize: 16, textAlign: "center", containerId: note.id, width: 140, strokeColor: "#8a4b00"
    });
    note.boundElements = [{ type: "text", id: noteText.id }];

    // 箭头
    var arrow1 = makeArrow(400, 310, 310, 155, {
      strokeColor: "#e03131", strokeWidth: 2.5,
      startBinding: { elementId: center.id, focus: -0.4, gap: 2 },
      endBinding: { elementId: b1.id, focus: 0.4, gap: 2 }
    });
    var arrow2 = makeArrow(600, 310, 690, 135, {
      strokeColor: "#1971c2", strokeWidth: 2.5,
      startBinding: { elementId: center.id, focus: 0.4, gap: 2 },
      endBinding: { elementId: b2.id, focus: -0.4, gap: 2 }
    });
    var arrow3 = makeArrow(500, 400, 500, 520, {
      strokeColor: "#2f9e44", strokeWidth: 2.5,
      startBinding: { elementId: center.id, focus: 0.5, gap: 2 },
      endBinding: { elementId: b3.id, focus: -0.5, gap: 2 }
    });

    elements.push(arrow1, arrow2, arrow3);
    elements.push(center, b1, b2, b3, note);
    elements.push(centerText, b1Text, b2Text, b3Text, noteText);
    return elements;
  }

  function renderExcalidraw() {
    if (typeof React === 'undefined') { setStatus("错误: React 未加载"); return; }
    if (typeof ReactDOM === 'undefined') { setStatus("错误: ReactDOM 未加载"); return; }
    if (typeof ExcalidrawLib === 'undefined' || !ExcalidrawLib.Excalidraw) {
      setStatus("错误: Excalidraw 未加载"); return;
    }
    setStatus("渲染中");
    try {
      var elements = buildElements();
      var root = ReactDOM.createRoot(document.getElementById('excalidraw-root'));
      root.render(
        React.createElement(ExcalidrawLib.Excalidraw, {
          initialData: {
            elements: elements,
            appState: {
              viewBackgroundColor: "#fdf6e3",
              scrollX: 80,
              scrollY: 60,
              zoom: { value: 0.85 }
            }
          }
        })
      );
      setTimeout(function() {
        var loading = document.getElementById('excalidraw-loading');
        if (loading) loading.classList.add('hide');
      }, 600);
      setStatus("完成");
    } catch(e) {
      setStatus("渲染错误: " + e.message);
      console.error(e);
    }
  }

  if (document.readyState === 'complete') {
    renderExcalidraw();
  } else {
    window.addEventListener('load', renderExcalidraw);
  }
})();
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
- 普通模板（outline/mindmap/cornell/knowledge）：默认 1500ms
- Excalidraw 模板：需要等待 React + Excalidraw 加载和渲染，建议 `--wait 4000`
- 公式较多时增加 `--wait 2500`
