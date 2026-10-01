---
name: hybrid-vector-bm25-retrieval
description: Retrieves memories using hybrid semantic vector and BM25 search.
---

# Hybrid Vector & BM25 Retrieval

## Overview
Combines dense neural embeddings with sparse BM25 lexical token matching using reciprocal rank fusion (RRF), ensuring exact keyword precision and broad semantic recall.

## Key Capabilities
- Sub-5ms in-process retrieval without external database roundtrips.
- Reciprocal rank fusion balancing exact identifiers and conceptual matches.
- Filtering by confidence tier, age decay, and domain category.
