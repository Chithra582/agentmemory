---
name: confidence-lifecycle-scoring
description: Manages confidence decay, fact reinforcement, and stale pruning.
---

# Confidence Lifecycle Scoring

## Overview
Maintains epistemic hygiene across the memory store by applying temporal decay curves, reinforcing frequently confirmed facts, and retiring obsolete decisions.

## Key Capabilities
- Half-life exponential confidence decay modeling.
- Reinforcement bonus calculations when memories are validated in successful builds.
- Automatic retirement and archiving of deprecated code patterns.
