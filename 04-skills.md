# Skills 技能完全指南

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解什么是 Skill 以及它与 Command 的区别
- ✅ 掌握 Skill 的两种加载模式（Agent 预加载 / 独立调用）
- ✅ 了解 12 个 frontmatter 配置字段
- ✅ 学会创建结构化的 Skill（包括 references/、examples/ 子目录）
- ✅ 掌握 Progressive Disclosure（渐进式披露）设计理念

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md) 中的基本概念
- ✅ 了解 [Subagents 智能体指南](./02-subagents.md) 中的 Agent 概念
- ✅ 了解 [Commands 命令指南](./03-commands.md) 中的 Command 概念

---

## 💡 什么是 Skill

**Skill**（技能）是一段可复用的指令集，可以被 **Agent 预加载** 或通过 **Skill 工具独立调用**。与 Command 不同，Skill 更灵活，支持渐进式披露复杂内容。

```
★ Insight ─────────────────────────────────────
Skill 是"乐谱"，不是"演奏"
定义"如何做"，不直接"做"
可以被多个 Agent 共用
─────────────────────────────────────────────────
```

### Skill vs Command vs Agent

| 特性 | Skill | Command | Agent |
|------|-------|---------|-------|
| 本质 | 指令模板 | 提示词模板 | 独立执行者 |
| 调用方式 | Skill 工具 / Agent 预加载 | `/command` | Agent 工具 |
| 上下文 | 可隔离（context: fork） | 复用主会话 | 独立隔离 |
| 复用性 | 高（可被多 Agent 共用） | 中（适合个人工作流） | 低（任务专用） |
| 适用场景 | 技能封装、跨 Agent 共享 | 工作流自动化 | 复杂多步骤任务 |

---

## 📂 Skill 文件结构

Skill 是一个**文件夹**，包含主文件 `SKILL.md` 和可选的子目录：

```
.claude/
└── skills/
    └── weather-fetcher/           # 技能文件夹
        ├── SKILL.md               # 主文件（必须）
        ├── references.md          # 参考文档（可选）
        ├── examples.md            # 示例代码（可选）
        └── scripts/               # 脚本文件（可选）
            └── fetch-weather.sh   # 辅助脚本
```

### 渐进式披露结构

```
★ Insight ─────────────────────────────────────
Progressive Disclosure（渐进式披露）
先显示核心内容
需要时再展开详细信息
避免一次性加载过多信息造成混乱
─────────────────────────────────────────────────
```

```
SKILL.md（主文件）
├── 核心指令（始终加载）
└── 简短参考（经常用到）

references.md（按需加载）
├── API 详细文档
├── 完整配置选项
└── 错误处理指南

examples.md（按需加载）
├── 基础用法示例
├── 高级用法示例
└── 边界情况处理
```

---

## 📋 12 个 Frontmatter 字段详解

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 技能名称，同时也是 `/skill-name` 的触发词 |
| `description` | string | **关键！** 自动发现的触发描述 |
| `argument-hint` | string | 参数提示 |
| `disable-model-invocation` | boolean | 禁止自动调用 |
| `user-invocable` | boolean | 设为 `false` 仅用于 Agent 预加载 |
| `allowed-tools` | list | 允许的工具 |
| `model` | string | 指定模型 |
| `effort` | string | 工作量级别 |
| `context` | string | 设为 `fork` 在隔离上下文中运行 |
| `agent` | string | `context: fork` 时的 Agent 类型 |
| `hooks` | object | 生命周期钩子 |
| `shell` | string | `!` 命令块使用的 shell |

---

## 🔄 Skill 的两种加载模式

### 模式一：Agent 预加载

Skill 被加载到 Agent 的上下文中，Agent 执行时可以使用该 Skill。

```yaml
# .claude/agents/weather-agent.md
name: weather-agent
description: 天气查询智能体
skills:          # 预加载这些 Skill
  - weather-fetcher   # Skill 名称
  - weather-formatter
```

### 模式二：独立调用

通过 Skill 工具直接调用 Skill。

```bash
# 在 Claude Code 中
Skill(weather-fetcher)   # 使用 Skill 工具调用
```

---

## 💻 实战：创建一个天气获取 Skill

### 场景

创建一个可复用的天气获取 Skill，可以被多个 Agent 共用。

### 步骤 1：创建 Skill 目录结构

```bash
mkdir -p .claude/skills/weather-fetcher
mkdir -p .claude/skills/weather-fetcher/references
mkdir -p .claude/skills/weather-fetcher/examples
```

### 步骤 2：编写主 SKILL.md 文件

```markdown
<!-- .claude/skills/weather-fetcher/SKILL.md -->
---
# 技能名称
name: weather-fetcher

# 触发描述 - 这是关键！
# Claude 根据这个判断何时应该调用此 Skill
description: |
  从 Open-Meteo API 获取天气预报数据
  使用时机：
  - 用户询问天气（"北京天气怎么样"）
  - 需要获取指定城市的温度
  - 需要显示天气图标或天气状况

# 仅用于 Agent 预加载，不显示在 / 菜单
user-invocable: false

# 支持 bash 命令
shell: bash
---

# 天气获取技能

你是一个专业的天气预报助手，擅长从 Open-Meteo API 获取天气数据。

## 核心能力

### 1. 获取当前天气
使用 curl 命令调用 Open-Meteo API：

```bash
# 获取迪拜的当前天气
curl "https://api.open-meteo.com/v1/forecast?latitude=25.20&longitude=55.27&current_weather=true"
```

### 2. API 参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| latitude | 纬度 | 25.20（迪拜） |
| longitude | 经度 | 55.27（迪拜） |
| current_weather | 获取当前天气 | true |

### 3. 常见城市坐标

| 城市 | 纬度 | 经度 |
|------|------|------|
| 北京 | 39.90 | 116.40 |
| 上海 | 31.22 | 121.46 |
| 迪拜 | 25.20 | 55.27 |
| 纽约 | 40.71 | -74.00 |

## 输出格式

返回格式化的天气信息：

```
🌡️ 温度：28°C
🌤️ 天气：晴朗
📍 位置：迪拜
```

## 注意事项

- 如果用户没有指定单位，默认使用摄氏度
- 如果 API 请求失败，告知用户并建议重试
- 不要硬编码城市坐标，使用参数化方式
```

### 步骤 3：编写参考文档

```markdown
<!-- references.md - 详细内容按需查阅 -->

# Open-Meteo API 参考

## API 端点

```
https://api.open-meteo.com/v1/forecast
```

## 完整参数列表

| 参数 | 类型 | 说明 |
|------|------|------|
| latitude | float | 纬度 (-90 ~ 90) |
| longitude | float | 经度 (-180 ~ 180) |
| current_weather | bool | 是否返回当前天气 |
| hourly | string | 每小时预报字段 |
| daily | string | 每日预报字段 |
| timezone | string | 时区 |

## 天气代码映射

| 代码 | 描述 |
|------|------|
| 0 | 晴朗 |
| 1 | 主要晴朗 |
| 2 | 多云 |
| 3 | 阴天 |
| 45 | 雾 |
| 48 | 雾淞 |
| 51 | 小毛毛雨 |
| 53 | 中毛毛雨 |
| 55 | 大毛毛雨 |
| 61 | 小雨 |
| 63 | 中雨 |
| 65 | 大雨 |
| 71 | 小雪 |
| 73 | 中雪 |
| 75 | 大雪 |
| 77 | 雪粒 |
| 80 | 小阵雨 |
| 81 | 中阵雨 |
| 82 | 大阵雨 |
| 85 | 小阵雪 |
| 86 | 大阵雪 |
| 95 | 雷暴 |
| 96 | 雷暴伴小冰雹 |
| 99 | 雷暴伴大冰雹 |

## 错误处理

```javascript
// 请求失败时
if (!response.ok) {
  throw new Error(`API 请求失败: ${response.status}`);
}
```
```

### 步骤 4：编写示例文档

```markdown
<!-- examples.md - 使用示例 -->

## 基础用法

### 获取单个城市天气

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=39.90&longitude=116.40&current_weather=true"
```

### 获取多个城市天气

```bash
# 北京
curl "https://api.open-meteo.com/v1/forecast?latitude=39.90&longitude=116.40&current_weather=true"

# 上海
curl "https://api.open-meteo.com/v1/forecast?latitude=31.22&longitude=121.46&current_weather=true"
```

## 高级用法

### 获取每日预报

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=39.90&longitude=116.40&daily=weathercode,temperature_2m_max,temperature_2m_min"
```

### 指定时区

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=39.90&longitude=116.40&timezone=Asia/Shanghai"
```
```

---

## 🎯 最佳实践

### 1. 写好 description（触发描述）

```yaml
# ❌ 太模糊
description: 天气相关技能

# ✅ 具体说明使用时机
description: |
  从 Open-Meteo API 获取天气预报数据
  使用时机：
  - 用户询问天气
  - 需要获取指定城市的温度
```

### 2. 使用渐进式披露

```
★ Insight ─────────────────────────────────────
SKILL.md 只放核心内容
详细文档放到 references.md
示例代码放到 examples.md
避免一次性加载过多信息
─────────────────────────────────────────────────
```

### 3. 包含 Gotchas（陷阱提示）

```markdown
## ⚠️ Gotchas（容易犯的错误）

1. **忘记处理 API 限流**
   - Open-Meteo 有请求限制
   - 添加适当的延迟

2. **坐标精度问题**
   - 使用浮点数而非整数
   - 保留 2 位小数即可
```

### 4. 保持 Skill 可组合

```yaml
# ✅ 单一职责，可组合
skills:
  - weather-fetcher      # 只负责获取
  - weather-formatter    # 只负责格式化

# ❌ 过于复杂
skills:
  - weather-all-in-one   # 什么都做
```

---

## ⚠️ 常见问题

### Q1：Skill 和 Command 哪个好？

| 场景 | 推荐 | 原因 |
|------|------|------|
| 需要被多个 Agent 共用 | Skill | 一次定义，多处使用 |
| 简单的一次性任务 | Command | 轻量级 |
| 需要隔离上下文 | Skill (context: fork) | 独立环境 |
| 个人日常工作流 | Command | 直接输入 `/` |

### Q2：如何在多个项目间共享 Skill？

有几种方式：

1. **复制 Skill 文件** - 最简单
2. **创建 Git 子模块** - 便于更新
3. **发布为 npm 包** - 适合公开发布

### Q3：Skill 支持动态输入吗？

支持。在 SKILL.md 中使用参数占位符，调用时传入实际值。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制概述 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 预加载 Skill |
| [03 - Commands 命令完全指南](./03-commands.md) | Command 与 Skill 的区别 |
| [09 - 编排工作流](./09-orchestration.md) | Command → Agent → Skill 编排 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
