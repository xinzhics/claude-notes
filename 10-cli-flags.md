# CLI 启动参数完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 掌握 Claude Code 的所有启动参数
- ✅ 学会在不同场景下使用合适的参数
- ✅ 了解环境变量的配置方式
- ✅ 掌握交互模式的配置

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 熟悉命令行终端操作

---

## 💻 基本启动

### 启动 Claude Code

```bash
claude                 # 基本启动
claude [flags]        # 带参数启动
claude [command]       # 执行命令后退出
```

---

## 📋 主要启动参数

### 模型参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--model` | 指定模型 | `--model sonnet` |
| `--fast` | 启用快速模式 | `--fast` |
| `--effort` | 工作量级别 | `--effort high` |

```bash
# 使用 Sonnet 模型
claude --model sonnet

# 使用高工作量级别
claude --effort max

# 组合使用
claude --model opus --effort high
```

### 权限参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--permission-mode` | 权限模式 | `--permission-mode auto` |
| `--no-input` | 无交互模式 | `--no-input` |

```bash
# 自动权限模式
claude --permission-mode auto

# 无提示模式（适合脚本）
claude --no-input --non-interactive
```

### 配置参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--config` | 指定配置文件 | `--config .claude/settings.json` |
| `--no-config` | 禁用配置加载 | `--no-config` |

```bash
# 使用特定配置
claude --config .claude/settings.dev.json

# 禁用配置（完全干净的环境）
claude --no-config
```

### 上下文参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--resume` | 恢复会话 | `--resume session-name` |
| `--parent` | 关联父会话 | `--parent parent-session-id` |

```bash
# 恢复之前的会话
claude --resume my-session

# 创建子会话
claude --parent abc123
```

### 输出参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--output` | 输出格式 | `--output json` |
| `--verbose` | 详细输出 | `--verbose` |

### 调试参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--debug` | 调试模式 | `--debug` |
| `--trace` | 追踪模式 | `--trace` |

```bash
# 开启调试
claude --debug

# 完整追踪
claude --trace
```

---

## 🔧 环境变量

### 常用环境变量

| 变量 | 说明 | 示例 |
|------|------|------|
| `CLAUDE_API_KEY` | API 密钥 | `export CLAUDE_API_KEY=sk-...` |
| `CLAUDE_CODE_SETTINGS` | 设置目录 | `export CLAUDE_CODE_SETTINGS=~/.claude` |
| `HTTP_PROXY` | HTTP 代理 | `export HTTP_PROXY=http://proxy:8080` |

### 配置代理

```bash
# macOS/Linux
export HTTP_PROXY=http://proxy.example.com:8080
export HTTPS_PROXY=http://proxy.example.com:8080
claude

# Windows
set HTTP_PROXY=http://proxy.example.com:8080
claude
```

---

## 💻 实战场景

### 场景 1：自动化脚本

```bash
#!/bin/bash
# 自动化代码审查脚本

claude --no-input --non-interactive \
  --permission-mode auto \
  --model sonnet \
  --resume previous-session \
  --output json \
  <<'EOF'
审查 PR #123 的代码变更
EOF
```

### 场景 2：CI/CD 集成

```bash
# GitHub Actions 示例
- name: Run Claude Code Review
  env:
    CLAUDE_API_KEY: ${{ secrets.CLAUDE_API_KEY }}
  run: |
    claude --no-input \
      --permission-mode auto \
      --model opus \
      <<'EOF'
    审查这个 PR 的安全性问题
    EOF
```

### 场景 3：多模型对比

```bash
# 使用不同模型测试
for model in haiku sonnet opus; do
  echo "Testing with $model..."
  claude --model $model --no-input <<'EOF'
  解释这段代码的作用
  EOF
done
```

---

## 🎯 最佳实践

### 1. 使用配置文件管理复杂配置

```bash
# ❌ 命令行过长
claude --model opus --effort high --permission-mode auto

# ✅ 配置文件
claude --config .claude/settings.prod.json
```

### 2. 环境变量存储密钥

```bash
# ❌ 命令行暴露密钥
claude --api-key sk-xxxxx

# ✅ 环境变量
export CLAUDE_API_KEY=sk-xxxxx
claude
```

### 3. 使用别名简化常用命令

```bash
# ~/.bashrc 或 ~/.zshrc
alias cc="claude"
alias cc-fast="claude --fast"
alias cc-review="claude --model opus --effort high"
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基本概念 |
| [06 - Settings 配置完全指南](./06-settings.md) | 配置文件 |
| [11 - Agent Teams 团队协作](./11-agent-teams.md) | 团队协作参数 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
