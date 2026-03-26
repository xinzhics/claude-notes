# Plugins 插件完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解什么是 Plugin 以及它与组件的关系
- ✅ 学会安装和管理插件
- ✅ 了解如何创建自己的插件
- ✅ 掌握插件市场的使用方法

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 了解 [Skills 技能完全指南](./04-skills.md)
- ✅ 了解 [Subagents 智能体指南](./02-subagents.md)

---

## 💡 什么是 Plugin

**Plugin**（插件）是一个**可分发 packages**，将多种组件打包在一起：
- Skills（技能）
- Subagents（智能体）
- Hooks（钩子）
- MCP Servers（MCP 服务器）

```
★ Insight ─────────────────────────────────────
Plugin = 组件的集合
Skills + Agents + Hooks + MCP = Plugin
可以一键安装整套工具
─────────────────────────────────────────────────
```

### Plugin vs 独立组件

| 特性 | Plugin | 独立组件 |
|------|---------|-----------|
| 包含内容 | 多种组件打包 | 单一组件 |
| 安装方式 | `/plugin` 或市场 | 手动创建 |
| 分发性 | 便于分享 | 仅本地 |
| 团队共享 | ✅ 非常方便 | ⚠️ 需要复制文件 |

---

## 📦 Plugin 的结构

### 目录结构

```
my-plugin/
├── .claude/
│   ├── skills/           # 技能
│   │   └── my-skill/
│   │       └── SKILL.md
│   ├── agents/           # 智能体
│   │   └── my-agent.md
│   ├── commands/         # 命令
│   │   └── my-command.md
│   └── hooks/           # 钩子
│       └── config/
│           └── hooks-config.json
├── skills/              # 也支持顶层 skills
│   └── another-skill/
│       └── SKILL.md
└── plugin.json         # 插件元数据
```

### plugin.json 示例

```json
{
  "name": "my-awesome-plugin",
  "version": "1.0.0",
  "description": "我的插件描述",
  "author": "Your Name",
  "marketplace": {
    "repo": "username/my-plugin",
    "tag": "v1.0.0"
  }
}
```

---

## 🔧 安装和管理插件

### 使用命令管理

```bash
/plugin              # 打开插件管理界面
/reload-plugins      # 重新加载插件（无需重启）
```

### 安装来自 GitHub 的插件

```bash
# 通过 marketplace 安装
/plugin install username/plugin-name

# 直接从 GitHub 安装
/plugin install github:username/repo
```

### 插件命令

| 命令 | 说明 |
|------|------|
| `/plugin` | 打开插件管理 |
| `/plugin list` | 列出已安装插件 |
| `/plugin install <name>` | 安装插件 |
| `/plugin uninstall <name>` | 卸载插件 |
| `/reload-plugins` | 重新加载 |

---

## 💻 实战：创建自己的插件

### 场景

创建一个包含天气查询功能的插件，可以在团队中共享。

### 步骤 1：创建插件目录

```bash
mkdir -p weather-plugin/.claude/skills
mkdir -p weather-plugin/.claude/agents
mkdir -p weather-plugin/.claude/commands
```

### 步骤 2：创建 plugin.json

```json
<!-- weather-plugin/plugin.json -->
{
  "name": "weather-tools",
  "version": "1.0.0",
  "description": "天气查询工具集",
  "author": "Your Team",
  "skills": [
    "weather-fetcher"
  ],
  "agents": [
    "weather-agent"
  ],
  "commands": [
    "weather"
  ]
}
```

### 步骤 3：添加 Skill

```markdown
<!-- weather-plugin/.claude/skills/weather-fetcher/SKILL.md -->
---
name: weather-fetcher
description: 从 Open-Meteo 获取天气数据
---

# 天气获取技能

使用 Open-Meteo API 获取天气预报...
```

### 步骤 4：添加 Agent

```yaml
<!-- weather-plugin/.claude/agents/weather-agent.md -->
---
name: weather-agent
description: 天气查询智能体
skills:
  - weather-fetcher
---

# 天气查询智能体

使用预加载的天气技能查询天气...
```

### 步骤 5：添加 Command

```markdown
<!-- weather-plugin/.claude/commands/weather.md -->
---
name: weather
description: 查询天气
---

# 天气查询命令

询问用户要查询的城市，然后调用 weather-agent 获取天气数据。
```

### 步骤 6：发布插件

```bash
# 1. 推送到 GitHub
git push

# 2. 创建 GitHub Release
# 添加 tag: v1.0.0

# 3. 团队成员安装
/plugin install yourusername/weather-plugin
```

---

## 🏪 插件市场

### 官方市场

Anthropic 提供了官方插件市场：
- [Discover Plugins](https://code.claude.com/docs/en/discover-plugins)
- [Create Marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)

### 创建团队市场

```json
<!-- settings.json -->
{
  "extraKnownMarketplaces": [
    {
      "name": "公司插件市场",
      "type": "github",
      "repo": "my-company/claude-plugins"
    }
  ]
}
```

### Marketplace 源类型

| 类型 | 说明 | 示例 |
|------|------|------|
| `github` | GitHub 仓库 | `"repo": "user/repo"` |
| `npm` | npm 包 | `"package": "@org/plugin"` |
| `directory` | 本地目录 | `"path": "/path/to/plugin"` |
| `url` | 直接 URL | `"url": "https://..."` |
| `settings` | 内联配置 | 小型插件 |

---

## 🎯 最佳实践

### 1. 保持插件职责单一

```
★ Insight ─────────────────────────────────────
一个插件应该围绕一个主题
不要做成"瑞士军刀"
保持插件内聚性
─────────────────────────────────────────────────
```

### 2. 使用清晰的命名

```bash
# ✅ 清晰
weather-plugin
code-review-tools
deployment-helper

# ❌ 模糊
my-plugin
tools
utils
```

### 3. 版本管理

```bash
# 使用语义化版本
v1.0.0  # 首次发布
v1.1.0  # 新增功能
v2.0.0  # 破坏性更新
```

### 4. 文档完善

```markdown
# README.md
## 安装
## 使用方法
## 贡献指南
## 许可证
```

---

## ⚙️ 插件配置

### settings.json 中的插件配置

```json
{
  "extraKnownMarketplaces": [
    {
      "name": "公司市场",
      "type": "github",
      "repo": "my-company/plugins"
    }
  ],
  "enabledPlugins": {
    "marketplace-name/plugin-name": {
      "enabled": true
    }
  },
  "skippedPlugins": ["unwanted/plugin"]
}
```

### 插件信任

```json
{
  "pluginTrustMessage": "此插件来自可信源..."
}
```

---

## ⚠️ 常见问题

### Q1：插件和独立组件哪个好？

| 场景 | 推荐 | 原因 |
|------|------|------|
| 团队共享 | ✅ Plugin | 一键安装整套工具 |
| 个人使用 | 两者皆可 | 个人偏好 |
| 微调修改 | 独立组件 | 更灵活 |

### Q2：如何更新插件？

```bash
/plugin update              # 更新所有
/plugin update name/plugin  # 更新特定插件
/reload-plugins            # 重新加载
```

### Q3：插件冲突怎么办？

检查是否有同名的 skills/agents/commands。插件内的组件使用 `plugin-name:component-name` 命名空间。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基本概念 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 组件 |
| [04 - Skills 技能完全指南](./04-skills.md) | Skill 组件 |
| [07 - Hooks 钩子完全指南](./07-hooks.md) | Hook 组件 |
| [18 - Rules 规则完全指南](./18-rules.md) | 规则组件 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
