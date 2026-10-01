# EXPLAINABILITY — AgentMemory Persistent Memory Engine

> **Admissibility & Transparency Report for OpenGAP / Agent Passport**  
> *Agent Name:* AgentMemory Persistent Memory Engine (`agentmemory-persistent-engine`)  
> *Specification:* OpenGAP v0.1.0  
> *Domain:* Developer Tools / Persistent Memory Engine  

---

## 1. Overview & Operational Purpose

AgentMemory Persistent Memory Engine is a zero-external-database cognitive memory framework engineered to provide autonomous coding agents with persistent cross-session recall, hybrid semantic-lexical search, and autonomous memory lifecycle management. Its primary operational purpose is to eliminate repetitive re-explanation in multi-session development workflows by preserving verified architectural decisions, project conventions, and debugging history across tools including Claude Code, Cursor, Codex, and Antigravity.

By enforcing confidence scoring, half-life decay, and reciprocal rank fusion within an embedded local footprint, AgentMemory ensures that coding assistants operate with persistent context without risking context bloat, memory poisoning, or cloud data leakage.

---

## 2. How the Agent Decides (Decision-Making Logic)

AgentMemory Persistent Memory Engine operates across a deterministic, multi-stage decision pipeline:

```
[Stage 1: Turn Ingest & Entity Parse] ──> [Stage 2: Hybrid Dual-Index Search] ──> [Stage 3: Confidence Rank & Filter]
                                                                                                  │
                                                                                                  ▼
[Stage 6: Graph Commit & Audit Trail] <── [Stage 5: Lifecycle Decay & Pruning] <── [Stage 4: Hook Context Injection]
```

### 2.1 Turn Ingestion & Entity Parsing
- **Decision:** The engine intercepts developer instructions and tool execution events, parsing out atomic technical assertions, file relationships, and explicit preferences.
- **Rules:** Reject vague or speculative statements. Memory candidates must represent verified facts (e.g. successful compilation, explicit architectural directives).

### 2.2 Hybrid Dual-Index Search
- **Decision:** Retrieve relevant memories using concurrent dense semantic vector embeddings and sparse BM25 lexical token indices.
- **Rules:** Combine rank positions using Reciprocal Rank Fusion (RRF). Prioritize exact keyword matches for identifiers, file paths, and error codes alongside conceptual matches.

### 2.3 Confidence Ranking & Filtering
- **Decision:** Score and filter candidate memories based on verification confidence, age decay, and semantic relevance to the active prompt.
- **Rules:** Exclude any memory whose decayed confidence falls below the operational threshold (0.5). Cap total injected memory size to 15% of the model context budget.

### 2.4 Lifecycle Decay & Graph Commitment
- **Decision:** Apply temporal decay to dormant memories, reinforce memories validated in successful turns, and write updated entity graphs to local storage.
- **Rules:** If a new memory contradicts an existing entry with higher confidence, flag the older memory as superseded and commit the verified revision.

---

## 3. Data Flow & Boundary Privacy

AgentMemory operates exclusively within the developer's local environment without transmitting repository data to external vector databases.

| Component / Boundary | Data Received | Processing & Retention | Destination / External Transmission |
|---|---|---|---|
| MCP Hook Gateway | User queries, tool call results, file changes | In-memory tokenization and entity extraction; session-scoped | Local memory engine |
| Local Vector/BM25 Index | Factual statements, code identifier tokens | Local embedded vector database and inverted index on disk | Local storage only |
| Knowledge Graph Store | Entity triplets (subject, predicate, object) | Local SQLite / JSON graph database; persistent across sessions | Local storage only |
| Audit Logger | Memory write/prune events, confidence scores | Tamper-evident structured JSON logging on local filesystem | Local disk logs |

AgentMemory Persistent Memory Engine complies with operational security and privacy standards:
- **No Cloud Data Exfiltration:** The memory engine requires zero external databases, cloud vector services, or third-party telemetry endpoints.
- **Epistemic Isolation:** Memory databases are namespaced per project repository, preventing cross-project context bleed.
- **Sanitized Model Payloads:** API keys, authorization tokens, passwords, and private personal data are automatically scrubbed prior to embedding.
- **Data Minimization:** Only high-signal architectural decisions and conventions are indexed, avoiding unconstrained raw session transcript storage.

---

## 4. Known Limitations & Failure Modes

Reviewers, auditors, and users should note the following operational constraints:

1. Stale Memory Retention During Rapid Codebase Refactors
   - *Limitation:* Major architectural rewrites may render existing memory entries obsolete before automatic decay cycles complete.
   - *Mitigation:* The engine provides explicit manual cache invalidation commands (`agentmemory clear` or category-specific prunes).

2. Cold-Start Retrieval in Brand New Repositories
   - *Limitation:* On newly initialized workspaces without prior session history, the memory engine provides no initial recall benefit.
   - *Mitigation:* Support one-shot codebase onboarding commands that index READMEs, architecture decision records (ADRs), and config files upon setup.

3. Embedding Model Version Drift
   - *Limitation:* Upgrading the underlying local embedding model can invalidate vector index distance metrics.
   - *Mitigation:* The engine embeds model architecture metadata in the index header and triggers automatic re-indexing when model version changes occur.

4. Token Budget Contention in Ultra-Long Prompts
   - *Limitation:* Extremely dense user prompts may leave insufficient context headroom for memory injection.
   - *Mitigation:* Enforce strict context ceilings (maximum 1,500 tokens) with dynamic degradation to top-1 recall under high prompt pressure.

---

## 5. Verification, Safety & Human Oversight

AgentMemory Persistent Memory Engine incorporates robust verification, safety gates, and human oversight controls across every layer of execution:

- **Real-Time Human Approval Gate:** Any bulk memory purge, index reset, or automated modification to user preference categories requires explicit human authorization.
- **Emergency Session Interrupt:** Developers can disable MCP memory injection instantly at any time via environment flags (`AGENTMEMORY_DISABLE=1`).
- **Step Quota Guardrails:** Autonomous memory consolidation routines are limited to a maximum of 25 turns per evaluation cycle to prevent background process runaway.
- **Structured Audit Logging:** Every memory creation, retrieval, decay penalty, and pruning event is recorded in structured JSON logs for audit inspection.
