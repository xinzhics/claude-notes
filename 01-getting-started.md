# Claude Code 入门完全指南

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 Claude Code 是什么以及它能做什么
- ✅ 了解 Claude Code 的四大核心扩展机制（Agents、Commands、Skills、Hooks）
- ✅ 掌握基础使用方式和常见命令
- ✅ 能够创建你的第一个自定义 Command 或 Skill

## 🔰 前置知识

- ✅ 会使用命令行终端（Terminal）
- ✅ 了解什么是编程和代码
- ✅ 知道如何安装 Node.js/npm（部分功能需要）

---

## 💡 核心概念

| 术语 | 英文 | 解释 |
|------|------|------|
| 智能体 | **Agent** | 可以独立执行任务的自动化程序 |
| 命令 | **Command** | 以 `/` 开头的快捷指令 |
| 技能 | **Skill** | 可复用的技能模块 |
| 钩子 | **Hook** | 自动化触发的事件处理 |

---

## 📖 什么是 Claude Code

Claude Code 是 Anthropic 公司开发的 AI 编程助手，直接在你的**终端**（Terminal）中运行。与其他 AI 编程工具不同，Claude Code 不只是一个聊天机器人——它是一个完整的**开发环境**，可以：

- 🔍 阅读和理解你的代码库
- ✏️ 直接编辑和创建文件
- 💻 执行终端命令
- 🔧 集成各种外部工具和服务
- 🤖 调用其他 AI 智能体（Agent）帮你完成任务

```
★ Insight ─────────────────────────────────────
Claude Code 的核心理念："你的副驾驶员"
不是替代你编程，而是增强你的能力
它在你熟悉的环境中工作（终端 + 代码编辑器）
─────────────────────────────────────────────────
```

---

## 🚀 快速开始

### 1. 安装 Claude Code

```bash
# 使用 npm 安装（需要 Node.js）
npm install -g @anthropic-ai/claude-code

# 或者使用 curl
curl -sSL https://storage.googleapis.com/claude-code/ | sh
```

### 2. 启动 Claude Code

```bash
# 进入你的项目目录
cd your-project-folder

# 启动 Claude Code
claude
```

### 3. 基本交互

```bash
# 直接输入你的问题或任务
> 帮我创建一个用户登录功能

# 使用 /plan 进入计划模式
/plan

# 查看帮助
/help
```

---

## 🏗️ Claude Code 的四大扩展机制

理解这四个概念是掌握 Claude Code 的关键：

```
★ Insight ─────────────────────────────────────
它们的关系就像一支乐队：
• Command = 点歌员（你告诉它要什么）
• Agent = 乐手（真正演奏的人）
• Skill = 乐谱（告诉乐手怎么演奏）
• Hook = 调音师（幕后调整）
─────────────────────────────────────────────────
```

### Command（命令）— `/` 开头的快捷指令

Command 是你输入 `/` 时看到的命令，是 Claude Code 的**入口点**。

```bash
/claude            # 打开帮助
/weather-orchestrator  # 运行天气工作流
/plan              # 进入计划模式
/model             # 切换 AI 模型
```

**特点**：
- 用户主动触发
- 简单易用
- 适合日常工作流程

### Agent（智能体）— 独立的任务执行者

Agent 是一个独立的 AI 程序，可以在**隔离的环境**中执行复杂任务。

```yaml
# .claude/agents/weather-agent.md
name: weather-agent
description: 获取天气数据并生成 SVG 卡片
skills:
  - weather-fetcher  # 预加载的技能
memory: project      # 持久化记忆
```

**特点**：
- 独立执行多步骤任务
- 可以调用其他 Agent
- 支持持久化身份和记忆

### Skill（技能）— 可复用的指令模块

Skill 是一段可以**被复用**的指令集，可以嵌入到 Agent 或 Command 中。

```yaml
# .claude/skills/weather-fetcher/SKILL.md
name: weather-fetcher
description: |
  从 Open-Meteo API 获取天气数据
  使用时机：当需要获取天气预报时调用
```

**特点**：
- 可插拔设计
- 支持渐进式披露（progressive disclosure）
- 可以预加载到 Agent 中

### Hook（钩子）— 事件触发器

Hook 让你在特定事件发生时**自动执行**某些操作。

```python
# .claude/hooks/config/hooks-config.json
{
  "events": ["PostToolUse"],
  "actions": ["run-code-formatter"]
}
```

**特点**：
- 事件驱动
- 可自定义脚本
- 支持团队共享配置

---

## 💻 快速实践：创建一个简单的 Command

### 步骤 1：创建 Command 文件

```bash
# 创建命令目录（如果不存在）
mkdir -p .claude/commands
```

### 步骤 2：编写 Command 文件

```markdown
<!-- .claude/commands/hello.md -->
name: hello
description: 向 Claude Code 打招呼
```

```markdown
# 向用户打招呼

你好！我是 Claude Code，你的 AI 编程助手。

我可以帮你：
- 编写和修改代码
- 解释代码逻辑
- 调试问题
- 执行终端命令

有什么我可以帮你的吗？
```

### 步骤 3：使用你的 Command

```bash
# 在 Claude Code 中输入
/hello
```

---

## ⚠️ 常见问题

### Q1：Claude Code 和 GitHub Copilot 有什么区别？

| 特性 | Claude Code | GitHub Copilot |
|------|-------------|----------------|
| 运行位置 | 独立终端 | IDE 内嵌 |
| 交互方式 | 对话式 | 自动补全 |
| 工具能力 | 完整终端 + 文件操作 | 主要代码补全 |
| 扩展性 | Agents/Skills/Commands | 相对有限 |

### Q2：Claude Code 可以替代 IDE 吗？

**不能**。Claude Code 是一个**命令行工具**，不是完整的代码编辑器。它最适合：
- 与你喜欢的编辑器配合使用
- 处理复杂的多步骤任务
- 自动化重复性工作

### Q3：Command 和 Agent 哪个更好？

**没有绝对答案**，取决于场景：

| 场景 | 推荐 | 原因 |
|------|------|------|
| 简单的一次性任务 | Command | 快速、直接 |
| 复杂的多步骤任务 | Agent | 隔离环境、不污染主上下文 |
| 需要复用的工作流 | Skill | 一次编写、多次使用 |

---

## 🎯 最佳实践

### 1. 从小处开始

不要一开始就创建复杂的 Agent 系统。先从简单的 Command 开始，熟悉 Claude Code 的工作方式。

### 2. 保持 CLAUDE.md 简洁

```markdown
<!-- ❌ 太多内容 -->
<!-- ✅ 简洁明了 -->
# CLAUDE.md
我的项目使用 React + TypeScript
使用 /mycommand 运行常见任务
```

### 3. 常用命令收藏

```bash
/plan          # 进入计划模式（推荐起步方式）
/model         # 切换模型
/context       # 查看上下文使用
/compact       # 压缩对话、释放空间
/permissions   # 管理权限
```

### 4. 定期清理对话

```bash
/clear         # 清除对话，重新开始
/compact       # 压缩历史，保留重要上下文
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [02 - Subagents 智能体完全指南](./02-subagents.md) | 深入了解 Agent 的 16 个配置字段 |
| [03 - Commands 命令完全指南](./03-commands.md) | 如何创建和使用自定义命令 |
| [04 - Skills 技能完全指南](./04-skills.md) | 两种 Skills 模式详解 |
| [05 - Memory 记忆系统完全指南](./05-memory.md) | 让 Claude 记住项目上下文 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
