# Agent Teams 团队协作

![难度](https://img.shields.io/badge/难度-高级-red?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 Agent Teams 的概念
- ✅ 学会配置多 Agent 协作
- ✅ 掌握 tmux 集成方式
- ✅ 了解 Git Worktree 并行开发

## 🔰 前置知识

- ✅ 了解 [Subagents 智能体指南](./02-subagents.md)
- ✅ 了解 [编排工作流](./09-orchestration.md)
- ✅ 熟悉终端和多会话管理

---

## 💡 什么是 Agent Teams

**Agent Teams** 允许多个 Agent 在同一个代码库上**并行工作**，通过共享任务协调实现协作。

```
★ Insight ─────────────────────────────────────
Agent Teams = 多 Agent 并行
每个 Agent 做不同任务
最后合并结果
─────────────────────────────────────────────────
```

### 单 Agent vs Agent Teams

| 特性 | 单 Agent | Agent Teams |
|------|----------|-------------|
| 任务处理 | 串行 | 并行 |
| 上下文 | 独立 | 共享代码库 |
| 通信 | 通过主会话 | 任务协调 |
| 适用场景 | 简单任务 | 复杂多任务 |

---

## 🔧 配置 Agent Teams

### 环境变量

```bash
# 启用 Agent Teams（实验性功能）
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

### 启用 Hook

```json
{
  "hooks": {
    "TeammateIdle": [...],
    "TaskCompleted": [...]
  }
}
```

---

## 💻 实战：并行代码审查

### 场景

同时审查多个文件的代码质量。

### 步骤 1：创建多个专项 Agent

```yaml
<!-- .claude/agents/code-reviewer.md -->
---
name: code-reviewer
description: 代码审查专家
model: sonnet
tools:
  - Read
  - Grep
  - Glob
---

# 代码审查 Agent

你是一个资深的代码审查专家。
审查给定的代码文件，检查：
1. 代码质量
2. 潜在 bug
3. 性能问题
4. 安全漏洞
```

```yaml
<!-- .claude/agents/security-reviewer.md -->
---
name: security-reviewer
description: 安全审查专家
model: sonnet
tools:
  - Read
  - Grep
---

# 安全审查 Agent

你是一个安全专家。
审查代码中的：
1. SQL 注入风险
2. XSS 漏洞
3. 敏感信息暴露
4. 权限控制问题
```

### 步骤 2：协调多个 Agent

```bash
# 启动多个 Agent 并行审查
Agent(code-reviewer, prompt: "审查 src/components/*.tsx")
Agent(security-reviewer, prompt: "审查 src/api/*.ts")
Agent(performance-reviewer, prompt: "审查 src/utils/*.py")
```

---

## 🔗 tmux 集成

### tmux 基本操作

```bash
# 创建命名会话
tmux new -s claude-workflow

# 分割窗口
Ctrl+b %    # 垂直分割
Ctrl+b "    # 水平分割

# 在窗口间切换
Ctrl+b 方向键
```

### 多 Claude 会话并行

```bash
# tmux 脚本：并行启动多个 Claude
#!/bin/bash

# 创建会话
tmux new-session -d -s workflow

# 分割并启动不同 Agent
tmux split-window -h -t workflow
tmux send-keys -t workflow:0.0 'claude --agent code-reviewer' C-m
tmux send-keys -t workflow:0.1 'claude --agent test-runner' C-m

# 附加到会话
tmux attach -t workflow
```

---

## 🌳 Git Worktree 并行开发

### Git Worktree 概念

```
★ Insight ─────────────────────────────────────
Git Worktree = 同一仓库的多个工作目录
每个分支有独立目录
可以同时在多个分支上工作
─────────────────────────────────────────────────
```

### 使用 Worktree

```bash
# 创建新分支的工作目录
git worktree add feature-1 ../feature-1-worktree

# 查看所有 worktree
git worktree list

# 在不同目录启动 Claude
cd ../feature-1-worktree
claude

# 完成后的清理
git worktree remove feature-1
```

### Claude Code 的 Worktree 支持

Claude Code 内置了 Worktree 隔离支持：

```bash
# 启动隔离的 Claude（使用 worktree）
claude --worktree feature-branch

# 自动创建新分支的 worktree
claude --worktree new-branch-name
```

---

## 📋 Agent Teams 任务协调

### 任务状态 Hook

```json
{
  "hooks": {
    "TaskCompleted": [{
      "type": "prompt",
      "prompt": "检查任务是否完成，$ARGUMENTS",
      "timeout": 30
    }],
    "TeammateIdle": [{
      "type": "prompt",
      "prompt": "检查团队成员是否空闲",
      "timeout": 30
    }]
  }
}
```

---

## 🎯 最佳实践

### 1. 避免任务冲突

```bash
# ❌ 多个 Agent 修改同一文件
Agent(A, prompt: "修改 src/app.py")
Agent(B, prompt: "修改 src/app.py")

# ✅ 分配不同文件
Agent(A, prompt: "修改 src/module_a.py")
Agent(B, prompt: "修改 src/module_b.py")
```

### 2. 使用清晰的命名

```bash
# Agent 命名
Agent(code-reviewer-frontend)
Agent(code-reviewer-backend)
Agent(code-reviewer-tests)
```

### 3. 合并结果

```bash
# 主 Agent 收集并合并所有结果
Agent(coordinator, prompt: "收集所有审查结果并生成报告")
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 基础 |
| [09 - 编排工作流](./09-orchestration.md) | 多组件协作 |
| [12 - 86个使用技巧精选](./12-tips-selection.md) | 并行开发技巧 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
