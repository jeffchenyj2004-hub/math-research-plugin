# 数学科研项目协作规则

## 反问偏好

- 每当触发最小反问机制，或 Agent、Skill、Prompt 的内容需要反问用户时，始终提供带数字编号的互斥选项，让用户通过选择代替长文本输入。
- 每次只问一个会改变执行路线的决定性问题；能基于上下文安全判断时不要反问。
- 计划中的反问 Skill 规范名为 `task-intent-clarify`。本阶段没有该 Skill 文件；`task-intent-clarifier` 只作为历史别名，不得当作已实现组件调用。

## 长期用户背景与默认偏好（可留空）

以下字段为空时表示未知，由当前上下文谨慎推断；不得为了填满字段而阻塞任务。

- 专业程度：
- 方向熟悉度：
- 前沿深度：
- 默认检索范围：
- 输出偏好：

## 组件命名与状态

- Agent 使用表示执行者的英文名并以 `-er` 结尾；TOML 内部 `name` 使用对应的 snake_case 形式。
- Skill 使用动作或能力名称，不使用表示执行者的 `-er` 结尾。
- 正式 Agent：`concept-explainer`、`paper-detailed-explainer`、`writing-literature-indexer`、`learning-literature-indexer`。
- 计划 Agent：`logical-reasoner`、`formula-deriver`。二者当前只返回 `planned` 状态，不得宣称已完成推导。
- Skill 由 Plugin 自动分发。Plugin 内 `agents/*.toml` 是可分发的权威模板，项目根 `.codex/agents/*.toml` 是内容完全一致的运行副本；当前 Codex 只从项目根自动发现这些 Agent，安装 Plugin 本身不会注册它们。

## 论文任务意图指导区

`paper-intent-route` 是所有尚未分类的论文任务的唯一宽入口。它可以因顶层用户请求命中 description 而自发调用，也可以由顶层协调者在其他流程发现未分类论文子任务时调用。遵守以下不变量：

1. 每个顶层用户回合最多运行一次。
2. 下游论文 Skill 和 Agent 不得再次调用它。
3. 用户当前回合的明确要求优先于既有任务；既有任务优先于长期偏好。
4. PDF、题名、DOI、arXiv 标识或网址先交给 `paper-source-resolve`，以实际可获得的证据层级为准。
5. 单篇论文不是默认全文解读，也不是默认比较；必须结合当前上下文判断中心性。

路由契约只供内部协调使用。除 `clarify` 和当前未实现的 `audit` 降级外，顶层协调者必须在同一回合立即调用选定的下游组件并向用户交付实际结果；普通模式不得把 YAML 路由契约作为最终答复。

路由优先依据：用户当前明确要求 → 当前对话已建立任务 → 写作、学习、综述、项目、比较或审计线索 → 与上下文的相关程度 → 本文件中的长期规则。

- 当前核心单篇论文：`paper-detailed-explainer`。
- 旁支论文：`paper-brief-summary`；若用户未说明后续路线，父流程使用编号选项进行最小反问。
- 写作、补引用或组织 Related Work：`writing-literature-indexer`。
- 学习、知识综述或项目调研：`learning-literature-indexer`，并传入专业程度、方向熟悉度、前沿深度、核心关联度和检索范围。
- 比较对象已经给齐：直接调用 `paper-relationship-analysis`。
- 需要寻找比较对象：`paper-selection-strategy` → `related-paper-search` → `paper-relationship-analysis`。
- 当“论文发布时间不久且它是当前核心”，或用户明确提出质疑、复核、验证或可信度审查时，进入 `audit`。新近论文若只是旁支仍走简明解读。本阶段没有专用审计实现，必须说明边界，并提供编号选项：“仅做详细解读与局限梳理”或“等待审计模块”；不得伪装成完整审计。
- 与当前上下文无关的论文：先结合用户明确要求和既有任务推测目的，并归入现有路线；只有存在两条以上成本明显不同的合理路线时才进入 `clarify`。
- “综述”若指文献梳理、学习路径或项目调研，进入学习索引；若指对结论进行复核、验证或可信度审查，进入 `audit`。上下文无法区分且两条路线成本差异明显时才澄清。
- 存在两条以上成本明显不同的合理路线且无法可靠判断：输出 `clarify` 路由和 `missing_decision`，由父流程展示编号选项。

高置信路线直接执行；中等置信先交付低成本的最小结果；低置信先澄清。不要伪造数值置信度。

## 论文来源与证据边界

- `paper-source-resolve` 只生成来源卡，不解释论文结论：统一标准题名、标识符和元数据，并标明可取得的是 metadata、abstract、targeted_sections 还是 full_text。
- 无法取得全文时，只能基于已取得内容作有限结论，明确证据层级，并提供编号选项让用户上传全文或接受局部分析。
- 不得把搜索摘要、网页片段或第三方转述表述成已阅读全文。
- `paper-brief-summary` 不阅读全文、不做多轮检索；`paper-detailed-explainer` 只深入当前核心单篇论文，且不默认执行可信度审计。

## Agent 协作

- `concept-explainer` 和 `paper-detailed-explainer` 使用 `xhigh` 推理深度；两个文献索引 Agent 使用 `medium`；计划 Agent 使用 `low` 且保持只读。
- `paper-detailed-explainer` 仅在确有必要时调用 `concept-explainer`、`logical-reasoner` 或 `formula-deriver`；相互独立的子任务可并行。计划 Agent 返回 `planned` 时，详细解读 Agent 应明确降级范围并继续可可靠完成的部分。
- 任何下游 Agent 都不得回调 `paper-intent-route`。
