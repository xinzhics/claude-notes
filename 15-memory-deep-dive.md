# Memory 机制深度解析

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 深入理解 Memory 的持久化机制
- ✅ 掌握 Auto-Memory 的工作原理
- ✅ 理解 Agent Memory 的隔离性

---

## 💡 Memory 类型总览

| 类型 | 位置 | 持久范围 | 说明 |
|------|------|----------|------|
| CLAUDE.md | 多层级 | 单项目 | 指令文档 |
| Auto-Memory | `~/.claude/` | 全局 | 自动记忆 |
| Agent Memory | `.claude/agent-memory/` | Agent | 特定智能体 |

---

## 🔄 CLAUDE.md 加载机制

### 向上查找规则

```
★ Insight ─────────────────────────────────────
Claude Code 从当前目录向上查找
每找到一个 CLAUDE.md 就加载
后加载的会覆盖先加载的
─────────────────────────────────────────────────
```

### 加载顺序

```
工作目录 → 父目录 → 祖父目录 → ... → 用户目录
   ↓           ↓           ↓
  CLAUDE.md   CLAUDE.md   CLAUDE.md
   (最近)     (中间)     (最远)
```

### 示例

```
# 目录结构
/home/user/
  CLAUDE.md (3)          ← 最后加载
  projects/
    myapp/
      CLAUDE.md (1)      ← 最先加载
      backend/
        CLAUDE.md (2)    ← 第二加载
```

### 加载结果

| 顺序 | 文件 | 说明 |
|------|------|------|
| 1 | myapp/backend/CLAUDE.md | 最具体 |
| 2 | myapp/CLAUDE.md | 中间层 |
| 3 | user/CLAUDE.md | 全局 |

---

## 🔧 Auto-Memory 机制

### 工作原理

```
用户对话
    ↓
Claude 分析关键信息
    ↓
自动写入 ~/.claude/agent-memory/
    ↓
下次会话自动加载
```

### 记忆文件格式

```markdown
---
name: auto-memory
description: 自动记忆
type: user
---

# 记忆内容

## 项目信息
- 使用 React 18
- TypeScript
- Vite 构建

## 用户偏好
- 喜欢使用 pnpm
- 代码检查用 ESLint
```

---

## 🤖 Agent Memory

### 隔离性

```
★ Insight ─────────────────────────────────────
Agent Memory 是隔离的
Agent A 写的记忆
Agent B 看不到
─────────────────────────────────────────────────
```

### 共享 Memory

```yaml
# Agent A 和 Agent B 共享项目记忆
Agent A:
  memory: project
  # 写入 .claude/agent-memory/project/

Agent B:
  memory: project
  # 读取同一目录
```

### 持久化策略

| 策略 | 说明 | 适用场景 |
|------|------|----------|
| `memory: user` | 跨项目共享 | 用户偏好 |
| `memory: project` | 项目内共享 | 团队规范 |
| `memory: local` | 仅本地 | 临时信息 |

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基础概念 |
| [05 - Memory 记忆系统完全指南](./05-memory.md) | 基础使用 |
| [02 - Subagents 智能体指南](./02-subagents.md) | Agent Memory |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
