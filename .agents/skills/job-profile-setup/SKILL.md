---
name: job-profile-setup
description: Set up or update the candidate profile and job-search preferences from documents, a CV, or a guided interview, including legacy /setup requests.
---

# Profile setup in Codex

Read the repository-root `AGENTS.md` and `CLAUDE.md`, then
[Codex compatibility](../../../docs/CODEX_COMPATIBILITY.md) and the canonical
[setup workflow](../../../.claude/commands/setup.md). Follow that workflow,
including its three onboarding paths, conflict resolution, and incremental
merges. Interpret user options such as `--section search` as workflow arguments.

Resolve all paths from the repository root. Preserve the profile and template
files in their canonical locations; this skill contains no separate profile.
Collect missing facts from the user or supplied documents. Never populate a
profile from assumptions about the person operating Codex.
