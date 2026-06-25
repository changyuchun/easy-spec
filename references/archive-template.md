# 归档模板（Level 3）

需求完成后执行归档，同时更新 Level 1 索引和生成 Level 2 摘要。

仅当用户明确说"这个需求做完了"或"归档这个需求"时使用。

---

## 归档执行顺序

### 第一步：创建 Level 3 归档目录

```
.easy-spec/archive/YYYY-MM-DD-<需求名称>/
├── Spec.md           ← 复制自 .easy-spec/spec/Spec.md
├── Task-List.md      ← 复制自 .easy-spec/spec/Task-List.md（如有）
├── Spec-CHANGELOG.md ← 复制自 .easy-spec/spec/Spec-CHANGELOG.md（如有）
└── retro.md          ← 本次归档时新生成
```

### 第二步：生成 retro.md（复盘记录）

向用户追问：
- 实际交付和规格的差异在哪里
- 哪些关键决策回头看是对的，哪些需要改
- 遗留了哪些技术债

```markdown
# 复盘记录 — <需求名称>

## 基本信息
- 完成日期：YYYY-MM-DD
- 实际工期（天）：
- 参与人员：

## 交付摘要
- 实际完成了哪些功能
- 相比原 Spec 有哪些差异（缩减 / 增加 / 变更）

## 关键决策记录
- 决策内容 — 原因 — 实际结果是否符合预期

## 技术债与遗留问题
- 具体问题描述

## 经验与教训
- 值得下次复用的做法
- 下次可以提前避免的坑
- 本应更早澄清的规格问题

## 对下一个需求的建议
- 如有关联需求，需要传递的上下文
```

### 第三步：更新 Level 1 索引

在 `.easy-spec/memory/index.md` 末尾追加一行：

```markdown
| <需求名称> | YYYY-MM-DD | <一句话描述（≤15字）> | <核心技术/领域> | [查看](summaries/<需求名称>.md) |
```

如果 `index.md` 不存在，先创建并写入表头：

```markdown
# 需求索引

| 需求名称 | 完成日期 | 一句话描述 | 核心技术/领域 | 摘要 |
|---------|---------|-----------|-------------|------|
```

### 第四步：生成 Level 2 摘要

读取 [summary-template.md](./summary-template.md)，在 `.easy-spec/memory/summaries/<需求名称>.md` 生成摘要文件。

内容来源：
- 核心实体 → 来自 Spec.md 第 6 节
- 关键决策 → 来自 retro.md + context.md 的决策记录
- 技术债 → 来自 retro.md
- 经验教训 → 来自 retro.md
- 关联需求 → 来自 Spec.md 第 10 节 + 用户补充

### 第五步：清空 context.md

将 `.easy-spec/memory/context.md` 重置为空：

```markdown
# 当前开发上下文

（待下个需求启动时写入）
```

---

## 归档后目录状态

```
.easy-spec/
├── spec/
│   └── Spec.md               ← 保留（作为下次迭代的起点）
├── memory/
│   ├── context.md            ← 已清空
│   ├── index.md              ← 已追加本次需求的一行
│   └── summaries/
│       └── <需求名称>.md     ← 新生成
└── archive/
    └── YYYY-MM-DD-<需求名称>/   ← 新建，只读
        ├── Spec.md
        ├── Task-List.md
        └── retro.md
```
