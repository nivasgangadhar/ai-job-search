---
framework_version: 1.0.1
---

# Agent Guidelines: AI Job Search

This workspace is structured to manage job search activities, scraper tools, CVs, cover letters, and interview preparation.

## Thin-Pointer Design (Single Source of Truth)

To prevent duplication and configuration drift across different AI agent frameworks (Claude Code, Google Antigravity, Codex, Cursor, Gemini CLI, etc.), this workspace uses a unified thin-pointer design. All agent runtimes should load the canonical specifications and candidate profiles from the files and directories below:

1. **Personal Candidate Profile:**
   - The candidate profile, contact details, education, and target preferences are defined in [CLAUDE.md](CLAUDE.md) and the individual profile methodology files under [.claude/skills/job-application-assistant/](.claude/skills/job-application-assistant/) (specifically `01-*.md` etc.).
2. **Canonical Workflow Specifications:**
   - The step-by-step instructions and triggers for tasks (setup, scrape, rank, apply, upskill, interview) are defined in the [.claude/](.claude/) directory (specifically under `.claude/skills/` and `.claude/commands/`).
   - Do not duplicate these rules or specifications. Treat `.claude/` files as the single source of truth.
3. **Portal Search Skills:**
   - Job-portal search CLIs live under [.agents/skills/](.agents/skills/) in the portable Agent Skills format (with a `SKILL.md` per portal). Only skills with `cli/src/cli.ts` are portals; the other skills are workflow entry points. The `/scrape` workflow in [.claude/skills/job-scraper/](.claude/skills/job-scraper/) orchestrates the portals.

## Codex workflow entry points

Read [CLAUDE.md](CLAUDE.md) for the shared profile and verification checklist. Its filename is retained for compatibility; Claude Code is not required. Before executing a workflow, read [the Codex compatibility guide](docs/CODEX_COMPATIBILITY.md), then the canonical source below. Paths in workflows resolve from the repository root unless explicitly marked as relative to the canonical skill directory.

| Request / legacy shorthand | Codex skill | Canonical workflow |
|---|---|---|
| Set up or update my profile / `/setup` | `$job-profile-setup` | `.claude/commands/setup.md` |
| Find jobs / `/scrape` | `$job-scrape` | `.claude/skills/job-scraper/SKILL.md` |
| Rank saved jobs / `/rank` | `$job-rank` | `.claude/commands/rank.md` |
| Evaluate a posting, tailor a CV or cover letter / `/apply` | `$job-apply` | `.claude/commands/apply.md` |
| Prepare for an interview / `/interview` | `$job-interview` | `.claude/commands/interview.md` |

Treat legacy slash commands as workflow names in ordinary requests, not as Codex built-in commands. `$ARGUMENTS` means the user's supplied arguments, URL, or pasted text. For individual application tasks, use the routing in `.claude/skills/job-application-assistant/SKILL.md`; for a full application use `/apply`, including compile, review, and recording.

Other workflows remain available by request: load `.claude/commands/<name>.md` for `outcome`, `expand`, `add-template`, `add-portal`, `html-report`, `gmail-sync`, `notion-sync`, or `reset`; load `.claude/skills/upskill/SKILL.md` for `upskill`. Apply the same compatibility guide. Read only the references the selected task needs. Do not copy profile data or workflow specifications into Codex entry points.

## Local work and authorization

- Do not push to GitHub or write through GitHub tools without the user's explicit approval. A local workflow request is not publishing approval.
- Keep candidate facts grounded in the existing profile sources. Using Codex to run the workspace does not establish professional experience with Codex or Claude Code.
- Preserve the workflow's existing user decision points (profile conflicts and inferred additions, fit before drafting, flagged postings, destructive resets, and external sync). Existing explicit session authorization can satisfy a decision point; do not ask again for the same action.
- Claude `allowed-tools`, agent `tools`, and `.claude/settings.json` permissions do not configure Codex. Honor the active Codex sandbox and approvals; never bypass them. Perform fetch, scoring, and content-review stages sequentially by default, following the compatibility guide. Agent role files describe responsibilities, not a Codex tool sandbox.
