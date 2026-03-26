# Rules 规则完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解什么是 Rules 以及它的作用
- ✅ 学会创建和组织 Rules
- ✅ 掌握 Rules 的加载机制
- ✅ 理解 Rules 与 CLAUDE.md 的关系

## 🔰 前置知识

- ✅ 了解 [Memory 记忆系统](./05-memory.md) 的层级加载
- ✅ 了解 [Settings 配置完全指南](./06-settings.md)

---

## 💡 什么是 Rules

**Rules**（规则）是 Claude Code 中的**强制行为约束**，用于：
- 拆分大型指令文件
- 强制特定操作必须走特定路径
- 团队共享行为规范

```
★ Insight ─────────────────────────────────────
Rule = 强制执行的规则
告诉 Claude "不许直接做，必须走那个 agent"
是一种防护机制
─────────────────────────────────────────────────
```

### Rules vs CLAUDE.md

| 特性 | Rules | CLAUDE.md |
|------|-------|-----------|
| 本质 | 强制约束 | 建议指导 |
| 加载 | 自动加载 | 向上查找 |
| 约束力 | 更高 | 较低 |
| 用途 | 防火线 | 项目说明 |

---

## 📂 Rules 文件结构

### 目录位置

```
.claude/
├── rules/
│   ├── markdown-docs.md     # 文档规则
│   ├── presentation.md      # 演示规则
│   └── custom/
│       └── my-rule.md      # 自定义规则
└── CLAUDE.md
```

### 规则文件格式

```markdown
# 规则名称

## 关键指令

这是规则的具体内容...

## 约束

这个规则强制执行以下行为：
- 必须使用特定的 agent
- 不许直接编辑某些文件
- 必须遵循特定流程
```

---

## 💻 实战：创建 Presentation 规则

### 场景

团队使用统一的 PPT 管理流程，所有 PPT 相关工作必须通过 presentation-curator agent。

### 创建规则文件

```markdown
<!-- .claude/rules/presentation.md -->
# Presentation 演示文稿规则

## 强制约束

**所有** presentation 相关工作必须委托给 `presentation-curator` agent：

1. 更新演示文稿
2. 修改幻灯片
3. 调整样式
4. 修复演示问题

**禁止**：直接编辑 `presentation/index.html` 或其他演示文件。

## 为什么

presentation-curator agent 有三个预加载的技能，保持演示文稿的结构、样式和概念框架同步。它还会在每次执行后自我进化，更新自己的技能以防止知识漂移。

绕过这个 agent 可能会破坏幻灯片编号、层级转换或样式一致性。

## 如何使用

```bash
# 在 Claude Code 中
Task(subagent_type="presentation-curator", description="更新第5页幻灯片", prompt="...")
```

## 例外

如果你确定需要直接编辑，请先咨询团队负责人。
```

### 在 CLAUDE.md 中引用

```markdown
<!-- CLAUDE.md -->
# 项目规范

## 演示文稿

所有演示相关工作遵循规则：`.claude/rules/presentation.md`

## 其他规范
...
```

---

## 💻 实战：创建文档规则

### 场景

规范团队文档的格式和结构。

### 创建规则文件

```markdown
<!-- .claude/rules/documentation.md -->
# 文档规范

## 文件组织

| 文档类型 | 目录位置 |
|----------|----------|
| Best Practice | `best-practice/` |
| Implementation | `implementation/` |
| Reports | `reports/` |
| Tips | `tips/` |
| Changelog | `changelog/<category>/` |

## 命名规范

- 使用小写字母和连字符
- 示例：`my-document.md`、`claude-commands.md`

## 内容规范

### 必须包含

- 标题
- 更新日期徽章
- 返回链接

### 格式化要求

- 使用表格进行对比
- 使用分级标题
- 代码块必须有语言标识

## 链接规范

- 使用相对链接
- 不使用绝对 URL
- 包含返回上级目录的链接
```

---

## 🔄 Rules 加载机制

### 与 CLAUDE.md 的关系

```
★ Insight ─────────────────────────────────────
Rules 和 CLAUDE.md 有不同的加载机制：
• CLAUDE.md = 向上查找
• Rules = .claude/rules/ 目录下自动加载
─────────────────────────────────────────────────
```

### 加载优先级

```
1. Rules (.claude/rules/*.md)      ← 自动加载
2. CLAUDE.md (向上查找)            ← 层级加载
3. Memory (auto-memory)            ← 动态加载
```

### 加载示例

```bash
# 目录结构
project/
├── .claude/
│   ├── rules/
│   │   ├── project-rules.md   # ← 自动加载
│   │   └── team-rules.md      # ← 自动加载
│   └── CLAUDE.md             # ← 向上查找
├── backend/
│   └── CLAUDE.md             # ← 向上查找（仅在 backend/ 下加载）
└── CLAUDE.md                 # ← 向上查找（最先）
```

---

## 🎯 最佳实践

### 1. Rules 用于强制约束

```
★ Insight ─────────────────────────────────────
Rule = 不可协商的规则
不是"建议"，是"必须"
不要用 Rule 来描述偏好
─────────────────────────────────────────────────
```

### 2. 保持 Rules 简洁

```markdown
<!-- ❌ 过长 -->
<!-- 规则包含大量解释和背景 -->

<!-- ✅ 简洁 -->
规则 + 原因 + 示例
```

### 3. 使用清晰的文件名

```bash
# ✅ 描述性名称
presentation.md
documentation.md
security-rules.md

# ❌ 模糊名称
rules.md
notes.md
```

### 4. 按主题组织

```
.claude/rules/
├── presentation.md    # 演示相关
├── documentation.md   # 文档相关
├── security.md       # 安全相关
└── team/
    ├── frontend.md   # 前端团队
    └── backend.md    # 后端团队
```

---

## ⚠️ 常见问题

### Q1：Rule 和 CLAUDE.md 冲突怎么办？

Rule 优先级更高。如果冲突，Rule 会覆盖 CLAUDE.md 中的内容。

### Q2：如何临时绕过 Rule？

不建议绕过。Rule 是设计用来强制执行的。如果你发现 Rule 不适用，应该修改 Rule 而不是绕过它。

### Q3：Rule 可以嵌套吗？

不支持嵌套。但你可以在 Rule 中引用其他 Rule 文件。

### Q4：Rule 能自动触发吗？

Rule 不是事件触发的。它们是持续加载的约束。任何时候 Claude 都会遵守 Rule 中的约束。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [05 - Memory 记忆系统完全指南](./05-memory.md) | CLAUDE.md 层级加载 |
| [06 - Settings 配置完全指南](./06-settings.md) | 配置系统 |
| [17 - Plugins 插件完全指南](./17-plugins.md) | 插件打包 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
