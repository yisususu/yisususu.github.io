---
title: Restoring Mooncake sleep-mode compatibility
project: vLLM-Ascend
activity_date: "2026-10-29"
summary: Replaced an unsupported Mooncake peer-disconnect call with native memory deregistration to restore the affected sleep path.
links:
  - label: "Issue #20160"
    url: https://github.com/vllm-project/vllm-ascend/issues/20160
  - label: "PR #20163"
    url: https://github.com/vllm-project/vllm-ascend/pull/20163
---

## The problem

With Mooncake KV transfer enabled, a sleep request could fail because `GlobalTE.unregister_buffer()` called `disconnect_all_peers()`, which the Mooncake Python binding did not expose. The failure happened before the allocator could release model memory.

## The change

The patch calls `unregister_memory()` directly. Current Mooncake Ascend Direct handles peer disconnection during native memory deregistration.

It removes the unsupported wrappers and unused worker RPC while preserving error checks and buffer re-registration on wake. The regression tests use strict test doubles that expose only supported APIs.

## Verification

The public PR records regression tests and two sleep/wake cycles on a single Ascend 910B3 with Qwen3-0.6B. The checks covered split and full wakeup, buffer re-registration, and matching deterministic output before and after sleep.

These checks used current Mooncake Ascend Direct on one node. Cross-node transfers and older implementations were outside the test scope. Full test commands and results are in the linked PR.
