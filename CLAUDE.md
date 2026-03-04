# CLAUDE.md — AI Native Camp 2기 (camp-2)

This file provides guidance for AI assistants working in this repository.

---

## Repository Overview

**AI Native Camp 2기** is a 7-day intensive curriculum teaching Claude Code to non-developers. It is distributed as a Claude Code skills package — learners install it with:

```bash
npx skills add ai-native-camp/camp-2 --agent claude-code --yes
```

Each "day" is a self-contained **Skill** (a slash command) that Claude executes step-by-step with the learner.

---

## Directory Structure

```
camp-2/
├── README.md                          # Curriculum overview + install instructions
├── CLAUDE.md                          # This file
├── .gitignore
└── .claude/
    └── skills/
        ├── day1-onboarding/           # Day 1: Claude Code 7대 기능 체험
        │   ├── SKILL.md               # Orchestrator — defines block order + STOP PROTOCOL
        │   └── references/            # Teaching blocks (block0-setup.md … block4-basics.md)
        ├── day1-test-skill/           # Sanity-check skill (verify skill system works)
        │   └── SKILL.md
        ├── day2-mcp-and-context-sync/ # Day 2: MCP + 나만의 Context Sync 스킬
        │   ├── SKILL.md
        │   ├── references/            # block0-concept.md … block10-finalize.md
        │   ├── templates/
        │   │   └── context-sync.md    # Skeleton with STUB placeholders
        │   └── scripts/
        │       └── mcp_servers.py     # GitHub MCP registry search tool (Python 3.12+)
        ├── day3-clarify/              # Day 3: Clarify + 나만의 스킬 + GitHub PR
        │   ├── SKILL.md
        │   ├── references/            # block0-concept.md … block4-prd-and-github.md
        │   └── templates/
        │       └── clarify-vague.md   # Template for building user's own Clarify skill
        └── day4-wrap-and-analyze/     # Day 4: Session Wrap 스킬 + 세션 분석 + 콘텐츠 소화
            ├── SKILL.md
            └── references/            # block0-concept.md … block5-content-experience.md
```

---

## Skill Architecture

### Frontmatter (YAML)

Every `SKILL.md` starts with:

```yaml
---
name: day1-onboarding
description: One-line description used for trigger matching
triggers:
  - "1일차"
  - "Day 1"
  - "온보딩"
---
```

### Block Reference Files

Each block is a Markdown file under `references/` with exactly three sections:

```
## EXPLAIN
> 공식 문서: <URL>
(Concept explanation with analogies, ASCII diagrams, tables)

## EXECUTE
(Step-by-step hands-on instructions for the learner)

## QUIZ
(Assessment question(s) to verify understanding)
```

### Templates

Templates under `templates/` are user-customizable skeletons. They use:
- `<!-- CUSTOMIZE -->` HTML comments to mark customization points
- `STUB` placeholders that learners progressively replace during exercises

---

## Core Teaching Protocol — STOP PROTOCOL

**Every block follows a strict 2-phase structure. Never deviate from this.**

### Phase A — Teach
1. Read the block's `## EXPLAIN` section → summarize concept for learner
2. Output the official documentation URL (marked `📖` or `> 공식 문서:`)
3. Read the block's `## EXECUTE` section → instruct the learner to perform the action
4. **STOP. Wait for the learner to report back. Do NOT proceed.**

### Phase B — Quiz
1. Read the block's `## QUIZ` section → present the question
2. Evaluate the learner's answer and give feedback
3. Ask: "다음 블록으로 넘어갈까요?" (Ready for the next block?)

### Forbidden Actions in Phase A
- Never ask quiz questions during Phase A
- Never call `AskUserQuestion` in Phase A (exceptions are explicitly documented in SKILL.md)
- Never skip the STOP at the end of Phase A
- Never merge Phase A and Phase B into one turn

---

## Key Concepts & Terminology

| Term | Definition |
|---|---|
| **Skill** | A slash-command recipe Claude executes; loaded on-demand (progressive disclosure) |
| **MCP** | Model Context Protocol — standard interface connecting Claude to external tools (USB-C analogy) |
| **Subagent** | Claude spawning another Claude instance to do parallel work |
| **Plugin** | Packaged bundle of [Skill + MCP + Hook + Agent] |
| **Clarify** | Transforming vague requirements into specific specs using `AskUserQuestion` |
| **Context Sync** | Aggregating Slack + Notion + Linear + Google into one document |
| **Session Wrap** | Structured session summary generated at session end |
| **STUB** | Placeholder in a template; replaced progressively during exercises |
| **STOP PROTOCOL** | Strict 2-phase (Teach → Quiz) pedagogy; never skip the stop |
| **Quiz-First** | Show quiz before summary; improves retention by 9–12% |

---

## Curriculum Map

| Day | Skill name | Duration | Topics |
|---|---|---|---|
| 1 | `day1-onboarding` | ~5h | Claude Code 7대 기능 체험 (Memory, Skill, MCP, Subagent, Agent Teams, Hook, Plugin) |
| 2 | `day2-mcp-and-context-sync` | ~3h | MCP 심화 + 나만의 Context Sync 스킬 제작 |
| 3 | `day3-clarify` | ~1.5h | Clarify 기법 + 나만의 스킬 제작 + GitHub PR 제출 |
| 4 | `day4-wrap-and-analyze` | ~1.5h | Session Wrap 스킬 제작 + 세션 분석 + 콘텐츠 소화 파이프라인 |

---

## Naming Conventions

- **Skill directories**: `day{N}-{topic}` (lowercase, hyphenated) — e.g., `day2-mcp-and-context-sync`
- **User-created skills**: prefix with `my-` — e.g., `my-context-sync`, `my-clarify`
- **Reference blocks**: `block{N}-{short-topic}.md` — e.g., `block3-break.md`, `block10-finalize.md`
- **Commits**: Use `feat:` / `docs:` / `fix:` prefixes with Korean descriptions matching the curriculum tone

---

## Development Workflows

### Adding a New Day / Skill

1. Create `.claude/skills/day{N}-{topic}/`
2. Write `SKILL.md` with YAML frontmatter + orchestration protocol
3. Create `references/block{N}-{topic}.md` files — each must have `## EXPLAIN`, `## EXECUTE`, `## QUIZ`
4. (Optional) Add `templates/` and `scripts/` as needed
5. Update `README.md` curriculum table

### Editing an Existing Block

- Keep the three-section structure (`## EXPLAIN` / `## EXECUTE` / `## QUIZ`) intact
- Always include the `> 공식 문서:` URL in `## EXPLAIN`
- If adding a new STOP PROTOCOL exception, document it explicitly in `SKILL.md`

### Scripts

`mcp_servers.py` (Day 2) — search GitHub's MCP server registry:

```bash
# Requires: Python 3.12+, httpx (auto-installed via PEP 723)
python mcp_servers.py <keyword>
```

- Caches the GitHub MCP README for 1 hour
- Falls back to cache on network failure

---

## Content Language & Style

- **Primary language**: Korean (한국어) — target audience is Korean non-developers
- Keep English for proper nouns: MCP, API, USB-C, Claude Code, GitHub, Slack, Notion, Linear, etc.
- Tone: conversational and encouraging, not academic
- Use analogies (USB-C for MCP, building phases for STUB progression)
- Prefer ASCII diagrams and tables over prose for architecture explanations

---

## What NOT to Do

- Do not skip or merge STOP PROTOCOL phases — this breaks the pedagogy
- Do not add content to blocks beyond the three required sections without updating the SKILL.md orchestrator
- Do not rename skill directories without updating `README.md` and any cross-references in other SKILL.md files
- Do not commit large binary files — only Markdown, Python scripts, and YAML frontmatter belong here
- Do not auto-advance through blocks without the learner's explicit confirmation
