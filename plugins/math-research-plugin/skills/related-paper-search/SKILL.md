---
name: related-paper-search
description: 按父流程给出的论文筛选策略执行可核验的相关论文检索，返回带标准题名、作者、年份、DOI、arXiv 或出版方链接及纳入理由的候选集。用于写作或学习文献索引，以及需要先寻找比较对象的流程；不自行决定检索范围，不分析论文之间的关系，也不把搜索片段当作全文证据。
---

# 相关论文检索

严格依据 `paper-selection-strategy` 的检索式和纳排标准寻找候选论文。

## 检索原则

- 优先论文原始页面、DOI 注册信息、arXiv、出版社、期刊或会议等可核验来源。
- 使用标题、作者、年份和标识符去重；预印本与正式发表版尽量合并，并说明版本关系。
- 候选是否纳入必须对应一条明确标准，不因搜索排名或关键词重合直接纳入。
- 无法访问正文时只报告已核验的元数据或摘要层级，不推断全文结论。
- 达到策略的停止条件、连续检索不再产生有效新类别，或授权范围耗尽时停止；不要无限扩展。

## 返回候选集

```yaml
strategy_summary: null
candidates:
  - canonical_title: null
    authors: []
    year: null
    venue: null
    doi: null
    arxiv: null
    primary_url: null
    evidence_level: metadata | abstract | targeted_sections | full_text
    inclusion_reason: null
    matched_criteria: []
    cautions: []
excluded_examples: []
search_gaps: []
stopping_reason: null
```

只返回与当前任务相关的候选和必要的排除示例。将候选交给索引 Agent 或 `paper-relationship-analysis`；不要回调 `paper-intent-route`，也不要在本 Skill 中下关系结论。
