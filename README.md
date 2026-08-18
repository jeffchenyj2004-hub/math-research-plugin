# 数学科研 Plugin

面向数学与数学物理科研的 Codex Plugin。目前提供概念解释、论文意图分流、论文来源解析、论文简明解读、论文筛选与检索、论文关系分析，以及配套的项目级 Agents。

## 仓库结构

- `plugins/math-research-plugin/`：Plugin 清单、正式 Skills 与 Agent 权威模板。
- `.codex/agents/`：可被当前项目发现的 Agent 运行副本。
- `.agents/plugins/marketplace.json`：当前项目的本地 Marketplace 登记。
- `AGENTS.md`：项目级协作规则、论文路由规则与长期偏好预留区。

## 当前状态

- 正式 Skills：`concept-explain`、`paper-intent-route`、`paper-source-resolve`、`paper-brief-summary`、`paper-selection-strategy`、`related-paper-search`、`paper-relationship-analysis`。
- 正式 Agents：`concept-explainer`、`paper-detailed-explainer`、`writing-literature-indexer`、`learning-literature-indexer`。
- 计划 Agents：`logical-reasoner`、`formula-deriver`，当前只返回 `planned` 状态。
- 反问模块仍在计划中，尚未生成 `task-intent-clarify` Skill。

## 小组使用

克隆私有仓库后，使用 Codex 打开仓库根目录。项目级 `AGENTS.md` 和 `.codex/agents/` 会随仓库一并共享；Plugin 源文件保存在 `plugins/math-research-plugin/`。

团队成员需要先获得仓库访问权限，才能克隆或拉取更新。

