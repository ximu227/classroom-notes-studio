# 课堂笔记工作室 · 设计系统

## 气质
「教师备课手账」—— 温暖、活泼、专业、清晰。3 种模板各有主题色，卡片化布局，适合打印和屏幕阅读。

## 技术栈
- **Tailwind CSS**（CDN）：原子化样式
- **KaTeX**（本地引用 `notes_output/katex/`）：LaTeX 公式渲染
- **通用渲染脚本**：`renderPoint()`（解析 `$...$` 公式）、`flushKatex()`（批量渲染）、`wrapEnglish()`（英文斜体）

> 注意：要点内容直接写 HTML，不使用 marked.js 等 Markdown 渲染库。KaTeX 必须本地引用，CDN 版在 Playwright file:// 页面中渲染不生效。

## 字体规范（强制）

```css
font-family: 'Times New Roman', 'Microsoft YaHei', serif;
```

- **汉字**：Microsoft YaHei（微软雅黑），**正体**（不斜体）
- **英文和数字**：Times New Roman，**斜体**（通过 JS `wrapEnglish()` 实现）
- **数学公式**：KaTeX 默认字体
- **实现原理**：CSS 按 font-family 顺序匹配，英文数字命中 Times New Roman，中文回退到微软雅黑；JS 遍历文本节点仅将 `[a-zA-Z0-9]+` 包裹为 `<span class="en">`，CSS `.en { font-style: italic }`，跳过 `.katex` 内容

## 英文斜体实现（强制）

所有模板必须包含以下 JS 和 CSS：

```css
.en { font-style: italic; }
```

```javascript
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
```

在页面加载完成后调用 `wrapEnglish(document.body)`。

## π 图标（标签头）

所有模板顶部标签头的编号圆统一替换为 π（圆周率）SVG 图标：

```html
<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
  <path d="M4 9h16"/>
  <path d="M4 9v2a8 8 0 0 0 16 0V9"/>
  <path d="M9 9v10"/>
  <path d="M15 9v10"/>
</svg>
```

标签头结构：`渐变圆角标签（含 π 图标 + 模板名） + 白色半透明描述胶囊`

## 3 种模板主题色

| 模板 | 主题色 | 背景 | 编号色 |
|---|---|---|---|
| 提纲笔记 | 紫 #7C3AED / 粉 #F472B6 | 蓝紫粉渐变 #EEF2FF→#F5F3FF→#FDF4FF | 粉→蓝→绿 循环 |
| 康奈尔笔记 | 红 #F87171 / 黄 #FBBF24 | 暖黄 #FFF8EC | 线索词彩色标签（6色循环） |
| 知识点笔记 | 绿 #10B981 | 绿渐变 #ECFDF5→#F0FDF4→#F7FEE7 | 红/蓝/绿/紫 循环 |

## 通用组件

### 模板标签头
每个模板顶部有一个标签头：
```
渐变圆角标签（含 π 图标 + 模板名） + 白色半透明描述胶囊
```
- 标签：`px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md`
- π 图标：`w-5 h-5`（SVG，stroke currentColor）
- 描述：`px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm`

### 装饰 SVG 图标
每个模板右上角有一个主题相关的 SVG 装饰图标，opacity 0.8，绝对定位。

### 内容卡片
- 白色背景 `bg-white`，`rounded-3xl shadow-lg`
- 内边距 `p-8`
- 标题：`text-3xl/4xl font-bold text-gray-800`
- 装饰条：`h-1.5 w-64 rounded-full` 渐变

### 章节编号圆（提纲笔记 level 1）
- `w-10 h-10 rounded-full text-white text-lg font-bold shadow-md`
- 渐变背景，每章颜色循环（粉→蓝→绿）

### 二级标题色条（提纲笔记 level 2）
- `w-1.5 h-6 rounded-full`，与章节同色
- 标题 `text-xl font-bold text-gray-700`

### 要点圆点
- `w-2.5 h-2.5 rounded-full`
- 与章节同色

### 关键词标签
- `px-2 py-0.5 rounded-md font-semibold`
- 浅色背景 + 深色文字

## 各模板详细配色

### 1. 提纲笔记
- 页面背景：`linear-gradient(135deg,#EEF2FF 0%,#F5F3FF 50%,#FDF4FF 100%)`
- 标签头渐变：`linear-gradient(135deg,#8B5CF6,#7C3AED)`
- 章节1（粉）：编号 `#F472B6→#EC4899`，圆点 `#F472B6`，标签 `bg:#FCE7F3 color:#BE185D`
- 章节2（蓝）：编号 `#60A5FA→#3B82F6`，圆点 `#3B82F6`，标签 `bg:#DBEAFE color:#1D4ED8`
- 章节3（绿）：编号 `#34D399→#10B981`，圆点 `#10B981`，标签 `bg:#D1FAE5 color:#047857`
- 装饰条：`linear-gradient(to right,#8B5CF6,#A78BFA)`
- level 2 缩进：`ml-12`，level 3 缩进：`ml-8`

### 2. 康奈尔笔记
- 页面背景：`#FFF8EC`
- 标签头渐变：`linear-gradient(135deg,#FBBF24,#F59E0B)`
- 活页本风格：左侧 `pl-10`，左侧圆环 `w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100`
- 表头分割线：`border-bottom:3px solid #F87171`
- 表格表头：线索词列 `bg:#FEF3C7 color:#92400E`，内容列 `bg:#DBEAFE color:#1D4ED8`
- 线索词标签：彩色背景圆角，每词不同色（粉/紫/紫红/绿/浅绿/青 6 色循环）
- 总结栏：左侧 `bg:#FECDD3 color:#9F1239` 固定宽，右侧 `bg:#FFF1F2` 内容

### 3. 知识点笔记
- 页面背景：`linear-gradient(135deg,#ECFDF5 0%,#F0FDF4 50%,#F7FEE7 100%)`
- 标签头渐变：`linear-gradient(135deg,#34D399,#10B981)`
- 2 列网格 `grid grid-cols-2 gap-5`
- 卡片1（红）：`bg:#FFF1F2 border:2px solid #FECDD3`，编号 `#FB7185→#F43F5E`，标题 `color:#9F1239`
- 卡片2（蓝）：`bg:#EFF6FF border:2px solid #BFDBFE`，编号 `#60A5FA→#3B82F6`，标题 `color:#1E40AF`
- 卡片3（绿）：`bg:#ECFDF5 border:2px solid #A7F3D0`，编号 `#34D399→#10B981`，标题 `color:#065F46`
- 卡片4（紫）：`bg:#F5F3FF border:2px solid #C4B5FD`，编号 `#A78BFA→#7C3AED`，标题 `color:#5B21B6`
- 步骤编号：`w-6 h-6 rounded-full bg-[#7C3AED] text-white text-xs`

## 设计禁忌
- ❌ 出现"AI 生成"字样
- ❌ 使用 font-mono / ui-monospace 覆盖数字字体
- ❌ 全局 `font-style: italic`（会导致中文也斜体，必须用 JS 仅包裹英文）
- ❌ 蓝紫渐变 + 圆角卡片的单调 AI 默认审美（每种模板要有鲜明主题色）
- ❌ 大量 emoji 作为图标（使用 SVG 线性图标或纯 CSS）
- ❌ CDN 版 KaTeX（Playwright file:// 页面中渲染不生效，必须本地引用）
