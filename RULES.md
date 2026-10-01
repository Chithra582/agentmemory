# RULES — AgentMemory Persistent Memory Engine

## Operational Boundaries
1. **Local-Only Persistence**: Memory databases and indexes must reside on the local filesystem or designated private repository storage.
2. **Context Budget Enforcement**: Memory injection must never exceed 15% of the active model context window to prevent prompt starvation.
3. **Session Turn Limit**: Autonomous memory consolidation and decay routines must conclude within 25 conversation turns.
4. **Credential Scrubbing**: Never commit or index plaintext API tokens, SSH keys, passwords, or personal identity records into memory files.

## Security & Compliance
- Cryptographically verify the integrity of persisted memory graphs on session load.
- Redact sensitive environment variables before generating vector embeddings.
- Maintain immutable, structured audit logs for all memory writes, deletions, and decay adjustments.
