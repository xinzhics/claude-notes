# MCP 服务器完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 MCP（Model Context Protocol）的概念
- ✅ 学会配置 MCP 服务器
- ✅ 了解推荐的 MCP 服务器及其用途
- ✅ 掌握 MCP 权限管理
- ✅ 理解 MCP 的三个作用域

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 了解 [Settings 配置完全指南](./06-settings.md) 中的权限配置

---

## 💡 什么是 MCP

**MCP（Model Context Protocol）** 是一种开放协议，允许 Claude Code 连接外部工具、数据源和 API。它就像一个"转接头"，让 Claude 能够使用各种第三方服务。

```
★ Insight ─────────────────────────────────────
MCP = Model Context Protocol
让 Claude 连接外部世界的桥梁
不写代码就能调用各种 API 和工具
─────────────────────────────────────────────────
```

### MCP vs 内置工具

| 特性 | 内置工具 | MCP |
|------|----------|-----|
| 数量 | 固定（Read、Write 等） | 可无限扩展 |
| 配置 | 内置 | 需要安装和配置 |
| 灵活性 | 固定功能 | 可自定义 |
| 性能 | 最优 | 有网络开销 |

---

## 📦 推荐的 MCP 服务器

### 日常必备（推荐安装）

| MCP | 功能 | 使用场景 |
|-----|------|----------|
| **Context7** | 获取最新的库文档 | 防止使用过时的 API |
| **Playwright** | 浏览器自动化 | 前端测试、UI 验证 |
| **Chrome** | 真实浏览器控制 | 调试用户看到的问题 |
| **DeepWiki** | GitHub 仓库文档 | 理解代码架构 |
| **Excalidraw** | 生成图表 | 架构图、流程图 |

### 工作流推荐

```
★ Insight ─────────────────────────────────────
推荐工作流：
研究（Context7/DeepWiki）→ 调试（Playwright/Chrome）→ 文档（Excalidraw）
─────────────────────────────────────────────────
```

---

## 📂 MCP 配置

### 配置文件位置

| 作用域 | 文件位置 | 说明 |
|--------|----------|------|
| **项目级** | `.mcp.json` | 团队共享，提交到 git |
| **用户级** | `~/.claude.json` | 个人配置，所有项目可用 |
| **智能体级** | Agent frontmatter | 特定 Agent 专用 |

### .mcp.json 示例

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    },
    "deepwiki": {
      "command": "npx",
      "args": ["-y", "deepwiki-mcp"]
    }
  }
}
```

### 服务器类型

#### stdio 类型（本地进程）

```json
{
  "mcpServers": {
    "local-server": {
      "command": "python",
      "args": ["/path/to/server.py"]
    }
  }
}
```

#### http 类型（远程服务器）

```json
{
  "mcpServers": {
    "remote-api": {
      "type": "http",
      "url": "https://api.example.com/mcp"
    }
  }
}
```

### 环境变量（敏感信息）

```json
{
  "mcpServers": {
    "remote-api": {
      "type": "http",
      "url": "https://api.example.com/mcp?token=${API_TOKEN}"
    }
  }
}
```

---

## 💻 实战：配置 Context7 MCP

### Context7 是什么

Context7 可以获取**最新的库文档**，解决 AI 训练数据过时的问题。

```
★ Insight ─────────────────────────────────────
Context7 = 实时文档获取
不用再担心 AI 说的 API 已经过时
直接在上下文中查看最新文档
─────────────────────────────────────────────────
```

### 步骤 1：安装

```bash
npx -y @upstash/context7-mcp
```

### 步骤 2：配置

```json
<!-- .mcp.json -->
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

### 步骤 3：使用

```
在 Claude Code 中，你可以说：
"用 Context7 查一下 React 18 的最新 Hooks API"
```

---

## 💻 实战：配置 Playwright MCP

### Playwright 是什么

Playwright 是一个浏览器自动化工具，可以自动操作浏览器进行测试。

### 步骤 1：安装

```bash
npx -y @playwright/mcp
```

### 步骤 2：配置

```json
<!-- .mcp.json -->
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    }
  }
}
```

### 步骤 3：使用

```
"帮我测试登录页面，填写表单并提交，截图确认结果"
```

---

## 🔐 MCP 权限管理

### 权限命名规则

```
mcp__<服务器名>__<工具名>
```

### 示例

```json
{
  "permissions": {
    "allow": [
      "mcp__*",                      // 所有 MCP 工具
      "mcp__context7__*",           // Context7 所有工具
      "mcp__playwright__browser_snapshot"  // 特定工具
    ],
    "deny": [
      "mcp__dangerous-server__*"    // 禁止危险服务器
    ]
  }
}
```

### MCP 设置项

在 `settings.json` 中：

```json
{
  "enableAllProjectMcpServers": true,    // 自动批准所有项目 MCP
  "enabledMcpjsonServers": ["context7", "playwright"],
  "disabledMcpjsonServers": ["untrusted-server"]
}
```

---

## 🔄 MCP 作用域

### 三个作用域

```
用户级（~/.claude.json）
    ↓ 覆盖
项目级（.mcp.json）
    ↓ 覆盖
智能体级（Agent frontmatter）
```

### 优先级

| 优先级 | 作用域 | 说明 |
|--------|--------|------|
| 最高 | 智能体级 | 仅该 Agent 可用 |
| 中 | 项目级 | 团队共享 |
| 最低 | 用户级 | 所有项目可用 |

---

## 🎯 最佳实践

### 1. 不要安装过多 MCP

```
★ Insight ─────────────────────────────────────
"MCP 越多越好" 是误区
建议最多 4-5 个日常使用的
过多会影响性能和稳定性
─────────────────────────────────────────────────
```

### 2. 使用环境变量管理密钥

```json
<!-- ❌ 暴露密钥 -->
"url": "https://api.example.com?key=sk-xxxx"

<!-- ✅ 使用环境变量 -->
"url": "https://api.example.com?key=${API_KEY}"
```

### 3. 提交公共配置，忽略私人配置

```json
<!-- .gitignore -->
.mcp.local.json
```

### 4. 按需启用/禁用

```json
{
  "permissions": {
    "allow": ["mcp__context7__query-docs"]
  }
}
```

---

## ⚠️ 常见问题

### Q1：MCP 服务器连接失败？

1. 检查命令是否正确：`npx -y @upstash/context7-mCP`
2. 检查网络连接
3. 查看错误日志：`claude --debug`

### Q2：MCP 工具没有响应？

可能原因：
- 服务器需要认证
- 网络超时
- 工具参数错误

### Q3：如何创建自定义 MCP？

使用官方 SDK：

```python
# server.py
from mcp.server import MCPServer
from mcp.types import Tool

server = MCPServer("my-server")

@server.tool("get_weather")
def get_weather(city: str):
    return {"temperature": 25, "city": city}
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制 |
| [06 - Settings 配置完全指南](./06-settings.md) | 权限配置 |
| [12 - 86个使用技巧精选](./12-tips-selection.md) | MCP 相关技巧 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
