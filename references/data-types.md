# 课堂笔记工作室 · 数据结构

## TemplateType（模板类型）

```typescript
type TemplateType = 'outline' | 'mindmap' | 'cornell' | 'knowledge' | 'excalidraw';
```

| 模板 ID | 中文名 | 适用场景 |
|---|---|---|
| outline | 提纲笔记 | 结构清晰，层次分明，适合复习 |
| mindmap | 思维导图 | 图文结合，逻辑清晰，适合梳理知识体系 |
| cornell | 康奈尔笔记 | 左侧关键词，右侧详细内容，方便复习回顾 |
| knowledge | 知识点笔记 | 系统整理，卡片化布局，突出重点便于记忆 |
| excalidraw | Excalidraw 手绘导图 | 开源手绘风格白板，可交互编辑 |

## 模板选择策略

- **默认**：`outline`（提纲笔记，通用性最强）
- 内容以知识体系/概念关联为主 → `mindmap`
- 需要关键词+详细内容对照复习 → `cornell`
- 内容以并列知识点/概念卡片为主 → `knowledge`
- 用户要求手绘风格/可交互 → `excalidraw`
- 用户明确指定 → 按用户要求
- 用户要求"全部"→ 5 种都生成

---

## NoteData（提纲笔记 / 通用）

```typescript
interface NoteData {
  title: string;       // 笔记标题，如"第1讲 一元一次方程的应用"
  subject: string;     // 学科
  date?: string;       // 日期（康奈尔用）
  summary: string;     // 本讲总结
  sections: OutlineSection[];
}

interface OutlineSection {
  heading: string;     // 章节标题，如"一、有理数基础复习"
  points: OutlinePoint[];
}

interface OutlinePoint {
  keyword?: string;    // 关键词标签（可选），如"绝对值："
  content: string;     // 要点内容，支持 HTML 标签和 LaTeX
}
```

提纲笔记中，每个章节自动分配颜色（粉→蓝→绿循环），points 中的 keyword 渲染为彩色标签。

---

## MindMapData（思维导图）

```typescript
interface MindMapData {
  title: string;       // 中心节点标题，可含换行 \n
  subject: string;
  branches: MindMapBranch[];  // 3 个分支（左上、右上、底部）
  note?: string;       // 右下角便签文字（可选）
}

interface MindMapBranch {
  position: 'left' | 'right' | 'bottom';  // 分支位置
  title: string;        // 分支标题
  items: string[];      // 分支下的知识点列表，2-4 个
  color: 'red' | 'blue' | 'green';  // 分支主题色
}
```

思维导图固定 3 个分支布局：左上（红）、右上（蓝）、底部（绿）。中心节点为紫色椭圆。

---

## CornellData（康奈尔笔记）

```typescript
interface CornellData {
  title: string;
  subject: string;
  date: string;         // 日期，如"2026/9/6"
  rows: CornellRow[];   // 关键词-内容对
  summary: string;      // 总结
}

interface CornellRow {
  keyword: string;      // 关键词，如"绝对值"
  content: string;      // 详细内容，支持 HTML 和 LaTeX
  color: 'pink' | 'purple' | 'fuchsia' | 'green' | 'emerald' | 'cyan';  // 关键词标签色
}
```

康奈尔笔记使用表格布局，左列关键词（彩色标签），右列详细内容。底部有总结栏。

---

## KnowledgeData（知识点笔记）

```typescript
interface KnowledgeData {
  title: string;
  subject: string;
  cards: KnowledgeCard[];  // 4 个知识点卡片（2x2 网格）
}

interface KnowledgeCard {
  title: string;           // 卡片标题
  color: 'red' | 'blue' | 'green' | 'purple';  // 卡片主题色
  type: 'text' | 'list' | 'steps';  // 内容类型
  content?: string;        // type=text 时的纯文本内容
  items?: string[];        // type=list 时的要点列表
  steps?: string[];        // type=steps 时的步骤列表（自动编号）
}
```

知识点笔记使用 2x2 网格卡片布局，每张卡片有独立主题色（红/蓝/绿/紫循环）。

---

## ExcalidrawData（手绘导图）

复用 `MindMapData` 结构，渲染时转换为 Excalidraw elements：
- 中心节点 → 椭圆（紫色）
- 3 个分支 → 圆角矩形（红/蓝/绿）
- 便签 → 直角矩形（黄色，roughness:2）
- 连接线 → 箭头

---

## 文件解析结果

```typescript
interface FileParseResult {
  content: string;
  fileName: string;
  pageCount: number;
  type: 'pdf';
}
```
