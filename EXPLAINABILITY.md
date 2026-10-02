# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **AgentMemory Persistent Memory Engine** (`agentmemory`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** AgentMemory Persistent Memory Engine (`agentmemory`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Cognitive Persistent Memory & Vector RAG  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

AgentMemory Persistent Memory Engine is a zero-external-database cognitive memory framework engineered to provide autonomous coding agents with persistent cross-session recall, hybrid semantic-lexical search, and autonomous memory lifecycle management. Its primary operational purpose is to eliminate repetitive re-explanation in multi-session development workflows by preserving verified architectural decisions, project conventions, and debugging history across tools including Claude Code, Cursor, Codex, and Antigravity.

### 1. Decision Architecture

The turn ingestion, hybrid retrieval, confidence filtering, and lifecycle management pipeline operates across a deterministic, five-stage architecture:

```
Developer Turn Event (User Query / Tool Execution Output / File Modification / Architecture Directive)
    │
    ▼
[Stage 1: Turn Ingestion & Entity Parsing]
    │  - Intercepts developer instructions and execution events in real time
    │  - Extracts atomic technical assertions, file dependency triplets, and preferences
    │  - Rejects speculative or unverified statements
    ▼
[Stage 2: Hybrid Dual-Index Search]
    │  - Queries embedded dense vector index and sparse BM25 lexical token inverted index
    │  - Merges rank positions using Reciprocal Rank Fusion (RRF)
    │  - Prioritizes exact matches for symbols, file paths, and compiler error codes
    ▼
[Stage 3: Confidence Ranking & Context Budgeting]
    │  - Evaluates memory verification confidence, age decay, and semantic relevance
    │  - Filters out memories below the confidence threshold (theta < 0.5)
    │  - Bounds injected memory context to <=15% of active LLM context budget
    ▼
[Stage 4: Lifecycle Decay & Graph Reconciliation]
    │  - Applies temporal half-life decay to dormant memories
    │  - Reinforces memories validated in successful tool turns
    │  - Resolves contradictions by superseding older low-confidence entries
    ▼
[Stage 5: Local Storage Commit & Audit Logging]
    │  - Commits updated knowledge triplets to local SQLite / JSON store
    │  - Sanitizes execution traces, scrubbing private credentials and paths
    │  - Emits tamper-evident structured audit records to local filesystem
    ▼
Validated Context-Enriched Memory & Auditable Knowledge Graph Record
```

### 2. Decision Logic & Memory Scoring Formulations

AgentMemory evaluates memory relevance, temporal decay, and reciprocal ranking using deterministic mathematical models:

1. **Reciprocal Rank Fusion Score ($S_{\text{RRF}}$)**:
   $$S_{\text{RRF}}(d) = \frac{w_v}{k + r_v(d)} + \frac{w_b}{k + r_b(d)}$$
   where $r_v(d)$ represents dense vector rank, $r_b(d)$ represents BM25 sparse rank, $k = 60$ is the smoothing constant, and weights $w_v = 0.60, w_b = 0.40$ balance semantic and lexical recall.

2. **Temporal Confidence Decay Metric ($C_{\text{decay}}$)**:
   $$C_{\text{decay}}(t) = C_0 \cdot \exp\left(-\frac{\Delta t}{\tau}\right) + R_{\text{reinforce}}$$
   where $C_0$ is initial verification confidence, $\Delta t$ is days since last recall, $\tau = 30\text{ days}$ is the decay half-life, and $R_{\text{reinforce}}$ rewards repeated successful developer validation. Memories where $C_{\text{decay}} < 0.50$ are pruned from prompt context.

### 3. Thresholding & Refusal Decision Criteria

AgentMemory Persistent Memory Engine enforces strict operational safety and integrity boundaries:
- **Refusal to Store Plaintext Credentials**: Memory candidates containing API keys, authorization tokens, private keys, or passwords trigger deterministic scrubbing and refusal (`ERR_CREDENTIAL_STORAGE_REFUSED`).
- **Refusal of Unverified Speculative Memory**: Ingested statements lacking empirical verification (successful compilation, explicit confirmation) are rejected (`ERR_SPECULATIVE_ASSERTION_REFUSED`).
- **Turn Ceiling Enforcement**: Memory retrieval and graph traversal loops enforce a ceiling of `max_turns: 25` to prevent runaway search loops (`WARN_TURN_BUDGET_EXCEEDED`).
- **Local Namespace Confinement**: Memory databases are isolated per project root; writes outside the active repository directory are blocked (`ERR_CROSS_WORKSPACE_LEAK_PREVENTED`).

### 4. Fallback Decision Mechanism

Continuous memory operations are guaranteed through multi-tier fault recovery:
- **Model Cascade Failover**: When the primary foundation model experiences latency spikes or HTTP 429 rate limits, the orchestrator cascades automatically between `claude-3-5-sonnet`, `gpt-4o`, and `gemini-2.0-flash`.
- **Pure BM25 Lexical Fallback**: If local vector embedding models fail or lack GPU acceleration, the engine transitions seamlessly to 100% deterministic local BM25 keyword retrieval.
- **Graceful Context Pruning**: When prompt contexts approach model limits, the memory injector degrades to summary entity triplets, preserving operational headroom.

### 5. Human-in-the-Loop Governance

Human developers retain absolute authority over memory contents and persistence:
- **Direct Memory Inspection & Editing**: Developers can inspect, edit, or delete any stored memory item directly through local CLI inspection commands.
- **Explicit Memory Deletion / Forget Command**: Developers can issue `/forget [topic]` or `/reset-memory` commands to purge specific concepts with verifiable 0-byte erasure.
- **Transparent Provenance Tracking**: Injected memory tokens explicitly cite source files, commit hashes, and verification timestamps in active chat prompts.

---

## The Data It Uses

AgentMemory operates under strict privacy, data minimization, and local workspace isolation standards.

### 1. Ingested Input Data

The agent processes only operational assets necessary to fulfill cognitive memory persistence:
- **Developer Instructions**: Queries describing architectural decisions, coding patterns, and user preferences.
- **Tool Execution Observations**: Compiler output logs, test results, and file edit diffs scoped to the repository.
- **Entity Triplets**: Subject-predicate-object semantic triplets extracted from verified developer actions.

### 2. Configuration & Reference Data

- **Memory Schema Definitions**: Pydantic schemas specifying entity relations, confidence thresholds, and decay half-lives.
- **Token Budget Allotments**: Model-specific context window bounds (capping memory injection at 15% of window).
- **Regex Redaction Rules**: Heuristic patterns for scrubbed credentials, JWTs, and local user paths.

### 3. Base Model & Inference Lineage

- **Deterministic Algorithmic Engines**: BM25 keyword search, reciprocal rank fusion, decay calculators, and SQLite storage engines executed natively in Python (100% deterministic with zero LLM variance).
- **Foundation LLMs**: High-capability frontier models (`claude-3-5-sonnet`, `gpt-4o`, `gemini-2.0-flash`) utilized for entity extraction, semantic embedding, and contradiction reconciliation.
- **Zero Training on User Code**: Developer memory databases, project conventions, and codebase triplets are never transmitted to external cloud servers or used for model training.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against memory poisoning, prompt injection, and excessive agency.
- **Zero External Cloud Databases**: All vector embeddings, BM25 indices, and SQLite graphs reside exclusively on the developer's local filesystem.
- **Automated PII & Secret Scrubbing**: Credentials, auth tokens, and private identifiers are scrubbed prior to index insertion.
- **Zero Commercial Monetization**: Memory contents, project conventions, and search queries are never shared, monetized, or sent to third-party telemetry sinks.

---

## Limitations

Understanding the operational boundaries and technical constraints of AgentMemory is essential for effective deployment.

### 1. Stale Memory Retention During Rapid Refactors
- **Limitation**: Large-scale architectural rewrites can render previously stored conventions obsolete before decay thresholds expire.
- **Mitigation**: The engine links memories to file paths and triggers automatic invalidation warnings when target files undergo major diffs.

### 2. Multi-Agent Concurrent Write Race Conditions
- **Limitation**: Multiple autonomous subagents writing to local SQLite memory stores concurrently can experience transient lock contention.
- **Mitigation**: The runtime implements write serialization queues and optimistic concurrency control with retry backoff.

### 3. Semantic Drift in Evolving Project Terminology
- **Limitation**: Changing domain naming conventions (e.g., renaming a core model class) can reduce semantic similarity against older memories.
- **Mitigation**: The engine merges lexical token indices with dense embeddings to maintain recall across vocabulary transitions.

### 4. Excessive Context Window Allocation
- **Limitation**: Ingesting too many historical memories into active prompts reduces space for current task instructions.
- **Mitigation**: The injector enforces a strict 15% token ceiling and summarizes older memories into concise bullet summaries.

### 5. Subjective Developer Preference Conflicts
- **Limitation**: Different developers working on the same repository may have conflicting stylistic preferences.
- **Mitigation**: AgentMemory supports user-scoped memory namespaces that isolate personal conventions from repository-wide architectural rules.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & memory scoring formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested instructions, observations & entity triplets | Section 1 | Verified |
| - Configuration, memory schemas & budget allotments | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Stale memory retention during rapid refactors | Section 1 | Verified |
| - Multi-agent concurrent write race conditions | Section 2 | Verified |
| - Semantic drift in evolving project terminology | Section 3 | Verified |
| - Excessive context window allocation | Section 4 | Verified |
| - Subjective developer preference conflicts | Section 5 | Verified |
