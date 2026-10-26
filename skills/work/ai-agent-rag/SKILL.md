---
name: ai-agent-rag
description: LLM / Agent / RAG / MCP / Tool Calling 相关项目的开发规范。当任务涉及大模型调用、Prompt、Agent State/Memory/Checkpoint、LangGraph、Ollama、Qdrant、Embedding、向量库、文档解析与分块、检索与重排、引用（Citation）、工具调用（Tool Calling）或 MCP 时使用。
---

# AI / Agent / RAG 项目规范

## 1. 总原则

- 先检查并理解现有架构，再动手。
- **不要为了实现简单功能直接引入新的 Agent Framework。**
- 优先复用已有：模型调用层、Prompt 管理、Tool 系统、Memory、RAG、Vector Store、MCP。
- 涉及 Agent State、Memory、Checkpoint、Tool Calling、Agent Run 等核心能力时，必须先理解现有数据流，再修改。

## 2. RAG：区分各环节，不要混为一谈

```text
文档解析 → Document → Chunk → Embedding → Vector Store
        → Retrieval → Rerank → Context → LLM → Citation
```

Document、Chunk、Vector、Retrieval Result、Context、Citation 是不同的数据对象，修改时先确认改动落在哪个环节，以及上下游数据结构是否受影响（例如改 Chunk 策略会影响已入库向量，需要提醒是否要重建索引）。
项目已有 Citation 机制时，优先复用，不另起一套。

## 3. Tool Calling

涉及工具调用时，优先检查：

- Tool 定义与 Tool Schema
- 参数校验
- Tool Executor
- Tool Result 的结构与回传
- Error Handling（工具失败时模型如何感知）
- Agent State 的更新方式

**不要让模型直接执行未经校验的危险操作**：涉及写库、删除、外部请求、文件系统、命令执行的工具，必须有参数校验和权限边界；高风险操作应有二次确认或人工审批环节。

## 4. 改动前自查

- 现有模型调用、Prompt、Tool、Memory 的入口分别在哪里？
- 本次改动会影响 Agent State / Checkpoint 的结构吗？是否影响历史数据的兼容？
- 是否有可以复用的现有组件，而不是新增框架或抽象？

不确定时先读代码和文档，仍无法确定再询问用户，不要猜测数据流。
