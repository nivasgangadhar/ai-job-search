# Codex migration audit

This increment adds Codex entry points for profile setup, scraping, ranking,
application generation, and interview preparation. The canonical workflows,
candidate profile locations, portal CLIs, templates, and state formats are
retained. No Claude Code process or Anthropic API call is required to execute
these workflows through Codex. Keeping `.claude/` is a compatibility choice,
not an instruction to install Claude Code.

## Codex quick start

1. Open `C:\Desktop\ai-job-search` as the Codex workspace. Start a fresh session
   if its instructions were loaded before these changes. `AGENTS.md` routes to
   the shared profile and workflows; no global configuration change is needed.
2. Use Python 3.10+ and Bun for the existing helpers and portal CLIs. Install
   portal dependencies only when needed with
   `python tools/install_portal_clis.py`. The installer discovers the six
   shipped CLIs and ignores the new workflow-only skills.
3. For generated PDFs, install a TeX distribution providing `lualatex` and
   `xelatex`, plus the packages documented in `SETUP.md`. For text checks,
   `pypdf` is optional; Poppler provides `pdftotext` and `pdftoppm` for geometry
   checks and visual rendering. Missing tools must be reported as incomplete
   verification, never successful verification.
4. Invoke a skill directly or use an ordinary request:

   | Goal | Example |
   |---|---|
   | Build the profile | `$job-profile-setup` or "Set up my profile from documents/" |
   | Change search preferences | `$job-profile-setup --section search` |
   | Find jobs | `$job-scrape` or "Find new jobs using my saved search preferences" |
   | Check portals | `$job-scrape health` |
   | Rank saved jobs | `$job-rank --limit 10 --top 5` |
   | Generate an application | `$job-apply <posting URL>` |
   | Prepare an interview | `$job-interview <company> <role>` |

The current profile and search strategy still contain template placeholders.
Run profile setup before a personalized live search or evaluation. This migration
does not populate personal facts or change portal enablement. Setup still offers
document import, single-CV import, or a guided interview. It preserves the
existing confirmation of inferred additions and conflicts.

Skills are discovered from repository `.agents/skills/`; if a new skill does
not appear, restart Codex. Legacy `/setup` and similar strings can be used as
workflow names in a request; they are not newly registered Codex slash commands.
See the official [skills documentation](https://learn.chatgpt.com/docs/build-skills)
and [AGENTS.md documentation](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
for discovery details.

## Dependency findings and treatment

| Dependency / location | Effect in Codex | Treatment in this increment |
|---|---|---|
| `CLAUDE.md`, `.claude/commands/`, `.claude/skills/` | Codex does not automatically discover Claude commands or load their profile. | Keep the single source of truth. Add explicit routing from `AGENTS.md` and five native skill manifests. |
| Slash commands and `$ARGUMENTS` | Claude invocation syntax has no automatic Codex registration or substitution. | Interpret names and arguments through native skills or ordinary requests. |
| `Read`, `Write`, `Edit`, `Glob`, `Grep`, `Bash`, `WebFetch`, `WebSearch`, `AskUserQuestion`, `Skill` | Tool names and exact interfaces differ. | Translate operations using available session tools in `CODEX_COMPATIBILITY.md`. Preserve fetch escalation and the posting gate. |
| `Agent` / `subagent_type` in apply, rank, scrape; `.claude/agents/*.md` | Claude role files are not Codex agent configuration. Fresh context and restricted tools are not implied. | Default to sequential fetch, score, and critique stages with the same outputs. Disclose the isolation difference; optional delegation requires actual support and authorization. |
| `.claude/settings.json`, `allowed-tools`, agent `tools:` | Claude permission grants, denials, and per-role restrictions do not configure Codex. | Retain for Claude compatibility; document Codex's active sandbox and approvals as the enforcement layer. Add no permissive config or hooks. |
| Bash heredocs, `SCRATCHPAD`, `python3`, cleanup commands | Native PowerShell and Windows Python differ. | Use `python`, exact UTF-8 data files, external temporary paths, tool working directories, and scoped native cleanup. |
| PDF inspection via Claude `Read` | A file read in Codex may provide no page images. | Keep compile, page, layout, ATS, and visual checks; render all pages locally for inspection where needed. Report missing capabilities honestly. |
| Mandatory **Claude Code** branding in `CLAUDE.md` and apply | Could insert unsupported tool experience into applications. | Replace with confirmed-profile evidence for whichever tools are named. Do not replace it with mandatory Codex branding. |
| Portal discovery by every `.agents/skills/*/SKILL.md` | New workflow skills could be misclassified as portals. | Require an adjacent `cli/src/cli.ts` before treating a skill as a portal, including health checks. Existing enablement, interfaces, and state helpers remain intact. |
| `Claude-User` in `robots_check.py` and web research guidance | Legacy crawler identity is present in the helper, even without a Claude runtime. | Preserve its conservative `*` / `Claude-User` policy gate. Explain that it is not Codex's web-client identity; a Codex policy denial must also be honored. No guard is removed. |
| `mcp__claude_ai_Gmail__*`, claude.ai connector instructions in Gmail sync | Connector names and authentication belong to Claude. | Discover equivalent connected Codex tools and map their real schemas; require full bodies and preserve approval before local status writes. Missing capabilities get a clean stop. |
| `claude mcp add`, `/mcp` in Notion sync | Claude connection commands do not configure Codex. | Use available connected service tools when requested; report missing connection without automatic authentication. |
| README and SETUP requiring Claude Code | Original onboarding incorrectly appears mandatory for Codex. | Add a separate Codex quick start while preserving the original instructions. |
| Python helpers, salary lookup, Bun portal CLIs, LaTeX assets | Filesystem and executable dependencies, not model-provider calls. | Reuse without runtime changes. Optional salary data stays optional; no new model API or key is introduced. |
| Framework versions, security/lint tooling, CI | Checks intentionally reference canonical paths and Claude metadata. | Retain paths and existing guards. Bump the changed `AGENTS.md` framework marker. Existing lint also discovers the new skills. |

## Workflow coverage

| Workflow | Preserved behavior |
|---|---|
| Profile setup | All three paths, partial updates, source reconciliation, explicit review of inferred facts, canonical contact/template blocks, query generation. |
| Scraping | Portal discovery and toggles, recency and location filters, quick fit and language checks, deduplication, expiry signals, bounded health probes, referral search links. |
| Ranking | Bounded candidate selection, fetched-text scoring, weightings and vetoes, expiry sweep, retry history, atomic state writes, links and reasons in the shortlist. |
| Applications | Gated posting fetch, fit-first decision, factual grounding, drafter/critique/revision stages, company cache, active templates, compilation and all PDF checks, `drafted` tracker row and posting archive, optional form fields. |
| Interview preparation | Exact application and stage selection, archive grounding, verified company research, STAR mapping, feedback, prep pack, optional mock interview, confirmed facts persisted to profile. |

Other workflows remain routed through `AGENTS.md`: `outcome`, `expand`,
`add-template`, `add-portal`, `html-report`, `reset`, `upskill`, `gmail-sync`,
and `notion-sync`. They are retained for incremental adaptation rather than
rewritten or deleted. Connected-service execution has not been tested here.

## Validation and limits

See the recorded checks below for this working copy. The migration itself does
not run profile onboarding, live job queries, apply to jobs, send email, or sync
candidate data externally. No GitHub writes, commits, or pushes are part of it.

Initial environment inspection found Python 3.13 and Bun. `lualatex`, `xelatex`,
`pdftotext`, and `pdftoppm` were not found on the current session's PATH, so
end-to-end PDF compilation and visual validation remain untested on this machine.
The sequential critique path preserves the review work but is not an independent
reviewer context or a replacement for an enforced read-only worker sandbox.

The initial Python suite ran 895 tests: 10 reset-test failures and 41 skips.
The failures invoked Windows `find.exe` from Cygwin shells (`FIND: Parameterformat
falsch`); the reset command and its tests were unchanged. Validation below records
the targeted rerun with a corrected process-local PATH separately from that run.

- Skill/command lint: passed for 14 skills and 12 commands, using PyYAML installed
  in the ignored repository `.venv` (no global Python packages changed).
- Each of the five new skills passed the skill-creator frontmatter validator.
- Security guards and framework version checks: passed.
- Core-workflow regression subset: 136 tests, passed with 9 skips for unavailable
  dependencies or environment-specific checks.
- Unchanged reset tests with Cygwin's tools first on the process-local PATH:
  16 tests, passed with 2 skips. No persistent PATH changes were made.
- Portal installer dry run: exactly the six shipped portal CLIs discovered;
  no workflow skill treated as a CLI and no installs performed by the dry run.
- Local link resolution and `git diff --check`: checked before handoff.
