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
   - [Premium Engineering Mindset](#premium-engineering-mindset--core)
   - [Two-Layer Knowledge Architecture](#two-layer-knowledge-architecture--core)
   - [SSOT Hierarchy & Conflict Resolution](#ssot-hierarchy--conflict-resolution--core)
2. [Directory Structure](#part-2-directory-structure-setup)
3. [Configuration & Schemas](#part-3-configuration--schemas)
4. [Work Item Templates](#part-4-work-item-templates)
   - [Definition of Ready (DoR)](#definition-of-ready-dor--core)
   - [Definition of Done (DoD)](#definition-of-done-dod--core)
   - [Engineering Workflow Contract](#engineering-workflow-contract--core)
5. [The /aman Skill](#part-5-the-aman-skill-implementation)
   - [WSJF Prioritization](#wsjf-prioritization--recommended)
6. [Memory System](#part-6-memory-system)
   - [Three-Tier Memory Architecture](#three-tier-memory-architecture--core)
   - [Memory Bank (Hot Memory)](#memory-bank-hot-memory--core)
7. [Sprint Lifecycle](#part-7-sprint-lifecycle)
   - [Parallel Development Streams](#parallel-development-streams--recommended)
   - [Mid-Sprint Change Management](#mid-sprint-change-management--core)
   - [Rearchitecture Workflow](#rearchitecture-workflow--recommended)
8. [Migration Strategies](#part-8-migration-strategies)
9. [Progressive Adoption](#part-9-progressive-adoption)
10. [Best Practices & Anti-Patterns](#part-10-best-practices--anti-patterns)
    - [Do It Now Philosophy](#do-it-now-philosophy--core)
    - [The Checklist Manifesto](#the-checklist-manifesto--core)
    - [Black-Box Design](#black-box-design-eskil-steenberg--recommended)
    - [Unix Philosophy](#unix-philosophy-17-rules--recommended)
    - [AI-Native Patterns](#ai-native-patterns--recommended)
    - [Knowledge Lifecycle](#knowledge-lifecycle-create--curate--retire--recommended)
    - [Search-Fix-Learn Debugging](#search-fix-learn-debugging--recommended)
    - [Interface-First Specification](#interface-first-specification--recommended)
    - [Performance Budgets](#performance-budgets--recommended)
    - [Graceful Degradation](#graceful-degradation--optional)
    - [Unix Philosophy Verification](#unix-philosophy-verification--recommended)
    - [The 90/9/1 Rule](#the-9091-rule--optional)
11. [Claude Code Integration](#part-11-claude-code-integration)
12. [Troubleshooting](#part-12-troubleshooting)
- [Appendix A: Quick Reference](#appendix-a-quick-reference-card)
- [Appendix B: Implementation Checklist](#appendix-b-implementation-checklist)
- [Appendix C: Framework Comparison](#appendix-c-framework-comparison)

---

## Part 1: Philosophy & Foundations

### The Core Problem

AI coding assistants create artifacts (code, docs, specs) faster than humans can consolidate. Traditional PM tools cannot keep pace. New patterns are needed.

### Premium Engineering Mindset 🔹 **Core**

**Philosophy:** "We don't ship 'good enough'. We ship excellent."

AmanPM is more than a PM system—it's a discipline multiplier. Premium Engineering means:

| Principle | What It Looks Like |
|-----------|-------------------|
| **Pride in Every Line** | Code you'd confidently show to any senior engineer |
| **Right the First Time** | Quality is the starting point, not the goal |
| **Build for the Reader** | Every line will be read 100x more than written |
| **Proactive Excellence** | Prevent problems, don't just fix them |
| **Zero Tolerance for Shortcuts** | CI failures, lint warnings, test gaps = immediate action |

**The Premium Test:** Before marking anything "done", ask: *"Would I stake my reputation on this?"*

This mindset drives all AmanPM practices:
- **TDD** catches bugs before they exist
- **Specs** prevent wasted work
- **Quality gates** ensure no surprises
- **"Do It Now"** eliminates debt accumulation

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

### Primary Workflow Model

AmanPM is designed for a specific workflow:

| Role | Tool | Function |
|------|------|----------|
| **Executor** | Claude Code | Runs `/aman` commands, maintains PM state |
| **Reviewers** | GPT, Gemini, etc. | Read `.aman-pm/` files to review decisions, plans, learnings |

This means:
- Claude Code skills (`.claude/skills/aman/`) power all automation
- Other AI models don't need special tooling—they read Markdown/YAML directly
- The file formats are model-agnostic; the automation is Claude-native

> **Why this works:** The value of cross-model compatibility is in *reviewing* project state, not executing PM commands. Any model can read structured files. Only your primary assistant needs the automation layer.

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

### Two-Layer Knowledge Architecture 🔹 **Core**

AmanPM separates knowledge into two layers for optimal AI performance:

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: CONSTITUTION (CLAUDE.md)                         │
│  Always loaded, high-signal, critical rules                │
├─────────────────────────────────────────────────────────────┤
│  • Project identity and current state                      │
│  • Non-negotiable workflow rules                           │
│  • SSOT hierarchy and conflict resolution                  │
│  • Key file locations                                      │
│  • DO NOT artificially limit length—focus on high-signal   │
└─────────────────────────────────────────────────────────────┘
                              │
                    Retrieves on demand
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: DISCOVERY (Skills, Guides, Knowledge)            │
│  Loaded when needed, detailed procedures                   │
├─────────────────────────────────────────────────────────────┤
│  • .claude/skills/ - Domain expertise on demand            │
│  • .aman-pm/memory-bank/ - Constraints and decisions       │
│  • .aman-pm/knowledge/ - Historical learnings              │
│  • Validation guides, templates, procedures                │
└─────────────────────────────────────────────────────────────┘
```

**Key Principle:** Layer 2 is ADDITIVE to Layer 1, not a replacement. High-value instructions stay in CLAUDE.md; detailed procedures go to skills/guides.

**When to Use RAG:** For projects with documentation exceeding ~100K tokens, consider local RAG tools (e.g., `mcp-local-rag`) to make Layer 2 searchable.

### The Three-Layer Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                 AI-NATIVE PM FILE SYSTEM                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRATEGIC LAYER (Human-authored, rarely changes)          │
│  ┌────────────────────────────────────────────────────┐    │
│  │ CLAUDE.md              │ validation/               │    │
│  │ (AI Rules + Context)   │ (Test Data + Criteria)    │    │
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
  - `validation/` - Test data and acceptance criteria
- **Operational**: AI maintains, human reviews (work items, learnings)
  - Includes `backlog/epics/` - stored here for consistency, but **human-owned** (AI proposes, human approves)
- **Auto-Generated**: Never edit manually, regenerated by `/aman sync`

> **Note on Epics:** Epics live in `.aman-pm/backlog/epics/` (operational layer by location) but are **strategically owned** by humans. AI can draft epics, but humans must approve before they become active. This balances organizational consistency with strategic control.

> **Note:** Earlier versions of this guide referenced standalone EXECUTION.md, ROADMAP.md, and VALIDATION.md files. The current implementation uses epics for roadmap, active sprints for execution focus, and a validation folder for test artifacts.

### System Invariants 🔹 **Core**

These rules are **non-negotiable**. Violating them breaks the system.

| Invariant | Meaning |
|-----------|---------|
| **Backlog Files Are Canonical** | Individual work item files (`.yaml`, `.md`) are the source of truth |
| **Index Is Derived** | `index.yaml` is a regenerable cache/snapshot—never edit manually |
| **AI Never Edits Strategy** | Epics, CLAUDE.md, validation/ are human-owned |
| **Memory Must Be Explicit** | If not written to a file, it is lost |

> **SSOT Clarification:** The backlog files (`backlog/**/active/*.yaml`) are the canonical source of truth. `index.yaml` is a derived snapshot regenerated by `/aman sync`. If they conflict, the backlog files win—regenerate the index.

**Corollary:** When in doubt, write to a file.

**Enforcement:**
- `/aman sync` regenerates `index.yaml`—never edit manually
- Strategic files require human approval before AI modification
- Session end ritual is the memory checkpoint—skip it, lose learnings

### SSOT Hierarchy & Conflict Resolution 🔹 **Core**

When files contain conflicting information, this hierarchy determines truth:

| Information | Authoritative Source | Other Files |
|-------------|---------------------|-------------|
| Work item details | `backlog/**/active/*.yaml` | `index.yaml` displays, doesn't own |
| Sprint state | `sprints/active/N/items.yaml` | `index.yaml` is snapshot |
| Project config | `config.yaml` | Nothing else |
| Feature status | Work item files | `index.yaml` counts are derived |
| Learnings | `knowledge/learnings.md` | `index.yaml` shows top 5 |

**Conflict Resolution Rules:**

1. **Status conflict:** Backlog files win. Regenerate `index.yaml` via `/aman sync`.
2. **Count mismatch:** Run `/aman sync` to regenerate from source files.
3. **Version conflict:** `config.yaml` wins. Update displays to match.

> **Rule:** `index.yaml` is ALWAYS derived. If it conflicts with source files, **regenerate it, don't edit it**.

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
├── memory-bank/             # Hot memory (always loaded)
│   ├── architectural-constraints.md  # Core project constraints
│   ├── active-decisions.md  # Pending decisions
│   └── session-context.md   # Current session state
│
├── knowledge/               # Cold storage (on-demand)
│   ├── learnings.md         # Technical discoveries
│   ├── learnings-archive/   # Summarized old learnings
│   ├── decisions.md         # Decision log
│   ├── patterns.md          # Observed patterns
│   └── velocity.json        # Historical velocity
│
├── rearchitecture/          # Architecture change proposals
│   ├── active/              # In-progress proposals
│   └── completed/           # Completed rearchitectures
│
├── templates/               # Item creation templates
│   ├── epic.yaml
│   ├── feature.yaml
│   ├── task.yaml
│   ├── bug.yaml
│   ├── spike.md
│   └── debt.md
│
├── checklists/                 # Layer 1 killer items (5-9 items each)
│   ├── index.md                # Registry with triggers & taxonomy
│   ├── CL-IMPL-DC-PreCommit.md     # Before git commit (7 items)
│   ├── CL-IMPL-DC-FeatureComplete.md  # Feature complete gate (9 items)
│   └── CL-*.md                 # Domain-specific checklists
│
├── product/                 # Feature specifications
│   └── F##-*.md
│
├── validation/              # Test data and results
└── guides/                  # PM system documentation (incl. Layer 2 quality-checklist.md)
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
└── settings.json                 # Hook configurations
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

Context window management is critical for AI performance. While models advertise large context windows (100K-200K tokens), the *effective* budget is reduced by system prompts, tool definitions, and conversation history. AmanPM is designed to minimize token usage at session start.

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

session_hygiene:               # Drift detection (self-healing)
  last_session_ended_cleanly: true
  sessions_since_learning_capture: 0
  last_session_end: "2026-01-31T18:00:00Z"

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

### Work Item Hierarchy

AmanPM uses a three-level hierarchy for organizing work:

```
EPIC-001: User Authentication System
├── FEAT-001: Login with email/password
│   ├── TASK-001: Create login form component
│   ├── TASK-002: Implement auth API endpoint
│   └── TASK-003: Add session management
├── FEAT-002: Password reset flow
│   ├── TASK-004: Email verification endpoint
│   └── TASK-005: Reset password UI
└── FEAT-003: OAuth integration (Google, GitHub)
```

### Epics vs Features

| Dimension | Epic | Feature |
|-----------|------|---------|
| **Scope** | Strategic initiative | Scope-bounded deliverable |
| **Owner** | Human-defined | Can be AI-proposed |
| **Timeframe** | Multi-sprint (weeks/months) | Single sprint (days) |
| **Granularity** | "What we're building" | "How we're building it" |
| **Children** | Contains features | Contains tasks |
| **Example** | "User Authentication System" | "Password Reset Flow" |

**Epic:**
- Answers: "What strategic goal are we pursuing?"
- Has `success_criteria` (measurable outcomes)
- Human writes the vision and approves
- Lives across multiple sprints

**Feature:**
- Answers: "What specific capability ships?"
- Has `acceptance_criteria` (testable conditions)
- AI can draft, human reviews
- Fits within one sprint

> **Rule of Thumb:** If you can't ship it in a week, it's probably an epic that needs decomposition into features.
>
> **Epic** = You'd mention it in a quarterly planning meeting
> **Feature** = You'd demo it in a sprint review

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

### Definition of Ready (DoR) 🔹 **Core**

Items must meet the **Definition of Ready** before entering a sprint. This prevents sprint disruption from unclear or blocked items.

**Universal DoR Checklist:**

```markdown
## Definition of Ready

Before an item can enter a sprint:

- [ ] Title is clear and action-oriented
- [ ] Acceptance criteria defined and testable
- [ ] Dependencies identified and documented
- [ ] Complexity estimated (T-shirt size)
- [ ] No blocking items remain unresolved
- [ ] Technical approach agreed (for M/L/XL items)
```

**Per-Type DoR Requirements:**

| Type | Additional DoR |
|------|----------------|
| **Feature** | Spec exists or drafted; parent epic identified |
| **Bug** | Reproduction steps documented; severity assessed |
| **Spike** | Research question clearly stated; timebox defined |
| **Debt** | Impact and scope defined; affected files listed |
| **Task** | Parent feature identified; action clearly stated |

**DoR Validation in `/aman groom`:**

When grooming the backlog, check each item against DoR:

```yaml
# /aman groom output
dor_validation:
  ready_items: 5
  not_ready:
    - id: FEAT-003
      missing: ["acceptance_criteria", "complexity"]
    - id: BUG-012
      missing: ["reproduction_steps"]
```

**DoR Enforcement:**

> **Rule:** Items that fail DoR cannot be added to a sprint. They must be clarified first.

### Definition of Done (DoD) 🔹 **Core**

Items must meet the **Definition of Done** before being marked complete. This ensures consistent quality and prevents technical debt.

**Universal DoD Checklist:**

```markdown
## Definition of Done

An item is DONE when:

- [ ] Code complete and compiles without errors
- [ ] All tests pass (unit + integration if applicable)
- [ ] CI pipeline green (if using CI)
- [ ] Self-review or peer review completed
- [ ] Learning captured (if any discovery during implementation)
- [ ] Documentation updated (if behavior changed)
```

**Per-Type DoD Requirements:**

| Type | Additional DoD |
|------|----------------|
| **Feature** | Acceptance criteria verified; validation guide created |
| **Bug** | Root cause documented; regression test added |
| **Spike** | Findings documented; recommendation provided |
| **Debt** | Tech debt entry updated; no new debt introduced |
| **Task** | Parent feature updated; no orphaned code |

**DoD Template for Features:**

```yaml
# Feature DoD Checklist
dod:
  code_complete: true
  tests_pass: true
  ci_green: true
  acceptance_criteria:
    - AC01: verified
    - AC02: verified
    - AC03: verified
  validation_guide_created: true
  learning_captured: true  # or "none"
  documentation_updated: true
```

**DoD Enforcement:**

> **Rule:** Items that fail DoD cannot be moved to `done/`. They remain `in_progress` until all criteria are met.

**Integration with `/aman done`:**

When running `/aman done FEAT-001`, the command should:
1. Display DoD checklist for that item type
2. Confirm each criterion is met
3. Only archive if all criteria pass
4. Log any captured learnings

### Engineering Workflow Contract 🔹 **Core**

Beyond DoR/DoD, this contract defines non-negotiable engineering practices. Violating it accumulates hidden debt.

#### Spec-Before-Code

**Rule:** No implementation starts without a specification.

Before writing ANY code:
- [ ] Spec exists with testable acceptance criteria (Given-When-Then format)
- [ ] Dependencies identified and documented
- [ ] Business need traceable to spec
- [ ] Technical approach agreed (for M/L/XL items)

> **No specification = No code.**

#### TDD with CI Parity

**Rule:** Tests drive implementation. Local must match CI exactly.

| Phase | Action |
|-------|--------|
| **RED** | Write failing test from acceptance criteria |
| **GREEN** | Write minimum code to pass |
| **REFACTOR** | Clean while tests stay green |
| **LINT** | Fix all warnings (full codebase, not just changed files) |
| **CI-CHECK** | Run equivalent of CI locally (exit code 0 required) |

**Verify Extension (Beyond Classic TDD):**
- Observability: Can operations be traced?
- Audit: Are decisions logged?
- Security: No vulnerabilities introduced?

#### Validation Guide Requirement 🔹 **Core**

**Rule:** Every feature produces a self-contained validation guide.

**Required Sections:**
1. **Quick Start** (4-5 commands for experienced users)
2. **Prerequisites** (exact versions, services)
3. **Test Data Setup** (scripts, not manual entry)
4. **Validation Scenarios** (command → expected output)
5. **Pass/Fail Criteria** (boolean checklist)
6. **Cleanup** (reproducible reset)

**Anti-Pattern (NEVER do this):**

```bash
# ❌ WRONG - Requires human interpretation
export TOKEN="<your-jwt-token-here>"
TENANT_ID="<your-tenant-uuid>"

# ✅ CORRECT - Self-contained acquisition
export TOKEN=$(./scripts/get-token.sh alice)
TENANT_ID=$(curl -s localhost:8080/api/v1/tenants | jq -r '.[0].id')
```

> A new developer with zero prior knowledge should complete validation using copy-paste only.

#### Human Validation Gate 🔹 **Core**

**Rule:** AI pauses after implementation. Human validates before marking complete.

```
Implementation Flow:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. AI implements feature following TDD
2. AI runs CI parity check (must pass)
3. AI creates validation guide
4. AI reports: "Ready for validation"
5. **AI STOPS** - Does not mark complete
6. Human executes validation guide
7. Human says: "F## validated" (explicit trigger phrase)
8. Only then: AI updates status to Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

> This gate prevents "technically correct but wrong" implementations.

#### Five-Gate Quality System 🔸 **Recommended**

For high-quality projects, implement a sequential quality gate system:

| Gate | Name | Pass Criteria |
|------|------|---------------|
| **1** | Specification | Spec exists, ACs testable, dependencies clear |
| **2** | TDD Compliance | RED-GREEN-REFACTOR followed, tests cover ACs |
| **3** | CI Parity | Local CI check passes (lint, tests, security) |
| **4** | Validation Guide | Self-contained guide created, zero placeholders |
| **5** | Human Validation | Human executes guide, says "F## validated" |

**Gate Enforcement:**
- Gates are sequential (no skipping to Gate 5)
- AI MUST pause after Gate 4, wait for human
- Blocked gates emit warnings, not silent failures

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
| `/aman checklist` | 🔹 **Core** | List/run checklists at pause points |
| `/aman pre-commit` | 🔹 **Core** | Pre-commit killer items (7 items, 60 sec) |
| `/aman feature-gate` | 🔹 **Core** | Feature complete killer items (9 items, 90 sec) |

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
/aman checklist    → list/run checklists
/aman pre-commit   → pre-commit killer items (7 items)
/aman feature-gate → feature complete killer items (9 items)
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
| `/aman checklist` | `[id]` | List or run checklist |
| `/aman pre-commit` | — | Pre-commit killer items |
| `/aman feature-gate` | — | Feature complete gate |

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

### Script-Based Sync for Data Integrity 🔹 **Core**

> **Warning:** LLMs are unreliable at arithmetic. For projects with >50 items, use a deterministic script instead of letting the LLM manually calculate counts.

**Problem:** When LLMs update `index.yaml`, they frequently miscalculate totals (e.g., `total_items: 45` should be `46`). These errors accumulate silently.

**Solution:** Create a sync script that scans directories and generates accurate YAML.

**Create `.aman-pm/scripts/sync.sh`:**

```bash
#!/bin/bash
# Deterministic index.yaml generator
# Usage: ./sync.sh > ../.aman-pm/index.yaml

set -e
cd "$(dirname "$0")/.."

# Count items by status
count_status() {
  grep -rl "^status: $1" backlog/*/active/ 2>/dev/null | wc -l | tr -d ' '
}

# Count items by type
count_type() {
  find "backlog/$1/active" -name "*.yaml" 2>/dev/null | wc -l | tr -d ' '
}

cat << EOF
snapshot:
  generated: "$(date -u +%Y-%m-%dT%H:%M:%SZ)"
  # ... rest of index structure

metrics:
  total_items: $(find backlog/*/active -name "*.yaml" 2>/dev/null | wc -l | tr -d ' ')
  by_status:
    backlog: $(count_status backlog)
    in_progress: $(count_status in_progress)
    done: $(count_status done)
EOF
```

**Update `commands/sync.md` to use the script:**

```markdown
## Sync Action

1. Run the deterministic sync script:
   \`\`\`bash
   bash .aman-pm/scripts/sync.sh > .aman-pm/index.yaml.tmp
   mv .aman-pm/index.yaml.tmp .aman-pm/index.yaml
   \`\`\`

2. The script guarantees accurate counts—no arithmetic required.
```

**When to Use:**
- Projects with >50 total items
- When count discrepancies are detected
- As a validation step during sprint-end

### WSJF Prioritization 🔸 **Recommended**

**Weighted Shortest Job First (WSJF)** is a prioritization method that balances urgency against effort. Use it during `/aman groom` to scientifically rank backlog items.

**Formula:**

```
WSJF = Cost of Delay / Job Size
```

Where:
- **Cost of Delay** = Business Value + Time Criticality + Risk Reduction (each 1-10)
- **Job Size** = Complexity score (XS=1, S=2, M=4, L=8, XL=16)

**Configuration:**

```yaml
# Add to config.yaml
prioritization:
  method: "WSJF"
  formula: "cost_of_delay / job_size"

  cost_of_delay_factors:
    - user_value      # 1-10: How much users want this
    - time_criticality # 1-10: Does delay reduce value?
    - risk_reduction  # 1-10: Does this reduce project risk?

  job_size_mapping:
    XS: 1
    S: 2
    M: 4
    L: 8
    XL: 16
```

**WSJF Calculation Example:**

| Item | User Value | Time Critical | Risk Reduction | CoD | Size | WSJF |
|------|------------|---------------|----------------|-----|------|------|
| FEAT-001 | 8 | 5 | 3 | 16 | M (4) | **4.0** |
| FEAT-002 | 6 | 8 | 2 | 16 | L (8) | **2.0** |
| BUG-003 | 3 | 9 | 7 | 19 | S (2) | **9.5** |

→ Priority order: BUG-003 (9.5) > FEAT-001 (4.0) > FEAT-002 (2.0)

**Integration with `/aman groom`:**

```yaml
# /aman groom output
grooming_results:
  ready_items: 5
  blocked_items: 2
  needs_clarification: 1

  wsjf_ranked:
    - id: BUG-003
      wsjf_score: 9.5
      recommendation: "Sprint candidate (high WSJF)"
    - id: FEAT-001
      wsjf_score: 4.0
      recommendation: "Sprint candidate"
    - id: FEAT-002
      wsjf_score: 2.0
      recommendation: "Consider deferring"

  stale_items:
    - id: TASK-015
      days_untouched: 14
      recommendation: "Archive or reprioritize"
```

**When to Use WSJF:**

| Scenario | Use WSJF? | Reason |
|----------|-----------|--------|
| Backlog >20 items | ✅ Yes | Need systematic ranking |
| Multiple competing features | ✅ Yes | Objective prioritization |
| Small backlog (<10 items) | ⚪ Optional | Simple P0-P3 may suffice |
| All items similar size | ⚪ Optional | WSJF adds little value |

**Simplified WSJF (Quick Mode):**

For faster grooming, use simplified scoring:

```
Quick WSJF = Priority Weight / Size

Where:
- P0 = 16, P1 = 8, P2 = 4, P3 = 2
- Size = T-shirt mapping (XS=1...XL=16)
```

---

## Part 6: Memory System

### Three-Tier Memory Architecture 🔹 **Core**

AmanPM uses a three-tier memory system to balance context richness with token efficiency:

```
┌─────────────────────────────────────────────────────────────┐
│              TIER 0: HOT MEMORY (memory-bank/)              │
│                   (Always loaded first)                     │
├─────────────────────────────────────────────────────────────┤
│  • Architectural constraints (critical project rules)       │
│  • Active decisions pending resolution                      │
│  • Current session context                                  │
└─────────────────────────────────────────────────────────────┘
                              │
                         Then load
                              ▼
┌─────────────────────────────────────────────────────────────┐
│              TIER 1: WARM MEMORY (index.yaml)               │
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
│              TIER 2: COLD STORAGE (knowledge/)              │
│                  (Loaded when needed)                       │
├─────────────────────────────────────────────────────────────┤
│  • Historical learnings (learnings-archive/)                │
│  • Old sprint retrospectives                                │
│  • Closed decisions (>30 days)                              │
│  • Full velocity history                                    │
└─────────────────────────────────────────────────────────────┘
```

**Memory Tier Summary:**

| Tier | Location | Purpose | When Loaded |
|------|----------|---------|-------------|
| **Tier 0: Hot** | `memory-bank/` | Critical constraints, active decisions | Every `/aman start` |
| **Tier 1: Warm** | `index.yaml`, recent learnings | Sprint state, project metrics | Every `/aman start` |
| **Tier 2: Cold** | `knowledge/`, `learnings-archive/` | Historical context | On-demand retrieval |

**Tier 2 Retrieval:** When you ask "didn't we try approach X before?", the AI searches `learnings-archive/` and surfaces relevant historical context.

### Memory Bank (Hot Memory) 🔹 **Core**

The memory bank stores information that must be available at every session start. Unlike learnings (which are discoveries), memory bank items are **constraints** and **active decisions**.

**Directory Structure:**

```
.aman-pm/memory-bank/
├── architectural-constraints.md   # Critical project rules
├── active-decisions.md            # Decisions pending resolution
└── session-context.md             # Current session state
```

**Architectural Constraints Template:**

```markdown
# Architectural Constraints (Always Active)

> **Purpose:** Critical constraints that must be respected in ALL implementations.
> This file is loaded at every `/aman start`.

## Core Architecture

{Describe your project's core architectural pattern}

**Example (Three-Brain Model for Auth):**

1. **Identity Brain ({Provider})** — "WHO are you?"
   - All authentication flows through {provider}
   - JWT tokens contain {specific claims}
   - Machine users use {token pattern}

2. **Policy Brain ({Service})** — "WHAT can you do?"
   - All permission checks go through {service}
   - {Describe authorization pattern}

3. **Execution Brain ({Framework})** — "HOW do we enforce it?"
   - Middleware validates JWT before business logic
   - {Describe middleware pattern}

## Integration Rules

- **Never bypass middleware** — Frontend calls API, API enforces auth
- **Token storage** — Use httpOnly cookies or secure storage, never localStorage
- **Tenant context** — Always pass {header} for multi-tenant operations

## Anti-Patterns (Never Do)

- {Anti-pattern 1}
- {Anti-pattern 2}
```

**Active Decisions Template:**

```markdown
# Active Decisions

> **Purpose:** Track decisions that are pending or recently made.
> Move to `knowledge/decisions.md` when resolved.

## Pending Decisions

### {Decision Title}
- **Status:** Pending
- **Context:** {Why this decision is needed}
- **Options:**
  - Option A: {description}
  - Option B: {description}
- **Blocking:** {What's waiting on this decision}
- **Due:** {Date if applicable}

## Recently Resolved (Last 7 Days)

### {Decision Title}
- **Status:** Decided
- **Decision:** {What was decided}
- **Date:** YYYY-MM-DD
- **ADR:** ADR-XXX (if applicable)
```

**Memory Bank Loading at Session Start:**

When `/aman start` runs, it MUST:
1. Load `memory-bank/architectural-constraints.md` (always)
2. Load `memory-bank/active-decisions.md` (always)
3. Surface any pending decisions as blockers
4. Then proceed with Tier 1 loading (index.yaml, learnings)

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

### Flashbulb Memories (Immediate Capture) 🔹 **Core**

Some learnings are too important to wait for `/aman end`. When the user expresses strong negative feedback, capture immediately.

**Trigger Phrases:**
- "Don't ever do that again"
- "This was a mistake"
- "Remember this for next time"
- "This is critical"
- "Never do X"
- "Always do Y instead"

**Immediate Action:** Write to `learnings.md` without waiting for session end.

**Format:**
```markdown
### YYYY-MM-DD (Flashbulb)

- **Learning**: {what went wrong or must be remembered}
  - **Trigger**: "{user's exact words}"
  - **Context**: {what was happening when this was said}
  - **Action**: {what to avoid or always do in future}
```

**Why This Matters:**
- Session may end abruptly (crash, timeout, user closes)
- Critical learnings have highest value and highest risk of loss
- Immediate capture while context is fresh

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

### Session Hygiene Warnings (Drift Detection) 🔹 **Core**

`/aman start` should check `session_hygiene` in `index.yaml` and emit warnings:

| Condition | Level | Message |
|-----------|-------|---------|
| `last_session_ended_cleanly: false` | ⚠️ Warning | "Previous session did not run `/aman end`. Learnings may be lost." |
| `sessions_since_learning_capture >= 3` | ⚠️ Warning | "No learnings captured in 3+ sessions. Consider what you've learned." |
| Sprint active, no progress in 3+ sessions | 🔴 Alert | "Sprint has stalled. Review blockers or adjust scope." |

**Implementation:** `/aman end` sets `last_session_ended_cleanly: true` and resets `sessions_since_learning_capture` if learnings were captured. `/aman start` sets `last_session_ended_cleanly: false` at session begin (to detect incomplete sessions).

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

### Parallel Development Streams 🔸 **Recommended**

For projects with multiple workstreams (e.g., Web + Mobile + Shared components), organize sprint items into parallel streams:

**Structure:**
- Each stream has a name, focus area, and item list
- Streams can be independent (parallel) or dependent (sequential)
- Retrospectives analyze per-stream performance

**Sprint Items with Streams:**

```yaml
# .aman-pm/sprints/active/1/items.yaml
sprint: 1
goal: "Initialize Frontend Infrastructure"
started: "2026-02-10"
ends: "2026-02-17"

streams:
  A:
    name: "Web Frontend"
    focus: "Next.js scaffold + auth integration"
    items:
      - id: FEAT-001
        title: "Next.js project scaffold"
        type: feature
        priority: P1
        complexity: M
        hours: 6
        status: backlog
        parent: EPIC-001
  B:
    name: "Mobile Foundation"
    focus: "React Native setup"
    items:
      - id: FEAT-002
        title: "React Native project scaffold"
        type: feature
        priority: P1
        complexity: M
        hours: 6
        status: backlog
        parent: EPIC-002
  C:
    name: "Shared Components"
    focus: "Cross-platform auth hooks"
    items:
      - id: TASK-001
        title: "Auth context provider"
        type: task
        priority: P1
        complexity: S
        hours: 3
        status: backlog

parallelization_strategy: |
  Streams A and B are independent, can run in parallel.
  Stream C is shared; complete before streams A/B integrate.

capacity:
  total_hours: 26
  committed_hours: 21
  buffer_hours: 5
  utilization: "81%"
```

**Stream Analysis in Retrospectives:**

```markdown
## Stream Analysis

| Stream | Planned | Completed | Notes |
|--------|---------|-----------|-------|
| A: Web | 3 items | 3 items | On track |
| B: Mobile | 2 items | 1 item | Device setup delayed |
| C: Shared | 2 items | 2 items | Complete |

**Lesson:** Stream B needed more buffer for device setup.
```

### Mid-Sprint Change Management 🔹 **Core**

Sprints need controlled flexibility. Use a **change budget** to handle unplanned work without derailing sprint goals.

**Change Budget Policy:**

```yaml
# Add to config.yaml
mid_sprint_policy:
  change_budget: "20%"           # Max % of sprint capacity for changes
  intake_lane: "mid_sprint_requests"
  triage_frequency: "daily"

  lanes:
    emergency:
      criteria: "P0 production issues only"
      action: "Immediate swap, document impact"
    standard:
      criteria: "All other changes"
      action: "Queue for next sprint or use change budget"

  replanning_trigger:
    condition: ">20% scope change"
    action: "Re-run planning, update sprint goal"
```

**Decision Tree:**

```
New request arrives mid-sprint:
├─ Is it P0 (production down)? → Emergency lane (immediate swap)
├─ Is change budget available? → Use budget, document impact
├─ Can it wait 1 week? → Queue for next sprint
└─ >20% scope change? → Trigger replanning ceremony
```

**Change Request Template:**

```markdown
## Mid-Sprint Change Request

**Item:** {ID and title}
**Priority:** {P0/P1/P2/P3}
**Requested By:** {name}
**Date:** {YYYY-MM-DD}

### Justification
{Why this can't wait for next sprint}

### Impact Analysis
- Sprint capacity used: {X hours}
- Items displaced: {list or "none"}
- Risk if deferred: {description}

### Decision
- [ ] Approved (within change budget)
- [ ] Deferred to next sprint
- [ ] Emergency lane (P0 only)

### Approved By: {name}
```

**Tracking Mid-Sprint Changes:**

Create `.aman-pm/sprints/active/{N}/mid-sprint-changes.md` to log all changes:

```markdown
# Sprint N: Mid-Sprint Changes

## Change Budget
- Allocated: 5 hours (20% of 26)
- Used: 2 hours
- Remaining: 3 hours

## Changes Log

### 2026-02-12: BUG-042 (Emergency)
- **Priority:** P0
- **Hours:** 2
- **Lane:** Emergency
- **Impact:** Displaced TASK-003 to next sprint
- **Outcome:** Fixed within 2 hours
```

### Rearchitecture Workflow 🔸 **Recommended**

When architectural assumptions are invalidated mid-project, use a formal **rearchitecture proposal** process.

**When to Use:**
- Core architectural pattern needs to change
- External dependency forces adaptation
- Performance requirements invalidate current approach
- Security finding requires structural changes

**Lifecycle:** Problem → Alternatives → Impact Analysis → Approval → Implementation → ADR

**Directory Structure:**

```
.aman-pm/rearchitecture/
├── active/
│   └── REARCH-001-frontend-auth-adaptation.md
└── completed/
    └── REARCH-000-example.md
```

**Rearchitecture Proposal Template:**

```markdown
---
id: REARCH-001
status: proposed  # proposed | approved | implementing | completed | rejected
created: 2026-02-15
author: {name}
related_adr: null  # Will be set when ADR is created
---

# REARCH-001: {Title}

## Problem Statement

{What architectural assumption is invalidated? Why can't we continue as-is?}

## Alternatives Considered

### Option A: {Name}
**Description:** {How this option works}
**Pros:** {Benefits}
**Cons:** {Drawbacks}
**Effort:** {T-shirt size}

### Option B: {Name}
**Description:** {How this option works}
**Pros:** {Benefits}
**Cons:** {Drawbacks}
**Effort:** {T-shirt size}

## Recommendation

**Selected Option:** {A or B}
**Rationale:** {Why this option is best}

## Code Impact Analysis

| Area | Files Affected | Breaking Changes | Migration Required |
|------|----------------|------------------|-------------------|
| API | 5 files | Yes | Yes |
| Database | 2 migrations | No | No |
| Frontend | 10 components | Yes | Yes |

## Implementation Plan

1. {Step 1 with estimated effort}
2. {Step 2 with estimated effort}
3. {Step 3 with estimated effort}

## Rollback Plan

{How to revert if the rearchitecture fails}

## Deprecation Candidates

- {Old pattern/code to deprecate after migration}

## Approval

- [ ] Technical review complete
- [ ] Impact analysis reviewed
- [ ] Rollback plan validated
- [ ] Human approval received

**Approved By:** {name}
**Approved Date:** {YYYY-MM-DD}
```

**Integration with ADRs:**

| Document | Purpose |
|----------|---------|
| **REARCH-XXX** | Documents the PROCESS (how we transition) |
| **ADR-XXX** | Documents the DECISION (what we chose and why) |

**Workflow:**
1. Create `REARCH-XXX` proposal in `.aman-pm/rearchitecture/active/`
2. Document alternatives and impact analysis
3. Get human approval
4. When approved, create `ADR-XXX` documenting the decision
5. Link REARCH to ADR: `related_adr: ADR-XXX`
6. Execute implementation plan
7. Update `learnings.md` with outcomes
8. Move REARCH to `completed/` when done

**Retrospective Integration:**

Add to sprint retrospective:
```markdown
## Roadmap Impact

- Did we learn anything that invalidates architectural assumptions? {yes/no}
- If yes, create REARCH-XXX proposal
```

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

### Sprint Retrospective Template 🔸 **Recommended**

Create `.aman-pm/sprints/history/{N}/retrospective.md` at sprint end:

```markdown
# Sprint {N} Retrospective

**Date:** YYYY-MM-DD
**Sprint Goal:** {Goal from goal.md}

## Metrics

| Metric | Planned | Actual | Notes |
|--------|---------|--------|-------|
| Items committed | 8 | 8 | - |
| Items completed | - | 7 | 1 carried over |
| Completion rate | - | 87.5% | - |
| Hours committed | 26 | 24 | 2 hrs buffer used |
| Capacity utilization | - | 92% | - |

## Stream Analysis (if using parallel streams)

| Stream | Planned | Completed | Blockers | Notes |
|--------|---------|-----------|----------|-------|
| A: {Name} | 3 | 3 | None | On track |
| B: {Name} | 3 | 2 | {Issue} | 1 carried over |
| C: {Name} | 2 | 2 | None | Complete |

## Mid-Sprint Changes

| Change | Priority | Hours | Lane | Impact |
|--------|----------|-------|------|--------|
| BUG-042 | P0 | 2 | Emergency | Displaced TASK-003 |

**Change Budget:** 5 hrs allocated, 2 hrs used, 3 hrs remaining

## What Went Well

- {Item 1}
- {Item 2}
- {Item 3}

## What Didn't Go Well

- {Item 1}
- {Item 2}

## Action Items for Next Sprint

- [ ] {Action 1 with owner}
- [ ] {Action 2 with owner}

## Learnings Captured

See `knowledge/learnings.md` for details:
- Learning 1: {summary}
- Learning 2: {summary}

## Roadmap Impact

- Did we learn anything that invalidates architectural assumptions? {yes/no}
- If yes, create REARCH-XXX proposal
- Roadmap adjustments needed? {yes/no}
```

**Retrospective Ceremony Flow:**

1. **Review metrics** (5 min) - Pull from `items.yaml` summary
2. **Stream analysis** (5 min) - Per-stream performance
3. **Change review** (5 min) - Mid-sprint changes and their impact
4. **What worked** (10 min) - Successes to continue
5. **What didn't** (10 min) - Issues to address
6. **Action items** (5 min) - Concrete next steps with owners
7. **Learnings** (5 min) - Capture any new learnings
8. **Roadmap check** (5 min) - Architecture assumptions still valid?

**Velocity Update:**

After retrospective, update `velocity.json` with sprint data and recalculate averages.

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

### Do It Now Philosophy 🔹 **Core**

**Philosophy:** "What you have to do tomorrow, do today; what you have to do today, do now."

Tech debt compounds at 200-400% interest per week. Small deferrals become catastrophes. Use this threshold-based decision tree:

| Effort Estimate | Decision | Rationale |
|-----------------|----------|-----------|
| **< 30 minutes** | ✅ DO NOW | Cost of context switching exceeds cost of doing |
| **30 min - 1.5 hours** | ✅ DO TODAY | Schedule in current session, don't defer |
| **1.5 - 4 hours** | 📅 SCHEDULE | Next session, create backlog item |
| **> 4 hours** | ⚠️ DEFER ONLY IF blocked | Requires explicit human approval + DEBT entry |

**Zero-Tolerance Categories (Never Defer):**
- CI/CD failures
- Security vulnerabilities
- Test failures
- Code formatting/linting
- Broken builds

**Deferral Conditions (ALL required for legitimate deferral):**
1. External blocker exists (not just "no time")
2. Documented in DEBT-XXX entry
3. Deadline assigned
4. Human approved deferral

**Metrics to Track:**
- Tech Debt Age: Target < 1 week average
- Deferral Rate: Target < 20% of encountered issues
- CI Stability: Target > 95% green builds

### The Checklist Manifesto 🔹 **Core**

**Philosophy:** "Checklists catch what expertise misses." — Atul Gawande

Based on the book "The Checklist Manifesto" which dramatically reduced surgical deaths by implementing simple verification checklists. The same principles apply to software development: even experts forget critical steps under pressure.

#### Two Types of Errors

| Error Type | Definition | Software Examples |
|------------|------------|-------------------|
| **Ignorance** | Don't know how | New technology, unfamiliar patterns |
| **Ineptitude** | Know but don't apply | Forgot `make ci-check`, skipped validation guide |

**Key Insight:** Most software failures are **ineptitude-class**—we KNOW to run CI checks but forget. Checklists are the solution.

#### Two Checklist Types

| Type | When to Use | Execution Style |
|------|-------------|-----------------|
| **READ-DO** | Sequential/critical tasks, emergencies | Read step → Do it → Next step |
| **DO-CONFIRM** | Routine tasks, experienced teams | Do from memory → Pause → Confirm |

**Naming Convention:** `CL-{DOMAIN}-{TYPE}-{Name}.md`
- Domains: IMPL (Implementation), SEC (Security), DBM (Database Migration), OPS (Operations), REL (Release)
- Types: DC (DO-CONFIRM), RD (READ-DO)
- Example: `CL-IMPL-DC-PreCommit.md`

#### The 5-9 Item Rule

- **Working memory limit:** Human brain holds 4-7 items max
- **60-90 seconds:** Longer checklists become distractions and get skipped
- **"Killer items" only:** Include ONLY steps most dangerous to skip

**Anti-Pattern:** 50+ item checklists → Cognitive overload → Checklist abandonment

#### Two-Layer Checklist System

| Layer | Purpose | Items | Time | Trigger |
|-------|---------|-------|------|---------|
| **Layer 1: Killer Items** | Quick verification gate | 5-9 | 60-90 sec | Every commit, feature gate |
| **Layer 2: Comprehensive** | Thorough review | 50+ | 15-30 min | Human validation only |

**Coexistence Model:**

```
Workflow Touch Point          Checklist(s) Triggered
─────────────────────────────────────────────────────
Before git commit     →  CL-IMPL-DC-PreCommit (Layer 1)
Feature "complete"    →  CL-IMPL-DC-FeatureComplete (Layer 1)
Human validation      →  quality-checklist.md (Layer 2)
```

**Principle:** Layer 1 runs EVERY time (mandatory). Layer 2 runs at VALIDATION boundaries (human).

#### Auto-Trigger Pause Points

| Pause Point | Checklist | Trigger Condition |
|-------------|-----------|-------------------|
| Pre-Commit | CL-IMPL-DC-PreCommit | Before `git commit` |
| Feature Complete | CL-IMPL-DC-FeatureComplete | AI claims "feature complete" |
| Session End | CL-REL-DC-SessionEnd | During `/aman end` |
| Security Code | CL-SEC-DC-SecurityReview | After auth/middleware changes |
| Migration | CL-DBM-RD-MigrationValidation | Migration files created/modified |

#### Essential Checklists (Starter Kit)

**Pre-Commit Killer Items (7 items, 60 sec):**

```markdown
## CL-IMPL-DC-PreCommit
**Type:** DO-CONFIRM | **Trigger:** Before `git commit` | **Time:** 60 sec

- [ ] `make ci-check` exit code 0
- [ ] No `<your-token-here>` placeholders in code
- [ ] No debug code (fmt.Println, console.log)
- [ ] No commented-out code blocks
- [ ] No secrets in committed files
- [ ] Changelog entry in unreleased.md (if meaningful)
- [ ] All TODO comments have DEBT-XXX reference
```

**Feature Complete Killer Items (9 items, 90 sec):**

```markdown
## CL-IMPL-DC-FeatureComplete
**Type:** DO-CONFIRM | **Trigger:** AI claims "feature complete" | **Time:** 90 sec

- [ ] `make ci-check` exit code 0 (not "I'll fix later")
- [ ] Validation guide exists and is self-contained
- [ ] No placeholders in validation guide
- [ ] All ACs implemented OR deferred with DEBT-XXX
- [ ] Regression tests created
- [ ] Changelog entry added to unreleased.md
- [ ] Future scope captured in future-enhancements.md
- [ ] Backlog item updated (if from BL-*)
- [ ] Git status shows only intended files
```

#### Checklist Item Writing Rules

| Rule | Good Example | Bad Example |
|------|--------------|-------------|
| Actionable | `make ci-check` exit code 0 | CI should pass |
| Verifiable | No `<token>` placeholders | Guide is complete |
| Single action | Run `govulncheck` | Run scan and fix issues |
| Binary | Yes/No checkable | Somewhat complete |
| Kill-worthy | Security vulnerability | Style preference |

#### /aman Checklist Commands

```
/aman checklist              # List available checklists
/aman checklist <id>         # Run specific checklist
/aman checklist --suggest    # AI suggests based on context
/aman pre-commit             # Alias: CL-IMPL-DC-PreCommit
/aman feature-gate           # Alias: CL-IMPL-DC-FeatureComplete
```

#### Checklist Runner Skill Integration

Create `.claude/skills/checklist-runner/SKILL.md`:

```markdown
# Checklist Runner Skill

## Auto-Load Triggers
- Before `git commit` → Run CL-IMPL-DC-PreCommit
- "Feature complete" → Run CL-IMPL-DC-FeatureComplete
- `/aman end` → Run CL-REL-DC-SessionEnd

## Execution Protocol
1. Load checklist from `.aman-pm/checklists/`
2. For each item:
   - If automatable: Run and report PASS/FAIL
   - If manual: State check clearly
3. Report: X/Y items passed
4. If ANY fail: STOP and block proceeding

## Example Output
=== CL-IMPL-DC-PreCommit (7 items) ===
[1/7] make ci-check exit code 0... ✅ PASS
[2/7] No placeholders in code... ✅ PASS
...
[6/7] Changelog entry exists... ❌ FAIL
RESULT: 6/7 PASS, 1 FAIL
⛔ STOP: Cannot commit until all items pass.
```

#### Makefile Integration

```makefile
.PHONY: checklist cl-precommit cl-feature

checklist:
	@echo "Available: cl-precommit, cl-feature, cl-security, cl-migration"

cl-precommit:
	@cat .aman-pm/checklists/CL-IMPL-DC-PreCommit.md

cl-feature:
	@cat .aman-pm/checklists/CL-IMPL-DC-FeatureComplete.md
```

#### Success Metrics

| Metric | Before | After Target |
|--------|--------|--------------|
| CI failures post-commit | ~30% | <5% |
| Validation guide placeholders | Frequent | Zero |
| Feature rework rate | 1-2/feature | <0.5 |
| Checklist completion time | N/A | <90 sec |

#### Monthly Review Process

1. Which items failed most often? (Remove if never fails)
2. What RCAs should checklists have caught? (Add items)
3. Keep to 5-9 items (remove lowest-value to add new)

**Reference:** [The Checklist Manifesto by Atul Gawande](https://atulgawande.com/book/the-checklist-manifesto/)

### Black-Box Design (Eskil Steenberg) 🔸 **Recommended**

**Philosophy:** "Every module should be a black box with a clean, documented API." — Eskil Steenberg

Based on principles from "Architecting Large Software Projects" for building systems that last decades.

#### The 4 Core Principles

| Principle | Meaning | Test |
|-----------|---------|------|
| **Black Box Interfaces** | Hide implementation, expose API only | Can you describe this module without mentioning implementation? |
| **Replaceable Components** | Rewritable from interface alone | Could a new dev rewrite this in a week using only the interface docs? |
| **Single Responsibility** | One module = one person can own it | Can you explain this module's purpose in one sentence? |
| **Primitive-First Design** | Build complexity through composition | Can you list 3-5 core types that flow through this module? |

#### Interface-First Pattern

```go
// BAD: Leaks implementation
type AuthService struct {
    zitadelClient *zitadel.Client  // Exposes vendor
    fgaClient     *openfga.Client  // Exposes vendor
}

// GOOD: Black box
type Authenticator interface {
    Authenticate(ctx context.Context, token string) (*Identity, error)
}

type Authorizer interface {
    Check(ctx context.Context, user, relation, object string) (bool, error)
}

// Multiple implementations possible:
// - ZitadelAuthenticator: Production
// - MockAuthenticator: Testing
// - LocalAuthenticator: Development
```

#### Module Boundary Verification

Before marking architecture work complete:

- [ ] **Primitives clear?** Can you list the core types that flow through?
- [ ] **Black box boundary?** Can you draw a box around this module?
- [ ] **Replaceable?** Could someone implement this without seeing current code?
- [ ] **Human-readable?** Will this be maintainable in 5 years?
- [ ] **Single responsibility?** Does each module have one obvious job?

#### Red Flags to Avoid

| Red Flag | Problem | Fix |
|----------|---------|-----|
| API exposes internal types | Leaky abstraction | Define interface types |
| Module too complex for one person | Violation of single responsibility | Split into focused modules |
| Hard-coded dependencies | Tight coupling | Use interfaces + injection |
| Interface requires internal knowledge | Not a true black box | Simplify interface |
| Changes cascade across modules | Poor boundaries | Strengthen interface contracts |

**Reference:** [Eskil Steenberg - Architecting Large Software Projects](https://www.youtube.com/watch?v=_xLgr6Ng4qQ)

### Unix Philosophy (17 Rules) 🔸 **Recommended**

**Philosophy:** "Write programs that do one thing and do it well." — Doug McIlroy

The foundational principles for building modular, maintainable software.

#### Core Rules Summary

**Design Principles:**

| # | Rule | Meaning |
|---|------|---------|
| 1 | **Modularity** | Simple parts, clean interfaces |
| 2 | **Clarity** | Clear > clever |
| 3 | **Composition** | Programs connect to programs |
| 4 | **Separation** | Policy ≠ mechanism |
| 5 | **Simplicity** | Add complexity only when necessary |

**Quality Principles:**

| # | Rule | Meaning |
|---|------|---------|
| 6 | **Parsimony** | Write small programs |
| 7 | **Transparency** | Visible for debugging |
| 8 | **Robustness** | Child of transparency + simplicity |
| 9 | **Representation** | Data > logic |
| 10 | **Least Surprise** | Do the expected thing |

**Operational Principles:**

| # | Rule | Meaning |
|---|------|---------|
| 11 | **Silence** | Say nothing unless surprising |
| 12 | **Repair** | Fail noisily, fail fast |
| 13 | **Economy** | Developer time > machine time |
| 14 | **Generation** | Write programs that write programs |
| 15 | **Optimization** | Prototype first, polish later |
| 16 | **Diversity** | No "one true way" |
| 17 | **Extensibility** | Design for future |

#### Implementation Patterns

**Rule of Silence:**
```go
// Only show progress if operation will take >5s
if estimatedDuration > 5*time.Second {
    showProgressBar()
}
```

**Rule of Composition:**
```bash
# Enable piping and scripting
myapp list --json | jq '.items[].id'
```

**Rule of Representation:**
```yaml
# config.yaml - Data, not code
authorization:
  default_role: "viewer"
  admin_bypass: true
```

**Rule of Repair:**
```go
// Fail fast with clear message
if err := req.Validate(); err != nil {
    return fmt.Errorf("invalid request: %w", err)
}
```

#### Verification Checklist

Before marking architecture work complete:

- [ ] **Modularity:** Does each module have a single responsibility?
- [ ] **Clarity:** Can a new developer understand this in <30 min?
- [ ] **Composition:** Can output be piped to other tools?
- [ ] **Separation:** Are all tunable values in config?
- [ ] **Simplicity:** Did I add only necessary complexity?
- [ ] **Transparency:** Can the decision process be explained?
- [ ] **Robustness:** What happens on failure? Is it graceful?

**Reference:** [The Art of Unix Programming](http://www.catb.org/~esr/writings/taoup/html/) - Eric S. Raymond

### AI-Native Patterns 🔸 **Recommended**

**Philosophy:** "If AI can't remember it, write it to a file."

Patterns for working effectively with LLM coding assistants across session boundaries.

#### The Curse of Instructions

Research shows LLM instruction-following degrades non-linearly:

| Instructions | Accuracy | Notes |
|--------------|----------|-------|
| 50 | ~95% | Safe zone |
| 100-150 | ~85% | Threshold for reasoning models |
| 250 | ~70% | Degradation accelerates |
| 500 | ~68% | Even best models struggle |

**Solution:** Tiered guideline loading instead of monolithic instruction files.

#### Tiered Guideline System

| Tier | What | Token Budget | When Loaded |
|------|------|--------------|-------------|
| **Tier 0** | CLAUDE.md core rules | ~500 tokens | Always |
| **Tier 1** | Task-matched skills | ~500 each | When trigger matches |
| **Tier 2** | Memory bank, guides | On-demand | When referenced |
| **Tier 3** | Historical archive | Never auto | Explicit queries only |

**Key Insight:** Keep CLAUDE.md small (~500 tokens). Move detailed patterns to skills.

#### Memory Bank Structure

```
.aman-pm/
├── context.md                # Current state (AI reads, both update)
├── memory-bank/              # Hot memory (always loaded)
│   ├── architectural-constraints.md
│   ├── active-decisions.md
│   └── session-context.md
├── knowledge/                # Warm memory (on-demand)
│   ├── learnings.md
│   └── decisions.md
└── sessions/archive/         # Cold memory (historical)
```

#### Session Workflow Pattern

```
/aman start
    ├── Load Tier 0 (CLAUDE.md)
    ├── Read context.md
    ├── Semantic match: sprint items → relevant skills
    ├── Surface recent learnings
    └── Show last session recommendations
         ↓
[Work with task-matched skills]
         ↓
/aman end
    ├── Run session-end checklist
    ├── Capture learnings
    ├── Update memory bank
    └── Generate handoff document
```

#### Flashbulb Memory (Critical Insights)

Some learnings are too important to wait for session end. Capture immediately when:

**Trigger Phrases:**
- "Don't ever do that again"
- "This was a mistake"
- "Remember this for next time"
- "Never do X" / "Always do Y"

**Action:** Write to `learnings.md` immediately, don't wait for `/aman end`.

#### Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Writing TODOs in chat | Vanishes next session | Write to backlog file |
| Skipping `/aman start` | Lost context | Make it muscle memory |
| Too many rules in CLAUDE.md | Degraded adherence | Move to skills |
| Stale memory bank | Wrong context | Auto-update via /aman |
| Skipping session end | Lost learnings | Always run `/aman end` |

#### Best Practices

1. **Front-load context** - Put important info at start of files
2. **Use pointers, not copies** - Reference files, don't duplicate
3. **Keep token budgets** - <4000 tokens always-loaded
4. **Progressive disclosure** - Tier 1 always, Tier 2 on-demand
5. **Machine state in YAML** - Human summaries in Markdown
6. **Capture learnings immediately** - Don't wait for session end

**Key Question:** *"Will the next session have this context automatically?"*

**Reference:** [Agentic Coding Handbook - Memory Bank](https://tweag.github.io/agentic-coding-handbook/WORKFLOW_MEMORY_BANK/)

### Knowledge Lifecycle: Create → Curate → Retire 🔸 **Recommended**

**Philosophy:** Change is the only constant. Build for adaptability, not permanence.

| Phase | Focus | Actions |
|-------|-------|---------|
| **CREATE** | Capture knowledge | Specs, ADRs, learnings, validation guides |
| **CURATE** | Maintain value | Monthly health checks, consolidate duplicates, update stale refs |
| **RETIRE** | Remove rot | Deprecate with grace period, archive, delete |

**Monthly Health Check Ritual:**
- [ ] Any docs not updated in 60+ days?
- [ ] Any specs for deferred features?
- [ ] Any pending decisions unresolved?
- [ ] Any learnings that should become guides?

**Deprecation Protocol:**

```markdown
---
**Status:** ⚠️ DEPRECATED (YYYY-MM-DD)
**Superseded By:** [New Approach](link)
**Removal Date:** YYYY-MM-DD (3 months out)
**Reason:** {Why deprecated}
---
```

**Marie Kondo Principles for Documentation:**
1. Does this spark utility? (If not, archive)
2. Would I write this again today? (If not, deprecate)
3. Does someone own this? (If not, assign or archive)

### Search-Fix-Learn Debugging 🔸 **Recommended**

**Philosophy:** Bugs are learning opportunities. Every fix should produce institutional knowledge.

| Phase | Goal | Actions |
|-------|------|---------|
| **SEARCH** | Gather all relevant info | Parallel search: codebase, official docs, web, issue trackers. Don't guess—investigate. |
| **FIX** | Apply incremental fixes | Test one change at a time. Document what didn't work. |
| **LEARN** | Convert to knowledge | Create RCA if novel. Add to issues database if pattern. Update docs if gap found. |

**RCA Triggers (When to Create):**

| Trigger | Action |
|---------|--------|
| CI/CD failure (not simple config) | RCA required |
| Same error class twice | RCA required (recurring pattern) |
| Investigation > 30 minutes | Consider RCA |
| Workaround used | Document in issues database |

**RCA Template:**

```markdown
# RCA-XXX: {Title}

## Summary
{What happened, when, impact}

## Root Cause Analysis (5 Whys)
1. Why? → Because...
2. Why? → Because...
(Continue until root cause found)

## Prevention Measures
- [ ] {Measure 1} - Status: implemented / pending
- [ ] {Measure 2} - Status: implemented / pending

## Related Items
- DEBT-XXX (if follow-up needed)
- ISSUE-XXX (if pattern documented)
```

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
   - Use Flashbulb Memories for critical learnings (see Part 6)

4. **Never skip `/aman end`**
   - Learnings are lost
   - Next session has no context

5. **Run sync periodically**
   - Keep state clean
   - Archive done items

6. **Keep CLAUDE.md high-signal, not artificially short**
   - DO NOT remove critical rules to meet arbitrary line limits
   - Move detailed procedures to skills, keep critical rules in CLAUDE.md
   - Focus on signal quality, not line count (350 lines is fine if all high-signal)

### Anti-Patterns

1. **Writing TODOs in chat**
   - Vanishes next session
   - Write to files instead

2. **Skipping session start**
   - Lost context from previous work
   - Always read index first

3. **Removing rules to shorten CLAUDE.md**
   - Removes critical guidance to meet arbitrary limits
   - Keep high-signal content; move procedures to skills

4. **Manual index editing**
   - index.yaml is auto-generated
   - Use `/aman sync`

5. **Stale memory bank**
   - Old learnings clutter context
   - Archive after 4 weeks (can automate in `/aman end`)

6. **Index-as-Source-of-Truth**
   - **Wrong:** Manually editing `index.yaml` counts and status
   - **Right:** Edit backlog files, regenerate index via `/aman sync`

7. **Placeholder Validation Guides**
   - **Wrong:** `TOKEN=<your-token-here>` requiring human interpretation
   - **Right:** `TOKEN=$(./scripts/get-token.sh)` with acquisition method

8. **Undefined Deferral**
   - **Wrong:** "We'll fix this later" with no tracking
   - **Right:** DEBT-XXX entry with priority, effort, and deadline

9. **AI Self-Validation**
   - **Wrong:** AI marks feature complete after running tests
   - **Right:** AI pauses at Gate 4, waits for human "F## validated"

10. **Implicit Tool Versions**
    - **Wrong:** "Use golangci-lint" without version
    - **Right:** "Use golangci-lint v2.7.2 (pinned in config)"

11. **Learnings Without Prevention**
    - **Wrong:** "Learned that X causes Y"
    - **Right:** "Learned X causes Y. Prevention: Add CI rule Z. See RCA-XXX."

12. **Performance as Afterthought**
    - **Wrong:** "We'll optimize later"
    - **Right:** Performance budget in spec, validated before completion

### Interface-First Specification 🔸 **Recommended**

**Philosophy:** "Can someone implement this without seeing the current code?"

Design modules as black boxes with clean, documented APIs. Replaceability is a quality metric.

| Principle | Application |
|-----------|-------------|
| **Black Box** | Hide implementation, expose API only |
| **Replaceable** | Module can be rewritten using only interface docs |
| **Single Responsibility** | One module = one person can own it |
| **Primitives First** | Build complexity through composition, not complicated base types |

**Verification Questions:**
1. Can you describe this module's purpose without mentioning implementation?
2. Could a new developer rewrite this in a week using only the interface docs?
3. Can you explain the module's purpose in one sentence?

**Interface Documentation Pattern:**

```go
// Searcher provides search capability over indexed content.
// Thread-safe for concurrent calls.
type Searcher interface {
    // Search returns results matching the query, sorted by relevance.
    // Returns at most limit results. Returns empty slice if no matches.
    Search(ctx context.Context, query Query) ([]Result, error)
}
```

**Red Flags:**
- API exposes internal types (leaky abstraction)
- Module too complex for one person to own
- Hard-coded dependencies instead of interfaces
- Changes cascade across multiple modules

### Performance Budgets 🔸 **Recommended**

**Philosophy:** "Performance is a feature, not an afterthought."

Define performance targets upfront in specs, not post-implementation.

| Operation Type | Target | User Perception |
|----------------|--------|-----------------|
| Instant feedback | < 100ms | "Instant" |
| Interactive | < 1s | "Responsive" |
| Background | < 10s | "Working" |
| Batch | Document | "Expected" |

**Spec Template Addition:**

```markdown
## Performance Requirements

| Metric | Target | Measurement |
|--------|--------|-------------|
| API Response | < 200ms p95 | Load test with k6 |
| Page Load | < 3s | Lighthouse audit |
| Query Time | < 50ms | DB query profiling |
```

**Performance-as-Feature Rules:**
1. Define targets in spec, not after implementation
2. Measure before declaring "done"
3. Budget includes all overhead (auth, logging, serialization)
4. Document degradation strategy if budget exceeded

### Graceful Degradation ⚪ **Optional**

**Philosophy:** "A partially working system is better than a completely broken one."

Design systems to degrade gracefully when components fail.

| Level | Capability | User Experience |
|-------|------------|-----------------|
| Full | All features operational | Normal operation |
| Degraded | Core features only | "Some features unavailable" |
| Minimal | Read-only / static | "Limited functionality" |
| Offline | Cached data only | "Working offline" |

**Error Message Design:**

```
❌ BAD: "Error: connection refused"

✅ GOOD:
⚠ External service unavailable

The payment processor is not responding.

To fix:
  1. Check if service is running
  2. Verify network connectivity
  3. Try again in 30 seconds

Continuing with cached data for now...
```

**Design Rules:**
1. Never fail completely if partial operation is possible
2. Always tell user what's wrong AND how to fix it
3. Log failures with context for debugging
4. Provide fallback behavior when external dependencies fail

### Unix Philosophy Verification 🔸 **Recommended**

**Philosophy:** "Simple parts, clean interfaces, composable tools."

Apply these 17 principles from Unix Philosophy to AmanPM artifacts:

| Category | Principles |
|----------|------------|
| **Design** | Modularity, Clarity, Composition, Separation, Simplicity |
| **Build** | Parsimony, Transparency, Robustness, Representation, Least Surprise |
| **Run** | Silence, Repair, Economy, Generation, Optimization, Diversity, Extensibility |

**Verification Checklist (Before Major Deliverables):**

- [ ] **Modularity:** Does each component have a single responsibility?
- [ ] **Clarity:** Can a new developer understand this in <30 min?
- [ ] **Composition:** Can output be piped/chained to other tools?
- [ ] **Separation:** Are all tunable values in config, not code?
- [ ] **Simplicity:** Did I add only necessary complexity?
- [ ] **Transparency:** Can the decision process be explained?
- [ ] **Robustness:** What happens on failure? Is it graceful?
- [ ] **Replaceability:** Can this be swapped without changing callers?

**Quick Reference:**

```
┌─────────────────────────────────────────────────────┐
│              UNIX PHILOSOPHY QUICK REF              │
├─────────────────────────────────────────────────────┤
│ DESIGN: Modular, Clear, Composable, Separate       │
│ BUILD:  Parsimony, Transparent, Robust, Data>Logic │
│ RUN:    Silent, Repair fast, Economical, Extensible│
├─────────────────────────────────────────────────────┤
│ KEY QUESTION: "Can this be understood and replaced │
│               by someone who's never seen it?"     │
└─────────────────────────────────────────────────────┘
```

### The 90/9/1 Rule ⚪ **Optional**

**Philosophy:** "Optimize for the common case, not edge cases."

Design for the 90% case first, then handle the remaining 10%.

| Segment | Need | Design Focus |
|---------|------|--------------|
| **90%** | No configuration | Works out of the box |
| **9%** | Minimal tweaks | Simple config file |
| **1%** | Full customization | Extension points |

**Application to AmanPM:**

```yaml
# For the 90%: No config file needed. Just `/aman start`.

# For the 9%: Simple .aman-pm/config.yaml tweaks
sprints:
  default_length: 7  # Changed from 14

# For the 1%: Custom templates, hooks, integrations
templates:
  feature: "custom-feature.yaml"
hooks:
  session_start: "scripts/my-startup.sh"
```

**Design Rules:**
1. Default behavior should serve 90% of users
2. Configuration should only be needed for deviations
3. Power user features should not complicate the basic experience
4. Document what most users need, not what's possible

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
/aman checklist    → list/run checklists
/aman pre-commit   → pre-commit killer items (7 items)
/aman feature-gate → feature complete killer items (9 items)
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
| `.aman-pm/checklists/` | Layer 1 killer items checklists |
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

### 4. Session Hygiene Check (Drift Detection)

Check `session_hygiene` in `index.yaml`:

| Check | Action |
|-------|--------|
| `last_session_ended_cleanly: false` | Show warning banner |
| `sessions_since_learning_capture >= 3` | Prompt for reflection |
| Sprint stalled (no progress 3+ sessions) | Alert and suggest review |

Then set `last_session_ended_cleanly: false` (will be reset by `/aman end`).

### 5. Stale Work Detection

Flag items needing attention:
- in_progress > 3 days: Warning
- in_progress > 7 days: Alert
- P0 items not started: Critical

### 6. Focus Recommendation

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
# 1. Create directory structure (note: subdirectories vary by type)
mkdir -p .aman-pm/{sprints/{active,history},sessions/{active,archive},knowledge,templates,validation}
mkdir -p .aman-pm/backlog/epics/{active,done}
mkdir -p .aman-pm/backlog/features/{active,done,deferred}
mkdir -p .aman-pm/backlog/tasks/{active,done}
mkdir -p .aman-pm/backlog/bugs/{active,resolved}
mkdir -p .aman-pm/backlog/spikes/{active,done}
mkdir -p .aman-pm/backlog/debt/{active,resolved}

# 2. Create v3.0 evolved directories
mkdir -p .aman-pm/rearchitecture/{active,completed}
mkdir -p .aman-pm/memory-bank

# 3. Create skill structure
mkdir -p .claude/skills/aman/{commands,shared}

# 4. Initialize knowledge files (cold storage)
cat > .aman-pm/knowledge/learnings.md << 'EOF'
# Project Learnings

## Active Learnings (Last 4 Weeks)

EOF

# 5. Initialize memory-bank (hot memory)
cat > .aman-pm/memory-bank/architectural-constraints.md << 'EOF'
# Architectural Constraints (Always Active)

> **Purpose:** Critical constraints that must be respected in ALL implementations.
> This file is loaded at every `/aman start`.

## Core Constraints

<!-- Add your project's architectural constraints here -->
<!-- Example: -->
<!-- - All authentication flows through {Identity Provider} -->
<!-- - All permission checks go through {Authorization Service} -->
<!-- - API endpoints must use {specific middleware pattern} -->

EOF

# 6. Create config.yaml (see Part 3)

# 7. Create skill files (see templates above)

# 8. Update CLAUDE.md with PM section
```

### Philosophy Skills (Optional but Recommended)

Create these skills for architecture and design guidance:

#### Black-Box Design Skill

Create `.claude/skills/black-box-design/SKILL.md`:

````markdown
---
name: black-box-design
description: |
  Eskil Steenberg's principles for building large-scale systems.
  Load when doing module extraction, interface definition, or refactoring.
tags: [architecture, refactoring, interfaces]
---

# Black Box Design Skill

## Core Principles

1. **Black Box Interfaces** - Hide implementation, expose API only
2. **Replaceable Components** - Rewritable from interface alone
3. **Single Responsibility** - One module = one person can own it
4. **Primitive-First Design** - Build complexity through composition

## Verification Questions

Before marking module work complete:

- [ ] Can you describe this module without mentioning implementation?
- [ ] Could a new dev rewrite this using only the interface docs?
- [ ] Can you explain this module's purpose in one sentence?
- [ ] Can you list 3-5 core types that flow through?

## Red Flags

| Red Flag | Fix |
|----------|-----|
| API exposes internal types | Define interface types |
| Module too complex | Split into focused modules |
| Hard-coded dependencies | Use interfaces + injection |
````

#### Unix Philosophy Skill

Create `.claude/skills/unix-philosophy/SKILL.md`:

````markdown
---
name: unix-philosophy
description: |
  The 17 rules of Unix Philosophy for software architecture.
  Load when doing architecture refactoring, CLI improvements, or interface design.
tags: [architecture, cli, design]
---

# Unix Philosophy Skill

## The 17 Rules (Summary)

**Design:** Modularity, Clarity, Composition, Separation, Simplicity
**Quality:** Parsimony, Transparency, Robustness, Representation, Least Surprise
**Operations:** Silence, Repair, Economy, Generation, Optimization, Diversity, Extensibility

## Verification Checklist

- [ ] Does each module have a single responsibility?
- [ ] Can a new developer understand this in <30 min?
- [ ] Can output be piped to other tools?
- [ ] Are all tunable values in config?
- [ ] Did I add only necessary complexity?
````

#### AI-Native Patterns Skill

Create `.claude/skills/ai-native-patterns/SKILL.md`:

````markdown
---
name: ai-native-patterns
description: |
  AI-native development patterns for LLM coding assistants.
  Load when doing PM work, memory improvements, or session workflow.
tags: [pm-system, memory, session]
---

# AI-Native Patterns Skill

## Core Principle

> "If AI can't remember it, write it to a file."

## Tiered Guideline System

| Tier | What | When Loaded |
|------|------|-------------|
| Tier 0 | CLAUDE.md core | Always |
| Tier 1 | Task-matched skills | On trigger |
| Tier 2 | Memory bank | On-demand |
| Tier 3 | Archive | Never auto |

## Session Workflow

```
/aman start → Load context, surface memory
[Work with task-matched skills]
/aman end → Capture learnings, generate handoff
```

## Flashbulb Triggers

Capture immediately when user says:
- "Don't ever do that again"
- "This was a mistake"
- "Never do X" / "Always do Y"
````

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
2. Keep CLAUDE.md high-signal (move procedures to skills, keep rules)
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

### Failure Scenarios (Narrative Walkthroughs)

These walkthroughs help you understand what happens when things go wrong and how to recover.

---

#### Scenario 1: "I forgot `/aman end` for 3 days"

**What Happened:**
- 3 development sessions completed without the session end ritual
- Learnings from those sessions were never captured
- `index.yaml` shows stale data

**Symptoms:**
- `/aman start` shows outdated sprint progress
- Recent learnings missing from memory surfacing
- Git log shows work commits but no session handoffs in `sessions/archive/`
- Drift detection warns: "Previous session did not run `/aman end`"

**Consequences:**
- Valuable discoveries lost (unless you remember them now)
- Next AI session has incomplete context
- Sprint metrics are inaccurate

**Recovery:**
1. Run `/aman sync` to update index from current file state
2. Manually review git commits from the past 3 days:
   ```bash
   git log --since="3 days ago" --oneline
   ```
3. Add missed learnings to `learnings.md` with backdated entries:
   ```markdown
   ### 2026-01-29 (Recovered)
   - **Learning**: {what you remember learning}
     - **Context**: {reconstructed from git commits}
   ```
4. Create a summary handoff for the missed period
5. Run `/aman sync` again to update memory section

**Prevention:**
- Enable drift detection (`session_hygiene` in `index.yaml`)
- Set a calendar reminder or IDE hook
- Make `/aman end` a habit (muscle memory)

---

#### Scenario 2: "index.yaml is corrupted/invalid YAML"

**What Happened:**
- Manual edit introduced a syntax error (missing colon, bad indentation)
- LLM wrote malformed YAML
- Merge conflict left corruption

**Symptoms:**
- `/aman start` fails or shows errors
- YAML parsers reject the file:
  ```
  yaml.scanner.ScannerError: mapping values are not allowed here
  ```
- Claude Code can't parse project state

**Consequences:**
- All `/aman` commands fail
- No visibility into sprint or backlog state
- Session start ritual broken

**Recovery:**
1. **Option A: Restore from git**
   ```bash
   git checkout HEAD~1 -- .aman-pm/index.yaml
   /aman sync
   ```

2. **Option B: Regenerate from scratch**
   ```bash
   rm .aman-pm/index.yaml
   /aman sync  # or run sync.sh script
   ```

3. **Validate before committing:**
   ```bash
   python -c "import yaml; yaml.safe_load(open('.aman-pm/index.yaml'))"
   ```

**Prevention:**
- **Never manually edit `index.yaml`** (use `/aman sync`)
- Use the deterministic sync script for regeneration
- Add a pre-commit hook to validate YAML syntax

---

#### Scenario 3: "Sprint ended but items still stuck in active/"

**What Happened:**
- `/aman sprint-end` ran but incomplete items weren't properly handled
- Items with `status: ready` or `in_progress` remain in `active/` folders
- Items still have `sprint: N` where N is a closed sprint

**Symptoms:**
- Items in `backlog/*/active/` folders with stale sprint assignments
- `/aman status` shows mismatched counts
- New sprint planning is confused by orphaned items

**Consequences:**
- Backlog becomes messy
- Sprint velocity calculations are wrong
- Items may be forgotten

**Recovery:**
1. **Find orphans:**
   ```bash
   grep -r "sprint: 3" .aman-pm/backlog/*/active/*.yaml
   ```

2. **For each orphaned item, decide disposition:**
   - **Carry to next sprint:** Update `sprint: 4`
   - **Return to backlog:** Set `sprint: null` and `status: backlog`
   - **Close as won't-do:** Move to `done/` with note

3. **Update each file's frontmatter accordingly**

4. **Run `/aman sync`** to regenerate index

**Prevention:**
- Always complete the sprint-end ceremony fully
- `/aman sprint-end` should prompt for incomplete items (see Part 7)
- Review the "Sprint End Checklist" before closing

---

## Appendix A: Quick Reference Card

### Essential Commands

```
/aman start        Begin session (surfaces memory + constraints)
/aman              Check status
/aman end          End session (capture learnings + handoff)

/aman add feature "Title"   Create feature
/aman done FEAT-001         Mark complete

/aman groom                 Analyze backlog (DoR + WSJF ranking)
/aman sprint-start "Goal"   Start sprint (with optional streams)
/aman sprint-end            End sprint (retrospective + velocity)
/aman rearch                Create rearchitecture proposal
```

### Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | State + memory (auto-generated) |
| `.aman-pm/config.yaml` | Configuration |
| `.aman-pm/memory-bank/` | Hot memory (constraints, active decisions) |
| `.aman-pm/knowledge/learnings.md` | Cold storage (persistent learnings) |
| `.aman-pm/sprints/active/N/` | Current sprint (with streams) |
| `.aman-pm/backlog/` | All work items |
| `.aman-pm/rearchitecture/` | Architecture change proposals |

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

### v3.0 Evolved Features

- [ ] `memory-bank/` directory with architectural constraints 🔹
- [ ] `rearchitecture/` directory structure 🔸
- [ ] DoR checklist applied to grooming 🔹
- [ ] DoD checklist applied to sprint-end 🔹
- [ ] WSJF prioritization in `/aman groom` 🔸
- [ ] Parallel streams in sprint items 🔸
- [ ] Mid-sprint change budget tracking 🔹
- [ ] Rearchitecture proposal workflow 🔸

### v4.0 Production-Hardened Features

- [ ] Premium Engineering mindset documented in CLAUDE.md 🔹
- [ ] Two-Layer Knowledge Architecture (Constitution + Discovery) 🔹
- [ ] SSOT Hierarchy with conflict resolution rules 🔹
- [ ] Engineering Workflow Contract (5-gate quality system) 🔹
- [ ] Do It Now Philosophy with threshold-based decisions 🔹
- [ ] Human Validation Gate (AI pauses at Gate 4) 🔹
- [ ] Search-Fix-Learn debugging workflow with RCA triggers 🔸
- [ ] Knowledge Lifecycle: Create → Curate → Retire 🔸
- [ ] Interface-First Specification principles 🔸
- [ ] Performance Budgets in feature specs 🔸
- [ ] Unix Philosophy verification checklist 🔸
- [ ] Graceful Degradation patterns ⚪
- [ ] 90/9/1 Rule applied to configuration ⚪
- [ ] Tech Debt tracking (DEBT-XXX entries) 🔹
- [ ] RCA/Postmortem infrastructure for learning capture 🔸

### v5.0 Checklist Manifesto Features

- [ ] `.aman-pm/checklists/` directory structure 🔹
- [ ] `checklists/index.md` registry with taxonomy 🔹
- [ ] CL-IMPL-DC-PreCommit.md (7 killer items) 🔹
- [ ] CL-IMPL-DC-FeatureComplete.md (9 killer items) 🔹
- [ ] CL-SEC-DC-SecurityReview.md (auth/security code) 🔸
- [ ] CL-DBM-RD-MigrationValidation.md (database migrations) 🔸
- [ ] `/aman checklist` commands in skill 🔹
- [ ] `checklist-runner` skill for AI automation 🔹
- [ ] Layer 2 quality-checklist.md triggered in workflow 🔹
- [ ] Makefile targets for checklists 🔸
- [ ] Auto-triggers at pause points (CLAUDE.md) 🔹

### v5.1 Philosophy Skills Features

- [ ] `.claude/skills/black-box-design/SKILL.md` 🔸
- [ ] `.claude/skills/unix-philosophy/SKILL.md` 🔸
- [ ] `.claude/skills/ai-native-patterns/SKILL.md` 🔸
- [ ] Skill triggers added to CLAUDE.md 🔸
- [ ] Skill registry updated with philosophy skills 🔸
- [ ] Black-Box Design section in adoption guide 🔸
- [ ] Unix Philosophy section in adoption guide 🔸
- [ ] AI-Native Patterns section in adoption guide 🔸

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
| **Memory Approach** | File-based, git-versioned | Workspace integration | Markdown artifacts | Embedded database (LadybugDB) |
| **Agent Model** | Single agent with skills | ~19 specialized agents (v6 alpha) | Tool-agnostic CLI | Multi-agent with QA |
| **Workflow** | Session → Sprint → Release | Plan → Architect → Implement | Specify → Plan → Tasks | Create → Code → QA loop |
| **Human Control** | High | High | Medium | Low |
| **Target User** | Solo/small teams | Enterprise teams | Individual devs | Teams wanting automation |
| **Learning Capture** | ✅ Explicit | ⚠️ Implicit | ❌ Not addressed | ⚠️ DB-based |
| **Cross-Model** | ✅ Data portable (Claude executes) | ⚠️ IDE-specific | ✅ 16+ agents | ❌ Claude-only |
| **Velocity Tracking** | ✅ Built-in | ❌ None | ❌ None | ❌ None |
| **Zero Infrastructure** | ✅ Yes | ✅ Yes | ✅ Yes | ⚠️ Embedded DB |

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
- [Scott Logic (2025)](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html): Spec Kit showed high review overhead in practice compared to iterative prompting

---

## Summary

AmanPM solves the AI memory problem through structured file-based persistence while embedding production-proven engineering discipline. The key principles:

**Foundation:**
1. **"If AI can't remember it, write it to a file"** - Structured persistence
2. **Human Director, AI Executor** - Clear role separation
3. **Premium Engineering Mindset** - "We don't ship 'good enough'. We ship excellent."

**Architecture:**
4. **Two-Layer Knowledge Architecture** - Constitution (CLAUDE.md) + Discovery (skills/guides)
5. **Three-layer file architecture** - Strategic → Operational → Auto-generated
6. **Three-tier memory** - Hot memory-bank → Cold knowledge → Archive
7. **SSOT Hierarchy** - Explicit conflict resolution rules

**Quality:**
8. **Five-Gate Quality System** - Spec → TDD → CI Parity → Validation Guide → Human Validation
9. **Do It Now Philosophy** - Threshold-based deferral (< 30 min = DO NOW)
10. **Search-Fix-Learn Debugging** - Every bug produces institutional knowledge

**Process:**
11. **Session discipline** - Explicit start and end rituals
12. **Sprint-based agile** - Grooming, planning, mid-sprint changes, retrospectives
13. **Quality gates** - Definition of Ready + Definition of Done
14. **Cross-model portability** - Works with Claude, GPT-5, Gemini

Start with session rituals, add backlog tracking, then sprints. Full adoption takes ~4 weeks.

### Unique Value Proposition

> "AmanPM is the knowledge-first AI development framework for developers who want their AI to remember what it learned, not just what it built."

**Key Differentiators:**
- Only framework with explicit learning capture
- Git-versioned audit trail without infrastructure
- Cross-model portable (no vendor lock-in)
- Velocity tracking for planning confidence

---

*Version 4.0.0 | Production-Hardened AmanPM with Engineering Discipline | 2026-02-02*

**Changelog v4.0.0:**
- **NEW: Premium Engineering Mindset** (Part 1) - "We don't ship 'good enough'. We ship excellent."
- **NEW: Two-Layer Knowledge Architecture** (Part 1) - Constitution (CLAUDE.md) + Discovery (skills/guides) separation
- **NEW: SSOT Hierarchy & Conflict Resolution** (Part 1) - Explicit rules for when files disagree
- **NEW: Engineering Workflow Contract** (Part 4) - Five-Gate Quality System with human validation gate
- **NEW: Do It Now Philosophy** (Part 10) - Threshold-based deferral decisions (30min/1.5hr/4hr)
- **NEW: Knowledge Lifecycle** (Part 10) - Create → Curate → Retire with monthly health checks
- **NEW: Search-Fix-Learn Debugging** (Part 10) - Systematic debugging with RCA triggers
- **NEW: Interface-First Specification** (Part 10) - Replaceability as a quality metric
- **NEW: Performance Budgets** (Part 10) - Performance targets defined in specs, not post-implementation
- **NEW: Graceful Degradation** (Part 10) - Partial operation better than complete failure
- **NEW: Unix Philosophy Verification** (Part 10) - 17-rule checklist for architecture decisions
- **NEW: The 90/9/1 Rule** (Part 10) - Design for common case, not edge cases
- **FIXED: CLAUDE.md length guidance** - Changed from "150 lines" to "high-signal, not artificially short"
- **EXPANDED: Anti-Patterns** - From 5 to 12 items including placeholders, implicit versions, AI self-validation
- **ENHANCED: Summary** - Now reflects all 14 key principles organized by category
- **ENHANCED: Implementation Checklist** (Appendix B) - Added v4.0 Production-Hardened Features
- **ENHANCED: Table of Contents** - Added all new section references with anchor links
- Synthesized recommendations from Claude, Gemini, and GPT-4 analyses
- Incorporated patterns from AmanMCP philosophy documents

**Changelog v3.0.0:**
- **NEW: Definition of Ready (DoR)** (Part 4) - Quality gate checklist before items enter sprint
- **NEW: Definition of Done (DoD)** (Part 4) - Completion criteria for work items
- **NEW: WSJF Prioritization** (Part 5) - Weighted Shortest Job First prioritization method
- **NEW: Three-Tier Memory Architecture** (Part 6) - Hot (memory-bank/) vs Cold (knowledge/) distinction
- **NEW: Memory Bank** (Part 6) - Always-loaded architectural constraints and active decisions
- **NEW: Parallel Development Streams** (Part 7) - Multi-workstream sprint management
- **NEW: Mid-Sprint Change Management** (Part 7) - Change budget protocol with intake/emergency lanes
- **NEW: Rearchitecture Workflow** (Part 7) - REARCH-XXX proposal process for architectural pivots
- **ENHANCED: Sprint Retrospective Template** (Part 7) - Stream analysis and capacity utilization metrics
- **ENHANCED: `/aman groom`** (Part 5) - Now includes DoR validation and WSJF ranking
- **ENHANCED: Bootstrap Script** (Part 11) - Added rearchitecture/ and memory-bank/ directories
- **ENHANCED: Implementation Checklist** (Appendix B) - Added new v3.0 components
- Updated Table of Contents with new section references
- Updated Summary to reflect three-tier memory architecture

**Changelog v2.1.1:**
- Added **Primary Workflow Model** section (Part 1) - clarifies Claude executes, other models review
- Clarified **SSOT semantics** - backlog files are canonical, `index.yaml` is derived cache
- Fixed **epics ownership** note - stored in operational layer but strategically human-owned
- Fixed **bootstrap script** - correct subdirectories per item type (bugs→resolved, features→deferred, etc.)
- Updated **competitor comparison** - Auto-Claude uses LadybugDB (not FalkorDB), BMAD ~19 agents (v6 alpha)
- Removed unverified "70K" token claim - replaced with general effective-context guidance
- Fixed Scott Logic citation - removed "10x slower" claim, now says "high review overhead"
- Added **Work Item Hierarchy** and **Epics vs Features** explanation (Part 4)

**Changelog v2.1.0:**
- Added **System Invariants** section (Part 1) - formal rules that must never be violated
- Added **Drift Detection** with `session_hygiene` fields in `index.yaml` schema (Part 3)
- Added **Session Hygiene Warnings** to `/aman start` (Part 6) - warns when sessions end uncleanly
- Added **Flashbulb Memories** (Part 6) - immediate learning capture for critical feedback
- Added **Script-Based Sync** (Part 5) - deterministic index generation for >50 item projects
- Added **Failure Case Walkthroughs** (Part 12) - narrative scenarios for common failures
- Updated Best Practices to reference Flashbulb Memories
- Updated `commands/start.md` template with drift detection step

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
