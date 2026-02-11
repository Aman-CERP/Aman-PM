# AmanPM

**AI-native, file-based project management for developers working with AI coding assistants.**

> AmanPM is an AI-native, file-based project management system that lives inside your repository, solving the "context amnesia" problem by giving AI coding assistants structured memory persistence, quality gates, and workflow automation — so the human directs while the AI executes.

> [!NOTE]
> AmanPM is in active development as part of building **[AmanERP](https://github.com/Aman-CERP)** — an AI-native ERP for SMBs and MSMEs. AmanPM was born out of the real-world need to manage complex, multi-sprint development with AI assistants, and continues to evolve alongside AmanERP.

---

## The Problem

AI coding assistants are powerful executors, but they forget everything between sessions. Every new conversation starts from zero — no memory of decisions made, patterns established, bugs fixed, or lessons learned.

**The result?** Repeated mistakes, architectural drift, lost context, and wasted time re-explaining things the AI already "knew" yesterday.

## The Solution

AmanPM solves this by treating **project memory as infrastructure**:

- **If AI can't remember it, write it to a file**
- **Spec before code** — no implementation without specification
- **AI proposes, human approves** — clear validation gates
- **Single Source of Truth** — link, never duplicate

```
/aman start → "go" / "do it" / "do those 3" → /aman end
```

That's it for 90% of sessions. You say what you want. The AI figures out the rest.

---

## Core Features

| Feature | Description |
|---------|-------------|
| **3-Tier Memory System** | Hot (always loaded), Warm (session start), Cold (on-demand) — AI never loses critical context |
| **Sprint Management** | Full sprint lifecycle with WSJF prioritization, grooming, execution, and retrospectives |
| **Quality Gates** | Two-layer checklist system inspired by *The Checklist Manifesto* — catches what expertise misses |
| **Backlog Management** | Unified SSOT for features, tasks, bugs, tech debt, spikes, and epics |
| **Validation Workflows** | AI implements → AI pauses → Human validates → AI proceeds. Clear handoff boundaries |
| **Session Handoffs** | Structured session start/end rituals that capture learnings and maintain continuity |
| **Release Management** | SemVer versioning with automated changelog generation |
| **Learning Capture** | Discoveries and patterns are recorded and surfaced in future sessions |
| **Analytics Dashboard** | Next.js dashboard with burndown charts, velocity tracking, and sprint forecasting |

---

## How It Works

AmanPM lives entirely in your repository as a `.aman-pm/` directory:

```
.aman-pm/
├── context.md              # Current project state (updated each session)
├── workflow.md             # Process rules and protocols
├── config.yaml             # PM configuration
├── index.yaml              # Auto-generated status snapshot
│
├── backlog/                # ALL work items (unified SSOT)
│   └── items/              # Features, tasks, bugs, debt, spikes, epics
├── sprints/                # Sprint management (active + history)
├── memory-bank/            # Hot memory (always loaded at session start)
├── knowledge/              # Learning capture and patterns
├── validation/             # Feature validation guides
├── decisions/              # Architecture Decision Records
├── checklists/             # Quality gate checklists
├── changelog/              # Version history
└── sessions/               # Session handoffs
```

### The Two Pillars

**1. Simplify Without Sacrifice**
> Reduce complexity at every opportunity, but never at the cost of quality or capability.

**2. Lazy Engineer + Premium Engineering**
> Automate everything that can be automated, but the output must be excellent. "Lazy" means efficient, not careless.

---

## Quick Start

### Session Workflow

| Command | When | What Happens |
|---------|------|--------------|
| `/aman start` | Session beginning | Loads memory, shows numbered suggestions |
| "go", "do it" | Start work | AI begins executing top suggestion |
| "do those 3" | Batch work | Spawns parallel work on multiple items |
| `/aman` | Quick check | Status anytime |
| `/aman end` | Session end | Captures learnings, creates handoff |

### Natural Language Triggers

| You Say | What Happens |
|---------|--------------|
| "go", "do it", "yes" | Start suggestion #1 |
| "do 1, 3, 5" | Start multiple items in parallel |
| "fix all debt" | Filter and batch |
| "continue", "what's next" | Re-show suggestions |

### Sprint Lifecycle

```
/aman groom        →  /aman sprint-start  →  [work]  →  /aman sprint-end
(creates plan)        (kicks off sprint)                  (closes sprint)
```

---

## Architecture

### Hybrid Agent Architecture (v4.0)

AmanPM uses a **Thin Router Pattern** that dispatches to 5 specialized agents:

| Agent | Responsibility | Key Capability |
|-------|---------------|----------------|
| **Session Manager** | start, end, status, health | Memory surfacing, session lifecycle |
| **Sprint Manager** | groom, sprint-start, sprint-end | Interactive ceremonies, WSJF planning |
| **Backlog Manager** | add, done, move, backlog, sync | Deterministic item CRUD |
| **Quality Manager** | pre-commit, feature-gate, checklist | Read-only quality checks |
| **Release Manager** | release, report | Version management with confirmation |

### AI-Native Development Model

```
┌─────────────────────────────────────────────────────┐
│                 HUMAN (Director)                     │
│  • Vision & Intent    • Validation    • Approval     │
└───────────────────────┬─────────────────────────────┘
                        │ Natural Language
                        ▼
┌─────────────────────────────────────────────────────┐
│               AI ASSISTANT (Executor)                │
│  • Knowledge Mgmt   • Coding        • Documentation │
│  • Debugging         • Analysis      • Process       │
└─────────────────────────────────────────────────────┘
```

---

## Adoption Guide

See **[AMANPM-ADOPTION-GUIDE.md](AMANPM-ADOPTION-GUIDE.md)** for the comprehensive adoption guide covering:

- Philosophy & Foundations
- Directory Structure Setup
- Configuration & Schemas
- Work Item Templates
- Sprint Lifecycle
- Memory System
- Migration Strategies
- Progressive Adoption (Week 1-4)
- Claude Code Integration
- Troubleshooting

---

## Philosophy

AmanPM is built on battle-tested principles:

| Principle | Meaning |
|-----------|---------|
| **If AI can't remember it, write it to a file** | Explicit memory over implicit knowledge |
| **Spec before code** | No implementation without approved specification |
| **Single Source of Truth** | Link, never duplicate |
| **AI proposes, human approves** | Clear validation gates |
| **Do it now, not later** | Fix today what you'd fix tomorrow |
| **Simplify without sacrifice** | Reduce complexity while preserving quality |

---

## Status

🚧 **Active Development** — AmanPM is being actively developed and battle-tested daily as part of building [AmanERP](https://github.com/Aman-CERP), an AI-native ERP system for SMBs and MSMEs. Features, patterns, and workflows are continuously refined based on real-world usage.

---

## License

MIT

---

*Built with ❤️ by the [Aman-CERP](https://github.com/Aman-CERP) team*
