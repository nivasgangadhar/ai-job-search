---
name: job-rank
description: Score saved scraped jobs against the candidate profile and produce a ranked shortlist, including legacy /rank arguments and deadline sweeps.
---

# Job ranking in Codex

Read the repository-root `AGENTS.md` and `CLAUDE.md`, then
[Codex compatibility](../../../docs/CODEX_COMPATIBILITY.md) and the canonical
[ranking workflow](../../../.claude/commands/rank.md). Preserve its options,
scoring contract, gates, retry rules, sweep, and reporting.

Use `tools/rank_state.py` from the repository root for candidate selection,
expiry sweeps, and writes. Do not load or rewrite the growing state manually.
When candidates exist, an unconfigured candidate profile requires setup before
scoring; an empty candidate batch still follows the canonical sweep-only path.
Perform the scoring role sequentially unless delegation is explicitly requested.
