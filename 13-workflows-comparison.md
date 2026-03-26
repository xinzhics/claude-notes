# 9大开发工作流对比

![难度](https://img.shields.io/badge/难度-进阶-orange?style=flat)
![更新时间](https://img.shields.io/badge/更新于-2026年3月-white?style=flat&labelColor=555)

---

## 📌 学习目标

学完本章后，你将掌握：

- ✅ 了解 9 个主流 Claude Code 工作流
- ✅ 理解各工作流的优缺点
- ✅ 根据场景选择合适的工作流

---

## 💡 工作流总览

| 工作流 | 特点 | 适合场景 |
|--------|------|----------|
| Superpowers | TDD-first、Iron Laws | 追求代码质量 |
| Everything Claude Code | 28 Agents、125 Skills | 大型复杂项目 |
| Spec Kit | spec-driven、constitution | 规范团队 |
| gstack | role personas | 角色扮演 |
| BMAD-METHOD | full SDLC | 完整开发生命周期 |
| Get Shit Done | fresh contexts | 快速交付 |
| OpenSpec | delta specs | 增量开发 |
| Compound Engineering | Compound Learning | 持续学习 |
| HumanLayer | RPI、context engineering | 人机协作 |

---

## 🏆 Superpowers

**⭐ 114k Stars** — TDD-first 开发工作流

### 核心特点

```
★ Insight ─────────────────────────────────────
Superpowers = 测试驱动开发
先写测试，再写代码
确保代码可测试
─────────────────────────────────────────────────
```

### 架构

```
Plan → Write Tests → Implement → Review
```

### 组件

| 类型 | 数量 | 示例 |
|------|------|------|
| Agents | 5 | reviewer, architect |
| Commands | 3 | implement, plan |
| Skills | 14 | testing, refactoring |

### 适用场景

- 追求代码质量的团队
- TDD 爱好者
- 需要严格代码审查的项目

---

## 📦 Everything Claude Code

**⭐ 109k Stars** — 最完整的 Claude Code 工作流

### 核心特点

- 28 个 Agents
- 125 个 Skills
- 63 个 Commands
- 评分系统（instinct scoring）
- AgentShield 保护

### 适用场景

```
★ Insight ─────────────────────────────────────
大仓库的最佳选择
几乎涵盖了所有使用场景
但学习曲线较陡
─────────────────────────────────────────────────
```

- 大型 Monorepo
- 需要全面工具链的团队
- 追求自动化的项目

---

## 📋 Spec Kit

**⭐ 82k Stars** — 规范驱动开发

### 核心特点

- **Spec First**：先写规格说明书
- **Constitution**：团队宪法（不可违反的规则）
- **22+ tools**：丰富的工具集

### 架构

```
Spec → Constitution → Implement → Verify
```

### 适用场景

- 规范化团队
- 需要清晰流程的项目
- 大型企业团队

---

## 🎭 gstack

**⭐ 48k Stars** — 角色扮演开发

### 核心特点

- Role Personas（角色人格）
- `/codex review` 命令
- 并行 Sprint

### 角色示例

| 角色 | 职责 |
|------|------|
| architect | 系统设计 |
| reviewer | 代码审查 |
| tester | 测试 |

### 适用场景

- 明确分工的团队
- 需要角色意识的开发

---

## 🚀 BMAD-METHOD

**⭐ 42k Stars** — 全周期开发

### 核心特点

- Full SDLC（完整开发生命周期）
- Agent Personas（智能体人格）
- 22+ platforms（多平台支持）

### 适用场景

```
★ Insight ─────────────────────────────────────
适合需要覆盖完整生命周期的项目
从需求到部署一条龙
─────────────────────────────────────────────────
```

- 完整产品开发
- 多平台部署
- 复杂项目

---

## ⚡ Get Shit Done

**⭐ 42k Stars** — 快速交付

### 核心特点

- Fresh 200K contexts（全新上下文）
- Wave execution（波浪式执行）
- XML plans（XML 格式计划）

### 适用场景

- 快速迭代项目
- 需要保持速度的团队
- MVP 开发

---

## 🔄 OpenSpec

**⭐ 34k Stars** — 增量规格

### 核心特点

- Delta specs（增量规格）
- Brownfield support（棕地支持）
- Artifact DAG（产物依赖图）

### 适用场景

- 遗留系统改造
- 增量功能开发
- 需要清晰变更管理的项目

---

## 🧪 Compound Engineering

**⭐ 11k Stars** — 持续学习

### 核心特点

- Compound Learning（持续学习）
- Multi-Platform CLI
- Plugin Marketplace

### 适用场景

```
★ Insight ─────────────────────────────────────
适合需要持续改进的团队
每次迭代都在学习
─────────────────────────────────────────────────
```

- 追求卓越的团队
- 持续改进项目

---

## 🔗 HumanLayer

**⭐ 10k Stars** — 人机协作

### 核心特点

- RPI（Review-Plan-Implement）
- Context Engineering
- 300k+ LOC 项目验证

### 架构

```
Review → Plan → Implement
```

### 适用场景

- 需要人工审查的工作流
- 强调人机协作的项目
- 安全敏感的应用

---

## 🎯 如何选择

### 选择指南

| 需求 | 推荐工作流 |
|------|-----------|
| 代码质量优先 | Superpowers |
| 大型复杂项目 | Everything Claude Code |
| 规范团队 | Spec Kit |
| 快速交付 | Get Shit Done |
| 增量开发 | OpenSpec |
| 人机协作 | HumanLayer |

### 新手推荐

```
★ Insight ─────────────────────────────────────
新手推荐顺序：
1. 从基础 Command 开始
2. 学习 Simple Workflow
3. 尝试 Superpowers
4. 根据需求选择其他
─────────────────────────────────────────────────
```

---

## 📚 相关资源

| 文档 | 说明 |
|------|------|
| [01 - Claude Code 入门完全指南](./01-getting-started.md) | 基础入门 |
| [09 - 编排工作流](./09-orchestration.md) | 架构模式 |
| [12 - 86个使用技巧精选](./12-tips-selection.md) | 实用技巧 |

---

<p align="center">
  <a href="../">← 返回中文教程目录</a>
</p>
