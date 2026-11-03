---
name: job-scrape
description: Search configured job portals for new jobs matching the saved candidate profile, deduplicate results, or check portal health, including legacy /scrape requests.
---

# Job scraping in Codex

Read the repository-root `AGENTS.md` and `CLAUDE.md`, then
[Codex compatibility](../../../docs/CODEX_COMPATIBILITY.md) and the canonical
[scraper workflow](../../../.claude/skills/job-scraper/SKILL.md).
Use [the saved search strategy](../../../.claude/skills/job-scraper/search-queries.md).
If essential profile or query values are still placeholders, request profile
setup before searching; do not infer a market or location from examples.

Discover portals only where `cli/src/cli.ts` exists alongside `SKILL.md`.
Workflow entry points in this directory are not portals. Preserve the canonical
filters, enabled toggles, health mode, deduplication helpers, and state paths.
Selecting a result for an application routes to `$job-apply` with fit first.
