# 86个使用技巧精选

![难度](https://img.shields.io/badge/难度-入门-blue?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 精选的 30+ 核心使用技巧
- ✅ 分类整理的实用建议
- ✅ 来自 Claude Code 团队的最佳实践

---

## 💡 Prompt 技巧（3条）

### 1. 挑战 Claude

> "grill me on these changes and don't make a PR until I pass your test."

让 Claude 严格审查，不要轻易放过问题。

### 2. 重做优化

> "knowing everything you know now, scrap this and implement the elegant solution"

当修复不满意时，要求 Claude 用更好的方式重做。

### 3. 放手让 Claude 修复

> "Claude fixes most bugs by itself — paste the bug, say 'fix', don't micromanage how"

简单 bug 直接让 Claude 自己修。

---

## 💡 计划技巧（6条）

### 4. 总是用 Plan Mode

```
★ Insight ─────────────────────────────────────
Plan Mode = 思考清楚再动手
先规划，再执行
减少返工
─────────────────────────────────────────────────
```

### 5. 从最小规格开始

> "start with a minimal spec or prompt and ask Claude to interview you"

先用简单需求，让 Claude 通过提问完善。

### 6. 分阶段计划

每个阶段设置多个测试（单元测试、自动化测试、集成测试）。

### 7. 双重审查

> "spin up a second Claude to review your plan as a staff engineer"

用第二个 Claude 来审查计划。

### 8. 详细规格减少歧义

> "write detailed specs and reduce ambiguity before handing work off"

越具体，输出越好。

### 9. 原型 > PRD

> "prototype > PRD — build 20-30 versions instead of writing specs"

快速构建原型比写文档更有效。

---

## 💡 CLAUDE.md 技巧（7条）

### 10. 保持简洁

```
★ Insight ─────────────────────────────────────
CLAUDE.md 应该 < 200 行
60 行左右是理想长度
太长会被忽略
─────────────────────────────────────────────────
```

### 11. 使用重要标签

```markdown
<important if="...">
  这个规则很重要
</important>
```

### 12. 多文件组织

```
Monorepo 中使用多个 CLAUDE.md：
- 根目录：通用规则
- 子目录：特定规则
```

### 13. 用 Rules 组织大文件

```bash
.claude/rules/    # 拆分大指令
```

### 14. 让任何人能跑测试

```markdown
"任何开发者应该能运行 'run the tests' 并第一次就成功"
```

### 15. 用 settings.json 代替注释

```json
// ❌ CLAUDE.md 中写
"不要添加 Co-Authored-By"

// ✅ settings.json 中配置
"attribution.commit": ""
```

### 16. 不要放敏感信息

```bash
# ❌
API_KEY=sk-xxxxx

# ✅
使用环境变量 process.env.API_KEY
```

---

## 💡 Agents 技巧（4条）

### 17. 职能特定 Agent

```
★ Insight ─────────────────────────────────────
一个 Agent 做一件事
不要做通用型 Agent
用专项 Agent + 专项 Skill
─────────────────────────────────────────────────
```

### 18. 用 Subagents 分担工作

> "say 'use subagents' to throw more compute at a problem"

复杂任务用 subagent 分担。

### 19. Agent Teams + Worktrees

并行开发，多个 Claude 同时工作。

### 20. 测试时间计算

> "separate context windows make results better"

分离上下文让结果更准确。

---

## 💡 Commands 技巧（3条）

### 21. 用 Commands 代替 Agents（简单任务）

```
★ Insight ─────────────────────────────────────
Command = 轻量级入口
Agent = 重量级执行
简单任务用 Command
─────────────────────────────────────────────────
```

### 22. 常用工作流转为 Command

> "use slash commands for every 'inner loop' workflow you do many times a day"

高频任务变成 `/command`。

### 23. 重复任务自动化

> "if you do something more than once a day, turn it into a skill or command"

重复 > 1次/天 → Command/Skill。

---

## 💡 Skills 技巧（9条）

### 24. 使用 context: fork

```yaml
context: fork   # 隔离执行
```

### 25. 大仓库用子文件夹

```
.claude/skills/
├── frontend/
│   └── SKILL.md
└── backend/
    └── SKILL.md
```

### 26. 包含 Gotchas 部分

```markdown
## Gotchas（容易犯的错误）

1. 忘记处理 API 限流
2. ...
```

### 27. description 是触发器

```yaml
# ❌ 太模糊
description: 天气技能

# ✅ 具体说明使用时机
description: |
  当用户询问天气时调用
  需要获取指定城市的温度时
```

### 28. 不要说显而易见的事

```
★ Insight ─────────────────────────────────────
Focus on pushing Claude out of its default behavior
专注于改变 Claude 的默认行为
不要说常识性内容
─────────────────────────────────────────────────
```

### 29. 给目标，不给步骤

```markdown
# ❌ 规定步骤
1. 调用 API
2. 解析 JSON
3. 返回结果

# ✅ 给目标
"获取用户请求的城市的天气数据"
```

### 30. 包含脚本和库

```bash
Skill 包含脚本
Claude 组合使用，不重复造轮子
```

### 31. 使用 !`command` 注入动态内容

```markdown
当前 Node.js 版本：`!`node --version``
```

---

## 💡 Hooks 技巧（5条）

### 32. 危险操作 Hook

```json
PreToolUse: [{
  "matcher": "Bash",
  "hooks": [{
    "type": "prompt",
    "prompt": "检查这个命令是否危险"
  }]
}]
```

### 33. 测量 Skill 使用

使用 PreToolUse 统计哪些 Skill 用得多。

### 34. 自动格式化

```json
PostToolUse: [{
  "hooks": [{
    "type": "command",
    "command": "code-formatter"
  }]
}]
```

### 35. 权限路由到 Opus

```json
PermissionRequest: [{
  "hooks": [{
    "type": "agent",
    "prompt": "检查这个权限请求是否安全"
  }]
}]
```

### 36. Stop Hook 收尾

```json
Stop: [{
  "hooks": [{
    "type": "prompt",
    "prompt": "检查任务是否完成"
  }]
}]
```

---

## 💡 工作流技巧（7条）

### 37. 手动 /compact

```
★ Insight ─────────────────────────────────────
Context 使用到 50% 时手动 compact
不要等自动触发
保持对话质量
─────────────────────────────────────────────────
```

### 38. 选对模型

| 任务 | 模型 |
|------|------|
| 计划/思考 | Opus |
| 写代码 | Sonnet |
| 快速任务 | Haiku |

### 39. 用 thinking mode

```bash
/model
# 启用 thinking mode
```

### 40. 定期重命名会话

```bash
/rename "TODO - 重构登录模块"
```

### 41. 用 Esc Esc 或 /rewind 纠错

Claude 跑偏时，撤销而不是修复。

### 42. ASCII 图很管用

> "use ASCII diagrams a lot to understand your architecture"

用 ASCII 画架构图。

### 43. /loop 和 /schedule

```bash
/loop     # 本地循环（最长3天）
/schedule # 云端循环（可离线）
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基础入门 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agents 深入 |
| [04 - Skills 技能完全指南](./04-skills.md) | Skills 深入 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
