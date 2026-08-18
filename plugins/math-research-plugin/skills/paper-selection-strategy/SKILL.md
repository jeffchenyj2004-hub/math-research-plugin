---
name: paper-selection-strategy
description: 根据写作、学习、综述、项目或比较目标制定论文检索范围、检索式、纳入排除标准、年代与方向侧重、比较维度和停止条件。只制定可执行筛选方案，不实际检索论文，也不分析论文关系；供文献索引 Agent 或顶层比较流程调用。
---

# 论文筛选策略

将研究目标和用户背景转化为可执行、可说明的文献筛选方案。

## 输入

尽量利用父流程已有信息；缺少非关键字段时采用保守默认，不为填表阻塞：

- `goal`: writing | learning | review | project | compare
- `topic` 与核心问题；
- `expertise`、`topic_familiarity`、`frontier_depth`；
- `core_relevance`、`search_scope`；
- 已知论文、时间或来源限制。

## 策略设计

- 写作路线优先引用用途、论点覆盖、方法来源、反面证据和研究缺口。
- 学习路线优先前置难度、概念依赖、奠基到前沿的顺序和可读性。
- 比较路线优先明确共同问题、可比假设、方法和证据维度，避免只因关键词相同就纳入。
- `narrow` 聚焦核心问题，`adjacent` 纳入直接邻近方向，`broad` 才扩展到跨方向背景。
- 年代范围应服务任务；不要默认只找最新论文，也不要因经典论文较旧而排除。

## 输出契约

```yaml
goal: writing | learning | review | project | compare
scope: narrow | adjacent | broad
search_queries: []
include_if: []
exclude_if: []
time_focus: null
venue_or_source_focus: []
comparison_dimensions: []
priority_order: []
stopping_condition: null
assumptions: []
```

检索式应覆盖术语别名、符号变体和必要的前置/邻近概念，但避免无依据扩大范围。把方案交给 `related-paper-search`；不要自行搜索或回调意图路由。
