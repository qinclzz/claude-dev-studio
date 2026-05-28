# 技能：项目知识化 (/onboard-project)

**触发时机**: 接手一个新工程时执行，每个工程一次性。前置条件：`/setup-studio` 已完成。

## 概述

对当前工程进行全量扫描，生成持久化的知识图谱和系统地图，供后续所有需求开发复用。不依赖具体需求，独立执行。

## 执行步骤

### 第一步：初始化项目 CLAUDE.md

调用内置 `/init` 技能，扫描项目结构，生成项目级 `CLAUDE.md`。

生成后检查并补充以下内容（若 `/init` 未覆盖）：

```markdown
## 技术栈
- 语言：Java
- 构建：Maven 多模块
- 框架：Spring Boot / Spring Cloud
- 数据库：（从配置文件读取）

## 模块结构
（列出各模块及职责）

## 编码规范
- 字符编码：UTF-8
- 遗留代码处理：遵循零信任阅读原则
- 禁止基于类名推测职责
- 所有分析结论必须附 文件路径:行号
```

### 第二步：graphify 全量扫描

在对话中输入以下指令触发 graphify 扫描：

```
请用 graphify 扫描当前项目，输出目录指定为 .claude/studio/
```

graphify 实际输出位置：

```
.claude/studio/
  graph.json     原始图谱数据（节点、边、社区分组）
  graph.html     可视化页面
  audit.md       扫描审计报告
```

扫描完成后，读取 `.claude/studio/graph.json` 中的社区列表（graphify 自动聚类的模块分组）。

### 第三步：社区命名确认（人工）

将 graphify 识别出的社区列表展示给用户，逐一确认业务含义：

```
graphify 识别到以下 N 个社区，请确认每个社区的业务名称：

社区 0（核心文件：XxxService, XxxMapper）→ 你认为这是？
社区 1（核心文件：YyyController, YyyDTO）→ 你认为这是？
...
```

用户确认后，将命名写入 `.claude/studio/community-names.json`。

### 第四步：生成 system-map.md

基于 graphify 数据和用户确认的社区命名，写入 `.claude/studio/system-map.md`：

```markdown
# 系统地图 (System Map)

> 生成时间：{date}
> 工程：{project-name}
> 扫描节点数：{N}，社区数：{M}

## 模块总览

| 社区ID | 业务名称 | 核心类 | 职责描述 |
|--------|---------|--------|---------|
| 0 | 商品管理 | GoodsService, GoodsMapper | ... |
| 1 | 仓库管理 | WarehouseController | ... |

## 模块依赖关系

（Mermaid 图，由 graphify 数据生成）

## 已知技术债

（扫描中发现的 @Deprecated 类、TODO 标记、超长类等）

## 更新记录

| 日期 | 触发原因 |
|------|---------|
| {date} | 初始扫描 |
```

### 第五步：安装 git commit hook（可选）

询问用户是否安装自动更新 hook：

> 是否在每次 git commit 后自动触发 graphify 增量更新？
> [是，安装 hook] [否，手动更新]

若选择安装，通过 hookify 写入 post-commit hook。

## 产物

```
.claude/studio/
  system-map.md          系统地图（人工可读）
  graph.json             graphify 原始数据（graphify 直接输出）
  graph.html             可视化页面（graphify 直接输出）
  audit.md               扫描审计报告（graphify 直接输出）
  community-names.json   社区命名映射（第三步人工确认后写入）
```

## 成功门控

- `system-map.md` 已生成，包含所有社区的业务命名
- 用户确认系统地图准确反映了工程结构
- `.claude/studio/` 目录已纳入 `.gitignore` 或已提交

## 后续步骤

知识化完成后，每次新需求执行 `/kickoff [需求描述]`。
