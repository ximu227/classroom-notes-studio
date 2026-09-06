# 课堂笔记工作室 · 设计系统

## 气质
「教师备课手账」—— 温暖、活泼、专业、清晰。5 种模板各有主题色，卡片化布局，适合打印和屏幕阅读。

## 技术栈
- **Tailwind CSS**（CDN）：原子化样式
- **KaTeX**（CDN）：LaTeX 公式渲染
- **marked.js**（CDN）：Markdown 渲染（可选，要点可直接写 HTML）
- **Excalidraw**（CDN，仅手绘导图模板）：React + Excalidraw 交互式手绘白板

## 字体规范（强制）

```css
font-family: 'Times New Roman', 'Microsoft YaHei', serif;
```

- **汉字**：Microsoft YaHei（微软雅黑）
- **英文和数字**：Times New Roman
- **数学公式**：KaTeX 默认字体
- **实现原理**：CSS 按 font-family 顺序匹配，英文数字命中 Times New Roman，中文回退到微软雅黑

## 5 种模板主题色

| 模板 | 主题色 | 背景 | 编号色 |
|---|---|---|---|
| 提纲笔记 | 紫 #7C3AED / 粉 #F472B6 | 蓝紫粉渐变 #EEF2FF→#F5F3FF→#FDF4FF | 粉→蓝→绿 循环 |
| 思维导图 | 黄 #F59E0B | 暖黄 #FFF8EC | 中心紫，分支红/蓝/绿 |
| 康奈尔笔记 | 红 #F87171 / 黄 #FBBF24 | 暖黄 #FFF8EC | 关键词彩色标签 |
| 知识点笔记 | 绿 #10B981 | 绿渐变 #ECFDF5→#F0FDF4→#F7FEE7 | 红/蓝/绿/紫 循环 |
| Excalidraw 手绘 | 橙 #f59e0b | 米黄 #fdf6e3→#fef3c7 | 手绘风格 |

## 通用组件

### 模板标签头
每个模板顶部有一个标签头：
```
渐变圆角标签（含编号圆 + 模板名） + 白色半透明描述胶囊
```
- 标签：`px-5 py-2.5 rounded-2xl text-white font-bold text-lg shadow-md`
- 编号圆：`w-7 h-7 rounded-full bg-white/25`
- 描述：`px-4 py-2 rounded-full bg-white/70 text-gray-500 text-sm`

### 装饰 SVG 图标
每个模板右上角有一个主题相关的 SVG 装饰图标，opacity 0.8，绝对定位。

### 内容卡片
- 白色背景 `bg-white`，`rounded-3xl shadow-lg`
- 内边距 `p-8`
- 标题：`text-3xl/4xl font-bold text-gray-800`
- 装饰条：`h-1.5 w-64 rounded-full` 渐变

### 章节编号圆
- `w-10 h-10 rounded-full text-white text-lg font-bold shadow-md`
- 渐变背景，每章颜色循环

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

### 2. 思维导图（纯 CSS）
- 页面背景：`#FFF8EC`
- 标签头渐变：`linear-gradient(135deg,#FBBF24,#F59E0B)`
- 中心节点：椭圆 `rounded-[50%]`，渐变 `#8B5CF6→#7C3AED`，白字
- 分支1（左上红）：`bg:#FFF1F2 border:3px solid #FDA4AF`，标题 `bg:#FECDD3 color:#9F1239`，子项 `bg:#FEE2E2 color:#9F1239`
- 分支2（右上蓝）：`bg:#EFF6FF border:3px solid #93C5FD`，标题 `bg:#BFDBFE color:#1E40AF`，子项 `bg:#DBEAFE color:#1E40AF`
- 分支3（底部绿）：`bg:#ECFDF5 border:3px solid #6EE7B7`，标题 `bg:#A7F3D0 color:#065F46`，子项 `bg:#D1FAE5 color:#065F46`
- 连接线：SVG 贝塞尔曲线，stroke-width 4
- 便签：右下角黄色 `bg:#FEF3C7`，旋转 6deg

### 3. 康奈尔笔记
- 页面背景：`#FFF8EC`
- 标签头渐变：`linear-gradient(135deg,#FBBF24,#F59E0B)`
- 活页本风格：左侧 `pl-10`，左侧圆环 `w-5 h-5 rounded-full border-2 border-gray-300 bg-gray-100`
- 表头分割线：`border-bottom:3px solid #F87171`
- 表格表头：关键词列 `bg:#FEF3C7 color:#92400E`，内容列 `bg:#DBEAFE color:#1D4ED8`
- 关键词标签：彩色背景圆角，每词不同色（粉/紫/紫红/绿/浅绿/青）
- 总结栏：左侧 `bg:#FECDD3 color:#9F1239` 固定宽，右侧 `bg:#FFF1F2` 内容

### 4. 知识点笔记
- 页面背景：`linear-gradient(135deg,#ECFDF5 0%,#F0FDF4 50%,#F7FEE7 100%)`
- 标签头渐变：`linear-gradient(135deg,#34D399,#10B981)`
- 2 列网格 `grid grid-cols-2 gap-5`
- 卡片1（红）：`bg:#FFF1F2 border:2px solid #FECDD3`，编号 `#FB7185→#F43F5E`，标题 `color:#9F1239`
- 卡片2（蓝）：`bg:#EFF6FF border:2px solid #BFDBFE`，编号 `#60A5FA→#3B82F6`，标题 `color:#1E40AF`
- 卡片3（绿）：`bg:#ECFDF5 border:2px solid #A7F3D0`，编号 `#34D399→#10B981`，标题 `color:#065F46`
- 卡片4（紫）：`bg:#F5F3FF border:2px solid #C4B5FD`，编号 `#A78BFA→#7C3AED`，标题 `color:#5B21B6`
- 步骤编号：`w-6 h-6 rounded-full bg-[#7C3AED] text-white text-xs`

### 5. Excalidraw 手绘导图
- 页面背景：`linear-gradient(135deg,#fdf6e3 0%,#fef3c7 100%)`
- 标签头渐变：`linear-gradient(135deg,#f59e0b,#d97706)`
- 容器：`#excalidraw-container`，70vh 高度，rounded-1rem
- 加载遮罩：`#fdf6e3` 背景，居中提示
- 画布背景色：`#fdf6e3`
- 中心节点：椭圆，紫边 `#7c3aed`，浅紫填充 `#ddd6fe`
- 分支框：圆角矩形，红/蓝/绿边框和浅填充
- 便签：直角矩形（round:false），黄填充 `#ffec99`，roughness:2
- 箭头：彩色箭头连接中心与分支

## 设计禁忌
- ❌ 出现"AI 生成"字样
- ❌ 使用 font-mono / ui-monospace 覆盖数字字体
- ❌ 蓝紫渐变 + 圆角卡片的单调 AI 默认审美（每种模板要有鲜明主题色）
- ❌ 大量 emoji 作为图标（使用 SVG 线性图标或纯 CSS）
