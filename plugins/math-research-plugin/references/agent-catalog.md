# Agent catalog

Plugin 内 `agents/*.toml` 是可分发的权威模板；Codex 项目自定义 Agent 的运行副本放在项目根 `.codex/agents/`，且必须与模板逐字一致。Plugin manifest 不声明虚构的 `agents` 字段，安装本 Plugin 只自动带入 Skills，不会自动注册这些 Agent。

| Agent | Plugin 模板 / 项目运行副本 | 状态 | 推理深度 | 主要 Skill |
|---|---|---|---|---|
| `concept-explainer` | `agents/concept-explainer.toml` / `.codex/agents/concept-explainer.toml` | 正式 | xhigh | `concept-explain` |
| `paper-detailed-explainer` | `agents/paper-detailed-explainer.toml` / `.codex/agents/paper-detailed-explainer.toml` | 正式 | xhigh | `paper-source-resolve`, `concept-explain` |
| `writing-literature-indexer` | `agents/writing-literature-indexer.toml` / `.codex/agents/writing-literature-indexer.toml` | 正式 | medium | `paper-selection-strategy`, `related-paper-search`, `paper-relationship-analysis` |
| `learning-literature-indexer` | `agents/learning-literature-indexer.toml` / `.codex/agents/learning-literature-indexer.toml` | 正式 | medium | `paper-selection-strategy`, `related-paper-search`, `paper-relationship-analysis` |
| `logical-reasoner` | `agents/logical-reasoner.toml` / `.codex/agents/logical-reasoner.toml` | 计划占位 | low | 暂无 |
| `formula-deriver` | `agents/formula-deriver.toml` / `.codex/agents/formula-deriver.toml` | 计划占位 | low | 暂无 |

TOML 内部运行名使用 snake_case；文件名和架构标识使用 kebab-case。Skill 路径均相对项目根，以便复制项目后继续工作。每次修改 Agent 时先改 Plugin 模板，再同步项目运行副本，并在验证中比较文件哈希，防止双源漂移。
