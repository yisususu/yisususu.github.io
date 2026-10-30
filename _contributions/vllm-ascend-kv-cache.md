---
title: Correct KV-cache copies across shared views
project: vLLM-Ascend
activity_date: "2026-10-28"
summary: Added layout-aware deduplication and source snapshots to prevent incomplete KV-cache copies across overlapping views, copy chains, and cycles.
links:
  - label: "Issue #20157"
    url: https://github.com/vllm-project/vllm-ascend/issues/20157
  - label: "PR #20158"
    url: https://github.com/vllm-project/vllm-ascend/pull/20158
---

## The problem

Distinct KV-cache views can share a starting address while covering different bytes. Deduplicating only by `data_ptr()` could skip a wider view and leave part of a destination block unchanged.

## The change

The patch distinguishes views by their layout and groups potentially overlapping memory regions. It snapshots every source in a group before writing destinations, preserving the original data through copy chains and cycles.

Temporary snapshots are released between disjoint groups.

## Verification

The public PR records block-copy regression tests, including tests with real NPU tensors, plus existing MLA lifecycle checks. A Qwen3.5-0.8B check produced matching token IDs with cold and warm caches, with instrumentation confirming that the modified helper ran.

Validation used one Ascend 910B3. The utility tests ran in isolation, and the model check overlaid the changed helpers onto an existing runtime. The complete current checkout with its pinned dependency was outside these local checks. Full commands and results are in the linked PR.
