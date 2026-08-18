---
name: paper-relationship-analysis
description: 基于已给齐的论文或筛选后的候选集，分析论文之间的继承、扩展、冲突、验证、竞争和互补关系，并区分作者明示、文本证据与分析推断。用于直接比较或检索后的关系整理；不负责寻找论文、不制定筛选范围，也不自动升级为论文可信度审计。
---

# 论文关系分析

围绕父流程指定的问题比较论文，不做无目标的逐篇摘要拼接。

## 前置输入

接受两类输入：

- `compare_direct`：用户已经给齐的论文及各自来源卡；
- `compare_search`：`related-paper-search` 返回的筛选候选及共同比较维度。

内容层级不足以支持某种关系判断时明确保留，不得依赖标题或搜索片段下结论。

## 分析维度

- 研究问题与适用对象是否相同；
- 假设、定义、度规、符号和边界条件是否可比；
- 方法、模型、数据或证明策略的承接与变化；
- 结果是在扩展、复现、验证、反驳，还是解决不同子问题；
- 表面冲突是否来自前提、尺度、统计口径或评价指标差异；
- 对父任务的互补用途、竞争解释和仍未覆盖的缺口。

## 关系标签

可使用 `inherits`、`extends`、`conflicts`、`validates`、`competes`、`complements`，也可在确有必要时用清晰文字补充。每条关系必须包括方向、比较维度、依据与证据等级。

```yaml
focus_question: null
relationships:
  - source_paper: null
    target_paper: null
    relation: inherits | extends | conflicts | validates | competes | complements
    dimension: null
    basis: explicit_citation | textual_evidence | analytical_inference
    evidence_level: metadata | abstract | targeted_sections | full_text
    explanation: null
    caveat: null
consensus: []
disagreements: []
gaps: []
```

把分析性推断明确标为 inference。关系分析不等于可信度审计；只有父流程已明确进入审计路线时才把材料转交审计模块，当前阶段不得声称专用审计已实现。不要回调意图路由。
