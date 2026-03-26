# Memory 记忆系统完全指南

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 Claude Code 的 Memory 机制
- ✅ 掌握 CLAUDE.md 的层级加载规则
- ✅ 学会在不同场景下使用合适的 Memory 策略
- ✅ 了解 Auto-Memory（自动记忆）功能
- ✅ 学会在 Monorepo（单体仓库）中组织 Memory

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md) 中的基本概念
- ✅ 了解 [Subagents 智能体指南](./02-subagents.md) 中的 memory 字段

---

## 💡 什么是 Memory

**Memory**（记忆）是 Claude Code 持久化上下文的方式，让 Claude 在不同会话中记住重要的项目信息、用户偏好和团队规范。

```
★ Insight ─────────────────────────────────────
Memory = 记忆系统
让 Claude 记住"你是谁""项目是什么"
下次对话时无需重复说明
─────────────────────────────────────────────────
```

### Memory 的类型

| 类型 | 位置 | 持久范围 | 说明 |
|------|------|----------|------|
| CLAUDE.md | 项目根目录 | 单项目 | 项目级记忆 |
| Rules | `.claude/rules/` | 单项目 | 强制行为规则 |
| Auto-Memory | `~/.claude/` | 全局 | 自动记忆 |
| Agent Memory | `.claude/agent-memory/` | Agent | 特定 Agent 的记忆 |

---

## 📂 CLAUDE.md 文件系统

### 文件位置规则

```
~/.claude/CLAUDE.md              # 全局记忆（所有项目）
~/project/.claude/rules/         # 项目规则
~/project/CLAUDE.md               # 项目记忆
~/project/backend/CLAUDE.md       # 子模块记忆
```

### 层级加载规则（重要！）

```
★ Insight ─────────────────────────────────────
Claude Code 使用"向上查找"加载 CLAUDE.md
从当前目录向上直到根目录
每个找到的 CLAUDE.md 都会被加载
─────────────────────────────────────────────────
```

### 加载方向图解

```
                    ┌─────────────────┐
                    │   / (根目录)     │
                    │   CLAUDE.md     │  ← 第3个加载
                    └────────┬────────┘
                             │ 向上
                             ▼
              ┌──────────────────────────────┐
              │   ~/mycompany/               │
              │   CLAUDE.md                  │  ← 第2个加载
              └─────────────┬────────────────┘
                            │ 向上
                            ▼
         ┌───────────────────────────────────┐
         │   ~/mycompany/myproject/          │
         │   CLAUDE.md                       │  ← 第1个加载（最先）
         └───────────────┬───────────────────┘
                         │
                         ▼ 当前工作目录
```

### 加载顺序

| 顺序 | 文件 | 说明 |
|------|------|------|
| 1 | `myproject/CLAUDE.md` | 当前目录，最先加载 |
| 2 | `mycompany/CLAUDE.md` | 父目录，第2个加载 |
| 3 | `~/.claude/CLAUDE.md` | 用户目录，最后加载 |

---

## 💻 实战：组织 Monorepo 的 Memory

### 场景

一个 Monorepo 项目，包含 frontend、backend、api 三个子项目。

```
mycompany/
├── CLAUDE.md              # 公司级规范（所有子项目共享）
├── frontend/
│   └── CLAUDE.md          # 前端特定规范
├── backend/
│   └── CLAUDE.md          # 后端特定规范
└── api/
    └── CLAUDE.md          # API 特定规范
```

### 1. 公司级 CLAUDE.md

```markdown
<!-- ~/mycompany/CLAUDE.md -->
# 公司级项目记忆

## 代码规范
- 使用 2 空格缩进
- Commit 消息格式：[类型] 简短描述

## 通用约定
- 所有 API 请求使用 HTTPS
- 敏感信息存储在环境变量中
- 禁止提交 .env 文件

## 联系方式
项目负责人：tech-lead@company.com
```

### 2. 前端子项目 CLAUDE.md

```markdown
<!-- ~/mycompany/frontend/CLAUDE.md -->
# 前端子项目记忆

## 技术栈
- React 18+
- TypeScript
- Tailwind CSS

## 特定规范
- 组件放在 `src/components/` 目录
- 使用 `/components` 命令创建组件
- 运行测试：`npm run test`

## 特殊规则
- 本项目使用 yarn，不用 npm
- 必须使用 pnpm-lock.yaml
```

### 3. 后端子项目 CLAUDE.md

```markdown
<!-- ~/mycompany/backend/CLAUDE.md -->
# 后端子项目记忆

## 技术栈
- Node.js 20+
- Express
- PostgreSQL

## 特定规范
- API 路由放在 `src/routes/` 目录
- 中间件放在 `src/middleware/` 目录
- 数据库迁移：`npm run migrate`

## 特殊规则
- 本项目使用 npm
- 环境变量文件：`.env.example`
```

---

## 🔄 Auto-Memory（自动记忆）

Claude Code 支持自动记忆功能，会自动保存重要的上下文。

### 开启自动记忆

```bash
/memory on    # 开启自动记忆
/memory off   # 关闭自动记忆
/memory       # 查看当前记忆状态
```

### Auto-Memory 文件位置

```
~/.claude/
├── CLAUDE.md              # 你的全局记忆
├── agent-memory/          # Agent 记忆
│   ├── myagent/
│   │   └── MEMORY.md
│   └── weather-agent/
│       └── MEMORY.md
└── projects/             # 项目记忆
    └── myproject/
        └── memory/
```

### Memory 文件格式

```markdown
---
name: my-agent-memory
description: 这个 Agent 的记忆
type: user    # user | project | local
---

# 记忆内容

## 已知信息
- 用户偏好使用 TypeScript
- 项目使用 React 框架
- 常用命令：npm run dev

## 重要决策
- 2026-03-20：决定使用 pnpm 作为包管理器
- 2026-03-22：采用 TDD 开发流程
```

---

## 📋 Agent Memory 配置

### 在 Agent 中启用 Memory

```yaml
# .claude/agents/weather-agent.md
---
name: weather-agent
description: 天气查询 Agent
memory: project    # 项目级记忆
---

# Agent 提示词
你是一个天气预报助手...
```

### Memory 的三种范围

| 范围 | 说明 | 适用场景 |
|------|------|----------|
| `user` | 用户级记忆，跨项目共享 | 用户偏好、通用习惯 |
| `project` | 项目级记忆，当前项目共享 | 项目规范、团队约定 |
| `local` | 本地记忆，仅当前会话有效 | 临时任务信息 |

### Memory 写入时机

```
★ Insight ─────────────────────────────────────
Agent 不会自动写入 Memory
需要在提示词中明确告诉它"记住这个"
或者使用 /memory 命令手动编辑
─────────────────────────────────────────────────
```

---

## 🎯 最佳实践

### 1. CLAUDE.md 保持简洁

```markdown
<!-- ❌ 太长（200+ 行） -->
<!-- ✅ 简洁（60-100 行） -->

<!-- 超过 200 行 Claude 可能忽略 -->
```

### 2. 使用 Rules 组织复杂逻辑

```markdown
<!-- CLAUDE.md - 只放核心指令 -->

# CLAUDE.md

## 项目规范
详见 `.claude/rules/project-rules.md`
```

```markdown
<!-- .claude/rules/project-rules.md -->
# 项目规则

## 编码规范
...

## Git 工作流
...
```

### 3. 善用环境变量

```markdown
<!-- 不要写死敏感信息 -->
<!-- ❌ -->
API_KEY=sk-xxxxxx

<!-- ✅ -->
使用环境变量 `process.env.API_KEY`
```

### 4. 项目特定 vs 全局

```bash
# 全局（~/.claude/CLAUDE.md）
你使用 TypeScript、VS Code

# 项目级（project/CLAUDE.md）
本项目使用 Python、Django
```

---

## ⚠️ 常见问题

### Q1：为什么 Claude 不读取我的 CLAUDE.md？

可能原因：
1. 文件名拼写错误（必须是 `CLAUDE.md`）
2. 文件不在正确的加载路径上
3. 文件内容太长被忽略（保持 200 行以内）

### Q2：多个 CLAUDE.md 冲突怎么办？

后加载的文件优先级更高。同一个指令，后加载的会覆盖先加载的。

### Q3：如何让 Claude 记住之前的对话？

1. **开启 Auto-Memory**：`/memory on`
2. **手动保存**：`/memory` 编辑记忆文件
3. **Resume 会话**：`/resume` 恢复之前的对话

### Q4：Monorepo 中子项目的记忆会泄露到其他子项目吗？

**不会**。子项目只加载自己路径上的 CLAUDE.md，不会加载兄弟目录的。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制概述 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 的 memory 字段 |
| [06 - Settings 配置完全指南](./06-settings.md) | settings.json 配置 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
