# 记忆系统设计

描述 easy-spec 的四级记忆体系，定义每级的内容、加载时机和生命周期。

---

## 四级记忆体系

```
Level 0  context.md          ← 当前会话进度（今天做到哪）
Level 1  index.md            ← 做过哪些需求（一行一个）
Level 2  summaries/<名>.md   ← 每个需求的概要（决策 + 实体 + 技术债）
Level 3  archive/<日期-名>/  ← 完整归档（Spec + Task-List + retro）
```

---

## Level 0：当前会话上下文

**文件**：`.easy-spec/memory/context.md`

**内容**：
- 当前需求名称与阶段
- 任务进度（已完成 / 进行中 / 待开始）
- 已知阻塞点
- 最近 5 条关键决策
- 下一步建议

**加载时机**：每次会话启动时**自动加载**（第一条消息触发）

**生命周期**：需求归档时清空，重置为空模板

---

## Level 1：需求索引

**文件**：`.easy-spec/memory/index.md`

**内容**：所有历史需求的一行索引（名称、日期、一句话描述、核心技术、Level 2 链接）

**加载时机**：每次会话启动时**自动加载**（和 context.md 一起）

**生命周期**：永久累积，每次归档时追加一行，不删除

**用途**：让 AI 在新需求启动时快速扫描历史，判断是否有相关需求，决定是否加载 Level 2

---

## Level 2：需求摘要

**文件**：`.easy-spec/memory/summaries/<需求名称>.md`

**内容**：
- 核心实体与状态
- 关键决策（选了什么 + 为什么）
- 已知技术债
- 经验与教训
- 关联需求（上下游）

**加载时机**：**按需加载**——AI 在 Level 1 中发现相关历史需求后，询问用户是否加载对应摘要

**生命周期**：归档时生成，后续迭代时可追加更新，永久保留

**用途**：用决策和教训为新需求提供参考，而不是重复踩坑

---

## Level 3：完整归档

**文件**：`.easy-spec/archive/<YYYY-MM-DD-需求名称>/`（含 Spec.md、Task-List.md、retro.md）

**内容**：需求交付时的完整规格和复盘记录

**加载时机**：**显式加载**——用户明确要求查看某个历史需求的完整规格时才读取

**生命周期**：只读，永久保留

**用途**：需要完整规格细节时的深度参考（如迭代旧需求、规格冲突排查）

---

## 加载策略

### 每次会话启动（自动）

```
1. 读取 .easy-spec/memory/context.md     → Level 0，了解当前进度
2. 读取 .easy-spec/memory/index.md       → Level 1，了解历史全貌
3. 根据本次需求描述，扫描 index.md 中的历史需求
   → 发现相关：询问用户是否加载对应 summaries/   → Level 2，按需
   → 无相关：直接开始
```

### 深度查阅（显式）

```
用户说：请查看用户标签系统的完整规格
  → 读取 .easy-spec/archive/2026-07-03-用户标签系统/Spec.md   → Level 3
```

---

## 第五层：Claude Code 全局记忆

**位置**：`~/.claude/projects/.../memory/`（Claude Code 管理，不在项目目录里）

**内容**：跨项目、跨需求的用户偏好和经验（由 Claude 自动写入）

| 类型 | 示例 |
|------|------|
| `user` | 用户是 Java 架构师，不解释 Spring 基础概念 |
| `feedback` | 测试不能 mock 数据库，必须打真实 DB |
| `project` | merge freeze 在 7 月 1 日 |
| `reference` | bug 追踪在 Linear project "BACKEND" |

**与 easy-spec 的关系**：这层记忆不归 easy-spec 管，由 Claude 自动维护，归档时不清空。
