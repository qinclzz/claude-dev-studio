# 技能：环境初始化 (/setup-studio)

**触发时机**: 首次在新机器或新项目上使用 Claude Code 时执行，全局一次性。

## 概述

检测并确认所有必装插件已就位，写入全局编码规范约束，配置 hookify 自动化规则，验证整套环境可用。

## 执行步骤

### 第一步：插件检测

检查以下插件是否已安装（通过 `/` 命令列表确认）：

| 插件 | 用途 | 安装命令 |
|------|------|---------|
| `superpowers` | TDD、调试、代码审查原子技能 | `/install superpowers` |
| `graphify` | 项目知识图谱生成 | `/install graphify` |
| `context7` | 实时文档查询 | `claude mcp add context7` |
| `security-guidance` | 安全扫描 | `/install security-guidance` |
| `hookify` | hook 自动化配置 | `/install hookify` |

若有缺失，展示对应安装命令，等待用户确认安装完成后继续。所有插件均为必装，任一缺失则阻塞后续步骤。

### 第二步：确认全局 CLAUDE.md 规范

确认 `~/.claude/CLAUDE.md` 包含以下约束，缺失则提示用户补充：

- 零信任阅读原则（类名不可信、副作用强制检索、结论必须附行号）
- 修改代码前先给出设计方案，经确认后再动手
- 代码修改完成后经确认再提交 git
- commit 前缀从 git log 获取，没有则由用户提供
- 字符编码：修改文件不得改变原编码，新建文件使用项目配置编码

### 第三步：配置 hookify 规则

调用 `/hookify` 技能，按以下规则逐条配置（hookify 会将规则写入 `.claude/settings.json` 的 `hooks` 字段）：

```
规则1 — PreToolUse(Edit/Write):
  触发：Claude 准备修改或新建文件时
  动作：检查当前会话是否已有用户确认的设计方案
        若无，输出提示"请先确认改造方案后再修改代码"并阻塞

规则2 — PostToolUse(Edit/Write):
  触发：Claude 完成文件修改后
  动作：输出提示"修改已完成，请确认无误后再执行 git commit"

规则3 — PreToolUse(Bash) 匹配 "git commit":
  触发：Claude 准备执行 git commit 命令时
  动作：检查 commit message 是否包含需求前缀
        若无前缀，输出提示"请提供 commit 前缀（可从 git log 获取）"并阻塞
```

配置完成后，检查 `.claude/settings.json` 中 `hooks` 字段已写入上述三条规则。

### 第四步：验证

逐项确认：
- [ ] 5个插件全部可用
- [ ] 全局 CLAUDE.md 规范已生效
- [ ] hookify 规则已写入 settings.json
- [ ] 在当前项目执行一次 `/onboard-project` 前置检查通过

## 产物

无文件产物，配置生效即完成。

## 成功门控

- 所有插件在 `/` 列表中可见
- hookify 规则在 `.claude/settings.json` 中可查到
- 用户明确确认环境就绪

## 后续步骤

环境就绪后，执行 `/onboard-project` 对当前工程进行知识化。
