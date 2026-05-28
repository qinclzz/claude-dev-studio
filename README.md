# claude-dev-studio

Claude Code 开发工作室技能包，适用于 Java 遗留项目的工程化开发流程。

## 技能列表

| 技能 | 命令 | 触发时机 |
|------|------|---------|
| 环境初始化 | `/setup-studio` | 首次使用，全局一次性 |
| 项目知识化 | `/onboard-project` | 接手新工程，每工程一次 |
| 需求启动 | `/kickoff` | 每次新需求开始 |
| 单任务开发 | `/dev` | 执行 backlog 中的单个任务 |
| 发布检查 | `/ship` | 所有任务完成后 |

## 使用流程

```
首次使用：  /setup-studio
接手工程：  /onboard-project
每次需求：  /kickoff → /dev（循环） → /ship
```

## 安装方式

将 `skills/` 目录下的文件夹复制到项目的 `.claude/skills/` 目录下：

```
your-project/
  .claude/
    skills/
      setup-studio/
        SKILL.md
      onboard-project/
        SKILL.md
      kickoff/
        SKILL.md
      dev/
        SKILL.md
      ship/
        SKILL.md
```

## 依赖插件

执行 `/setup-studio` 时会自动检测，以下插件需提前了解：

| 插件 | 用途 | 安装命令 |
|------|------|---------|
| `superpowers` | TDD、调试、代码审查原子技能 | `/install superpowers` |
| `graphify` | 项目知识图谱生成 | `/install graphify` |
| `context7` | 实时文档查询 | `claude mcp add context7` |
| `security-guidance` | 安全扫描 | `/install security-guidance` |
| `hookify` | hook 自动化配置 | `/install hookify` |

## 文档产物路径

每次需求执行后，文档统一存放在：

```
docs/requirements/{YYYYMMDD}-{slug}/
  PRD.md       需求说明
  ADR.md       架构决策
  backlog.md   任务清单
  REVIEW.md    代码审查报告
  GATE.md      质量门控报告
```
