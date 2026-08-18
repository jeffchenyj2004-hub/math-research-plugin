---
name: paper-source-resolve
description: 将父流程提供的论文 PDF、题名、DOI、arXiv 标识或网址解析为统一的论文来源卡，确认标准身份、元数据、访问状态和实际可取得的内容层级。仅负责来源和证据边界，不总结论文观点、不判断用户意图、不评价可信度；通常由 paper-intent-route 或论文 Agent 调用。
---

# 论文来源解析

把不同形式的论文引用统一成可供下游可靠使用的来源卡。名称使用动作式 `resolve`；它不是负责解释论文的 Agent。

## 输入

接受 PDF 文件或其可读内容、论文题名、DOI、arXiv 标识、论文页面或直接 PDF 网址。父流程可以附带目标段落或待读问题。

## 工作方式

1. 识别 `reference_type`，优先使用论文正文首页、DOI 注册信息、arXiv 页面或出版方页面确认身份。
2. 统一标准题名和可用标识符，记录作者、年份和来源。无法交叉核实时明确标注，不猜测缺失字段。
3. 实际检查能取得的内容，不以链接形式推定全文可读。
4. 若父流程只需要特定问题且可访问相应章节，标记 `targeted_sections` 并列出取得的章节。
5. 返回来源卡和一条证据说明，不进行论文总结。

## 来源卡

```yaml
reference_type: pdf | title | doi | arxiv | url
canonical_identity: 标准题名及可用标识符
metadata:
  authors: []
  year: null
  venue: null
content_level: metadata | abstract | targeted_sections | full_text
access_status: available | partial | unavailable
available_sections: []
source_notes: null
```

## 证据边界

- 只取得元数据时标记 `metadata`；取得摘要才可标记 `abstract`。
- 搜索结果片段、新闻稿、引用页面或第三方转述不等同于原论文摘要或正文。
- 网页需要登录、被阻止、链接失效或只显示预览时，使用 `partial` 或 `unavailable`。
- `full_text` 仅用于确已读取可用全文的情况；有 PDF 文件但未能读取也不能这样标记。
- 身份存在多个候选时列出冲突，并把 `canonical_identity` 标为未确认，交还父流程决定。
- 下游需要全文而当前不足时，明确差距；父流程再向用户提供“上传全文”或“接受局部分析”的编号选项。
