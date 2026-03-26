# 编排工作流：Command → Agent → Skill

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 **Command → Agent → Skill** 编排架构
- ✅ 掌握两种 Skill 模式的区别
- ✅ 学会设计一个完整的工作流
- ✅ 理解天气系统的实现案例

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 了解 [Commands 命令指南](./03-commands.md)
- ✅ 了解 [Subagents 智能体指南](./02-subagents.md)
- ✅ 了解 [Skills 技能完全指南](./04-skills.md)

---

## 💡 什么是编排工作流

**编排工作流**是将多个组件（Command、Agent、Skill）组合在一起，形成一个完整的自动化流程。

```
★ Insight ─────────────────────────────────────
编排 = 协调多个组件
每个组件做一件事
组合起来完成复杂任务
─────────────────────────────────────────────────
```

### 三种组件的角色

| 组件 | 角色 | 职责 |
|------|------|------|
| **Command** | 入口 + 协调者 | 用户交互、流程编排 |
| **Agent** | 执行者 | 使用预加载的 Skill 获取数据 |
| **Skill** | 技能模块 | 独立执行特定任务 |

---

## 🏗️ 核心架构：Command → Agent → Skill

### 流程图

```
╔══════════════════════════════════════════════════════════════════╗
║              ORCHESTRATION WORKFLOW                              ║
║           Command  →  Agent  →  Skill                          ║
╚══════════════════════════════════════════════════════════════════╝

                         ┌───────────────────┐
                         │  用户交互入口       │
                         └─────────┬─────────┘
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  /weather-orchestrator — Command (入口点)            │
         └─────────────────────────┬───────────────────────────┘
                                   │
                              Step 1：询问用户
                                   │
                                   ▼
                      ┌────────────────────────┐
                      │  选择温度单位 C° 或 F°  │
                      └────────────┬───────────┘
                                   │
                         Step 2：调用 Agent
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-agent — Agent (预加载 weather-fetcher)      │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          返回：温度 + 单位
                                   │
                         Step 3：调用 Skill
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-svg-creator — Skill (独立执行)             │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          ┌────────┴────────┐
                          ▼                 ▼
                   ┌────────────┐    ┌────────────┐
                   │weather.svg │    │ output.md  │
                   └────────────┘    └────────────┘
```

---

## 🔄 两种 Skill 模式

```
★ Insight ─────────────────────────────────────
Skill 有两种加载模式：
1. Agent 预加载 = 内化知识，随时可用
2. Skill 工具调用 = 独立执行，专注输出
─────────────────────────────────────────────────
```

### 模式 1：Agent 预加载（Agent Skill）

```yaml
# .claude/agents/weather-agent.md
---
name: weather-agent
skills:          # 预加载这些 Skill
  - weather-fetcher   # 启动时注入到上下文
---
```

**特点**：
- Skill 内容在 Agent 启动时注入
- Agent 可以直接使用这些知识
- 适合"需要知道"类型的技能

### 模式 2：Skill 工具调用（Skill）

```markdown
# Command 中调用
Skill(weather-svg-creator)   # 独立执行
```

**特点**：
- 通过 Skill 工具调用
- 在调用者的上下文中执行
- 适合"执行输出"类型的任务

---

## 💻 实战：天气查询工作流

### 案例概述

```
用户输入 → /weather-orchestrator
        ↓
    Command 询问温度单位
        ↓
    Agent 获取天气数据（使用预加载的 Skill）
        ↓
    Skill 生成 SVG 卡片
        ↓
    显示结果
```

### 组件 1：Command（入口）

```markdown
<!-- .claude/commands/weather-orchestrator.md -->
---
name: weather-orchestrator
description: 获取迪拜天气并生成 SVG 天气卡片
model: haiku
---

# 天气查询工作流

你是一个天气查询助手。请按以下步骤执行：

## 步骤 1：询问用户
询问用户希望使用摄氏度(C)还是华氏度(F)：

"请选择温度单位：
1. 摄氏度 (C)
2. 华氏度 (F)"

等待用户回复后继续。

## 步骤 2：调用 Agent 获取天气
根据用户选择，使用 Agent 工具调用 weather-agent：

```
Agent(weather-agent, prompt: "获取迪拜当前天气，温度单位：${用户选择}")
```

weather-agent 会返回温度数据。

## 步骤 3：调用 Skill 生成 SVG
使用 Skill 工具调用 weather-svg-creator：

```
Skill(weather-svg-creator)
```

## 步骤 4：展示结果
向用户展示：
- 温度数值
- 温度单位
- SVG 卡片位置
- 输出文件位置
```

### 组件 2：Agent（数据获取）

```yaml
<!-- .claude/agents/weather-agent.md -->
---
name: weather-agent
description: 获取天气预报数据
model: sonnet
color: green
memory: project
skills:
  - weather-fetcher    # 预加载的 Skill
tools:
  - WebFetch
  - Read
---

# 天气查询智能体

你是一个专业的天气预报助手。使用预加载的 weather-fetcher 技能获取天气数据。

## 任务
1. 理解用户选择的温度单位
2. 使用 weather-fetcher 技能获取迪拜天气
3. 返回温度和单位

## 返回格式
请以清晰的格式返回：
- 温度数值
- 温度单位
- 数据来源
```

### 组件 3：Agent Skill（预加载的知识）

```markdown
<!-- .claude/skills/weather-fetcher/SKILL.md -->
---
name: weather-fetcher
description: 从 Open-Meteo API 获取天气预报
user-invocable: false
---

# 天气获取技能

你将学会如何从 Open-Meteo API 获取天气数据。

## API 调用方法

使用 curl 命令：

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=25.20&longitude=55.27&current_weather=true"
```

## 返回数据解析

API 返回 JSON：
- current_weather.temperature：温度
- current_weather.weathercode：天气代码

## 温度单位

| 参数 | 说明 |
|------|------|
| temperature_unit=celsius | 摄氏度 |
| temperature_unit=fahrenheit | 华氏度 |

## 迪拜坐标
- 纬度：25.20
- 经度：55.27
```

### 组件 4：Skill（独立执行）

```markdown
<!-- .claude/skills/weather-svg-creator/SKILL.md -->
---
name: weather-svg-creator
description: 创建 SVG 天气卡片
---

# SVG 天气卡片创建

请创建一个精美的 SVG 天气卡片。

## 输入数据

从对话上下文中获取：
- 温度数值
- 温度单位

## 输出文件

1. 创建 SVG 文件：`orchestration-workflow/weather.svg`
2. 创建摘要文件：`orchestration-workflow/output.md`

## SVG 设计要求

- 尺寸：400x200 像素
- 包含天气图标
- 显示温度
- 显示位置（迪拜）
- 美观的配色方案
```

---

## 🎯 最佳实践

### 1. 保持组件职责单一

```
★ Insight ─────────────────────────────────────
每个组件只做一件事：
Command = 协调流程（不执行具体任务）
Agent = 获取数据（使用预加载 Skill）
Skill = 生成输出（独立执行）
─────────────────────────────────────────────────
```

### 2. 使用 Agent Skill 获取知识

```yaml
# Agent 预加载知识型 Skill
skills:
  - api-fetcher      # 如何调用 API
  - data-parser      # 如何解析数据
```

### 3. 使用 Skill 执行输出

```bash
# Command 调用 Skill 执行独立任务
Skill(weather-svg-creator)
Skill(report-generator)
Skill(file-formatter)
```

### 4. 清晰的接口设计

```bash
# Agent 返回结构化数据
{
  "temperature": 26,
  "unit": "celsius",
  "location": "迪拜",
  "source": "Open-Meteo"
}
```

---

## ⚠️ 常见问题

### Q1：Command 可以直接调用多个 Agent 吗？

可以。但建议通过 Agent 收集所有数据，再统一调用 Skill 生成输出。

### Q2：Skill 能在 Agent 中调用吗？

可以，但需要注意上下文继承。Agent 中调用 Skill 会使用 Agent 的上下文。

### Q3：如何处理错误？

```bash
# 在 Command 中添加错误处理
如果 Agent 调用失败：
  1. 重试一次
  2. 如果仍然失败，返回错误信息给用户
  3. 建议可能的解决方案
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制概述 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 创建与使用 |
| [03 - Commands 命令完全指南](./03-commands.md) | Command 创建与使用 |
| [04 - Skills 技能完全指南](./04-skills.md) | Skill 的两种模式 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
