---
name: paper-intent-route
description: 对任何尚未分类且可能涉及学术论文的顶层请求进行一次轻量意图分流，包括用户提供 PDF、论文题名、DOI、arXiv 或网址，要求阅读、总结、比较或审查论文，以及为写作、引用、Related Work、学习、综述或项目调研寻找文献。读取项目 AGENTS.md 的论文任务意图指导区和对话上下文，输出结构化路由；不分析论文正文。纯概念问题且没有具体论文或文献需求时不使用；下游已收到路由契约时不得再次使用。
---

# 论文意图分流

这是论文任务的唯一宽入口。保持轻量，只判断路线，不完成正文分析、检索或可信度审计。

## 两种调用方式

### 顶层隐式调用

当前顶层用户回合出现以下任一信号，且论文任务尚未分类时，可因 description 命中而自发调用：

- 提供 PDF、论文全名、DOI、arXiv 标识或论文网址；
- 要求阅读、总结、解释、比较、质疑或验证一篇或多篇论文；
- 写论文、补引用、组织 Related Work 或寻找论据；
- 为学习知识点、做文献综述或项目调研寻找论文。

### 由顶层协调者调用

其他流程发现一个尚未分类的论文子任务时，顶层协调者可以显式调用本 Skill，并传入当前任务、上下文和已有用户背景。只接受尚无路由契约的任务。

## 不变量

- 每个顶层用户回合最多执行一次；若本回合已有路由结果，复用它。
- 下游 Skill 或 Agent 不得调用或回调本 Skill。
- 不要因论文中出现一个术语而为纯概念解释重新路由。
- 不把发送单篇论文自动等同于全文解读或比较。

## 路由步骤

1. 读取项目 `AGENTS.md` 的论文任务意图指导区、当前回合和已有任务上下文。
2. 若输入含论文来源标识且尚无来源卡，先调用 `paper-source-resolve`，再结合可用内容层级判断阅读深度。
3. 按以下优先级判断：当前回合明确要求 → 已建立的对话任务 → 写作、学习、综述、项目、比较或审计线索 → 上下文相关性 → 长期偏好。
4. 向顶层协调者返回一份内部路由契约和一句简短依据，不在本 Skill 内执行下游正文工作。

路由契约仅供内部协调使用，不是面向用户的普通答复。除 `clarify` 和当前未实现的 `audit` 降级外，顶层协调者必须在同一回合立即调用选定下游，并向用户交付实际解读、索引、检索或关系分析结果；不得只展示 YAML 后结束回合。

## 路由契约

```yaml
route: detailed | brief | writing_index | learning_index | compare_direct | compare_search | audit | clarify
target_papers: []
context_relevance: central | peripheral | unrelated | unknown
comparison_signal: explicit | inferred | none
confidence: high | medium | low
reading_depth: abstract_only | targeted_sections | full_text
user_context:
  expertise: known | inferred | unknown
  topic_familiarity: known | inferred | unknown
  frontier_depth: foundational | balanced | frontier
  core_relevance: core | adjacent | peripheral
  search_scope: narrow | adjacent | broad
missing_decision: null
```

只使用 `high`、`medium`、`low`，不要伪造数值置信度。

## 判定规则

- 当前核心单篇论文：`detailed`。
- 上下文旁支论文：`brief`。若用户没有指定后续安排，完成最小解读后由父流程用编号选项澄清。
- 明确只要简要总结：`brief`，完成后不额外反问。
- 写作、引用、Related Work：`writing_index`。
- 学习、文献梳理型综述、项目调研：`learning_index`，传递用户程度、熟悉度、前沿深度、核心关联和范围。
- 比较对象已给齐：`compare_direct`；需要先寻找对象：`compare_search`。
- 当论文发布时间不久且它是当前核心，或用户明确要求质疑、复核、验证或可信度审查时：`audit`。新近论文若只是上下文旁支，仍为 `brief`。当前审计模块未实现，父流程必须说明限制，并提供编号选项“仅做详细解读与局限梳理”或“等待审计模块”；不得伪装完成审计。
- 与当前上下文无关的论文：先依据用户明确要求、既有任务和所有现有路线推测目的并归类；只有两条以上成本明显不同的路线仍同样合理时才 `clarify`。
- “综述”若指整理文献和学习路径，使用 `learning_index`；若指复核、验证论文结论或审查可信度，使用 `audit`。语义不清且两条路线成本差异明显时才 `clarify`。
- 有两条以上成本明显不同的合理路线且无法可靠判断：`clarify`，在 `missing_decision` 中写出唯一决定性问题；当前没有反问 Skill，由父流程提供二至三个编号选项。

高置信直接执行；中置信先给低成本最小结果；低置信先澄清。
