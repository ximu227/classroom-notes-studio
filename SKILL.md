---
name: classroom-notes-studio
description: 从 PDF 讲义/教材/试卷生成 3 种风格的课堂笔记图片。支持提纲笔记、康奈尔笔记、知识点卡片，自动识别学科、填写挖空、渲染 LaTeX 公式，输出精美 PNG 笔记图。当用户上传 PDF 并要求生成笔记/课堂笔记/复习提纲/康奈尔笔记，或提到"把这个 PDF 做成笔记"时使用。
---

# 课堂笔记工作室

上传 PDF → 提取文本 → AI 生成结构化笔记 → 3 种模板渲染为精美笔记图片（PNG）。

## 3 种笔记模板

| 模板 ID | 名称 | 风格特点 | 适用场景 |
|---|---|---|---|
| outline | 提纲笔记 | 蓝紫粉渐变背景，彩色章节编号，层级嵌套缩进 | 通用复习，结构清晰 |
| cornell | 康奈尔笔记 | 活页本风格，左关键词右内容，底部总结 | 复习回顾 |
| knowledge | 知识点笔记 | 绿渐变背景，2x2 彩色卡片网格 | 概念卡片记忆 |

## 工作流程（5 步）

### 第 1 步：提取 PDF 文本

```bash
python3 <skill_dir>/scripts/extract_pdf.py <pdf_path> --output <output_txt>
```
- 如果提取到的文本为空或极少，提示用户该 PDF 可能是扫描版
- 记录页数和字符数

### 第 2 步：生成结构化笔记 JSON

读取 `references/prompts.md` 中的完整提示词，将 PDF 文本填入 `{fileText}`，然后**由你自己（AI）** 按照提示词要求输出严格的 JSON。

所有 3 种模板共用统一的 `NoteData` 数据结构（详见 `references/data-types.md`）：
- `title` / `subject` / `summary`
- `sections[]`：每个 section 有 `level`（1/2/3）、`heading`、`points[]`、可选 `cues[]`（仅 level 1）、可选 `children[]`

生成后按 `references/prompts.md` 末尾的质量检查清单逐项核验。

### 第 3 步：选择笔记模板

根据 `references/data-types.md` 中的模板选择策略决定模板：
- 默认 `outline`（提纲笔记）
- 需要关键词+详细内容对照 → `cornell`
- 并列知识点/概念卡片 → `knowledge`
- 用户明确指定 → 按用户要求
- 用户要求"全部"→ 3 种都生成

### 第 4 步：生成 HTML 笔记页面

读取 `references/templates.md`，按照所选模板的 HTML 结构生成完整的 HTML 文件。

技术栈：
- **Tailwind CSS**（CDN）：原子化样式
- **KaTeX**（本地引用，`notes_output/katex/`）：LaTeX 公式渲染

关键要求：
- 页面宽度 1080px
- 字体：`font-family:'Times New Roman','PingFang SC','Hiragino Sans GB','Microsoft YaHei','Noto Sans SC',serif`（中文苹方/冬青/微软雅黑/思源黑体，英文 Times New Roman）
- **英文斜体**：JS `wrapEnglish()` 遍历文本节点，仅将 `[a-zA-Z]+`（英文字母，不含数字）包裹为 `<span class="en">`，CSS `.en { font-style: italic }`，跳过 `.katex`；公式占位 `.formula-placeholder` 加 `margin: 0 4px` 避免公式粘连
- **公式渲染**：要点内容用 `$...$` 包裹 LaTeX，`renderPoint()` 解析为占位 span，`flushKatex()` 调用 `katex.renderToString` 渲染
- **标签头用 π 图标**：所有模板顶部标签头的编号圆替换为 π（圆周率）SVG 图标
- 配色严格遵循 `references/design-system.md`
- 右上角有主题 SVG 装饰图标

将 HTML 写入工作目录的临时文件，如 `note_outline.html`。

### 第 5 步：渲染为 PNG 图片

```bash
python3 <skill_dir>/scripts/render_notes.py <html_path> <output_png> --width 1080
```
- 输出文件名：`{标题}_{模板名}.png`
- 默认等待 1500ms
- 公式较多时增加 `--wait 2500`
- 渲染完成后通过 `present_files` 交付图片

## 资源索引

| 文件 | 用途 | 何时读取 |
|---|---|---|
| `references/prompts.md` | 完整提示词 + 统一 NoteData 输出格式 + 质量检查清单 | 第 2 步生成笔记前 |
| `references/data-types.md` | 统一 NoteData 数据结构 + 3 种模板类型 + 选择策略 | 第 2、3 步 |
| `references/design-system.md` | 配色/字体/3 种模板主题色/通用组件/英文斜体规范 | 第 4 步生成 HTML 时 |
| `references/templates.md` | 3 种模板的 HTML/CSS/JS 完整规范 + 通用渲染脚本 | 第 4 步生成 HTML 时 |
| `scripts/extract_pdf.py` | PDF 文本提取 | 第 1 步 |
| `scripts/render_notes.py` | HTML → PNG 截图 | 第 5 步 |

## 注意事项

1. **挖空必须填写**：PDF 中的填空题/下划线挖空，必须根据学科知识补全答案并用加粗标记
2. **LaTeX 安全**：简单符号用 Unicode（× ÷ ± ≥ ≤ → ·），复杂结构才用 `\frac{}` `\sqrt{}` 等。禁止 `\times \Rightarrow \to \cdot \div \pm \ge \le \ne`（JSON 解析会导致反斜杠丢失）
3. **字体规范（强制）**：汉字使用 PingFang SC/Hiragino Sans GB/Microsoft YaHei/Noto Sans SC（正体），英文使用 Times New Roman（斜体），数字使用 Times New Roman（正体），公式使用 KaTeX 默认字体。CSS 写法：`font-family:'Times New Roman','PingFang SC','Hiragino Sans GB','Microsoft YaHei','Noto Sans SC',serif`，配合 JS `wrapEnglish()` 实现英文斜体。禁止使用 `font-mono` 覆盖数字字体。
4. **不要出现"AI 生成"字样**
5. **长 PDF 处理**：超过 2 万字时，可先生成大纲再分章节生成
6. **学科识别**：必须从标准学科名中选择
7. **标题编号锁死**：title 必须严格保持原文的讲次编号（如"第1讲"），禁止修改、重新排序或改成其他编号
8. **加粗格式校验**：`**关键词**` 必须包裹实际文字，禁止出现 `****`（四个星号）或 `**` 后无内容
9. **points 不加编号前缀**：points 数组中不要再加 "1." "2." 等编号前缀（前端会按渲染顺序自动加）
10. **层级结构**：sections 使用 level 1/2/3 + children 嵌套，cues 仅在 level 1 节点使用（康奈尔左侧关键词栏）
