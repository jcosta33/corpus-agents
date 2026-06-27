---
name: corpus-evidence-checker
description: >-
  Retired — folded into corpus-reviewer as its proof-first mode. When you only need a finished task's
  Verify checks re-run and the verbatim output pasted (not a full review packet), delegate to
  corpus-reviewer in proof-first mode. This stub stays only to redirect inbound references; do not
  install it as its own agent.
tools: Read, Grep, Glob, Bash
---

# corpus-evidence-checker (retired → corpus-reviewer proof-first mode)

This agent has been **retired**. Its whole job — re-run every `## Verify` item yourself and paste the
verbatim output, then flag any claim that lacks matching evidence — is exactly **step 2 of
[`corpus-reviewer`](./corpus-reviewer.md)**, a strict subset of the review packet. Keeping a separate
agent for it was a sequencing convenience, not a second role.

## Use corpus-reviewer's proof-first mode instead

When you want the checks proven _now_ — without the diff-read and coverage-table work of a full review —
delegate to **[`corpus-reviewer`](./corpus-reviewer.md)** and ask for its **proof-first mode**: re-run
the task's Verify items, paste the verbatim output (command · last lines · exit status), confirm each
run actually collected the named tests, and flag every claim with no matching re-run as **Unverified** —
stopping before the diff-read, coverage table, and maintainability lenses. Same no-verdict contract:
it produces evidence; the human (or the full review) judges.

This stub carries `name: corpus-evidence-checker` only so a stale inbound reference resolves to this
redirect. Do not copy it into a repo as a working agent.
