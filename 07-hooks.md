# Hooks 钩子完全指南

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 理解 Hooks 的概念和作用
- ✅ 掌握 22 种 Hook 事件及其触发时机
- ✅ 学会配置 Hook（命令型、提示型、代理型）
- ✅ 了解 Hook 的最佳实践和安全考虑
- ✅ 能够创建自定义 Hook 处理脚本

## 🔰 前置知识

- ✅ 了解 [Claude Code 入门指南](./01-getting-started.md)
- ✅ 了解 [Settings 配置完全指南](./06-settings.md) 中的权限配置
- ✅ 熟悉 Python 或 JavaScript 脚本编写

---

## 💡 什么是 Hooks

**Hooks**（钩子）是在特定事件发生时**自动执行**的脚本。它们运行在 Claude Code 的**主循环之外**，可以监控、修改、阻止某些操作。

```
★ Insight ─────────────────────────────────────
Hook = 事件触发器
在关键时刻插入你的逻辑
比如：每次执行命令前、每次会话结束时
─────────────────────────────────────────────────
```

### Hook vs Agent vs Command

| 特性 | Hook | Agent | Command |
|------|------|-------|---------|
| 触发方式 | 事件自动触发 | 手动调用 | 手动输入 `/` |
| 执行位置 | 主循环之外 | 独立上下文 | 复用主上下文 |
| 用途 | 监控、拦截、通知 | 执行任务 | 工作流编排 |

---

## 📋 22 种 Hook 事件

Hooks 在 Claude Code 生命周期中有 **22 个事件点**：

### 📊 按用途分类

#### 1. 工具执行类（Tool Execution）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 1 | `PreToolUse` | 工具调用**之前** | ✅ `tool_name` |
| 2 | `PostToolUse` | 工具调用**成功后** | ✅ `tool_name` |
| 3 | `PostToolUseFailure` | 工具调用**失败后** | ✅ `tool_name` |

#### 2. 权限类（Permissions）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 4 | `PermissionRequest` | 请求权限时 | ✅ `tool_name` |

#### 3. 会话生命周期类（Session Lifecycle）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 5 | `SessionStart` | 会话开始时 | ✅ `source` |
| 6 | `SessionEnd` | 会话结束时 | ✅ `reason` |
| 7 | `Setup` | `/setup` 命令执行时 | ❌ |
| 8 | `ConfigChange` | 配置文件变更时 | ✅ `source` |

#### 4. 压缩/上下文类（Compaction）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 9 | `PreCompact` | 压缩对话**前** | ✅ `trigger` |
| 10 | `PostCompact` | 压缩对话**后** | ✅ `trigger` |

#### 5. 智能体类（Agent）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 11 | `SubagentStart` | 子智能体启动时 | ✅ `agent_type` |
| 12 | `SubagentStop` | 子智能体停止时 | ✅ `agent_type` |
| 13 | `TeammateIdle` | 团队成员空闲时 | ❌ |
| 14 | `TaskCompleted` | 后台任务完成时 | ❌ |

#### 6. 用户交互类（User Interaction）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 15 | `UserPromptSubmit` | 用户提交提示词时 | ❌ |
| 16 | `Notification` | 发送通知时 | ✅ `notification_type` |
| 17 | `Stop` | Claude 停止响应时 | ❌ |

#### 7. 记忆类（Memory）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 18 | `InstructionsLoaded` | 指令文件加载时 | ❌ |

#### 8. 工作区类（Worktree）

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 19 | `WorktreeCreate` | 创建工作区时 | ❌ |
| 20 | `WorktreeRemove` | 删除工作区时 | ❌ |

#### 9. MCP 类

| # | Hook | 触发时机 | 支持 Matcher |
|---|------|----------|-------------|
| 21 | `Elicitation` | MCP 请求用户输入时 | ✅ `mcp_server_name` |
| 22 | `ElicitationResult` | 用户响应 MCP 输入后 | ✅ `mcp_server_name` |

---

## 🔧 Hook 类型

### 类型 1：命令型（command）

最常用的类型，运行 shell 命令：

```json
{
  "type": "command",
  "command": "python3 .claude/hooks/scripts/hooks.py",
  "timeout": 5000,
  "async": true,
  "statusMessage": "处理中..."
}
```

### 类型 2：提示型（prompt）

发送提示给 AI 模型进行判断：

```json
{
  "type": "prompt",
  "prompt": "检查这个操作是否安全：$ARGUMENTS",
  "timeout": 30
}
```

**适用事件**：PreToolUse、PostToolUse、Stop、SubagentStop

### 类型 3：代理型（agent）

启动子智能体验证条件：

```json
{
  "type": "agent",
  "prompt": "验证所有测试是否通过",
  "timeout": 120
}
```

**适用事件**：PreToolUse、PostToolUse、Stop、SubagentStop

### 类型 4：HTTP 型（http）

POST JSON 到外部 URL：

```json
{
  "type": "http",
  "url": "http://localhost:8080/hooks",
  "timeout": 30,
  "headers": {
    "Authorization": "Bearer $TOKEN"
  },
  "allowedEnvVars": ["TOKEN"]
}
```

---

## 💻 实战：创建一个声音通知 Hook

### 场景

当 Claude 完成一个耗时的任务时，播放提示音。

### 步骤 1：创建 Hook 脚本

```python
<!-- .claude/hooks/scripts/hooks.py -->
#!/usr/bin/env python3
"""
Claude Code Hook 脚本 - 声音通知
"""
import sys
import json
import os

def play_sound(sound_file):
    """播放声音文件"""
    project_dir = os.environ.get('CLAUDE_PROJECT_DIR', '.')
    sound_path = os.path.join(project_dir, '.claude', 'hooks', 'sounds', sound_file)

    # 根据平台选择播放器
    import platform
    system = platform.system()

    if system == 'Darwin':  # macOS
        os.system(f'afplay "{sound_path}"')
    elif system == 'Linux':
        os.system(f'paplay "{sound_path}"')
    elif system == 'Windows':
        os.system(f'powershell -c (New-Object Media.SoundPlayer "{sound_path}").PlaySync()')

def main():
    # 从 stdin 读取 JSON
    data = json.load(sys.stdin)
    event = data.get('hook_event_name', '')

    # 事件到声音的映射
    sound_map = {
        'Stop': 'stop.wav',
        'SubagentStop': 'agent-stop.wav',
        'PostToolUse': 'tool-complete.wav',
        'TaskCompleted': 'task-complete.wav'
    }

    if event in sound_map:
        play_sound(sound_map[event])

if __name__ == '__main__':
    main()
```

### 步骤 2：配置 Hook

```json
{
  "hooks": {
    "Stop": [{
      "type": "command",
      "command": "python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py",
      "timeout": 5000,
      "async": true,
      "statusMessage": "播放提示音"
    }],
    "SubagentStop": [{
      "type": "command",
      "command": "python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/hooks.py",
      "timeout": 5000,
      "async": true
    }]
  }
}
```

---

## 💻 实战：权限预审 Hook

### 场景

在执行敏感操作前，要求 AI 确认这是一个安全的操作。

### 步骤 1：创建安全检查脚本

```python
<!-- .claude/hooks/scripts/security_check.py -->
#!/usr/bin/env python3
"""
安全检查 Hook - 在执行命令前检查安全性
"""
import sys
import json
import re

# 危险命令模式
DANGEROUS_PATTERNS = [
    r'rm\s+-rf\s+/',          # 删除根目录
    r'drop\s+database',        # 删除数据库
    r'--force',               # 强制推送
    r'curl.*\|.*sh',          # 管道执行脚本
]

def is_dangerous(command):
    """检查命令是否危险"""
    for pattern in DANGEROUS_PATTERNS:
        if re.search(pattern, command, re.IGNORECASE):
            return True
    return False

def main():
    data = json.load(sys.stdin)
    tool_name = data.get('tool_name', '')
    arguments = data.get('arguments', {})

    # 只检查 Bash 命令
    if tool_name == 'Bash':
        command = arguments.get('command', '')
        if is_dangerous(command):
            # 返回阻止执行的指令
            print(json.dumps({
                "continue": True,  # 改为 false 可以阻止
                "stopReason": "⚠️ 检测到危险命令！",
                "systemMessage": f"危险命令被检测到: {command[:50]}..."
            }))
            return

    # 安全命令，继续执行
    print(json.dumps({"continue": True}))

if __name__ == '__main__':
    main()
```

### 步骤 2：配置 PreToolUse Hook

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "python3 ${CLAUDE_PROJECT_DIR}/.claude/hooks/scripts/security_check.py",
        "timeout": 5000
      }]
    }]
  }
}
```

---

## 🎯 最佳实践

### 1. 使用 async 避免阻塞

```json
<!-- ✅ 异步执行，不阻塞 Claude -->
{
  "type": "command",
  "command": "python3 hook.py",
  "async": true
}

<!-- ❌ 同步执行，会减慢 Claude -->
{
  "type": "command",
  "command": "python3 hook.py",
  "async": false
}
```

### 2. 设置合理的 timeout

```json
{
  "type": "command",
  "command": "python3 hook.py",
  "timeout": 5000  <!-- 5秒足够 -->
}
```

### 3. 使用 Matcher 过滤

```json
<!-- 只在特定工具触发 -->
{
  "PreToolUse": [{
    "matcher": "Bash",
    "hooks": [{ ... }]
  }]
}
```

### 4. 管理 Hook 依赖

```bash
# Python 依赖
pip install -r requirements.txt

# 在 Hook 脚本中处理
try:
    import some_package
except ImportError:
    pass  # 优雅降级
```

---

## ⚠️ 常见问题

### Q1：Hook 阻塞了 Claude 怎么办？

```bash
# 检查 timeout 设置
# 增加 timeout 或改为 async

# 或者直接禁用
/hooks
# 然后禁用相关 Hook
```

### Q2：如何调试 Hook？

```bash
# 查看 Hook 日志
cat .claude/hooks/logs/hooks-log.jsonl

# 添加调试输出
echo "Debug: $@" >> /tmp/hook-debug.log
```

### Q3：Hook 能访问哪些环境变量？

| 变量 | 说明 |
|------|------|
| `$CLAUDE_PROJECT_DIR` | 项目根目录 |
| `$CLAUDE_CODE_REMOTE` | 是否远程会话 |
| `$CLAUDE_ENV_FILE` | SessionStart 专用 |

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 四大扩展机制 |
| [06 - Settings 配置完全指南](./06-settings.md) | 权限配置 |
| [11 - Agent Teams 团队协作](./11-agent-teams.md) | TeammateIdle、TaskCompleted Hook |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
