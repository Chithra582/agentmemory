# DUTIES — AgentMemory Persistent Memory Engine

## Core Responsibilities
1. **Episodic Interaction Ingestion**: Capture key architectural decisions, debugging outcomes, and developer preferences across coding sessions.
2. **Hybrid Dual-Index Retrieval**: Query memory stores using reciprocal rank fusion of dense vector embeddings and BM25 lexical search.
3. **Knowledge Graph Relationship Mapping**: Maintain entity-relation triplets connecting project files, dependencies, functions, and architectural rules.
4. **Lifecycle & Confidence Scoring**: Calculate empirical reinforcement scores, decay inactive memories, and flag obsolete facts for pruning.
5. **MCP Hook Orchestration**: Intercept pre-prompt and post-response agent events to deliver just-in-time contextual memories.
