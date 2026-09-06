# 课堂笔记工作室 (Classroom Notes Studio)

从 PDF 讲义/教材/试卷自动生成 5 种风格的课堂笔记图片。

## 5 种笔记模板

| 模板 | 名称 | 风格特点 |
|---|---|---|
| outline | 提纲笔记 | 蓝紫粉渐变背景，彩色章节编号，关键词标签 |
| mindmap | 思维导图 | 暖黄背景，中心椭圆+三色分支卡片，SVG曲线连接 |
| cornell | 康奈尔笔记 | 活页本风格，左关键词右内容表格，底部总结 |
| knowledge | 知识点笔记 | 绿渐变背景，2x2 彩色卡片网格 |
| excalidraw | 手绘导图 | 米黄背景，Excalidraw 交互式手绘白板 |

## 功能特性

- 自动识别学科（语文/数学/英语/物理/化学等）
- 自动填写 PDF 中的挖空/填空题
- LaTeX 公式渲染（KaTeX）
- 5 种模板一键生成
- 输出精美 PNG 笔记图

## 字体规范

- 汉字：Microsoft YaHei（微软雅黑）
- 英文和数字：Times New Roman
- 数学公式：KaTeX 默认字体

## 技术栈

- Tailwind CSS（CDN）
- KaTeX（公式渲染）
- React + Excalidraw（手绘导图模板）
- Playwright（HTML → PNG 渲染）
- pdfplumber（PDF 文本提取）

## 目录结构

```
classroom-notes-studio/
├── SKILL.md                    # 技能主入口
├── scripts/
│   ├── extract_pdf.py          # PDF 文本提取
│   └── render_notes.py         # HTML → PNG 渲染
└── references/
    ├── prompts.md              # 笔记生成提示词
    ├── data-types.md           # 5 种数据结构定义
    ├── design-system.md        # 设计系统（配色/字体/组件）
    └── templates.md            # 5 种模板 HTML/CSS 完整规范
```

## 使用方法

1. 上传 PDF 文件
2. 选择笔记模板（或自动选择）
3. 生成结构化笔记 JSON
4. 渲染为 PNG 图片

## 渲染等待时间

- 普通模板：1500ms
- 公式较多：2500ms
- Excalidraw 手绘导图：4000ms（React + Excalidraw 加载较慢）
