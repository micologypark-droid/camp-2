# CLAUDE.md — AI Native Camp 2기 (camp-2)

This file provides guidance for AI assistants (Claude Code and others) working in this repository.

---

## Project Overview

**AI Native Camp - 2기** is an educational curriculum project that teaches Claude Code skills to non-developers (비개발자). The curriculum is delivered entirely as Claude Code Skills — meta-learning where skills teach skills.

- **Language:** Korean (content), Markdown + Python (structure)
- **Target audience:** Non-developers learning AI-native workflows
- **Format:** 4-day intensive camp (~100 minutes/day)
- **Philosophy:** Working Backward approach — start from the desired outcome, build backward

---

## Repository Structure

```
camp-2/
├── README.md                        # Installation & curriculum overview (Korean)
├── CLAUDE.md                        # This file
├── .gitignore                       # Ignores .DS_Store, .omc/
└── .claude/
    └── skills/                      # All curriculum skills
        ├── day1-onboarding/         # Day 1: Claude Code 7 core features
        │   ├── SKILL.md             # Skill entrypoint (141 lines)
        │   └── references/          # Per-block reference docs
        ├── day1-test-skill/         # Skill system verification
        │   └── SKILL.md             # Simple success-message skill (36 lines)
        ├── day2-mcp-and-context-sync/  # Day 2: MCP + building Context Sync skill
        │   ├── SKILL.md             # Main skill file (358 lines)
        │   ├── references/          # Per-block reference docs (11 files)
        │   ├── templates/
        │   │   └── context-sync.md  # Skeleton template for user's skill
        │   └── scripts/
        │       └── mcp_servers.py   # MCP server search utility (Python 3.12+)
        ├── day3-clarify/            # Day 3: Clarify plugin + GitHub PR
        │   ├── SKILL.md             # Main skill file (186 lines)
        │   ├── references/          # Per-block reference docs (5 files)
        │   └── templates/
        │       └── clarify-vague.md # Template for user's Clarify skill
        └── day4-wrap-and-analyze/   # Day 4: session-wrap + content digestion
            ├── SKILL.md             # Main skill file (208 lines)
            └── references/          # Per-block reference docs (6 files)
```

---

## Skill Architecture

### SKILL.md Structure

Each skill follows a consistent **Block → Phase A/B** pattern:

```
[Block N: Title] (~X minutes)
  Phase A: EXPLAIN — AI assistant explains the concept
  Phase B: EXECUTE — User runs commands / does hands-on work
  [QUIZ] — AskUserQuestion blocks for interactive validation
```

Key conventions inside SKILL.md files:
- **`[EXPLAIN]`** sections are delivered by the AI without user action
- **`[EXECUTE]`** sections require the user to run commands or interact with Claude Code
- **`[QUIZ]`** sections use `AskUserQuestion` for interactive multiple-choice or open-ended questions
- **`[BONUS]`** blocks are optional extensions
- **`[BREAK]`** sections are intentional pauses for configuration tasks
- Reference files are loaded inline via `[references/block-N-name.md]` directives

### Reference Files

Each `references/` directory holds per-block markdown files with detailed explanations, examples, and command references. These are loaded on-demand when the relevant block runs — they are not loaded up front.

### Templates

`templates/` directories contain skeleton files that users fill in during the session. These are the starting points for hands-on projects (e.g., building a Context Sync skill or a Clarify plugin).

---

## Curriculum Summary

| Day | Skill ID | Title | Duration | Key Outcome |
|-----|----------|-------|----------|-------------|
| 1 | `day1-onboarding` | Claude Code 7 Features | ~90 min | Understand Memory, Skill, MCP, Subagent, Agent Teams, Hook, Plugin |
| 1 | `day1-test-skill` | Skill System Verification | ~2 min | Confirm skills are installed |
| 2 | `day2-mcp-and-context-sync` | MCP & Context Sync | ~170 min | Build a 4-source Context Sync skill |
| 3 | `day3-clarify` | Clarify & GitHub | ~100 min | Build a Clarify plugin + submit first GitHub PR |
| 4 | `day4-wrap-and-analyze` | Wrap & Analyze | ~100 min | Build session-wrap skill + content digestion pipeline |

---

## Development Workflow

### Branching

- **Main branch:** `main`
- **Feature branches:** `claude/<description>-<sessionId>` (e.g., `claude/add-claude-documentation-FppLt`)
- Never push directly to `main` or `master`

### Commit Convention

Use conventional commit prefixes:
- `feat:` — new skill or block added
- `docs:` — content updates to SKILL.md or reference files
- `fix:` — bug fix in skill logic or script
- `refactor:` — restructuring without changing behavior

Example commit messages from this repo:
```
feat: Day 3 Clarify & GitHub, Day 4 Wrap & Analyze 스킬 추가
docs: Block 0 AskUserQuestion 설명 보강
docs: README 커리큘럼 테이블 업데이트
```

### Git Push

Always push with upstream tracking:
```bash
git push -u origin <branch-name>
```

### Skill Installation (end-user)

Users install all skills with:
```bash
npx skills add ai-native-camp/camp-2 --agent claude-code --yes
```

---

## Python Script

### `mcp_servers.py`

Located at `.claude/skills/day2-mcp-and-context-sync/scripts/mcp_servers.py`

- **Purpose:** Searches for MCP (Model Context Protocol) servers and generates `.mcp.json` configuration
- **Runtime:** Python 3.12+
- **Dependency:** `httpx` (must be installed separately — no requirements.txt)
- **Caching:** 1-hour TTL cache to avoid redundant network calls
- **Usage:** Called during Day 2 Block 1 to help users discover and configure MCP servers

---

## Key Conventions for AI Assistants

### Content Language

All SKILL.md content and reference files are written in **Korean**. When adding or modifying curriculum content, write in Korean unless the specific block intentionally mixes English (e.g., CLI commands, code).

### Skill File Editing

- **SKILL.md files are the curriculum source of truth.** Treat them like code — changes should be intentional and reviewed.
- Each block should remain self-contained. Avoid creating dependencies between non-sequential blocks.
- When adding a new block, follow the existing `[Block N]` → `Phase A` → `Phase B` → `[QUIZ]` template.
- Reference files should be named `block<N>-<slug>.md` and placed in the skill's `references/` directory.

### AskUserQuestion Usage

Skills use the `AskUserQuestion` tool for quizzes. When authoring quizzes:
- Provide 3–4 answer options
- Include one clearly correct answer and plausible distractors
- Follow each quiz with brief feedback on the correct answer in the next Phase A section

### No External Build System

There is no npm build, Makefile, or test runner. Validation is done interactively through the skill's quiz structure. Do not add build tooling unless explicitly requested.

### File Hygiene

- `.gitignore` tracks `.DS_Store` and `.omc/` — do not commit these
- Do not add `node_modules/`, `.env`, or credential files
- Keep the repository flat — all curriculum lives under `.claude/skills/`

---

## Remote and Git Config

- **Remote:** `origin` → `http://local_proxy@127.0.0.1:58836/git/micologypark-droid/camp-2`
- **Git user:** Claude (noreply@anthropic.com)
- **Signing:** SSH commit signing is enabled

---

## Quick Reference

| Task | Command |
|------|---------|
| Install skills (user) | `npx skills add ai-native-camp/camp-2 --agent claude-code --yes` |
| Run MCP server search | `python3 .claude/skills/day2-mcp-and-context-sync/scripts/mcp_servers.py` |
| Check current branch | `git branch --show-current` |
| Push feature branch | `git push -u origin <branch-name>` |
