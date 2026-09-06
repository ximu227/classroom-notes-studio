# 课堂笔记工作室 · 数据结构

## TemplateType（模板类型）

```typescript
type TemplateType = 'outline' | 'cornell' | 'knowledge';
```

| 模板 ID | 中文名 | 适用场景 |
|---|---|---|
| outline | 提纲笔记 | 结构清晰，层次分明，适合复习 |
| cornell | 康奈尔笔记 | 左侧线索词，右侧详细内容，方便复习回顾 |
| knowledge | 知识点笔记 | 系统整理，卡片化布局，突出重点便于记忆 |

## 模板选择策略

- **默认**：`outline`（提纲笔记，通用性最强）
- 需要关键词+详细内容对照复习 → `cornell`
- 内容以并列知识点/概念卡片为主 → `knowledge`
- 用户明确指定 → 按用户要求
- 用户要求"全部"→ 3 种都生成

---

## NoteData（统一数据结构，3 种模板共用）

```typescript
interface NoteData {
  title: string;       // 笔记标题，如"第1讲 一元一次方程的应用"（严格保持原文讲次编号）
  subject: string;     // 学科
  date?: string;       // 日期（康奈尔用），如"2026/9/6"
  summary: string;     // 本讲总结（200 字内）
  sections: NoteSection[];
}

interface NoteSection {
  level: 1 | 2 | 3;    // 层级：1=一级标题（整章/主题），2=二级标题（子主题），3=三级标题（概念点）
  heading: string;     // 章节标题，如"一、有理数基础复习"
  cues?: string[];     // 线索词（仅 level 1 使用，2-4 个，每个 2-6 字，康奈尔左侧栏）
  points: string[];    // 要点列表（3-6 条，支持 markdown + LaTeX，不加编号前缀）
  children?: NoteSection[];  // 子节点（level 2/3）
}
```

### 层级结构说明

- **level 1**：一级标题（整章/主题），必须有 `cues`（线索词）和 `points`，可有 `children`（level 2）
- **level 2**：二级标题（子主题/分支），必须有 `points`，可有 `children`（level 3）
- **level 3**：三级标题（概念点/叶子节点），必须有 `points`，无 `children`
- **每个节点都必须有 points**（无论是否有 children），这样所有模板都能正常显示
- **cues 仅在 level 1 节点使用**，用于康奈尔笔记左侧线索栏
- 一份优秀的笔记至少包含 2-3 个 level 1，每章 2-4 个 level 2，每节点 3-6 个 points

### 各模板渲染方式

- **outline（提纲笔记）**：递归渲染所有层级，level 1 用编号圆+大标题，level 2 用色条缩进，level 3 用更深缩进
- **cornell（康奈尔笔记）**：level 1 的 cues 作为左侧线索词，level 1 的 points + 所有子节点内容展平为右侧详细内容
- **knowledge（知识点笔记）**：取 level 1 章节作为 4 张卡片（2x2 网格），卡片内容为该章节的 points + 子节点摘要

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
