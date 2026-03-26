# Advanced Tool Use 高级工具使用

![难度](https://img.shields.io/badge/难度-高级-red?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 高级工具使用模式
- ✅ 工具组合技巧
- ✅ 自定义工具集成

---

## 💡 工具生态

Claude Code 提供了丰富的内置工具：

| 类别 | 工具 | 说明 |
|------|------|------|
| **文件** | Read, Write, Edit | 文件操作 |
| **搜索** | Glob, Grep | 代码搜索 |
| **终端** | Bash | 执行命令 |
| **网络** | WebFetch | 获取网页 |
| **智能体** | Agent | 调用子智能体 |

---

## 🔄 工具组合模式

### 模式 1：搜索 → 读取 → 修改

```bash
# 1. 搜索文件
Glob("**/*.ts")

# 2. 读取内容
Read("file.ts")

# 3. 修改
Edit("file.ts", ...)
```

### 模式 2：批量操作

```
★ Insight ─────────────────────────────────────
使用 Glob + Bash 批量处理
先搜索，再循环操作
避免逐个手动处理
─────────────────────────────────────────────────
```

```bash
# 查找所有测试文件
Glob("**/*.test.ts")

# 批量运行测试
Bash("for f in **/*.test.ts; do npm test $f; done")
```

---

## 🎯 高级技巧

### 1. 使用 Grep 的高级模式

```bash
# 正则搜索
Grep(pattern: "function\\s+\\w+\\(", type: "ts")

# 搜索并显示上下文
Grep(pattern: "TODO", context: 3)
```

### 2. WebFetch 缓存

```bash
# 获取并缓存网页
WebFetch(url: "https://api.example.com/docs")

# 在后续请求中使用缓存
```

### 3. Agent 工具的高级用法

```bash
# 传递复杂参数
Agent(agent-type: "general-purpose",
      prompt: """
      任务：重构以下代码
      要求：
      1. 提取重复代码
      2. 添加类型注解
      3. 编写单元测试

      代码：
      ${Read("src/main.ts")}
      """)
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基础概念 |
| [02 - Subagents 智能体完全指南](./02-subagents.md) | Agent 工具 |
| [06 - Settings 配置完全指南](./06-settings.md) | 工具权限 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
