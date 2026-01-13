# AI-Native Project Management Guide

A comprehensive guide to the AmanMCP project management system.

---

## 1. Philosophy

### What is AI-Native PM?

AI-native project management treats AI as a **first-class team member**, not just a tool. This means:

- **Full context sharing** - AI has access to all project artifacts
- **Structured memory** - Learnings persist between sessions
- **Explicit handoffs** - Sessions have formal start/end boundaries
- **Accountability** - Every action is documented

### Core Principles

| Principle | Traditional PM | AI-Native PM |
|-----------|---------------|--------------|
| **Memory** | Human memory + notes | Structured knowledge base |
| **Handoffs** | Informal "where we left off" | Formal session documents |
| **Discovery** | Lost unless documented | Captured in learnings.md |
| **Planning** | Sprint planning meetings | Continuous AI-assisted grooming |
| **Tracking** | Manual status updates | Real-time item progression |
| **Audit** | Meeting notes, emails | Immutable session archives |

### Why This Matters

Traditional PM assumes humans remember context between sessions. AI doesn't. Without explicit memory systems, every session starts from scratch, wasting time re-establishing context.

AI-native PM solves this by:

1. **Persisting learnings** in structured files
2. **Surfacing context** at session start
3. **Capturing discoveries** immediately
4. **Creating accountability** through handoffs

---

## 2. System Architecture

### Directory Structure

```
.aman-pm/
├── README.md              # PM system overview
├── config.yaml            # Sprint & workflow configuration
├── index.yaml             # Auto-generated status snapshot + memory
├── schema.sql             # SQLite schema for queries
│
├── backlog/               # Work items (what to DO)
│   ├── epics/             # Strategic initiatives
│   ├── features/          # Scope-bounded deliverables
│   ├── tasks/             # Atomic work units
│   ├── bugs/              # Defects (active/ and resolved/)
│   ├── spikes/            # Research tasks
│   └── debt/              # Technical debt (active/ and resolved/)
│
├── product/               # What EXISTS (completed features)
│   └── F##-*.md           # Feature specs + validations
│
├── sprints/               # Time-boxed execution
│   ├── current            # Symlink to active sprint
│   ├── active/            # Current sprint details
│   └── history/           # Completed sprints (immutable)
│
├── sessions/              # Session accountability
│   ├── templates/         # Handoff template
│   ├── active/            # Current session
│   └── archive/           # Completed sessions (immutable)
│
├── knowledge/             # Persistent memory
│   ├── learnings.md       # Technical discoveries
│   ├── decisions.md       # Architecture decisions
│   ├── patterns.md        # Observed patterns
│   ├── velocity.json      # Historical velocity data
│   └── architecture-triggers.yaml
│
├── validation/            # Test data and results
├── postmortems/           # RCAs and incident analysis
├── processes/             # Workflow documentation
├── guides/                # This guide and others
└── templates/             # Item creation templates
```

### Work Item Hierarchy

```
EPIC (Strategic initiative - Human created)
  └── FEATURE (Scope-bounded deliverable)
        └── TASK (Atomic work unit)

Also tracked:
- BUG (Defect)
- SPIKE (Research/investigation)
- DEBT (Technical debt)
```

### Item Statuses

| Status | Description |
|--------|-------------|
| `backlog` | Not yet scheduled for any sprint |
| `ready` | Groomed and ready to be worked on |
| `in_progress` | Currently being worked on |
| `review` | Work complete, awaiting validation |
| `done` | Completed and validated |
| `archived` | Moved to archive |

### Priority Levels

| Priority | Description |
|----------|-------------|
| `P0` | Critical - blocks everything |
| `P1` | High - must complete this sprint |
| `P2` | Medium - should complete if capacity |
| `P3` | Low - nice to have |

### Complexity (T-Shirt Sizing)

| Size | Description |
|------|-------------|
| `XS` | Trivial, < 1 hour |
| `S` | Small, 1-2 hours |
| `M` | Medium, half day |
| `L` | Large, full day |
| `XL` | Extra large, multiple days |

---

## 3. Session Workflow

### The Lazy Workflow (3 Commands)

**For daily use, you only need to remember 3 commands:**

| Command | What Happens Automatically |
|---------|---------------------------|
| `/aman start` | Sync → Status → Memory → Recommendations |
| `/aman` | Sync → Status (quick mid-session check) |
| `/aman end` | Sync (with auto-archive) → Learnings → Handoff |

**Everything else is automatic:**

- **Sync** happens on start, status, and end - never run manually
- **Archive** happens during sync - done items auto-move to done/ folders
- **AI agents** update item status in files - sync picks up changes

**Your workflow:**

```
/aman start → [work, AI updates items] → /aman → [more work] → /aman end
```

### Starting a Session

```
/aman start
```

This command:
0. **[INVISIBLE] Auto-syncs** (scans files, auto-archives done items)

1. Checks environment (git status, uncommitted changes)
2. Reads `index.yaml` for current state
3. **Surfaces memory:**
   - Items flagged for review
   - Recent learnings (top 5)
   - Pending decisions
   - Architecture impacts
4. Shows sprint status and day number
5. Recommends focus based on priorities
6. Warns of stale items (in_progress > 3 days)

**Output includes:**

- Sprint banner with status
- Memory banners (prominent if items need review)
- Recommended focus for the session
- Quick commands

### During a Session

```
/aman               # Quick status check (auto-syncs)
/aman status        # Same as above
/aman backlog       # List backlog items
/aman done TASK-001 # Mark item complete + auto-archive + cascade
/aman add bug "Fix crash"  # Create items
```

**Best practices:**

- AI agents update item status directly in files - you rarely need `/aman done`
- Run `/aman` for quick status checks (syncs automatically)
- Capture discoveries as they happen
- Flag items that need human review
- Use the TodoWrite tool to track complex tasks

### Ending a Session

```
/aman end
```

This command:
0. **[INVISIBLE] Auto-syncs with auto-archive:**

- Scans files for status changes
- Moves done items from active/ to done/ folders
- Moves resolved bugs/debt to resolved/ folders
- Regenerates index.yaml

1. Summarizes work completed
2. **Prompts for learnings capture:**
   - Technical discoveries
   - Patterns that work/don't work
   - Decisions and rationale
3. **Prompts for items to flag:**
   - Items needing human review
   - Blocked items
   - Architectural concerns
4. Generates session handoff document
5. Archives handoff to `sessions/archive/`
6. Shows next session recommendations

### Session Handoff

Every session produces an **accountability document** that includes:

| Section | Content |
|---------|---------|
| Session Summary | Goal and outcome |
| Work Completed | Items progressed, files modified |
| Discoveries & Learnings | What was learned |
| Blockers & Issues | Problems encountered |
| Next Session Recommendations | What to do next |
| Verification Checklist | Quality gates |
| Session Metrics | Quantitative data |
| Accountability Signature | Who and when |

Handoffs are stored in `.aman-pm/sessions/archive/sprint-N/` grouped by sprint number.

---

## 4. Sprint Workflow

### Sprint Cycle (1 Week)

| Day | Activity | Commands |
|-----|----------|----------|
| 0 (Planning) | Backlog grooming, sprint goal | `/aman groom`, set goal |
| 1-4 (Execution) | Daily work sessions | `/aman start`, `/aman end` |
| 5 (Ceremonies) | Review, retrospective, close | `/aman report`, `/aman sprint-end` |

### Sprint Planning

#### 1. Backlog Grooming (`/aman groom`)

AI-assisted analysis of backlog health:

- Identifies stale items (no activity > 2 weeks)
- Finds missing estimates or priorities
- Suggests priority adjustments
- Flags architecture impacts

**With impact analysis:**

```
/aman groom --impact embedding-change
```

Analyzes how planned architecture changes affect backlog items.

#### 2. Start Sprint (`/aman sprint-start`)

Create new sprint with directory structure and files. Sprint number is auto-incremented:

```bash
/aman sprint-start "Stabilization + Search Quality"   # Auto-creates next sprint
/aman sprint-start 5 "Goal"                           # Override: explicit number
```

This command:

1. Validates no active sprint (or --force)
2. Calculates next sprint number from config (`sprint.current + 1`)
3. Creates `sprints/active/{N}/` directory
4. Generates `goal.md` from template
5. Generates `items.yaml` (empty, ready for items)
6. Updates `config.yaml` with `sprint.current: {N}`
7. Updates `index.yaml` with current_sprint section

#### 3. Add Items to Sprint

```bash
/aman sprint-add SPIKE-001
/aman sprint-add FEAT-RR1
```

This updates the item's `sprint` field and adds it to `items.yaml`.

#### 4. Goal Setting

Sprint goal in `sprints/active/{N}/goal.md`:

```markdown
# Sprint 2 Goal

## Theme
AI-Native Project Management Foundation

## Success Criteria
- [ ] All PM skills implemented
- [ ] Session workflow documented
- [ ] 80%+ items completed

## Capacity
- AI sessions: 10
- Human review hours: 5

## Risks
- May discover more scope during implementation
```

#### 3. Item Assignment

Add items to sprint in `sprints/active/{N}/items.yaml`:

```yaml
items:
  - id: FEAT-001
    priority: P0
  - id: TASK-001
    priority: P0
    parent: FEAT-001
```

### Sprint Execution

Items flow through statuses:

```
backlog → ready → in_progress → review → done
```

Daily pattern:

1. `/aman start` - Get context
2. Work on items - Update status as you go
3. `/aman end` - Capture and handoff

### Sprint End (`/aman sprint-end`)

Close the current sprint with full ceremony:

```bash
/aman sprint-end              # Full ceremony
/aman sprint-end --quick      # Minimal (archive only)
```

**Actions performed:**

1. **Generate Report** - Same as `/aman report`
2. **Prompt for Retrospective** - What went well/didn't, improvements
3. **Update Velocity** - Add sprint metrics to `velocity.json`
4. **Handle Incomplete Items** - Mark as carried over
5. **Archive Sprint** - Move to `sprints/history/{N}/`
6. **Update Config** - Clear/increment sprint number
7. **Smart Prompt: Start Next Sprint** - Prompts "Start Sprint N+1?" with goal input

**Carried-over items:**

- Items with `in_progress` or `ready` status are flagged
- They appear in next sprint planning
- No manual intervention needed

---

## 5. Memory System

### Why Memory Matters

Without explicit memory, AI forgets everything between sessions. The `knowledge/` directory is AI's long-term memory, enabling:

- **Continuity** - Pick up where left off
- **Pattern recognition** - Learn from history
- **Informed decisions** - Access to past rationale
- **Velocity prediction** - Historical data

### Memory Types

| File | Content | Updated When |
|------|---------|--------------|
| `learnings.md` | Technical discoveries | Session end |
| `decisions.md` | Architecture decisions | When decided |
| `patterns.md` | Observed patterns | When patterns emerge |
| `velocity.json` | Historical velocity | Sprint end |
| `architecture-triggers.yaml` | Impact definitions | When architecture changes |

### learnings.md Format

```markdown
# Project Learnings

## Sprint 2 Session 2026-01-09-001

- **Learning:** AI agents need explicit memory systems
  - **Context:** Discovered during PM implementation
  - **Implication:** Must design for context persistence
  - **Action:** Created knowledge/ directory

- **Learning:** Full backlog destructuring IS minimum viable
  - **Context:** Initial design was too simplified
  - **Implication:** AI needs detailed item structure
  - **Action:** Created numbered file approach
```

### Memory Surfacing

At `/aman start`, AI surfaces:

1. **Items flagged for review** - Prominent banner
2. **Recent learnings** - Top 5 from last sessions
3. **Pending decisions** - Awaiting human input
4. **Architecture impacts** - If rearchitecture planned

This ensures continuity between sessions.

### index.yaml Memory Section

```yaml
memory:
  needs_review:
    - id: FEAT-MP2
      reason: "MLX is 55x faster - Ollama pool may be obsolete"
  recent_learnings:
    - "AI agents need explicit memory systems"
    - "Full backlog destructuring IS minimum viable"
  pending_decisions: []
  architecture_impacts: []
  velocity_insights:
    - "Average 8 items/sprint"
```

---

## 6. Rearchitecture Processing

### When Rearchitecture Happens

Major architectural changes require reorganizing the backlog:

- Items become obsolete
- New items need creation
- Existing items need modification
- Dependencies shift

### The `/aman arch` System

An AI-driven system that processes rearchitecture documents automatically:

```text
Input Document → Parse → Triggers → Impact Analysis → Generate Items → Summary
                                    (parallel agents)  (parallel agents)
```

### How to Use

**Step 1:** Create rearchitecture document

```bash
cp .aman-pm/templates/rearchitecture-input.md rearch-my-change.md
# Fill in required sections
```

**Step 2:** Process it

```bash
/aman arch rearch-my-change.md
```

**Step 3:** Review staged output

```text
.aman-pm/rearchitecture/REARCH-001/summary.md  ← Review this
.aman-pm/rearchitecture/REARCH-001/staged/     ← Preview new items
```

**Step 4:** Apply approved changes

```bash
/aman arch apply REARCH-001
```

### Arch Commands

| Command | Action |
|---------|--------|
| `/aman arch <file>` | Process rearchitecture document |
| `/aman arch apply <id>` | Apply approved changes |
| `/aman arch apply <id> --only=new-items` | Apply only new items |
| `/aman arch status <id>` | Check processing status |
| `/aman arch abort <id>` | Cancel in-progress |

### Required Input Sections

| Section | Purpose |
|---------|---------|
| Executive Summary | What this achieves |
| What's Changing | Technical description |
| Why (Rationale) | Problem + alternatives |
| Affected Components | Architecture tags |
| New Specifications | Items to create |
| Deprecation Candidates | Items that might become obsolete |
| Success Criteria | How to validate |

### Impact Categories

| Category | Meaning | Action |
|----------|---------|--------|
| `obsolete` | No longer needed | Archive |
| `modified` | Requirements changed | Update ACs |
| `adopted` | Absorbed into rearch | Remove from backlog |
| `review_required` | AI uncertain | Human decision |

### Integration with PM System

- **Architecture triggers** stored in `knowledge/architecture-triggers.yaml`
- **Impact analysis** uses architecture tags on backlog items
- **Session start** surfaces pending rearchitectures
- **Decisions** logged in `knowledge/decisions.md`

### Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/templates/rearchitecture-input.md` | Input template |
| `.aman-pm/rearchitecture/index.yaml` | Tracking index |
| `.aman-pm/processes/rearchitecture-system.md` | Full documentation |
| `.claude/skills/aman/commands/arch.md` | Command details |

---

## 7. Accountability & Audit

### Audit Trail

Every action produces documentation:

| Artifact | Evidence |
|----------|----------|
| Session handoffs | Who did what, when |
| Item history | Status changes with timestamps |
| Knowledge updates | Learnings with context |
| Sprint archives | Immutable historical record |
| Commit history | Code changes |

### Verification Points

Before marking work done:

1. **Tests pass** - `make ci-check` exit code 0
2. **Coverage maintained** - 25%+ threshold
3. **Changelog updated** - `docs/changelog/unreleased.md`
4. **Handoff generated** - Session end properly executed

### Review Workflow

Items can be flagged for human review:

```yaml
memory:
  needs_review:
    - id: FEAT-MP2
      reason: "Architecture decision required"
```

These appear prominently at session start with:

- What needs review
- Why it was flagged
- Suggested action

### Immutable Archives

The following are **never modified** after creation:

- `sprints/history/` - Past sprint data
- `sessions/archive/` - Session handoffs
- `backlog/bugs/resolved/` - Resolved bugs
- `backlog/debt/resolved/` - Resolved debt

This ensures reliable audit trail.

---

## 8. Best Practices

### For Sessions

1. **Always start with `/aman start`** - Get full context
2. **Update item status immediately** - Not at the end
3. **Capture learnings as discovered** - Don't wait
4. **Flag items for review before ending** - Surface concerns
5. **Always end with `/aman end`** - Generate handoff

### For Sprints

1. **Keep sprints to 1 week** - Short feedback loops
2. **Don't overcommit capacity** - Use velocity data
3. **Groom backlog before planning** - Know what's ready
4. **Run retrospective even for solo work** - Continuous improvement

### For Memory

1. **Be specific in learnings** - Include context and implications
2. **Document decisions with rationale** - Why, not just what
3. **Update velocity after each sprint** - Track trends
4. **Review architecture-triggers when planning** - Impact awareness

### For Accountability

1. **Never skip handoffs** - Every session produces evidence
2. **Don't modify archives** - Immutable history
3. **Link items to work** - Traceable actions
4. **Commit often** - Git history is evidence

---

## 9. Troubleshooting

### Common Issues

**Q: Session seems to have lost context**

A: Run `/aman start` to reload. Check that last session ended with `/aman end`. Read last handoff from `sessions/archive/`.

**Q: Item not showing in sprint**

A: Check item has `sprint: N` (numeric) in frontmatter. Run `/aman status` to trigger auto-sync.

**Q: Learnings not appearing at session start**

A: Learnings must be in `knowledge/learnings.md` with correct format. Check `index.yaml` memory section.

**Q: Velocity seems wrong**

A: Velocity is calculated from completed items in sprint archives. Check `velocity.json` data and sprint history.

**Q: Handoff not generated**

A: Ensure `/aman end` completed successfully. Check `sessions/active/` for incomplete handoffs.

**Q: Memory not being surfaced**

A: Check `index.yaml` has `memory:` section. Run `/aman status` to trigger auto-sync.

---

## 10. Release Workflow

### When to Release

Release when:

- Sprint complete with features validated
- Bug fixes accumulated
- Milestone reached

### Release Process

```bash
# 1. End current session
/aman end

# 2. Verify CI passes
make ci-check

# 3. Create release (auto-detects version)
/aman release

# 4. Push changes
git push origin main
git push origin v0.2.2
```

### Version Auto-Detection

The `/aman release` command:

1. Reads current version from `VERSION` file
2. Analyzes `unreleased.md` content
3. Suggests appropriate bump:

| Content Found | Suggested Bump | Example |
|---------------|----------------|---------|
| "## Added" has content | MINOR | 0.2.1 → 0.3.0 |
| Only "## Fixed/Changed" | PATCH | 0.2.1 → 0.2.2 |
| "breaking" keyword | Warn MAJOR | 0.2.1 → 1.0.0 |

### Release Flags

| Flag | Effect |
|------|--------|
| `/aman release` | Auto-detect and prompt |
| `/aman release --patch` | Force PATCH bump |
| `/aman release --minor` | Force MINOR bump |
| `/aman release --major` | Force MAJOR bump |
| `/aman release 0.3.0` | Explicit version |
| `/aman release --dry-run` | Preview only |

### Files Modified

| File | Change |
|------|--------|
| `VERSION` | Updated version number |
| `README.md` | Version badge updated |
| `docs/changelog/{minor}/v{version}.md` | Created from unreleased.md |
| `docs/changelog/unreleased.md` | Reset to template |
| `docs/changelog/CHANGELOG.md` | New version entry added |

**Note:** All these updates are now automated by `scripts/release.sh`. The version consistency is verified by `scripts/check-version-consistency.sh`.

---

## Commands Quick Reference

See `quick-reference.md` for the complete command reference.

### The Lazy Workflow (Recommended)

| Command | What Happens Automatically |
|---------|---------------------------|
| `/aman start` | Sync → Status → Memory → Recommendations |
| `/aman` | Sync → Status (quick mid-session check) |
| `/aman end` | Sync (with auto-archive) → Learnings → Handoff |

**Note:** Sync and archive are automatic - rarely need to run manually.

### Session Commands

| Command | Action |
|---------|--------|
| `/aman start` | Auto-sync → context + memory |
| `/aman end` | Auto-sync → auto-archive → learnings → handoff |
| `/aman end --quick` | Fast session end (minimal prompts) |
| `/aman end --release` | Full session end + create release |

### Backlog Commands

| Command | Action |
|---------|--------|
| `/aman` or `/aman status` | Sprint + project status (auto-syncs) |
| `/aman backlog` | List all items |
| `/aman backlog priority:P0` | Filter by priority |
| `/aman done <id>` | Mark complete + auto-archive + cascade |
| `/aman add <type> <title>` | Create item |

**Note:** Sync and archive happen automatically. No manual commands needed.

### Sprint Commands

| Command | Action |
|---------|--------|
| `/aman sprint-start "goal"` | Start next sprint (auto-numbered) |
| `/aman sprint-add <id>` | Add item to current sprint |
| `/aman sprint-end` | End sprint + prompt to start next |
| `/aman report` | Sprint summary |
| `/aman groom` | AI backlog analysis + smart prompts |

### Release Commands

| Command | Action |
|---------|--------|
| `/aman release` | Auto-detect version and release |
| `/aman release --patch` | Force PATCH bump |
| `/aman release --minor` | Force MINOR bump |
| `/aman arch <file>` | Process rearchitecture doc |

---

*Version 2.3.0 | Part of the AmanMCP : Local RAG based Semantic Search*
