# AmanPM Adoption Guide

> **A comprehensive guide for implementing AI-Native Project Management in any project**

This guide enables Claude Code (or any AI assistant) to understand and implement the AmanPM system. AmanPM is designed for AI-assisted development where humans direct and AI executes.

> **Positioning:** "AmanPM is the knowledge-first AI development framework for developers who want their AI to remember what it learned, not just what it built."

---

## Adoption Priority Legend

Throughout this guide, features are marked with their adoption priority:

| Priority | Meaning |
|----------|---------|
| 🔹 **Core** | Implement first, required for basic function |
| 🔸 **Recommended** | Implement after core works |
| ⚪ **Optional** | Nice to have, can skip initially |

---

## Table of Contents

1. [Philosophy & Foundations](#part-1-philosophy--foundations)
2. [Directory Structure](#part-2-directory-structure-setup)
3. [Configuration & Schemas](#part-3-configuration--schemas)
4. [Work Item Templates](#part-4-work-item-templates)
5. [The /aman Skill](#part-5-the-aman-skill-implementation)
6. [Memory System](#part-6-memory-system)
7. [Sprint Lifecycle](#part-7-sprint-lifecycle)
8. [Migration Strategies](#part-8-migration-strategies)
9. [Progressive Adoption](#part-9-progressive-adoption)
10. [Best Practices & Anti-Patterns](#part-10-best-practices--anti-patterns)
11. [Claude Code Integration](#part-11-claude-code-integration)
12. [Troubleshooting](#part-12-troubleshooting)
- [Appendix A: Quick Reference](#appendix-a-quick-reference-card)
- [Appendix B: Implementation Checklist](#appendix-b-implementation-checklist)
- [Appendix C: Framework Comparison](#appendix-c-framework-comparison)

---

## Part 1: Philosophy & Foundations

### The Core Problem

AI coding assistants create artifacts (code, docs, specs) faster than humans can consolidate. Traditional PM tools cannot keep pace. New patterns are needed.

### The AI-Native Solution

**Core Principle:** "If AI can't remember it, write it to a file."

AI assistants lose context between sessions. AmanPM solves this by:
- Writing decisions, learnings, and state to structured files
- Reading those files at session start
- Updating them at session end
- Creating persistent memory across sessions

### When to Use AmanPM

**Use AmanPM when:**
- Solo developer or small team (1-5 people)
- Project duration >2 weeks with >10 meaningful decisions
- Knowledge persistence matters more than automation
- Cross-model flexibility is important (Claude, GPT-5, Gemini)
- You want auditable, git-versioned project memory

**Consider alternatives when:**
- Enterprise teams needing orchestration → [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD)
- Greenfield projects with complex specs → [GitHub Spec Kit](https://github.com/github/spec-kit)
- Teams wanting maximum automation → [Auto-Claude](https://github.com/AndyMik90/Auto-Claude)
- Quick prototypes with no persistence needs → Direct prompting

### AmanPM's Unique Value

| Differentiator | Why It Matters |
|----------------|----------------|
| **Cross-model portability** | Switch Claude → GPT-5 mid-project without losing memory |
| **Git-versioned audit trail** | Time-travel debugging of decisions |
| **Learning capture semantics** | Remember *why*, not just *what* |
| **Zero infrastructure** | No database, no APIs, just files |
| **Velocity tracking built-in** | Confidence intervals for planning |
| **Session boundaries** | Explicit context handoffs |

### Human Director, AI Executor

| Role | Human | AI Agent |
|------|-------|----------|
| **Vision** | Define product direction | Elaborate into features |
| **Planning** | Review and approve | Break down into tasks |
| **Execution** | Review outputs | Implement, test, document |
| **Quality** | Final validation | Continuous verification |
| **Learning** | Strategic decisions | Pattern recognition |

### The Three-Layer Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                 AI-NATIVE PM FILE SYSTEM                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRATEGIC LAYER (Human-authored, rarely changes)          │
│  ┌────────────────────────────────────────────────────┐    │
│  │ CLAUDE.md     │ epics/active/ │ validation/        │    │
│  │ (AI Rules)    │ (What NEXT)   │ (How VERIFY)       │    │
│  └────────────────────────────────────────────────────┘    │
│              │                                              │
│              ▼                                              │
│  OPERATIONAL LAYER (.aman-pm/ - AI maintains, human reviews)│
│  ┌────────────────────────────────────────────────────┐    │
│  │  backlog/      sprints/       knowledge/           │    │
│  │  sessions/     templates/     product/             │    │
│  └────────────────────────────────────────────────────┘    │
│              │                                              │
│              ▼                                              │
│  AUTO-GENERATED LAYER (Never edit manually)                │
│  ┌────────────────────────────────────────────────────┐    │
│  │ index.yaml (State + Memory)                        │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Layer Rules:**
- **Strategic**: Human writes, rarely changes
  - `CLAUDE.md` - AI behavior rules and project context
  - `epics/active/` - High-level initiatives (replaces traditional ROADMAP.md)
  - `validation/` - Test data and acceptance criteria
- **Operational**: AI maintains, human reviews (work items, learnings)
- **Auto-Generated**: Never edit manually, regenerated by `/aman sync`

> **Note:** Earlier versions of this guide referenced standalone EXECUTION.md, ROADMAP.md, and VALIDATION.md files. The current implementation uses epics for roadmap, active sprints for execution focus, and a validation folder for test artifacts.

---

## Part 2: Directory Structure Setup

### Core Directory Tree

Create this structure in your project:

```
.aman-pm/
├── config.yaml              # PM configuration
├── index.yaml               # Auto-generated status snapshot + memory
├── README.md                # System documentation
│
├── backlog/                 # Work items (individual files)
│   ├── README.md            # Backlog conventions
│   ├── index.yaml           # Auto-generated catalog
│   ├── epics/
│   │   ├── active/          # In-progress epics
│   │   └── done/            # Completed epics
│   ├── features/
│   │   ├── active/          # Backlog/in-progress features
│   │   ├── done/            # Completed features
│   │   └── deferred/        # Strategically deferred
│   ├── tasks/
│   │   ├── active/
│   │   └── done/
│   ├── bugs/
│   │   ├── active/
│   │   └── resolved/
│   ├── spikes/
│   │   ├── active/
│   │   └── done/
│   └── debt/
│       ├── active/
│       └── resolved/
│
├── sprints/
│   ├── active/              # Current sprint (e.g., active/3/)
│   │   └── {N}/
│   │       ├── goal.md      # Sprint goal
│   │       └── items.yaml   # Sprint items
│   └── history/             # Completed sprints (immutable)
│       └── {N}/
│           ├── goal.md
│           ├── items.yaml
│           └── retrospective.md
│
├── sessions/
│   ├── README.md
│   ├── templates/
│   │   └── session-handoff.md
│   ├── active/              # Current session
│   └── archive/             # Completed sessions
│       └── sprint-{N}/
│
├── knowledge/               # Persistent memory
│   ├── learnings.md         # Technical discoveries
│   ├── learnings-archive/   # Summarized old learnings
│   ├── decisions.md         # Decision log
│   ├── patterns.md          # Observed patterns
│   └── velocity.json        # Historical velocity
│
├── templates/               # Item creation templates
│   ├── epic.yaml
│   ├── feature.yaml
│   ├── task.yaml
│   ├── bug.yaml
│   ├── spike.md
│   └── debt.md
│
├── product/                 # Feature specifications (optional)
│   └── F##-*.md
│
├── validation/              # Test data and results (optional)
└── guides/                  # PM system documentation
```

### Claude Code Skill System (Required for Automation) 🔹 **Core**

The `/aman` skill is the automation engine. Without it, AmanPM is just a folder structure. The skill provides:
- Session start/end rituals with memory surfacing
- Automatic archival and index regeneration
- Sprint lifecycle management
- Learning capture and summarization

**Directory Structure:**

```
.claude/
├── skills/
│   └── aman/
│       ├── SKILL.md              # Main dispatcher + command reference
│       ├── commands/             # Subcommand implementations
│       │   ├── start.md          # Session start (memory + status)
│       │   ├── end.md            # Session end (learnings + handoff)
│       │   ├── status.md         # Sprint + project status
│       │   ├── sync.md           # Archive done items + regenerate index
│       │   ├── backlog.md        # List/filter items
│       │   ├── item.md           # add/done/move operations
│       │   ├── sprint.md         # sprint-start/end/add
│       │   ├── groom.md          # Backlog analysis (9 checks)
│       │   └── release.md        # Version + changelog
│       └── shared/               # Reusable logic
│           ├── memory.md         # Memory surfacing/capture
│           └── sync.md           # Archive + index logic
└── settings.json                 # Hook configurations (optional)
```

**SKILL.md Frontmatter:**

```yaml
---
name: aman
description: |
  Unified AI-Native Project Management.
  Single entry point for all PM operations: sessions, sprints, backlog, releases.
---
```

**Dispatch Pattern:**

The main SKILL.md acts as a dispatcher. When user invokes `/aman <subcommand>`:

1. **No subcommand** → Execute `status` (smart default)
2. **Known subcommand** → Load `commands/<subcommand>.md` and execute
3. **Unknown subcommand** → Show help

**Command → File Mapping:**

| Subcommand | File | Shared Dependencies |
|------------|------|---------------------|
| start | commands/start.md | shared/memory.md |
| end | commands/end.md | shared/memory.md, shared/sync.md |
| status | commands/status.md | — |
| sync | commands/sync.md | — |
| backlog | commands/backlog.md | — |
| add, done, move | commands/item.md | shared/sync.md |
| sprint-start, sprint-end | commands/sprint.md | shared/sync.md |
| groom | commands/groom.md | — |
| release | commands/release.md | — |

**Implementing in a New Project:**

To implement AmanPM automation in a new project, Claude Code should:

1. Create `.claude/skills/aman/SKILL.md` with dispatch logic
2. Create each command file with specific instructions
3. Create shared files for reusable operations
4. Ensure CLAUDE.md references the workflow

See [Part 11: Claude Code Integration](#part-11-claude-code-integration) for complete implementation templates.

---

## Part 3: Configuration & Schemas

### Token Budget Guidelines

Context window management is critical for AI performance. Claude's 200K window can shrink to 70K with many tools enabled. AmanPM is designed to minimize token usage at session start.

**Cold-Start Token Budget (Target: <2500 tokens)**

| Component | Target | Notes |
|-----------|--------|-------|
| `index.yaml` snapshot | <1000 tokens (~3KB) | Essential state only |
| Recent learnings | <500 tokens | Top 5 learnings only |
| Sprint context | <500 tokens | Current sprint items |
| Session recommendations | <500 tokens | From last handoff |

**Memory Tiers** 🔹 **Core**

| Tier | What's Loaded | When |
|------|---------------|------|
| **Tier 1: Always** | `index.yaml`, current sprint, top 5 learnings | Every `/aman start` |
| **Tier 2: On-Demand** | Historical learnings, old retrospectives | When referenced |

**Context Rot Prevention**

Research shows AI performance degrades as context grows ([Factory.ai, 2025](https://factory.ai/news/context-window-problem)). AmanPM mitigates this through:
- 4-week learning archive cycle (move old to `learnings-archive/`)
- Structured summarization (not just archival)
- Selective memory surfacing (top 5, not all)

### config.yaml

Create `.aman-pm/config.yaml`:

```yaml
# AmanPM Configuration
version: "2.0"

# Sprint Configuration
sprint:
  naming_pattern: "numeric"    # Sequential numbering (1, 2, 3...)
  current: null                # No active sprint initially
  total_completed: 0           # Historical count
  length_days: 7               # Sprint duration (1 week recommended)
  start_day: monday            # Day sprints start

# Team Capacity
# Formula: team_size × hours_per_day × days_per_sprint × focus_factor
capacity:
  team_size: 1                 # Number of engineers
  hours_per_day: 6.5           # Productive hours (realistic, not 8)
  days_per_sprint: 5           # Working days per sprint
  focus_factor: 0.8            # 80% focus (meetings, interruptions)
  total_hours: 26              # Calculated capacity

# T-Shirt Sizing
estimation:
  sizing_type: "t-shirt"
  size_to_hours:
    XS: 1                      # < 1 hour (trivial)
    S: 3                       # 2-4 hours (half day)
    M: 6                       # 4-8 hours (1 day)
    L: 12                      # 8-16 hours (2 days)
    XL: 24                     # 16-32 hours (consider splitting)

# Status Workflow
statuses:
  - backlog                    # Not yet scheduled
  - ready                      # Groomed, ready for sprint
  - in_progress                # Currently being worked
  - review                     # In review/validation
  - done                       # Completed
  - archived                   # Moved to archive

# Priority Levels
priorities:
  - P0                         # Critical - blocks everything
  - P1                         # High - must complete this sprint
  - P2                         # Medium - should complete if capacity
  - P3                         # Low - nice to have

# Complexity Sizes
complexity:
  - XS
  - S
  - M
  - L
  - XL

# Work Item Types
types:
  - epic                       # Strategic initiative (human-defined)
  - feature                    # Scope-bounded deliverable
  - task                       # Atomic work unit
  - bug                        # Defect fix
  - spike                      # Research/investigation
  - debt                       # Technical debt

# ID Patterns
id_patterns:
  epic: "EPIC-{###}"
  feature: "FEAT-{###}"
  task: "TASK-{###}"
  bug: "BUG-{###}"
  spike: "SPIKE-{###}"
  debt: "DEBT-{###}"
```

### index.yaml Schema

The `index.yaml` file is auto-generated. Here's the expected structure:

```yaml
snapshot:
  generated: "2026-01-15T10:00:00Z"
  sprint: 3                    # Current sprint number
  sprint_day: 2                # Day within sprint
  version: "0.1.0"             # Project version

capacity:
  total_hours: 26
  committed_hours: 18
  utilization: "69%"

metrics:
  total_items: 45
  by_status:
    backlog: 20
    ready: 5
    in_progress: 3
    done: 17
  by_type:
    feature: 25
    task: 10
    bug: 5
    debt: 5

memory:
  needs_review: []             # Items flagged for human attention
  recent_learnings: []         # Top 5 recent learnings
  pending_decisions: []        # Decisions awaiting approval
  velocity_insights: {}        # Estimation patterns

current_sprint:
  id: 3
  goal: "Implement search feature"
  items:
    total: 5
    completed: 2
    in_progress: 1
  progress: "40%"

velocity:
  average: 8.5                 # Items per sprint
  trend: "stable"

next_actions:
  - "Complete FEAT-015"
  - "Review SPIKE-003 results"
```

---

## Part 4: Work Item Templates

### Epic Template

Create `.aman-pm/templates/epic.yaml`:

```yaml
id: "EPIC-{###}"
type: epic
title: "{Short descriptive title}"
status: backlog
priority: P1
owner: human

context: |
  {Why does this epic exist? What problem does it solve?
  What is the expected outcome? Who benefits?}

alignment:
  parent_epic: "{EPIC-### if this is a sub-epic}"
  strategic_goal: "{What strategic goal does this support}"

created: "{YYYY-MM-DD}"
target_sprint: null

children: []

success_criteria:
  - "{Measurable outcome 1}"
  - "{Measurable outcome 2}"

phase: null
sequence: null
tags: []
```

### Feature Template

Create `.aman-pm/templates/feature.yaml`:

```yaml
id: "FEAT-{###}"
type: feature
parent: "{EPIC-###}"
title: "{Short descriptive title}"
status: backlog
priority: P1
complexity: M

context: |
  {Why does this feature exist? What user problem does it solve?
  What is the expected behavior?}

spec: null

acceptance_criteria:
  - "{AC01: Specific, testable criterion}"
  - "{AC02: Specific, testable criterion}"

created: "{YYYY-MM-DD}"
created_by: human
sprint: null

children: []
blocked_by: []
blocks: []

phase: null
sequence: null
tags: []
```

### Task Template

Create `.aman-pm/templates/task.yaml`:

```yaml
id: "TASK-{###}"
type: task
parent: "{FEAT-###}"
title: "{Short action-oriented title}"
status: backlog
priority: P1
complexity: S

context: |
  {Why is this task needed? What specific action should be taken?
  What files/components are affected?}

action: |
  {Detailed description of what to implement/fix/change}

affected_files:
  - "{path/to/file}"

created: "{YYYY-MM-DD}"
created_by: ai
sprint: null

session: null
completed_at: null
completed_by: null

blocked_by: []
blocks: []

phase: null
sequence: null
tags: []
```

### Bug Template

Create `.aman-pm/templates/bug.yaml`:

```yaml
id: "BUG-{###}"
type: bug
title: "{Short description of the bug}"
status: backlog
priority: P1
complexity: M

context: |
  {What is the bug? How was it discovered?
  What is the impact? Who is affected?}

reproduction:
  steps:
    - "{Step 1}"
    - "{Step 2}"
  expected: "{What should happen}"
  actual: "{What actually happens}"

location:
  file: "{path/to/file}"
  line: null
  component: "{Component name}"

root_cause: null
fix: null

created: "{YYYY-MM-DD}"
created_by: ai
sprint: null

related_to: []
blocked_by: []
blocks: []

phase: null
sequence: null
tags: []
```

### Spike Template

Create `.aman-pm/templates/spike.md`:

```markdown
---
id: SPIKE-{###}
type: spike
status: backlog
priority: P2
complexity: M
timebox: "{1d|2d|3d|1w}"
parent: null
sprint: null
created: "{YYYY-MM-DD}"
created_by: "{human|ai}"
---

# SPIKE-{###}: {Research question}

## Research Question

{Clear question this spike aims to answer}

## Context

{Why we need to research this. What decision depends on it?}

## Scope

### In Scope
- {What will be investigated}

### Out of Scope
- {What will NOT be investigated}

## Approach

1. {Research step 1}
2. {Research step 2}

## Success Criteria

- [ ] Research question answered with evidence
- [ ] Recommendation documented
- [ ] Decision can be made based on findings

## Findings

{Fill in during/after research}

## Recommendation

{Final recommendation based on findings}
```

### Debt Template

Create `.aman-pm/templates/debt.md`:

```markdown
---
id: DEBT-{###}
type: debt
status: backlog
priority: P2
complexity: M
created: "{YYYY-MM-DD}"
created_by: "{human|ai}"
sprint: null
---

# DEBT-{###}: {Title}

## Problem

{What technical debt exists?}

## Impact

{What problems does this cause? Why fix it now?}

## Proposed Solution

{How should this be fixed?}

## Acceptance Criteria

- [ ] {Criterion 1}
- [ ] {Criterion 2}

## Affected Files

- `path/to/file`
```

### Status Workflow

```
backlog → ready → in_progress → review → done → archived

Alternative paths:
  backlog → deferred (strategic deferral)

For bugs/debt:
  → resolved (fixed)
```

---

## Part 5: The /aman Skill Implementation

### Command Adoption Priority

| Command | Priority | Description |
|---------|----------|-------------|
| `/aman start` | 🔹 **Core** | Loads context, surfaces memory |
| `/aman end` | 🔹 **Core** | Captures learnings, generates handoff |
| `/aman status` | 🔹 **Core** | Sprint + project overview |
| `/aman sync` | 🔹 **Core** | Archives done items, regenerates index |
| `/aman backlog` | 🔹 **Core** | Lists items with filters |
| `/aman add` | 🔸 **Recommended** | Creates items (can add AI-generated ACs) |
| `/aman done` | 🔹 **Core** | Marks complete + archives |
| `/aman groom` | 🔸 **Recommended** | Backlog analysis + prioritization |
| `/aman sprint-start` | 🔹 **Core** | Creates new sprint |
| `/aman sprint-end` | 🔹 **Core** | Closes sprint with ceremony |
| `/aman report` | 🔸 **Recommended** | Sprint summary |
| `/aman release` | 🔸 **Recommended** | Version + changelog |

### SKILL.md Template

Create `.claude/skills/aman/SKILL.md`:

```markdown
---
name: aman
description: |
  AI-Native Project Management.
  Single entry point for all PM operations.
---

# /aman - Unified Project Management

## The Lazy Workflow (3 Commands)

| Command | What Happens |
|---------|--------------|
| `/aman start` | Sync → Status → Memory → Recommendations |
| `/aman` | Quick status check |
| `/aman end` | Sync → Learnings → Handoff |

## All Commands

```
/aman              → status (smart default)
/aman start        → begin session
/aman end          → end session
/aman status       → sprint + project status
/aman sync         → archive done items + regenerate indexes
/aman backlog      → list items
/aman add <type>   → create item
/aman done <id>    → mark complete
/aman groom        → backlog analysis
/aman report       → sprint report
/aman sprint-start → create sprint
/aman sprint-end   → close sprint
/aman release      → create release
```

## Command Reference

| Command | Args | Description |
|---------|------|-------------|
| `/aman start` | — | Begin session with context + memory |
| `/aman end` | — | End session, capture learnings, handoff |
| `/aman status` | — | Sprint + project status |
| `/aman sync` | — | Archive done items + regenerate indexes |
| `/aman backlog` | `[filter]` | List items |
| `/aman add` | `<type> <title>` | Create item |
| `/aman done` | `<id>` | Mark complete + archive |
| `/aman sprint-start` | `["goal"]` | Start new sprint |
| `/aman sprint-end` | — | End sprint with ceremony |
| `/aman groom` | — | Backlog analysis |
| `/aman report` | — | Sprint report |
| `/aman release` | — | Create release |

## Daily Workflow

```
/aman start → [work] → /aman → [more work] → /aman end
```

## Sprint Workflow

```
/aman groom → /aman sprint-start "Goal" → daily work → /aman sprint-end
```

## Dispatch Logic

When user invokes `/aman <subcommand>`:
1. No subcommand → Execute `status`
2. Known subcommand → Execute command
3. Unknown → Show help

## Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | Status snapshot + memory |
| `.aman-pm/backlog/` | Work items |
| `.aman-pm/sprints/active/N/` | Current sprint |
| `.aman-pm/knowledge/learnings.md` | Persistent learnings |
```

### Essential Commands to Implement First

1. **`/aman start`**: Read `index.yaml`, surface memory, show status
2. **`/aman end`**: Capture learnings, sync, generate handoff
3. **`/aman status`**: Quick sprint and project overview
4. **`/aman sync`**: Move done items to done folders, regenerate `index.yaml`

---

## Part 6: Memory System

### Two-Tier Memory Architecture 🔹 **Core**

AmanPM uses a two-tier memory system to balance context richness with token efficiency:

```
┌─────────────────────────────────────────────────────────────┐
│                    TIER 1: ALWAYS LOAD                      │
│                   (Target: <1000 tokens)                    │
├─────────────────────────────────────────────────────────────┤
│  • index.yaml snapshot (sprint, metrics, next_actions)      │
│  • Top 5 recent learnings                                   │
│  • Current sprint goal + items                              │
│  • Last session's recommendations                           │
└─────────────────────────────────────────────────────────────┘
                              │
                    When referenced
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  TIER 2: ON-DEMAND                          │
│                  (Loaded when needed)                       │
├─────────────────────────────────────────────────────────────┤
│  • Historical learnings (learnings-archive/)                │
│  • Old sprint retrospectives                                │
│  • Closed decisions (>30 days)                              │
│  • Full velocity history                                    │
└─────────────────────────────────────────────────────────────┘
```

**Tier 2 Retrieval:** When you ask "didn't we try approach X before?", the AI searches `learnings-archive/` and surfaces relevant historical context.

### What Gets Remembered Where

| Information | Location | Updated When |
|-------------|----------|--------------|
| Session context | `sessions/active/` | During session |
| Sprint items | `sprints/active/N/items.yaml` | `/aman sync` |
| Learnings | `knowledge/learnings.md` | `/aman end` |
| Decisions | `knowledge/decisions.md` | When decisions made |
| Velocity | `knowledge/velocity.json` | `/aman sprint-end` |
| State snapshot | `index.yaml` | `/aman sync` |

### Learning Capture Format 🔹 **Core**

In `.aman-pm/knowledge/learnings.md`:

```markdown
# Project Learnings

## Active Learnings (Last 4 Weeks)

### 2026-W03

#### Session 2026-01-15-001 (Feature Implementation)

- **Learning**: Config files should show only overrides, not defaults
  - **Context**: Users confused by verbose default configs
  - **Implication**: Simpler configs = better DX
  - **Action**: Removed defaults from example config

- **Learning**: TDD catches integration issues early
  - **Context**: Caught API mismatch in RED phase
  - **Action**: Prioritize TDD for external integrations
```

### Session Handoff Structure 🔸 **Recommended**

> **Adoption Note:** Start with minimal handoffs (just session summary and next actions). Add the full structure below as your workflow matures.

Create `.aman-pm/sessions/templates/session-handoff.md`:

```markdown
---
session_id: YYYY-MM-DD-NNN
sprint: N
started: YYYY-MM-DDTHH:MM:SSZ
ended: YYYY-MM-DDTHH:MM:SSZ
status: completed|interrupted|blocked
---

# Session Handoff: YYYY-MM-DD-NNN

## Session Summary

**Goal:** [What this session set out to accomplish]
**Outcome:** [Completed|Partial|Blocked] - [Brief description]

## Work Completed

### Items Progressed

| ID | Item | Start Status | End Status |
|----|------|--------------|------------|
| FEAT-001 | Description | backlog | done |

### Files Modified

| File | Change Type | Description |
|------|-------------|-------------|
| `path/to/file` | Modified | Brief description |

## Discoveries & Learnings

- **[Discovery]:** [What was learned]
  - **Impact:** [How this affects the project]

## Next Session Recommendations

### Immediate Focus (Start Here)

1. **[Action]:** [What to do first]
   - Context: [Why]
   - Files: `path/to/file`

### Queue (Priority Order)

1. [Item]: [Description]
2. [Item]: [Description]

## Session Metrics

| Metric | Value |
|--------|-------|
| Items completed | 0 |
| Items progressed | 0 |
| Files modified | 0 |
```

### Memory Surfacing at Session Start 🔹 **Core**

When `/aman start` runs, surface:

1. **Recent learnings** (top 3-5 from `learnings.md`)
2. **Items flagged for review** (from `index.yaml` memory section)
3. **Current sprint status** (from `index.yaml`)
4. **Stale items** (in_progress > 3 days)
5. **Next session recommendations** (from last handoff)

---

## Part 7: Sprint Lifecycle

### 1-Week Sprint Cycle

```
Day 0 (Planning):
  /aman groom           # Analyze backlog
  /aman sprint-start    # Create sprint

Days 1-4 (Execution):
  /aman start           # Begin session
  [work]
  /aman end             # End session

Day 5 (Review):
  /aman report          # Sprint summary
  /aman sprint-end      # Close sprint
```

### Sprint Goal File

Create for each sprint at `.aman-pm/sprints/active/{N}/goal.md`:

```markdown
# Sprint {N}: {Theme}

**Dates:** YYYY-MM-DD to YYYY-MM-DD

## Goal

{1-2 sentence sprint objective}

## Success Criteria

1. {Criterion 1}
2. {Criterion 2}

## Capacity

| Metric | Value |
|--------|-------|
| Total hours | 26 |
| Committed | 20 |
| Utilization | 77% |
| Buffer | 6 hrs |

## Items

| ID | Complexity | Title |
|----|------------|-------|
| FEAT-001 | M | Feature description |
```

### Sprint Items File

Create `.aman-pm/sprints/active/{N}/items.yaml`:

```yaml
sprint: 3
goal: "Sprint goal here"
started: "2026-01-15"
ends: "2026-01-22"

items:
  - id: FEAT-001
    title: "Feature description"
    type: feature
    priority: P1
    complexity: M
    hours: 6
    status: in_progress
    sequence: 1

summary:
  total: 5
  completed: 2
  by_complexity:
    S: 2
    M: 2
    L: 1
  completion_rate: "40%"
```

### Sprint-End Incomplete Item Handling 🔸 **Recommended**

When a sprint ends with incomplete items, follow this checklist:

```
Sprint End Checklist:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. IDENTIFY incomplete items
   - Status: ready, in_progress, or review (not done)
   - Still assigned to ending sprint

2. PROMPT for disposition
   "X items not completed. For each item:
    - Carry over to next sprint?
    - Defer to backlog?
    - Close as won't-do?"

3. UPDATE item metadata
   - Carry over: sprint = next_sprint_number
   - Defer: sprint = null, status = backlog
   - Won't-do: status = archived, add reason

4. VALIDATE index.yaml
   - Completion % should match actual done items
   - No orphaned items in active/ with sprint assignment

5. DOCUMENT in retrospective
   - List items not completed
   - Note reasons for incompletion
   - Capture as learning if pattern emerges

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

> **Design Note:** Your `/aman sprint-end` implementation should prompt for incomplete items (status `ready` or `in_progress`) and ask whether to carry over, defer, or close them. This prevents orphaned items in `active/` folders.

### Velocity Tracking 🔹 **Core**

Update `.aman-pm/knowledge/velocity.json` at sprint end:

```json
{
  "meta": {
    "last_updated": "2026-01-22",
    "format_version": "1.0"
  },
  "sprints": {
    "1": {"planned": 8, "completed": 8, "velocity": 8},
    "2": {"planned": 10, "completed": 9, "velocity": 9},
    "3": {"planned": 8, "completed": 7, "velocity": 7}
  },
  "trends": {
    "average_velocity": 8,
    "trend": "stable"
  }
}
```

---

## Part 8: Migration Strategies

### Cross-Model Compatibility 🔹 **Core**

AmanPM files work across AI models without modification:

| Model | Compatible | Notes |
|-------|------------|-------|
| Claude (Opus/Sonnet) | ✅ | Full skill integration |
| GPT-4/5 | ✅ | Works via file reading |
| Gemini | ✅ | Works via file reading |
| Cursor/Windsurf | ✅ | IDE file access |

**Why Portability Matters:**
- No vendor lock-in
- Switch models mid-project without losing memory
- Use different models for different tasks (Claude for code, Gemini for reasoning)
- Future-proof against model evolution

**Structured Markdown Conventions (Model-Agnostic):**

```markdown
# Learnings Format
## YYYY-MM-DD: Title
- **Learning**: [What was learned]
- **Context**: [Why this matters]
- **Action**: [What was done / what to do]

# Decision Format (ADR-style)
## Decision: [Title]
- **Status**: [Proposed|Accepted|Deprecated]
- **Context**: [Background]
- **Decision**: [What we decided]
- **Consequences**: [Trade-offs]
```

### Scenario 1: Greenfield (New Project)

**Assessment:**
- No existing PM system
- May have TODO comments in code
- May have basic README

**Steps:**

1. **Create directory structure**
   ```bash
   mkdir -p .aman-pm/{backlog/{epics,features,tasks,bugs,spikes,debt}/{active,done,resolved,deferred},sprints/{active,history},sessions/{active,archive},knowledge,templates}
   ```

2. **Create config.yaml** (use template from Part 3)

3. **Initialize knowledge files**
   ```bash
   echo "# Project Learnings\n\n## Active Learnings" > .aman-pm/knowledge/learnings.md
   echo "# Decisions Log" > .aman-pm/knowledge/decisions.md
   echo '{"sprints": {}, "trends": {}}' > .aman-pm/knowledge/velocity.json
   ```

4. **Copy templates** (from Part 4)

5. **Extract TODOs from code**
   - Search: `grep -rn "TODO\|FIXME\|HACK" --include="*.go" --include="*.ts"`
   - Create items based on content

6. **Create first sprint**

**Mapping TODOs:**

| Pattern | Item Type |
|---------|-----------|
| `// TODO: Add feature` | Feature |
| `// FIXME: Bug when` | Bug |
| `// HACK: Temporary` | Debt |
| `// XXX: Research` | Spike |

### Scenario 2: GitHub Issues Migration

**Assessment:**
```bash
gh issue list --state open --limit 200
gh issue list --state closed --limit 100
gh label list
```

**Steps:**

1. **Export issues**
   ```bash
   gh issue list --state all --json number,title,state,labels,milestone,body > issues.json
   ```

2. **Create AmanPM structure** (same as greenfield)

3. **Map issues to items**

   | GitHub Label | AmanPM Type | Priority |
   |--------------|-------------|----------|
   | bug, defect | bug | P1 if critical |
   | enhancement, feature | feature | P2 |
   | documentation | task | P3 |
   | question, research | spike | P2 |

4. **Convert each issue**
   - Create markdown file with YAML frontmatter
   - Include GitHub reference: `github_issue: 42`
   - Preserve original description

5. **Map milestones to epics**

6. **Create cross-reference file** at `.aman-pm/knowledge/github-migration.yaml`

**Validation:**
- [ ] All open issues have AmanPM items
- [ ] Milestones mapped to epics
- [ ] `/aman status` shows correct counts

### Scenario 3: Ad-hoc Documentation Migration

**Assessment:**
```bash
find . -type f \( -name "TODO*" -o -name "ROADMAP*" -o -name "NOTES*" \)
```

**Steps:**

1. **Inventory docs**
   - TODO.md, ROADMAP.md, CHANGELOG.md
   - Any planning docs in docs/

2. **Parse TODO files**
   - `- [ ] Item` → Create item
   - `- [x] Item` → Create in done/

3. **Parse ROADMAP**
   - Version headings → Epics
   - Items under version → Features

4. **Deduplicate**
   - List all extracted items
   - Merge duplicates

5. **Archive original docs**
   ```bash
   mkdir -p docs/archive/pre-amanpm
   mv TODO.md docs/archive/pre-amanpm/
   ```

**Priority inference:**

| Indicator | Priority |
|-----------|----------|
| "urgent", "critical" | P0 |
| "important", top of list | P1 |
| "should", "would be nice" | P2 |
| "maybe", "future" | P3 |

### Scenario 4: Other PM Tools (Jira, Linear)

**Steps:**

1. **Export data**
   - Jira: Export CSV or use API
   - Linear: Settings > Export

2. **Create mapping config**
   ```yaml
   mapping:
     issue_types:
       Story: feature
       Bug: bug
       Task: task
       Epic: epic
     statuses:
       "To Do": backlog
       "In Progress": in_progress
       Done: done
     priorities:
       Highest: P0
       High: P1
       Medium: P2
       Low: P3
   ```

3. **Convert items**
   - Preserve original IDs as metadata
   - Include link to original

4. **Convert sprints**
   - Map to `.aman-pm/sprints/history/`

5. **Create cross-reference**

**Validation:**
- [ ] All active items migrated
- [ ] Sprint history preserved
- [ ] External links documented

---

## Part 9: Progressive Adoption

### Week 1: Session Rituals Only

Focus on establishing habits:

1. Create minimal structure:
   - `.aman-pm/knowledge/learnings.md`
   - `.aman-pm/sessions/`

2. Practice workflow:
   - Start each session by reading previous notes
   - End each session by capturing learnings

3. No formal items yet - just notes

### Week 2: Add Backlog Tracking

1. Create `backlog/` structure
2. Convert current work to items
3. Use simple frontmatter:
   ```yaml
   id: FEAT-001
   status: in_progress
   ```

### Week 3: Add Sprint Structure

1. Create first sprint:
   - `sprints/active/1/goal.md`
   - `sprints/active/1/items.yaml`

2. Practice:
   - `/aman status` to check progress
   - Move items through statuses

### Week 4: Full Workflow

1. Complete `config.yaml`
2. Set up velocity tracking
3. Run full sprint cycle:
   - Groom → Sprint start → Work → Sprint end

---

## Part 10: Best Practices & Anti-Patterns

### Best Practices

1. **Front-load context in files**
   - Important info at the top
   - AI reads files top-to-bottom

2. **Use pointers, not copies**
   - Reference files instead of duplicating
   - Copies go stale

3. **Capture learnings immediately**
   - Don't wait for session end
   - Context is freshest during work

4. **Never skip `/aman end`**
   - Learnings are lost
   - Next session has no context

5. **Run sync periodically**
   - Keep state clean
   - Archive done items

6. **Keep CLAUDE.md under 150 lines**
   - AI instruction-following degrades at 100-250 instructions
   - Move details to skills

### Anti-Patterns

1. **Writing TODOs in chat**
   - Vanishes next session
   - Write to files instead

2. **Skipping session start**
   - Lost context from previous work
   - Always read index first

3. **Too many rules in CLAUDE.md**
   - Degraded adherence
   - Use tiered skills

4. **Manual index editing**
   - index.yaml is auto-generated
   - Use `/aman sync`

5. **Stale memory bank**
   - Old learnings clutter context
   - Archive after 4 weeks (can automate in `/aman end`)

### Common Pitfalls

| Pitfall | Prevention |
|---------|------------|
| Incomplete sprint-end | Use mandatory ceremony |
| Velocity not tracked | Update at sprint-end |
| Items in wrong folder | Run `/aman sync` |
| Lost learnings | Capture immediately |
| Duplicate items | Run duplicate detection |

---

## Part 11: Claude Code Integration

AmanPM automation is powered by Claude Code skills. This section provides complete implementation templates so Claude Code can set up AmanPM in any new project.

### Claude Code Memory Hierarchy

```
Claude Code Memory Hierarchy          AmanPM Equivalent
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Managed policy (org-wide)         →   N/A (project-scoped)
Project memory (CLAUDE.md)        →   Points to .aman-pm/index.yaml
Project rules (.claude/rules/)    →   Type-specific rules
User memory (~/.claude)           →   Personal velocity prefs
CLAUDE.local.md                   →   Personal overrides
```

### Skill Implementation Templates

#### Main Dispatcher: SKILL.md

Create `.claude/skills/aman/SKILL.md`:

````markdown
---
name: aman
description: |
  Unified AI-Native Project Management.
  Single entry point for all PM operations: sessions, sprints, backlog, releases.
---

# /aman - Unified Project Management

## The Lazy Workflow (3 Commands)

| Command | What Happens Automatically |
|---------|---------------------------|
| `/aman start` | Sync → Status → Memory → Recommendations |
| `/aman` | Quick status check |
| `/aman end` | Sync → Learnings → Handoff |

## All Commands

```
/aman              → status (smart default)
/aman start        → begin session
/aman end          → end session
/aman status       → sprint + project status
/aman sync         → archive done items + regenerate indexes
/aman backlog      → list items
/aman add <type>   → create item
/aman done <id>    → mark complete
/aman groom        → backlog analysis
/aman sprint-start → create sprint
/aman sprint-end   → close sprint
/aman release      → create release
```

## Dispatch Logic

When user invokes `/aman <subcommand>`:

1. **No subcommand** → Execute `status` (smart default)
2. **Known subcommand** → Load `commands/<subcommand>.md` and execute
3. **Unknown subcommand** → Show help

## Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | Status snapshot + memory |
| `.aman-pm/backlog/` | Work items |
| `.aman-pm/sprints/active/N/` | Current sprint |
| `.aman-pm/knowledge/learnings.md` | Persistent learnings |
````

#### Session Start: commands/start.md

Create `.claude/skills/aman/commands/start.md`:

````markdown
# /aman start - Session Start

## Overview

Use at the beginning of each development session to load context and surface memory.

## Actions Performed

### 1. Read Index State

Read `.aman-pm/index.yaml` and extract:
- Current sprint number and day
- Sprint goal and progress
- Memory section (learnings, reviews, decisions)

### 2. Surface Memory

Display from `index.yaml` → `memory` section:

| Memory Type | Action |
|-------------|--------|
| `needs_review` | Show warning banner |
| `recent_learnings` | Show top 3-5 |
| `pending_decisions` | Show blockers |

### 3. Sprint Status

Display:
- Sprint N (Day X/7)
- Goal: {sprint goal}
- Progress: X/Y items (Z%)
- Items in_progress

### 4. Stale Work Detection

Flag items needing attention:
- in_progress > 3 days: Warning
- in_progress > 7 days: Alert
- P0 items not started: Critical

### 5. Focus Recommendation

Suggest what to work on based on:
- Priority (P0 before P1)
- Dependencies (unblocked items first)
- Sprint day (urgency increases near end)

## Output Format

```markdown
# Session Start - YYYY-MM-DD HH:MM

## Memory from Previous Sessions

### Recent Learnings
1. **Learning title** (date)
2. **Learning title** (date)

## Sprint N (Day X/7)
**Goal:** {sprint goal}
**Progress:** X/Y items (Z%)

### Currently In Progress
- [FEAT-001] Description (P1) - started X days ago

### Recommended Focus
1. Complete FEAT-001 (P1, in_progress)
2. Start TASK-002 (P0, ready)
```

## Files Read

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | Sprint status + memory |
| `.aman-pm/sessions/archive/{latest}` | Last session handoff |
````

#### Session End: commands/end.md

Create `.claude/skills/aman/commands/end.md`:

````markdown
# /aman end - Session End

## Overview

Use at the end of each development session to capture learnings and generate handoff.

## Actions Performed

### 1. Work Summary

Summarize work completed this session:
- Items moved to done
- Items started (now in_progress)
- New items created

### 2. Learning Capture

**Always prompt:**

```
## Learning Capture

Any learnings from this session to persist?

Examples:
- Technical discoveries
- Patterns that work/don't work
- Things to remember

Enter learnings (one per line, or 'none'):
```

**Save to:** `.aman-pm/knowledge/learnings.md`

### 3. Sync

Run sync to:
- Move done items to done/ folders
- Regenerate index.yaml
- Update metrics

### 4. Generate Handoff

Create handoff at `.aman-pm/sessions/archive/sprint-N/YYYY-MM-DD-NNN.md`:

```markdown
---
session_id: YYYY-MM-DD-NNN
sprint: N
started: YYYY-MM-DDTHH:MM:SSZ
ended: YYYY-MM-DDTHH:MM:SSZ
---

# Session Handoff

## Session Summary
**Goal:** What this session accomplished
**Outcome:** Completed/Partial/Blocked

## Work Completed
| ID | Item | Status |
|----|------|--------|

## Learnings Captured
- Learning 1
- Learning 2

## Next Session Recommendations
1. First priority
2. Second priority
```

### 5. Git Status Check

- Check for uncommitted changes
- Prompt for commit if needed

## Files Updated

| File | Update |
|------|--------|
| `.aman-pm/knowledge/learnings.md` | Append learnings |
| `.aman-pm/index.yaml` | Regenerated |
| `.aman-pm/sessions/archive/` | New handoff file |
````

#### Sync: commands/sync.md

Create `.claude/skills/aman/commands/sync.md`:

````markdown
# /aman sync - Archive and Regenerate

## Overview

Archives done items and regenerates index.yaml. Run periodically to keep state clean.

## Actions Performed

### 1. Archive Done Items

For each item type (features, tasks, bugs, debt, spikes):
- Scan `active/` folder for items with `status: done`
- Move to `done/` folder (or `resolved/` for bugs/debt)
- Preserve file content

### 2. Regenerate index.yaml

Read all backlog files and generate fresh index:

```yaml
snapshot:
  generated: "YYYY-MM-DDTHH:MM:SSZ"
  sprint: N
  sprint_day: X
  version: "X.Y.Z"

capacity:
  total_hours: 26
  committed_hours: N
  utilization: "X%"

metrics:
  total_items: N
  by_status:
    backlog: N
    ready: N
    in_progress: N
    done: N
  by_type:
    feature: N
    task: N
    bug: N

memory:
  needs_review: []
  recent_learnings: []
  pending_decisions: []

current_sprint:
  id: N
  goal: "Sprint goal"
  items:
    total: N
    completed: N
  progress: "X%"

next_actions:
  - "Action 1"
  - "Action 2"
```

### 3. Update Backlog Index

Regenerate `.aman-pm/backlog/index.yaml` with item catalog.

## Output

```
Sync complete:
- Archived: 2 items
- Index regenerated
- Sprint 3: 5/8 items (62%)
```
````

#### Shared Memory Logic: shared/memory.md

Create `.claude/skills/aman/shared/memory.md`:

````markdown
# Shared: Memory Operations

## Reading Memory

Read `.aman-pm/index.yaml` → `memory` section:

```yaml
memory:
  needs_review:
    - id: FEAT-001
      reason: "May be obsolete"
      flagged: "2026-01-15"
  recent_learnings:
    - "Learning 1 (2026-01-15)"
    - "Learning 2 (2026-01-14)"
  pending_decisions:
    - "Decision awaiting approval"
```

## Writing Learnings

Append to `.aman-pm/knowledge/learnings.md`:

```markdown
### YYYY-MM-DD

- **Learning**: {what was learned}
  - **Context**: {why this matters}
  - **Action**: {what was done / what to do}
```

## Updating Memory Index

After writing learnings, update `index.yaml` → `memory.recent_learnings`:
- Keep only top 5 most recent
- Format: "{learning} (YYYY-MM-DD)"

## Flagging Items for Review

Add to `index.yaml` → `memory.needs_review`:

```yaml
- id: FEAT-001
  reason: "User-provided reason"
  flagged: "YYYY-MM-DD"
```
````

### CLAUDE.md Integration

Add to your project's `CLAUDE.md`:

```markdown
## AI-Native PM

**Session Workflow:** `/aman start` → work → `/aman end`

**SSOT:** `.aman-pm/index.yaml` (use `/aman start` to load)

**Key Rules:**
- Never edit index.yaml directly (use /aman sync)
- Capture learnings at session end
- Run /aman sync after completing items
```

### Path-Specific Rules

Create `.claude/rules/aman-pm.md`:

```markdown
---
paths:
  - ".aman-pm/**/*"
---

# AmanPM File Rules

When modifying .aman-pm/ files:
- Never edit index.yaml directly (use /aman sync)
- Always update frontmatter dates when editing items
- Validate YAML syntax before saving
- Use status values: backlog, ready, in_progress, review, done
```

### Bootstrap Script

To set up AmanPM in a new project, Claude Code should:

```bash
# 1. Create directory structure
mkdir -p .aman-pm/{backlog/{epics,features,tasks,bugs,spikes,debt}/{active,done,resolved,deferred},sprints/{active,history},sessions/{active,archive},knowledge,templates,validation}

# 2. Create skill structure
mkdir -p .claude/skills/aman/{commands,shared}

# 3. Initialize knowledge files
cat > .aman-pm/knowledge/learnings.md << 'EOF'
# Project Learnings

## Active Learnings (Last 4 Weeks)

EOF

# 4. Create config.yaml (see Part 3)

# 5. Create skill files (see templates above)

# 6. Update CLAUDE.md with PM section
```

### Implementation Checklist for New Projects

When implementing AmanPM in a new project:

- [ ] Create `.aman-pm/` directory structure
- [ ] Create `.aman-pm/config.yaml` with capacity settings
- [ ] Create `.aman-pm/knowledge/learnings.md`
- [ ] Create `.claude/skills/aman/SKILL.md` (dispatcher)
- [ ] Create `.claude/skills/aman/commands/start.md`
- [ ] Create `.claude/skills/aman/commands/end.md`
- [ ] Create `.claude/skills/aman/commands/sync.md`
- [ ] Create `.claude/skills/aman/commands/status.md`
- [ ] Create `.claude/skills/aman/shared/memory.md`
- [ ] Add PM section to CLAUDE.md
- [ ] Create first epic or feature item
- [ ] Run `/aman start` to verify setup

---

## Part 12: Troubleshooting

### Common Issues

**1. `/aman start` shows stale data**

| Symptom | Cause | Fix |
|---------|-------|-----|
| Old sprint shown | `index.yaml` not regenerated | Run `/aman sync` |
| Missing learnings | Not captured at session end | Check `learnings.md` manually |
| Wrong item counts | Items in wrong folders | Verify folder structure |

**2. Items orphaned after sprint-end**

```
Problem: Items with status 'ready' remain in active/ after sprint closes
Cause: /aman sprint-end only moves 'done' items

Fix:
1. List orphaned items: ls .aman-pm/backlog/*/active/*.yaml
2. For each orphan, either:
   - Reset sprint: null in frontmatter
   - Move to done/ if actually complete
3. Run /aman sync to regenerate index
```

**3. Learning capture not working**

| Symptom | Cause | Fix |
|---------|-------|-----|
| No prompt for learnings | `/aman end` not run | Always run before closing |
| Learnings not in index | Not in top 5 recent | Check `learnings.md` directly |
| Duplicate learnings | Multiple session ends | Deduplicate manually |

**4. Memory surfacing incomplete**

```
Checklist:
- [ ] index.yaml exists and is valid YAML
- [ ] memory section present in index.yaml
- [ ] recent_learnings array populated
- [ ] needs_review array checked
```

**5. Velocity tracking not updating**

| Symptom | Cause | Fix |
|---------|-------|-----|
| velocity.json empty | No `/aman sprint-end` run | Complete sprint ceremony |
| Trend always "stable" | Insufficient data | Need 3+ sprints |
| Wrong counts | Items not marked done | Verify item statuses |

### Recovery Procedures

**Corrupted index.yaml**

```bash
# Option 1: Regenerate
rm .aman-pm/index.yaml
/aman sync

# Option 2: Restore from git
git checkout HEAD~1 -- .aman-pm/index.yaml
/aman sync
```

**Lost learnings**

```bash
# Check git history for learnings.md
git log --oneline -20 -- .aman-pm/knowledge/learnings.md
git show <commit>:.aman-pm/knowledge/learnings.md
```

**Duplicate items**

```bash
# Find duplicates by title
grep -h "^title:" .aman-pm/backlog/**/active/*.yaml | sort | uniq -d
```

### Performance Issues

**Slow `/aman start`**

| Cause | Fix |
|-------|-----|
| Large index.yaml (>25KB) | Reduce to <3KB; archive old data |
| Too many active items | Move stale items to backlog |
| Many learnings in Tier 1 | Archive learnings >4 weeks old |

**Context window exhaustion**

```
Symptoms:
- Claude forgets earlier context
- Instructions not followed
- Repeated questions about known items

Fixes:
1. Run /clear between major tasks
2. Keep CLAUDE.md under 150 lines
3. Archive old learnings (4+ weeks)
4. Use /aman status for quick checks (not full /aman start)
```

### Validation Commands

```bash
# Verify YAML syntax
python -c "import yaml; yaml.safe_load(open('.aman-pm/index.yaml'))"

# Count items by status
grep -r "^status:" .aman-pm/backlog/ | sort | uniq -c

# Find items without sprint
grep -L "^sprint:" .aman-pm/backlog/*/active/*.yaml

# Check for orphaned sprint assignments
grep -r "sprint: [0-9]" .aman-pm/backlog/*/active/*.yaml
```

---

## Appendix A: Quick Reference Card

### Essential Commands

```
/aman start        Begin session
/aman              Check status
/aman end          End session

/aman add feature "Title"   Create feature
/aman done FEAT-001         Mark complete

/aman groom                 Analyze backlog
/aman sprint-start "Goal"   Start sprint
/aman sprint-end            End sprint
```

### Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | State + memory (auto-generated) |
| `.aman-pm/config.yaml` | Configuration |
| `.aman-pm/knowledge/learnings.md` | Persistent learnings |
| `.aman-pm/sprints/active/N/` | Current sprint |
| `.aman-pm/backlog/` | All work items |

### Daily Workflow

```
/aman start → work → /aman end
```

### Sprint Workflow

```
/aman groom → /aman sprint-start → daily sessions → /aman sprint-end
```

### Status Flow

```
backlog → ready → in_progress → review → done
```

---

## Appendix B: Implementation Checklist

### Minimum Viable Implementation

- [ ] Create `.aman-pm/` directory
- [ ] Create `config.yaml` with basic settings
- [ ] Create `knowledge/learnings.md`
- [ ] Create `backlog/` with type subdirectories
- [ ] Create at least one work item
- [ ] `/aman start` can read state
- [ ] `/aman end` can capture learnings

### Full Implementation

- [ ] All directory structures in place
- [ ] All templates created
- [ ] `index.yaml` auto-generation working
- [ ] Sprint tracking functional
- [ ] Velocity tracking functional
- [ ] Session handoffs working
- [ ] `/aman sync` archiving done items
- [ ] Learning summarization (4+ weeks) ⚪
- [ ] Sprint-end orphan handling ⚪
- [ ] AC auto-generation ⚪

### Integration Checklist

- [ ] CLAUDE.md references AmanPM workflow
- [ ] Skills registered (if using Claude Code)
- [ ] @imports configured for key files ⚪
- [ ] Path-specific rules in `.claude/rules/` ⚪
- [ ] Session hooks configured ⚪
- [ ] Team onboarded to workflow

---

## Appendix C: Framework Comparison

### AI Development Framework Landscape (2025-2026)

| Dimension | AmanPM | BMAD Method | GitHub Spec Kit | Auto-Claude |
|-----------|--------|-------------|-----------------|-------------|
| **Philosophy** | "If AI can't remember, write to file" | Multi-agent agile team | Spec is source of truth | Autonomous agents |
| **Memory Approach** | File-based, git-versioned | Workspace integration | Markdown artifacts | Graph database (FalkorDB) |
| **Agent Model** | Single agent with skills | 21+ specialized agents | Tool-agnostic CLI | Multi-agent with QA |
| **Workflow** | Session → Sprint → Release | Plan → Architect → Implement | Specify → Plan → Tasks | Create → Code → QA loop |
| **Human Control** | High | High | Medium | Low |
| **Target User** | Solo/small teams | Enterprise teams | Individual devs | Teams wanting automation |
| **Learning Capture** | ✅ Explicit | ⚠️ Implicit | ❌ Not addressed | ⚠️ Graph-based |
| **Cross-Model** | ✅ Claude, GPT-5, Gemini | ⚠️ IDE-specific | ✅ 16+ agents | ❌ Claude-only |
| **Velocity Tracking** | ✅ Built-in | ❌ None | ❌ None | ❌ None |
| **Zero Infrastructure** | ✅ Yes | ✅ Yes | ✅ Yes | ❌ Needs FalkorDB |

### When to Choose Each

```
                     Project Size & Complexity
                              ↑
                    BMAD      │      Auto-Claude
                 (Enterprise) │     (Automation)
                              │
                              │       AmanPM
                              │   (Knowledge Focus)
                              │
                  Spec Kit    │      Cursor/IDE
                (Greenfield)  │       (Ad-hoc)
                              └────────────────────→
                          Human Control    Automation
```

### Research References

- [Letta Research (2025)](https://www.letta.com/blog/benchmarking-ai-agent-memory): File-based memory achieves 74% accuracy on LoCoMo benchmark
- [Factory.ai (2025)](https://factory.ai/news/context-window-problem): Context rot degrades performance as input grows
- [Scott Logic (2025)](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html): Spec Kit 10x slower than iterative prompting in practice

---

## Summary

AmanPM solves the AI memory problem through structured file-based persistence. The key principles:

1. **"If AI can't remember it, write it to a file"**
2. **Human Director, AI Executor**
3. **Three-layer architecture** (Strategic → Operational → Auto-generated)
4. **Two-tier memory loading** (Always-load + On-demand)
5. **Session discipline** (explicit start and end rituals)
6. **Token budget awareness** (target <2500 tokens cold-start)
7. **Cross-model portability** (works with Claude, GPT-5, Gemini)
8. **Learnings persist forever** (captured, summarized, archived)

Start with session rituals, add backlog tracking, then sprints. Full adoption takes ~4 weeks.

### Unique Value Proposition

> "AmanPM is the knowledge-first AI development framework for developers who want their AI to remember what it learned, not just what it built."

**Key Differentiators:**
- Only framework with explicit learning capture
- Git-versioned audit trail without infrastructure
- Cross-model portable (no vendor lock-in)
- Velocity tracking for planning confidence

---

*Version 2.0.0 | Updated with research-backed improvements | 2026-02-01*

**Changelog v2.0.0:**
- Added adoption priority indicators throughout (🔹 Core, 🔸 Recommended, ⚪ Optional)
- Added token budget guidelines (Part 3)
- Added two-tier memory architecture (Part 6)
- Added sprint-end incomplete handling (Part 7)
- Added cross-model compatibility notes (Part 8)
- Added Claude Code integration guide with full skill templates (Part 11)
- Added troubleshooting guide (Part 12)
- Added framework comparison (Appendix C)
- Updated positioning and competitive analysis
- Fixed strategic layer diagram to match reality (epics, CLAUDE.md, validation/ instead of standalone EXECUTION.md/ROADMAP.md/VALIDATION.md)
- Expanded skill system documentation from "Optional" to "Required for Automation" with complete implementation templates (Part 2, Part 11)
