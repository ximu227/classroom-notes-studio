---
name: classroom-notes-studio
description: 从 PDF 讲义/教材/试卷生成 5 种风格的课堂笔记图片。支持提纲笔记、思维导图、康奈尔笔记、知识点卡片、Excalidraw 手绘导图，自动识别学科、填写挖空、渲染 LaTeX 公式，输出精美 PNG 笔记图。当用户上传 PDF 并要求生成笔记/课堂笔记/复习提纲/思维导图/康奈尔笔记，或提到"把这个 PDF 做成笔记"时使用。
---

# 课堂笔记工作室

上传 PDF → 提取文本 → AI 生成结构化笔记 → 5 种模板渲染为精美笔记图片（PNG）。

## 5 种笔记模板

| 模板 ID | 名称 | 风格特点 | 适用场景 |
|---|---|---|---|
| outline | 提纲笔记 | 蓝紫粉渐变背景，彩色章节编号，关键词标签 | 通用复习，结构清晰 |
| mindmap | 思维导图 | 暖黄背景，中心椭圆+三色分支卡片，SVG曲线连接 | 知识体系梳理 |
| cornell | 康奈尔笔记 | 活页本风格，左关键词右内容表格，底部总结 | 复习回顾 |
| knowledge | 知识点笔记 | 绿渐变背景，2x2 彩色卡片网格 | 概念卡片记忆 |
| excalidraw | 手绘导图 | 米黄背景，Excalidraw 交互式手绘白板 | 手绘风格，可编辑 |

## 工作流程（5 步）

### 第 1 步：提取 PDF 文本

```bash
python3 <skill_dir>/scripts/extract_pdf.py <pdf_path> --output <output_txt>
```
- 如果提取到的文本为空或极少，提示用户该 PDF 可能是扫描版
- 记录页数和字符数

### 第 2 步：生成结构化笔记 JSON

读取 `references/prompts.md` 中的完整提示词，将 PDF 文本填入 `{fileText}`，目标模板填入 `{templateType}`，然后**由你自己（AI）** 按照提示词要求输出严格的 JSON。

根据所选模板输出对应的数据结构（详见 `references/data-types.md`）：
- **outline** → `NoteData`：title / subject / sections[]（heading + points[keyword, content]）
- **mindmap / excalidraw** → `MindMapData`：title / branches[3]（left/right/bottom） / note?
- **cornell** → `CornellData`：title / subject / date / rows[]（keyword, content, color） / summary
- **knowledge** → `KnowledgeData`：title / subject / cards[4]（title, color, type, content/items/steps）

生成后按 `references/prompts.md` 末尾的质量检查清单逐项核验。

### 第 3 步：选择笔记模板

根据 `references/data-types.md` 中的模板选择策略决定模板：
- 默认 `outline`（提纲笔记）
- 知识体系/概念关联 → `mindmap`
- 关键词+详细内容对照 → `cornell`
- 并列知识点/概念卡片 → `knowledge`
- 手绘风格/可交互 → `excalidraw`
- 用户明确指定 → 按用户要求
- 用户要求"全部"→ 5 种都生成

### 第 4 步：生成 HTML 笔记页面

读取 `references/templates.md`，按照所选模板的 HTML 结构生成完整的 HTML 文件。

技术栈：
- **Tailwind CSS**（CDN）：原子化样式
- **KaTeX**（CDN）：LaTeX 公式渲染
- **React + Excalidraw**（CDN，仅 excalidraw 模板）

关键要求：
- 页面宽度 1080px
- 字体：`font-family:'Times New Roman','Microsoft YaHei',serif`（中文微软雅黑，英文数字 Times New Roman）
- 配色严格遵循 `references/design-system.md`
- 要点内容直接写 HTML，公式用 `<span id="f1"></span>` 占位 + JS 渲染
- 每个模板顶部有标签头（编号圆+模板名+描述胶囊）
- 右上角有主题 SVG 装饰图标
- excalidraw 模板需要完整的 buildElements() 函数构造手绘元素

将 HTML 写入工作目录的临时文件，如 `note_outline.html`。

### 第 5 步：渲染为 PNG 图片

```bash
python3 <skill_dir>/scripts/render_notes.py <html_path> <output_png> --width 1080
```
- 输出文件名：`{标题}_{模板名}.png`
- 普通模板默认等待 1500ms
- **excalidraw 模板必须增加等待**：`--wait 4000`（React + Excalidraw 加载渲染较慢）
- 公式较多时增加 `--wait 2500`
- 渲染完成后通过 `present_files` 交付图片

## 资源索引

| 文件 | 用途 | 何时读取 |
|---|---|---|
| `references/prompts.md` | 完整提示词 + 5 种模板输出格式 + 质量检查清单 | 第 2 步生成笔记前 |
| `references/data-types.md` | 5 种数据结构 + 模板类型 + 选择策略 | 第 2、3 步 |
| `references/design-system.md` | 配色/字体/5 种模板主题色/通用组件 | 第 4 步生成 HTML 时 |
| `references/templates.md` | 5 种模板的 HTML/CSS/JS 完整规范 | 第 4 步生成 HTML 时 |
| `scripts/extract_pdf.py` | PDF 文本提取 | 第 1 步 |
| `scripts/render_notes.py` | HTML → PNG 截图 | 第 5 步 |

## 注意事项

1. **挖空必须填写**：PDF 中的填空题/下划线挖空，必须根据学科知识补全答案并用加粗标记
2. **LaTeX 安全**：简单符号用 Unicode（× ÷ ± ≥ ≤ → ·），复杂结构才用 `\frac{}` `\sqrt{}` 等
3. **字体规范（强制）**：汉字使用 Microsoft YaHei，英文和数字使用 Times New Roman，公式使用 KaTeX 默认字体。CSS 写法：`font-family:'Times New Roman','Microsoft YaHei',serif`。禁止使用 `font-mono` 覆盖数字字体。
4. **不要出现"AI 生成"字样**
5. **长 PDF 处理**：超过 2 万字时，可先生成大纲再分章节生成
6. **excalidraw 渲染**：必须 `--wait 4000`，且 HTML 中必须包含完整的 React + Excalidraw CDN 和 buildElements() 函数
7. **学科识别**：必须从标准学科名中选择
8. **要点内容写法**：直接写 HTML，不要用 marked.js；关键词用 `<span class="px-2 py-0.5 rounded-md font-semibold">` 包裹；公式用 `<span id="xxx"></span>` 占位后通过 katex.renderToString 渲染
9. **思维导图固定 3 分支**：left（红）/ right（蓝）/ bottom（绿），不要增减
10. **知识点笔记固定 4 卡片**：red/blue/green/purple 顺序，2x2 网格
