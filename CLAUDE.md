# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个 **Claude Code 技能包**，专为 Java 遗留项目工程工作流设计。本仓库不包含应用源代码，只包含技能定义文件（`SKILL.md`），供复制到目标项目的 `.claude/skills/` 目录中使用。

## 技能工作流

五个技能按固定顺序使用，构成完整的开发生命周期：

```
/setup-studio → /onboard-project → /kickoff → /dev (循环) → /ship
```

| 技能目录 | 命令 | 触发时机 |
|---|---|---|
| `skills/setup-studio/` | `/setup-studio` | 全局一次性初始化 |
| `skills/onboard-project/` | `/onboard-project` | 每个新项目一次 |
| `skills/kickoff/` | `/kickoff` | 每个新需求启动时 |
| `skills/dev/` | `/dev` | 每个 backlog 任务执行时 |
| `skills/ship/` | `/ship` | 所有任务完成后发布检查 |

## 外部依赖插件

技能运行依赖以下插件（`/setup-studio` 会检测是否缺失并阻断流程）：

- `superpowers` — 提供 TDD、调试、代码审查、分支完成等原子技能
- `graphify` — 代码库知识图谱生成
- `context7` — 上下文增强
- `security-guidance` — 安全审查
- `hookify` — git hook 和 settings.json 规则配置

## 技能产物约定

`/kickoff` 在 `docs/requirements/{YYYYMMDD}-{slug}/` 下生成三个文档，`/dev` 和 `/ship` 依赖这些文档：

- `PRD.md` — 需求澄清文档（5 个必答问题）
- `ADR.md` — 影响分析与方案决策记录
- `backlog.md` — 原子任务列表（每任务 ≤4h），含任务状态（`todo` / `in_progress` / `done`）

`/onboard-project` 在 `.claude/studio/` 下生成：

- `graph.json` / `graph.html` — 知识图谱
- `audit.md` — 图谱审计报告
- `community-names.md` — 业务模块命名确认
- `system-map.md` — 系统模块总览与依赖图

## 修改技能文件的注意事项

每个 `SKILL.md` 内部有严格的步骤顺序和用户确认节点（"等待用户确认后再继续"），修改时必须保留这些阻断点，不得将需要用户决策的步骤改为自动执行。

`/dev` 技能的零信任阅读规则（反向追踪法）和 `/ship` 技能的 `[阻塞]` 分类机制是核心设计，修改时需保持其语义完整性。
