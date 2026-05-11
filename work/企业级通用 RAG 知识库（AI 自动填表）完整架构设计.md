# 企业级通用 RAG 知识库（AI 自动填表）完整架构设计

你这个系统本质上不是：

```text
聊天机器人
```

而是：

# “企业知识治理 + AI填表系统”

核心目标：

- 多来源知识融合
- 高准确率字段填充
- 可追溯
- 可扩展
- 可治理
- 可演进

------

# 一、系统总体架构（推荐）

```text
                    ┌────────────────────┐
                    │ 业务表单/前端系统   │
                    └─────────┬──────────┘
                              ↓
                    ┌────────────────────┐
                    │ Field Fill Agent    │
                    └─────────┬──────────┘
                              ↓
                 ┌──────────────────────────┐
                 │ Retrieval Planner         │
                 └──────┬─────────┬────────┘
                        ↓         ↓
                 ┌─────────┐ ┌────────────┐
                 │ Fact DB │ │ Semantic DB│
                 └────┬────┘ └──────┬─────┘
                      ↓             ↓
                 ┌────────────────────────┐
                 │ Conflict Resolver       │
                 └──────────┬─────────────┘
                            ↓
                 ┌────────────────────────┐
                 │ LLM Reasoning Layer     │
                 └──────────┬─────────────┘
                            ↓
                 ┌────────────────────────┐
                 │ Structured Output JSON  │
                 └────────────────────────┘


─────────────────────────────────────────────

文档处理侧：

Document Sources
    ↓
Weknora 文档解析
    ↓
Token Chunks
    ↓
Stateful Extraction Pipeline
    ↓
Fact Candidates
    ↓
Canonical Normalize
    ↓
Fact Store
```

------

# 二、核心设计思想（最重要）

整个系统：

# 不是：

```text
Document → Embedding → QA
```

而是：

# “Document → Facts → Retrieval → Structured Reasoning”

即：

```text
文档
 ↓
事实
 ↓
语义层
 ↓
Agent检索规划
 ↓
结构化填表
```

------

# 三、系统拆分（完整）

建议拆成：

| 层级                   | 作用              |
| ---------------------- | ----------------- |
| 1. 数据接入层          | 接收文档/系统数据 |
| 2. 文档解析层          | PDF/PPT/OCR解析   |
| 3. Chunk层             | 文档中间表示      |
| 4. Stateful Extraction | 状态化事实抽取    |
| 5. Fact Layer          | 结构化事实库      |
| 6. Semantic Layer      | 摘要/语义层       |
| 7. Retrieval Layer     | 混合检索          |
| 8. Agent Layer         | 填表智能体        |
| 9. Governance Layer    | 质量治理          |

------

# 四、各层详细设计

------

# 1. 数据接入层（Ingestion Layer）

支持：

```text
1. 客户手工录入
2. ERP/API
3. PDF
4. PPT
5. Word
6. Excel
7. 图片
8. OCR扫描件
```

------

# 推荐设计

```json
{
  "document_id": "...",
  "company_id": "...",
  "source_type": "annual_report",
  "upload_time": "...",
  "version": "...",
  "hash": "..."
}
```

------

# 存储

推荐：

- MinIO
- OSS

数据库只存 metadata。

------

# 五、文档解析层（Parsing Layer）

你当前：

# 推荐直接复用：

[Weknora Github](https://github.com/Canner/WrenAI?utm_source=chatgpt.com)

做：

```text
文档解析
chunk
embedding
retrieval API
```

即可。

------

# 六、Chunk Layer（核心中间层）

由于 Weknora 是 token chunk：

你需要：

# 增加：

```text
Chunk Semantic Memory
```

------

# Chunk 数据结构

```json
{
  "chunk_id": "...",
  "document_id": "...",
  "chunk_index": 18,

  "content": "...",

  "prev_chunk": "...",
  "next_chunk": "...",

  "pseudo_section": "公司治理",

  "embedding_id": "...",

  "metadata": {
    "page": 18
  }
}
```

------

# 七、Pseudo Section Recovery（非常关键）

因为 Weknora 不支持 section：

你需要：

# 自己恢复：

```text
逻辑章节
```

------

# 方法：

检测：

```text
第一章
1.1
（一）
Company Overview
```

以及：

- heading pattern
- OCR layout
- markdown heading

------

# 八、Stateful Extraction Pipeline（核心）

这是整个系统最重要部分。

------

# 目标：

解决：

- chunk割裂
- 上下文丢失
- 表格跨页
- 字段漂移

------

# 核心思想：

# “状态化连续抽取”

------

# 处理流程

```text
ordered chunks
    ↓
load memory
    ↓
sliding window
    ↓
LLM extraction
    ↓
update memory
    ↓
next chunk
```

------

# 九、Extraction Memory（极其重要）

建议维护：

------

# 1. Section Memory

```json
{
  "current_section": "公司基本情况"
}
```

------

# 2. Pending Fields

```json
{
  "pending_fields": [
    "legal_representative"
  ]
}
```

------

# 3. Active Entity

```json
{
  "active_company": "XX新能源"
}
```

------

# 4. Table Memory

```json
{
  "active_table": "主要财务指标"
}
```

------

# 5. Previous Chunk Summary

```json
{
  "topic": "公司治理"
}
```

------

# 十、Sliding Window（非常推荐）

不要独立抽 chunk。

建议：

```text
prev chunk tail
+ current chunk
+ next chunk head
```

例如：

```text
20% + 100% + 20%
```

------

# 十一、Fact Candidate Extraction（推荐）

不要直接写正式 fact。

先：

# 生成 candidate。

------

# 输出：

```json
{
  "field": "legal_representative",
  "value": "张三",
  "confidence": 0.92,
  "source_chunk_id": "...",
  "source_document_id": "..."
}
```

------

# 十二、Canonical Field Registry（系统灵魂）

这是核心中的核心。

------

# 作用：

统一字段语义。

------

# 示例：

```json
{
  "canonical_field": "legal_representative",

  "aliases": [
    "法人",
    "法定代表人",
    "企业法人"
  ],

  "retrieval_strategy": {
    "primary": "fact",
    "fallback": "semantic"
  },

  "value_type": "person"
}
```

------

# 十三、Fact Layer（主知识库）

推荐：

# PostgreSQL + pgvector

------

# Fact Table

```json
{
  "fact_id": "...",

  "entity_id": "...",

  "canonical_field": "legal_representative",

  "value": "张三",

  "source_type": "annual_report",

  "source_priority": 90,

  "effective_date": "2024-12-31",

  "confidence": 0.95,

  "source_chunk_id": "...",

  "source_document_id": "..."
}
```

------

# 十四、Source Priority（必须）

企业级必须有：

```text
可信度体系
```

------

# 示例：

| 来源         | priority |
| ------------ | -------- |
| 客户人工确认 | 100      |
| ERP          | 95       |
| 年报         | 90       |
| 募集说明书   | 88       |
| 企查查       | 80       |
| OCR推断      | 60       |
| LLM推断      | 30       |

------

# 十五、Semantic Layer（语义层）

不是替代 fact。

而是：

# 补充。

------

# 存：

------

# 1. Chunk Summary

------

# 2. Section Summary

------

# 3. Document Summary

------

# 4. Entity Summary

最重要。

例如：

```text
公司是一家新能源企业...
```

------

# 十六、ES（全文检索层）

推荐：

Elasticsearch

------

# 作用：

不是 semantic。

而是：

# “精准全文召回”

------

# ES 最适合：

| 内容       | 是否适合 ES |
| ---------- | ----------- |
| OCR文本    | YES         |
| 数字       | YES         |
| 编号       | YES         |
| 法律关键词 | YES         |
| 精准术语   | YES         |

------

# 十七、Vector DB（语义检索层）

负责：

# semantic retrieval

------

# 推荐：

- pgvector
- Milvus

------

# 存：

```text
summary embedding
semantic chunk embedding
```

不是所有原始 chunk。

------

# 十八、Hybrid Retrieval（核心）

最终：

# 三层检索：

------

# 1. SQL Retrieval

结构化字段。

------

# 2. ES Retrieval

关键词。

------

# 3. Vector Retrieval

语义。

------

# 十九、Retrieval Planner（真正核心）

这是未来最重要模块。

------

# 作用：

决定：

```text
字段该去哪查
```

------

# 示例：

| 字段         | 检索策略    |
| ------------ | ----------- |
| 法人         | SQL         |
| 统一信用代码 | ES          |
| 主营业务     | Vector      |
| 风险分析     | ES + Vector |

------

# 二十、Field Fill Agent（填表 Agent）

不是聊天。

而是：

# “结构化推理系统”

------

# 工作流：

```text
Field
 ↓
Semantic Resolve
 ↓
Retrieval Planning
 ↓
Hybrid Retrieval
 ↓
Conflict Resolve
 ↓
LLM Reasoning
 ↓
JSON Output
```

------

# 二十一、Conflict Resolver（核心）

企业系统必须有。

------

# 解决：

```text
多个来源冲突
```

------

# 规则：

按：

```text
source priority
+
effective date
+
confidence
```

排序。

------

# 二十二、最终输出（必须可追溯）

不要只输出 value。

必须：

```json
{
  "field": "legal_representative",

  "value": "张三",

  "confidence": 0.95,

  "source": {
    "document": "2024年报",
    "page": 18,
    "chunk_id": "..."
  }
}
```

------

# 二十三、Dify 在系统里的定位

[Dify 官方网站](https://dify.ai/?utm_source=chatgpt.com)

不要让 Dify 管核心知识。

------

# Dify 更适合：

| 功能                | 是否适合 |
| ------------------- | -------- |
| Workflow            | YES      |
| Agent orchestration | YES      |
| Approval            | YES      |
| HITL                | YES      |
| 核心事实治理        | NO       |

------

# 二十四、系统最终架构（推荐）

```text
                 ┌───────────────────┐
                 │ Business Frontend  │
                 └─────────┬─────────┘
                           ↓
                 ┌───────────────────┐
                 │ Field Fill Agent   │
                 └─────────┬─────────┘
                           ↓
                 ┌───────────────────┐
                 │ Retrieval Planner  │
                 └────┬──────┬──────┘
                      ↓      ↓
               ┌────────┐ ┌─────────┐
               │ Fact DB│ │Semantic │
               └────┬───┘ └────┬────┘
                    ↓          ↓
               ┌──────────────────┐
               │ Conflict Resolver │
               └────────┬─────────┘
                        ↓
                 ┌──────────────┐
                 │ LLM Reasoning │
                 └──────┬───────┘
                        ↓
                 Structured JSON


─────────────────────────────────────

Document Pipeline:

Documents
   ↓
Weknora Parse
   ↓
Token Chunks
   ↓
Pseudo Section Recovery
   ↓
Stateful Extraction
   ↓
Fact Candidates
   ↓
Canonical Normalize
   ↓
Fact Store
```