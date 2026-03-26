# Settings 配置完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 Claude Code 的配置层级系统
- ✅ 掌握 settings.json 的主要配置项
- ✅ 学会配置权限管理（Permissions）
- ✅ 理解模型配置（Model Config）
- ✅ 了解沙盒模式（Sandboxing）的使用

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 了解 [Memory 记忆系统](./05-memory.md) 的层级加载

---

## 💡 配置层级系统

Claude Code 使用**层级化的配置系统**，不同层级的配置可以覆盖：

```
★ Insight ─────────────────────────────────────
配置优先级（从高到低）：
Local > Project > User > CLI Arguments
─────────────────────────────────────────────────
```

### 配置层级

| 层级 | 文件位置 | 作用范围 | 典型用途 |
|------|----------|----------|----------|
| **CLI Arguments** | 命令行参数 | 仅当前会话 | `--model`, `--permission-mode` |
| **Local** | `settings.local.json` | 仅本地用户 | 个人临时配置 |
| **Project** | `settings.json` | 项目团队 | 团队共享规范 |
| **User** | `~/.claude/settings.json` | 用户全局 | 通用偏好 |
| **Managed** | 托管策略 | 组织范围 | 企业强制策略 |

### 配置合并规则

```javascript
// 最终配置 = Managed + User + Project + Local + CLI
// 后面的会覆盖前面的
```

---

## 📂 settings.json 文件结构

### 完整示例

```json
{
  // 模型配置
  "model": "sonnet",
  "effort": "medium",

  // 权限管理
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Read(/src/**)",
      "Edit(/src/**/*.ts)"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  },

  // MCP 服务器
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  },

  // 输出格式
  "outputStyle": "explanatory",

  // 钩子配置
  "hooks": {
    "PreToolUse": [{
      "type": "command",
      "command": "python3 .claude/hooks/scripts/hooks.py"
    }]
  }
}
```

---

## ⚙️ 主要配置项

### 模型配置（Model Config）

| 配置项 | 类型 | 说明 |
|--------|------|------|
| `model` | string | 默认模型：`haiku`、`sonnet`、`opus` |
| `effort` | string | 工作量级别：`low`、`medium`、`high`、`max` |
| `fastMode` | boolean | 快速模式（使用 Opus 但输出更快） |

```json
{
  "model": "sonnet",
  "effort": "high"
}
```

### 权限管理（Permissions）

```
★ Insight ─────────────────────────────────────
权限配置是最重要的安全设置
正确配置可以防止误操作
同时保持足够的灵活性
─────────────────────────────────────────────────
```

#### 权限语法

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",           // 允许 npm run 命令
      "Read(/src/**)",             // 允许读取 src 目录
      "Edit(/src/**/*.ts)",        // 允许编辑 TypeScript 文件
      "mcp__context7__*",         // 允许 Context7 MCP 所有工具
      "WebFetch"                  // 允许获取网页
    ],
    "deny": [
      "Bash(rm -rf *)",           // 禁止删除操作
      "Bash(git push)"            // 禁止推送
    ]
  }
}
```

#### 权限粒度

| 工具 | 粒度控制 | 示例 |
|------|----------|------|
| `Bash` | 命令行模式 | `Bash(npm run *)` |
| `Read` | 文件路径 | `Read(/src/**)` |
| `Edit` | 文件路径 + 模式 | `Edit(/docs/**/*.md)` |
| `Write` | 文件路径 | `Write(/tmp/**)` |
| `Glob` | 文件模式 | `Glob(**/*.json)` |
| `Grep` | 目录 + 模式 | `Grep(/src, console.log)` |
| `MCP` | 服务器.工具 | `mcp__playwright__*` |

### 沙盒模式（Sandboxing）

沙盒模式可以限制 Claude Code 的文件和网络访问：

```json
{
  "sandbox": {
    "enabled": true,
    "fileRestrictions": {
      "allow": ["/project/src/**", "/project/tests/**"],
      "deny": ["/project/secrets/**"]
    },
    "networkRestrictions": {
      "allow": ["api.github.com", "npm.org"],
      "deny": ["*.internal"]
    }
  }
}
```

### MCP 服务器配置

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
    }
  }
}
```

### 输出格式（Output Style）

```json
{
  "outputStyle": "explanatory"
}
```

| 格式 | 说明 |
|------|------|
| `explanatory` | 详细输出，带 ★ Insight 框 |
| `concise` | 简洁输出 |
| `minimal` | 最小化输出 |

### 显示/UX 配置

```json
{
  "display": {
    "showLineNumbers": true,       // 显示行号
    "showFilePaths": true,        // 显示文件路径
    "color": "magenta"            // CLI 输出颜色
  },
  "spinnerVerb": "auto"           // 动态调整动词
}
```

---

## 💻 实战：配置团队项目

### 场景

为前端项目配置 Claude Code，团队成员都需要遵循相同的规范。

### 步骤 1：创建项目级 settings.json

```bash
mkdir -p .claude
```

### 步骤 2：配置 settings.json

```json
{
  "model": "sonnet",
  "effort": "high",

  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(npx *)",
      "Read(/src/**)",
      "Read(/tests/**)",
      "Read(/package.json)",
      "Edit(/src/**/*.ts)",
      "Edit(/src/**/*.tsx)",
      "Edit(/src/**/*.css)",
      "Write(/src/**/*.ts)",
      "Write(/src/**/*.tsx)",
      "mcp__*"
    ],
    "deny": [
      "Bash(rm *)",
      "Bash(mv *)",
      "Bash(git push --force)",
      "Read(/src/**/secrets.ts)",
      "Edit(/src/**/secrets.ts)"
    ]
  },

  "outputStyle": "explanatory",

  "display": {
    "showLineNumbers": true,
    "color": "green"
  }
}
```

### 步骤 3：添加本地覆盖（可选）

```json
<!-- .claude/settings.local.json -->
{
  "display": {
    "color": "blue"
  }
}
```

---

## 🎯 最佳实践

### 1. 使用通配符时要谨慎

```json
<!-- ❌ 过于宽松 -->
"allow": ["Bash(*)"]

<!-- ✅ 精确控制 -->
"allow": ["Bash(npm run *)", "Bash(npx *)"]
```

### 2. 区分 allow 和 deny

```json
<!-- ✅ deny 应该是兜底的 -->
"deny": [
  "Bash(rm -rf *)",     // 危险操作
  "Bash(>*)"            // 输出重定向
]
```

### 3. 使用本地配置管理敏感信息

```json
<!-- .gitignore -->
.claude/settings.local.json

<!-- settings.local.json - 不提交到 git -->
{
  "apiKeys": {
    "github": "ghp_xxxxx"
  }
}
```

### 4. 为不同环境使用不同配置

```bash
# 开发环境
claude --config .claude/settings.dev.json

# 生产环境
claude --config .claude/settings.prod.json
```

---

## ⚠️ 常见问题

### Q1：如何临时绕过权限限制？

```bash
# 使用 bypassPermissions 模式
claude --permission-mode bypassPermissions

# 或者在会话中
/permissions
```

### Q2：配置不生效怎么办？

1. 检查文件语法：`cat .claude/settings.json | jq`
2. 检查文件位置：必须在 `.claude/` 目录下
3. 重启 Claude Code

### Q3：Managed Settings 是什么？

企业管理员可以通过托管策略强制某些配置，员工无法覆盖。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基本概念 |
| [05 - Memory 记忆系统](./05-memory.md) | 配置层级加载 |
| [08 - MCP 服务器完全指南](./08-mcp.md) | MCP 配置详解 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
