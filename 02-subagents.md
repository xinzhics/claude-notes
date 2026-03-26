# Subagents 智能体完全指南

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解什么是 Subagent（子智能体）以及它的工作原理
- ✅ 学会创建自定义 Subagent 并配置各种属性
- ✅ 了解 16 个 frontmatter 配置字段
- ✅ 掌握 Subagent 的典型使用场景
- ✅ 能够创建你的第一个自定义 Agent

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md) 中的基本概念
- ✅ 知道如何在终端运行 Claude Code
- ✅ 熟悉 YAML 语法（Subagent 使用 YAML frontmatter）

---

## 💡 什么是 Subagent

**Subagent** 是一个独立的 AI 智能体，运行在**隔离的上下文环境**中。它可以被主 Claude Code 会话调用，执行特定任务，然后返回结果。

```
★ Insight ─────────────────────────────────────
Subagent 就像是"分身术"
主 Agent 派一个分身去完成任务
分身在独立的环境中工作
不会污染主会话的上下文
─────────────────────────────────────────────────
```

### Subagent vs 主会话

| 特性 | 主会话 Claude Code | Subagent |
|------|-------------------|----------|
| 上下文 | 与你共享完整对话历史 | 独立上下文，任务结束即销毁 |
| 工具权限 | 继承你的全部权限 | 可精确控制权限范围 |
| 内存持久化 | 对话结束即丢失 | 可配置 memory 持久化 |
| 适用场景 | 需要连续推理的工作 | 独立、隔离的任务 |

---

## 📂 Subagent 文件结构

Subagent 定义文件位于 `.claude/agents/` 目录下：

```
.claude/
└── agents/
    ├── weather-agent.md      # 天气查询 Agent
    ├── time-agent.md         # 时间查询 Agent
    └── presentation-curator.md  # PPT 编辑 Agent
```

### 文件格式

```markdown
---
# YAML frontmatter - 配置文件
name: my-agent
description: 这个 Agent 做什么
tools:
  - Read
  - Write
  - Bash
model: sonnet
memory: project
---

# 下面是 Agent 的系统提示词
你是一个专业的...
```

---

## 📋 16 个 Frontmatter 字段详解

### 基础配置（必须了解）

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | **必须**。Agent 唯一标识符，使用小写字母和连字符 |
| `description` | string | **必须**。描述何时调用此 Agent，配合 `"PROACTIVELY"` 可自动触发 |
| `tools` | list | 允许使用的工具列表（如 `Read`, `Write`, `Bash`） |
| `model` | string | 指定使用的模型：`haiku`、`sonnet`、`opus`、`inherit` |

### 权限与安全

| 字段 | 类型 | 说明 |
|------|------|------|
| `permissionMode` | string | 权限模式：`default`、`acceptEdits`、`dontAsk`、`bypassPermissions`、`plan` |
| `disallowedTools` | list | 禁止使用的工具（与 tools 配合精确控制） |

### 执行控制

| 字段 | 类型 | 说明 |
|------|------|------|
| `maxTurns` | integer | 最大交互轮次，防止无限循环 |
| `background` | boolean | 是否始终作为后台任务运行 |

### 技能与记忆

| 字段 | 类型 | 说明 |
|------|------|------|
| `skills` | list | **重要**！预加载的技能列表，Agent 启动时自动加载 |
| `memory` | string | 持久化记忆范围：`user`（用户级）、`project`（项目级）、`local`（本地） |

### 高级配置

| 字段 | 类型 | 说明 |
|------|------|------|
| `mcpServers` | list | MCP 服务器配置 |
| `hooks` | object | 生命周期钩子（如 `PreToolUse`、`PostToolUse`） |
| `effort` | string | 工作量级别：`low`、`medium`、`high`、`max` |
| `isolation` | string | 设为 `"worktree"` 可在独立 git 工作目录中运行 |
| `initialPrompt` | string | 自动提交的初始提示词 |
| `color` | string | CLI 输出颜色（`green`、`magenta` 等） |

---

## 💻 实战：创建一个天气查询 Agent

### 完整示例

```markdown
<!-- .claude/agents/weather-agent.md -->
---
# Agent 名称：唯一标识符
name: weather-agent

# 何时调用此 Agent
description: |
  当用户询问天气或需要获取天气预报时调用此 Agent
  支持城市名称查询，返回温度和天气状况

# 允许的工具
tools:
  - Read      # 读取文件
  - Write     # 写入文件
  - Bash      # 执行命令
  - Glob      # 搜索文件
  - Grep      # 搜索内容

# 使用 sonnet 模型
model: sonnet

# 预加载天气获取技能
skills:
  - weather-fetcher

# 项目级记忆 - 记住之前的查询结果
memory: project
---

# 系统提示词 - 告诉 Agent 如何工作
你是一个专业的天气预报助手。

你的工作流程：
1. 理解用户查询的城市
2. 使用 weather-fetcher 技能获取天气数据
3. 将结果格式化为用户友好的响应

注意：
- 如果用户没有指定单位，默认使用摄氏度
- 如果城市不存在，告知用户并建议类似城市
```

### 预加载的 Skill 文件

```markdown
<!-- .claude/skills/weather-fetcher/SKILL.md -->
name: weather-fetcher
description: |
  从 Open-Meteo API 获取天气预报数据
  使用时机：需要获取天气预报时调用此技能

# 技能指令
你将学会如何获取天气数据：

## API 调用方法
使用 curl 命令调用 Open-Meteo API：

```bash
# 获取迪拜的天气
curl "https://api.open-meteo.com/v1/forecast?latitude=25.20&longitude=55.27&current_weather=true"
```

## 返回数据解析
API 返回 JSON 格式，包含：
- current_weather.temperature：当前温度
- current_weather.weathercode：天气代码

## 天气代码映射
| 代码 | 天气 |
|------|------|
| 0 | 晴朗 |
| 1-3 | 多云 |
| 45-48 | 雾 |
| 51-67 | 雨 |
| 71-77 | 雪 |
```

---

## 🔧 如何调用 Subagent

### 在主会话中调用

```bash
# 使用 Agent 工具调用
# 这是 Claude Code 的内置工具，用于启动子 Agent

/agents    # 查看所有可用的 Agent
```

### 在代码中调用

如果你想通过代码调用，可以使用 Claude Agent SDK：

```javascript
// 导入 Agent SDK
import { Agent } from '@anthropic-ai/claude-agent-sdk';

// 创建 Agent 实例
const agent = new Agent({
  name: 'weather-agent',
  // 配置其他选项...
});

// 执行任务
const result = await agent.run('北京今天天气怎么样？');
console.log(result);
```

---

## 🎯 最佳实践

### 1. 保持 Agent 职责单一

```
★ Insight ─────────────────────────────────────
一个 Agent 应该只做一件事
做得很好（Single Responsibility Principle）
不要让一个 Agent 既查天气又做 PPT
─────────────────────────────────────────────────
```

**❌ 不推荐：一个 Agent 做所有事

```yaml
name: super-agent
description: 什么都能做的超级 Agent
```

**✅ 推荐：职责单一的 Agent

```yaml
name: weather-agent
description: 只负责天气查询

name: ppt-agent
description: 只负责 PPT 制作
```

### 2. 使用 Skills 实现技能复用

```yaml
# Agent 只负责编排，技能单独定义
name: weather-agent
skills:
  - weather-fetcher    # 可复用
  - weather-formatter  # 可复用
```

### 3. 合理配置 Memory

```yaml
# 需要跨会话记住信息
memory: project

# 临时任务不需要
memory: local
```

### 4. 使用 PROACTIVELY 实现自动触发

```yaml
# 当 Claude Code 检测到相关任务时，自动调用此 Agent
description: |
  PROACTIVELY 当用户需要代码审查时调用
  检测关键词：review、PR、pull request
```

---

## ⚠️ 常见问题

### Q1：Subagent 和主会话共享上下文吗？

**不共享**。每个 Subagent 有独立的上下文环境。这是设计上的优势——你可以并行运行多个 Agent 而不互相干扰。

### Q2：Agent 挂掉了怎么办？

```bash
/tasks    # 查看后台任务状态
/stop     # 停止卡住的 Agent
```

### Q3：如何让 Agent 之间共享信息？

通过 **Memory** 系统：

```yaml
# Agent A 写入记忆
memory: project
# 写入文件到 .claude/agent-memory/myagent/

# Agent B 读取记忆
memory: project
# 从同一位置读取
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制概述 |
| [03 - Commands 命令完全指南](./03-commands.md) | Command 与 Agent 的配合使用 |
| [04 - Skills 技能完全指南](./04-skills.md) | 技能的两种加载模式 |
| [11 - Agent Teams 团队协作](./11-agent-teams.md) | 多 Agent 协作 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
