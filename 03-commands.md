# Commands 命令完全指南

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解什么是 Command 以及它与 Agent 的区别
- ✅ 掌握 Command 的 12 个配置字段
- ✅ 学会创建自定义 Command
- ✅ 了解 64 个内置 Command 的分类和用途
- ✅ 能够将重复性工作封装为 Command

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md) 中的基本概念
- ✅ 了解 [Subagents 智能体指南](./02-subagents.md) 中的 Agent 概念
- ✅ 熟悉 Markdown 语法

---

## 💡 什么是 Command

**Command**（命令）是以 `/` 开头的快捷指令，是 Claude Code 的**入口点**。它定义了一段提示词模板，当用户输入 `/command-name` 时，这段提示词会被注入到 Claude Code 的上下文中。

```
★ Insight ─────────────────────────────────────
Command 是"快捷方式"
定义一次，多次使用
适合高频重复的工作流程
─────────────────────────────────────────────────
```

### Command vs Agent：核心区别

| 特性 | Command | Agent |
|------|---------|-------|
| 本质 | 提示词模板 | 独立执行的智能体 |
| 上下文 | 复用主会话上下文 | 独立隔离上下文 |
| 启动方式 | 用户输入 `/` 触发 | 通过 Agent 工具调用 |
| 适用场景 | 简单、一次性任务 | 复杂、多步骤任务 |
| 执行速度 | 快（无额外开销） | 慢（需要启动新会话） |

### 什么时候用 Command vs Agent？

| 场景 | 推荐 | 原因 |
|------|------|------|
| "帮我解释这段代码" | Command | 简单一次性任务 |
| "创建一个完整的用户系统" | Agent | 复杂多步骤，需要隔离环境 |
| "每天早上生成报告" | Command + 定时任务 | 适合工作流自动化 |
| "并行处理 10 个文件" | Agent | 需要独立上下文避免干扰 |

---

## 📂 Command 文件结构

Command 文件位于 `.claude/commands/` 目录下：

```
.claude/
└── commands/
    ├── time-command.md         # 时间命令
    ├── weather-orchestrator.md  # 天气编排命令
    └── custom/
        └── my-command.md       # 自定义命令
```

### 文件格式

```markdown
---
# YAML frontmatter - 配置文件
name: my-command
description: 这个命令做什么
argument-hint: [参数提示]
---

# 这里是提示词内容
当你输入 /my-command 时，这段内容会被注入到上下文中

## 任务描述
具体要做什么

## 执行步骤
1. 第一步
2. 第二步
```

---

## 📋 12 个 Frontmatter 字段详解

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 命令名称，同时也是 `/name` 的触发词 |
| `description` | string | 命令描述，用于自动补全提示 |
| `argument-hint` | string | 参数提示，如 `[issue-number]` |
| `disable-model-invocation` | boolean | 设为 `true` 禁止自动调用此命令 |
| `user-invocable` | boolean | 设为 `false` 隐藏于 `/` 菜单 |
| `allowed-tools` | list | 允许使用的工具 |
| `model` | string | 指定模型：`haiku`、`sonnet`、`opus` |
| `effort` | string | 工作量级别：`low`~`max` |
| `context` | string | 设为 `fork` 在隔离上下文中运行 |
| `agent` | string | `context: fork` 时指定 Agent 类型 |
| `shell` | string | `!` 命令块使用的 shell：`bash` 或 `powershell` |
| `hooks` | object | 生命周期钩子 |

---

## 💻 实战：创建一个"代码审查"Command

### 场景

你经常需要审查 PR，希望有一个快捷命令来启动代码审查流程。

### 步骤 1：创建 Command 文件

```bash
mkdir -p .claude/commands
```

### 步骤 2：编写 Command

```markdown
<!-- .claude/commands/code-review.md -->
---
# 命令名称：输入 /code-review 触发
name: code-review

# 描述：显示在自动补全中
description: |
  对当前的 PR 或代码变更进行全面的代码审查
  检查点：代码质量、安全漏洞、性能问题、测试覆盖

# 参数提示
argument-hint: [PR编号，可选]

# 指定使用 opus 模型进行深度分析
model: opus

# 高工作量级别
effort: high
---

# 代码审查命令

你是一个资深的代码审查专家。请对以下内容进行审查：

## 审查范围
- 如果提供了 PR 编号：审查该 PR 的所有变更
- 如果没有提供：审查所有未提交的变更（git diff）

## 审查维度

### 1. 代码质量
- [ ] 代码是否符合项目编码规范
- [ ] 是否有重复代码可以提取
- [ ] 变量/函数命名是否清晰
- [ ] 是否有不必要的复杂性

### 2. 安全性
- [ ] 是否有 SQL 注入风险
- [ ] 是否有 XSS 跨站脚本风险
- [ ] 敏感信息是否硬编码
- [ ] 权限检查是否完整

### 3. 性能
- [ ] 是否有明显的性能问题
- [ ] 数据库查询是否需要优化
- [ ] 是否有不必要的循环或递归

### 4. 测试覆盖
- [ ] 新功能是否有测试
- [ ] 测试是否覆盖边界情况
- [ ] 现有测试是否可能因变更而失败

## 输出格式

请按以下格式输出审查结果：

```
## 📋 代码审查报告

### ✅ 通过项
（列出做得好的地方）

### ⚠️ 需要改进
（列出需要修改的问题，按严重程度排序）

### 🔴 阻塞问题
（必须修复才能合并的问题）

### 💡 建议
（可选的改进建议）
```

## 执行步骤

1. 首先获取 PR 信息或 git diff
2. 逐文件进行审查
3. 对照检查清单
4. 生成审查报告
```

### 步骤 3：使用你的 Command

```bash
# 在 Claude Code 中输入
/code-review

# 或者带参数
/code-review 123
```

---

## 📦 64 个内置 Command 一览

Claude Code 提供了丰富的内置命令，按功能分类：

### 🔐 认证相关（Auth）

| 命令 | 说明 |
|------|------|
| `/login` | 通过 OAuth 登录 Claude Code |
| `/logout` | 登出 |
| `/upgrade` | 打开升级页面 |

### ⚙️ 配置相关（Config）

| 命令 | 说明 |
|------|------|
| `/config` | 打开设置界面 |
| `/theme` | 更改颜色主题 |
| `/color` | 设置提示栏颜色 |
| `/keybindings` | 自定义快捷键 |
| `/permissions` | 管理工具权限 |
| `/statusline` | 配置状态栏 |
| `/vim` | 启用 Vim 模式 |
| `/sandbox` | 配置沙盒模式 |

### 📊 上下文相关（Context）

| 命令 | 说明 |
|------|------|
| `/context` | 可视化上下文使用情况 |
| `/cost` | 显示 Token 使用统计 |
| `/usage` | 显示计划限额 |
| `/extra-usage` | 配置超额计费 |
| `/insights` | 生成使用分析报告 |

### 🐛 调试相关（Debug）

| 命令 | 说明 |
|------|------|
| `/doctor` | 检查 Claude Code 健康状态 |
| `/feedback` | 生成问题反馈链接 |
| `/release-notes` | 查看发布说明 |
| `/tasks` | 查看后台任务 |

### 🔧 扩展相关（Extensions）

| 命令 | 说明 |
|------|------|
| `/agents` | 管理自定义智能体 |
| `/hooks` | 管理钩子配置 |
| `/mcp` | 管理 MCP 服务器 |
| `/skills` | 查看可用技能 |
| `/plugin` | 管理插件 |

### 💾 记忆相关（Memory）

| 命令 | 说明 |
|------|------|
| `/memory` | 编辑记忆文件、开启自动记忆 |

### 🧠 模型相关（Model）

| 命令 | 说明 |
|------|------|
| `/model` | 切换模型 |
| `/effort` | 设置工作量级别 |
| `/fast` | 切换快速模式 |

### 📁 项目相关（Project）

| 命令 | 说明 |
|------|------|
| `/init` | 初始化新项目 |
| `/diff` | 查看变更 |
| `/pr-comments` | 查看 PR 评论 |

### 🔄 会话相关（Session）

| 命令 | 说明 |
|------|------|
| `/branch` | 分支当前对话 |
| `/clear` | 清除对话历史 |
| `/compact` | 压缩对话释放上下文 |
| `/exit` | 退出 CLI |
| `/rename` | 重命名会话 |
| `/resume` | 恢复之前的会话 |

---

## 🎯 最佳实践

### 1. Command 的命名规范

```markdown
<!-- ✅ 推荐：描述性强 -->
name: code-review
name: create-component
name: run-tests

<!-- ❌ 避免：模糊或太短 -->
name: cr
name: do-stuff
```

### 2. 保持 Command 简洁

```
★ Insight ─────────────────────────────────────
Command 是提示词模板，不是完整应用
如果太复杂，考虑用 Agent 替代
─────────────────────────────────────────────────
```

### 3. 使用参数提示

```markdown
<!-- 提供清晰的参数提示 -->
argument-hint: [文件名]

<!-- 用户输入 /create-component button -->
```

### 4. 将高频任务转为 Command

```bash
# 每天都要做的事 → Command
/code-review      # 审查 PR
/deploy-staging  # 部署到测试环境
/generate-report # 生成日报
```

---

## ⚠️ 常见问题

### Q1：Command 和 Skill 有什么区别？

| 特性 | Command | Skill |
|------|---------|-------|
| 调用方式 | `/command` | `Skill` 工具或 Agent 预加载 |
| 上下文 | 复用主会话 | 可隔离运行 |
| 用途 | 工作流编排 | 可复用技能 |

### Q2：Command 支持动态参数吗？

有限支持。你可以使用 `argument-hint` 提示参数，但参数传递需要通过解析输入实现。

### Q3：可以嵌套调用 Command 吗？

不能直接嵌套。但你可以在 Command 的提示词中告诉用户使用其他 Command。

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制概述 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 与 Command 的配合 |
| [04 - Skills 技能完全指南](./04-skills.md) | Skill 的两种模式 |
| [09 - 编排工作流](./09-orchestration.md) | Command → Agent → Skill 编排模式 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
