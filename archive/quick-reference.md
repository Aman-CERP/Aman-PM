# AI-Native PM Quick Reference

## The `/aman` Command

One command for all project management. Replaces 7 separate skills.

---

## The Lazy Workflow (3 Commands)

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

---

## All Commands

```
/aman              → status (smart default)
/aman start        → begin session
/aman end          → end session
/aman backlog      → list items
/aman done <id>    → mark complete + cascade
/aman groom        → backlog analysis
/aman report       → sprint report
/aman sprint-end   → close sprint with ceremony
/aman arch <file>  → rearchitecture
/aman release      → create release (auto-version)
```

---

## Session Commands

| Command | Action |
|---------|--------|
| `/aman start` | Begin session with context + memory |
| `/aman end` | End session, capture learnings, handoff |
| `/aman end --quick` | Fast session end (minimal prompts) |
| `/aman end --release` | Full session end + create release |

## Backlog Commands

| Command | Action |
|---------|--------|
| `/aman status` | Sprint + project status |
| `/aman backlog` | List all items |
| `/aman backlog priority:P0` | Filter by priority |
| `/aman backlog status:in_progress` | Filter by status |
| `/aman backlog type:task` | Filter by type |
| `/aman backlog blocked` | Show blocked items |
| `/aman add bug "title"` | Create bug |
| `/aman add task "title"` | Create task |
| `/aman add feature "title"` | Create feature |
| `/aman done ITEM-001` | Mark complete + auto-archive + cascade |
| `/aman move ITEM-001 done` | Change status (use `done` for completion) |

**Note:** Sync and archive happen automatically. No manual commands needed.

## Sprint Commands

| Command | Action |
|---------|--------|
| `/aman sprint-start "goal"` | Start next sprint (auto-numbered) |
| `/aman sprint-start 5 "goal"` | Start specific sprint (override) |
| `/aman sprint-add ITEM-ID` | Add to current sprint |
| `/aman sprint-end` | End sprint + prompt to start next |
| `/aman sprint-end --quick` | Quick archive (minimal prompts) |
| `/aman report` | Sprint summary |
| `/aman report --mid` | Mid-sprint check |

## Grooming Commands

| Command | Action |
|---------|--------|
| `/aman groom` | Full AI analysis (9 checks) + smart prompts |
| `/aman groom --quick` | Stale + priority only |
| `/aman groom --context` | Missing context focus |
| `/aman groom --impact REARCH-XXX` | Architecture impact |
| `/aman groom --plan` | View development plan |
| `/aman groom --resequence` | AI sequence suggestions + prompt to apply |

**Smart prompts:** Groom offers to apply archive and resequence recommendations automatically.

## Architecture Commands

| Command | Action |
|---------|--------|
| `/aman arch <file>` | Process rearchitecture doc |
| `/aman arch apply <id>` | Apply approved changes |
| `/aman arch apply <id> --only=new-items` | Apply only new items |
| `/aman arch status <id>` | Check processing status |
| `/aman arch abort <id>` | Cancel in-progress |

## Release Commands

| Command | Action |
|---------|--------|
| `/aman release` | Auto-detect version and release |
| `/aman release --patch` | Force PATCH bump (0.2.1 → 0.2.2) |
| `/aman release --minor` | Force MINOR bump (0.2.1 → 0.3.0) |
| `/aman release --major` | Force MAJOR bump (0.2.1 → 1.0.0) |
| `/aman release 0.3.0` | Explicit version (override) |
| `/aman release --dry-run` | Preview without changes |
| `/aman release --no-tag` | Skip git tag |
| `/aman release --skip-ci` | Skip CI check |

## Smart Shortcuts

| Shortcut | Equivalent |
|----------|------------|
| `/aman urgent` | `/aman backlog priority:P0` |
| `/aman stale` | Items in_progress > 3 days |
| `/aman blocked` | `/aman backlog blocked` |
| `/aman mine` | Items touched this session |

---

## Item Flow

```
backlog → ready → in_progress → review → done → archived
```

## Priorities

| Priority | Meaning |
|----------|---------|
| P0 | Critical - blocks everything |
| P1 | High - must complete |
| P2 | Medium - should complete |
| P3 | Low - nice to have |

---

## Daily Workflow (Lazy Version)

```
Morning:
  /aman start            # Auto-sync → context → memory

During work:
  [AI agents update item status directly in files]
  /aman                  # Quick status check (auto-syncs)

End of day:
  /aman end              # Auto-sync → auto-archive → learnings → handoff
```

**Note:** You rarely need `/aman done <id>` - AI agents update status directly, and sync picks up changes automatically.

## Sprint Lifecycle

```
/aman sprint-start "Goal"     # Auto-create next sprint
       ↓
/aman sprint-add ITEM-001     # Add items
       ↓
[Work sessions]                # /aman start + /aman end daily
       ↓
/aman report --mid            # Mid-sprint check (Day 3)
       ↓
/aman report                   # End-of-sprint report (Day 5)
       ↓
/aman sprint-end              # Close sprint → prompts "Start Sprint N+1?"
```

**Note:** Sprint-end offers to start next sprint automatically.

---

## Key Files

| File | Purpose |
|------|---------|
| `.aman-pm/index.yaml` | Status snapshot + memory |
| `.aman-pm/config.yaml` | Sprint configuration |
| `.aman-pm/knowledge/learnings.md` | Persistent learnings |
| `.aman-pm/sessions/archive/` | Session handoffs |
| `.aman-pm/sprints/active/N/` | Current sprint |
| `.aman-pm/sprints/history/N/` | Past sprints (immutable) |
| `.aman-pm/knowledge/velocity.json` | Velocity history |

---

## Before Marking Done

- [ ] Tests pass (`make ci-check`)
- [ ] Coverage maintained
- [ ] Changelog updated

---

*Full documentation: `archive/ai-native-pm-guide.md`*
