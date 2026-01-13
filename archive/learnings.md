# Project Learnings

> Persistent knowledge that survives sessions. Session-start surfaces recent learnings.

---

## 2026-W03

### Session 2026-01-13-007 (PM System Audit + Sprint 6 Planning)

- **Learning**: Backlog grooming should complete the full planning cycle, not end at resequencing
  - **Context**: User insight that `/aman groom` ends at resequencing but doesn't offer sprint creation
  - **Implication**: Workflow gap where context is lost between groom and sprint-start
  - **Action**: Added Sprint Planning Integration to groom.md (FEAT-PM1)

- **Learning**: Velocity tracking must be automatic, not manual - stale data is worse than no data
  - **Context**: velocity.json was stuck at Sprint 2 while actual project was at Sprint 5
  - **Implication**: Sprint planning decisions made without accurate historical data
  - **Action**: Fixed velocity.json, enhanced sprint.md with explicit update instructions

- **Learning**: AI-native PM research shows 100% precision and 45% time savings with proper tooling
  - **Context**: arXiv study on GenAI-Enabled Backlog Grooming; V2Solutions on velocity prediction
  - **Implication**: Investment in PM tooling has high ROI
  - **Action**: Created 12 FEAT-PM tickets based on research findings

- **Learning**: "Fix the tools before using them" - PM system improvements unlock better sprint planning
  - **Context**: User chose PM System First over FEAT-QI4 (100% Tier 1) for Sprint 6
  - **Implication**: Meta-productivity: better tools → better execution → better outcomes
  - **Action**: Sprint 6 focus on FEAT-PM1, PM2, PM3; FEAT-QI4 deferred to Sprint 7

---

### Session 2026-01-13-006 (BUG-049 Fix + FEAT-QI3 Validation)

- **Learning**: Bleve index corruption is caused by incomplete/truncated `index_meta.json`, not by binary rebuilds directly
  - **Context**: BUG-049 appeared to corrupt index after `make build`, but root cause was partial file writes during process termination
  - **Implication**: The fix needs integrity validation on open, not process management
  - **Action**: Added `validateIndexIntegrity()` to check file size > 0 and JSON parseable before `bleve.Open()`

- **Learning**: Auto-recovery for corrupted indices is better than error-and-die
  - **Context**: Bleve provides `ErrorIndexMetaCorrupt` but default behavior crashes; users must manually `rm -rf .amanmcp`
  - **Implication**: Poor DX when simple auto-clear + "please reindex" would restore functionality
  - **Action**: Implemented auto-clear with `slog.Warn` for corrupted indices, prompting reindex

- **Learning**: Go method receiver syntax breaks naive function search patterns
  - **Context**: "Search function" decomposed to `func Search` but actual code is `func (e *Engine) Search(...)`
  - **Implication**: FEAT-QI3 multi-query fusion improved Q2 but Q7/Q8 still fail due to method syntax
  - **Action**: Future work should consider Go-specific patterns like `func (*` or `func (e *`

- **Learning**: Validation baseline scripts use looser matching than manual testing
  - **Context**: Manual test for "Where is vector store created" checked for exact `hnsw.go`, script just checks for `internal/store/hnsw`
  - **Implication**: Manual testing can appear worse than automated results; always use official baseline script
  - **Action**: Use `./scripts/dogfood-baseline.sh --tier1` for accurate validation

- **Learning**: Multi-query fusion works best for natural language "How does X work" queries
  - **Context**: FEAT-QI3 fixed "How does RRF fusion work" (now returns fusion.go) but not "Search function"
  - **Implication**: Decomposition to multiple keywords + file patterns effective for explanation queries
  - **Action**: Pattern-based decomposition is query-type dependent; different strategies for different patterns

---

### Session 2026-01-13-004 (MLX Default on Apple Silicon)

- **Learning**: ADR decisions don't implement themselves - verify code matches architectural decisions
  - **Context**: ADR-035 approved MLX as default on Apple Silicon, but `newDefaultWithFallback()` still hardcoded Ollama
  - **Implication**: Decisions recorded but not implemented can persist unnoticed for multiple releases
  - **Action**: When reviewing ADRs, trace through to actual implementation; consider ADR implementation checklist

- **Learning**: Helper functions may exist but not be used - audit before creating new ones
  - **Context**: `isAppleSilicon()` existed at factory.go:140-143 but `newDefaultWithFallback()` didn't call it
  - **Implication**: Code duplication risk; missed optimization opportunities; wasted effort creating what exists
  - **Action**: Search for existing helpers before implementation; grep for function signatures

---

### Session 2026-01-13-001 (BUG-060: Config Validation + Logging Enhancement)

- **Learning**: When adding a new provider/feature, ALL validation paths must be updated
  - **Context**: MLX embedder was added but `config.Validate()` validProviders map wasn't updated
  - **Implication**: Silent fallback to defaults loses project-specific config (31% file count variance)
  - **Action**: Created RCA-017; recommend using `embed.ValidProviders()` as single source of truth

- **Learning**: Silent fallbacks mask bugs - they delay discovery and complicate debugging
  - **Context**: Config load error caused silent fallback to `config.NewConfig()`, losing all exclude patterns
  - **Implication**: User sees "wrong" results but no error message; requires debug logging to find root cause
  - **Action**: Consider warning logs when falling back to defaults

- **Learning**: Initial hypotheses can be confidently wrong - always verify with data
  - **Context**: 95% confident bug was "test methodology" - ran proper benchmark with `rm -rf .amanmcp`, bug was REAL
  - **Implication**: Expert intuition without data verification leads to wasted investigation time
  - **Action**: Always reproduce before theorizing; data beats intuition

- **Learning**: Log visibility requires immediate sync for `tail -f` style tools
  - **Context**: `slog` entries not appearing in `amanmcp-logs -f` due to buffered writes
  - **Implication**: Real-time debugging impossible without `Sync()` after writes
  - **Action**: Added `immediateSync` to RotatingWriter, enabled by default

---

## 2026-W02

### Session 2026-01-12-008 (/aman Skill Simplification v1.2.0)

- **Learning**: Parallel agent audit effectively uncovers command overlaps
  - **Context**: 3 agents analyzed commands, workflows, and utilities simultaneously - found sync in 7+ places, archive in 3 places
  - **Implication**: Sequential analysis would miss cross-cutting concerns; parallel exploration finds patterns
  - **Action**: Used Explore agents for comprehensive skill system audit

- **Learning**: Internal operations should be automatic, not exposed as user commands
  - **Context**: `/aman sync` and `/aman archive` created cognitive load - users asked "when do I run these?"
  - **Implication**: Exposing internal operations causes decision paralysis and forgotten steps
  - **Action**: Made sync/archive automatic; removed user-facing commands

- **Learning**: Smart prompts within commands beat two-step workflows
  - **Context**: `--resequence` then `--apply-resequence` required users to remember recommendations and re-invoke
  - **Implication**: State between commands is lost; users forget or get distracted
  - **Action**: Merged into single command with "Apply? [all/1,2/none]" prompt

- **Learning**: Simplification increases power (paradox)
  - **Context**: Removed 2 commands, merged 1 workflow, added 2 smart prompts
  - **Implication**: Fewer commands = faster execution, fewer decisions, fewer forgotten steps
  - **Action**: "Lazy 3" workflow: `/aman start` → work → `/aman done` → `/aman end`

### Session 2026-01-12-007 (FEAT-UX3: Build/Install UX Overhaul + Release Automation)

- **Learning**: "Lazy engineer" audits reveal hidden friction that users won't report
  - **Context**: Proactive UX audit found PATH auto-config missing, backend switching hidden, env vars undocumented
  - **Implication**: Need to think like a frustrated new user, not an expert; users silently abandon
  - **Action**: Created FEAT-UX3; implemented auto-config, backend parity, verification, doc fixes

- **Learning**: Release automation must be atomic - all version references updated by single command
  - **Context**: VERSION file was 0.2.6 but README badge showed 0.2.4; manual updates drift
  - **Implication**: Any manual step in release process will eventually be forgotten
  - **Action**: Enhanced release.sh to update VERSION, README badge, changelog, and reset unreleased.md atomically

- **Learning**: Version consistency requires automated validation, not manual vigilance
  - **Context**: Drift between VERSION file and README badge went unnoticed until explicit audit
  - **Implication**: Humans forget to check; automated scripts don't
  - **Action**: Added check_product_version() to scripts/check-version-consistency.sh

- **Learning**: Documentation contradictions erode trust - standardize ruthlessly
  - **Context**: Model version varied (0.6b vs 8b), MLX claims varied (1.7x vs 55x) across docs
  - **Implication**: Users lose confidence when docs contradict; they stop trusting anything
  - **Action**: Standardized all model references to qwen3-embedding:0.6b, all MLX claims to ~1.7x

- **Learning**: AI can't remember to be proactive - encode behaviors into instructions
  - **Context**: Asked "how would you remember to capture learnings?" - answer is I won't unless it's in CLAUDE.md
  - **Implication**: Any behavior you want AI to do consistently must be written into its instructions
  - **Action**: Added "Learning Capture (Proactive)" section to CLAUDE.md always-active rules

### Session 2026-01-12-006 (Sprint 4 Cleanup: FEAT-DIM1, FEAT-MEM2, DEBT-024)

- **Learning**: Go embedding cache already has LRU via hashicorp/golang-lru/v2
  - **Context**: FEAT-MEM2 (LRU bounded cache) was marked as obsolete because examination of `internal/embed/cache.go` showed LRU already implemented
  - **Implication**: No additional memory work needed; cache is bounded at 1000 items (~3-16 MB)
  - **Action**: Closed FEAT-MEM2 as obsolete; no implementation required

- **Learning**: --bm25-only flag is safer than auto-reindex for dimension mismatch recovery
  - **Context**: FEAT-DIM1 originally proposed --auto-reindex, but this could destroy user's index unexpectedly
  - **Implication**: Explicit user action (reindex or fallback to BM25) is better than automatic destructive operations
  - **Action**: Implemented --bm25-only flag; deferred --auto-reindex as too risky

- **Learning**: DEBT-024 "10x MCP overhead" claim was INVALID - cold-start vs warm comparison
  - **Context**: Detailed instrumentation showed MCP overhead is <5ms, not 900ms
  - **Implication**: Model loading (~1.2s) on first request was conflated with MCP protocol overhead
  - **Action**: Cross-encoder inference is O(n) at ~40ms/doc - this is inherent, not a bug

### Session 2026-01-12-005 (Backend Decision + Observability Implementation)

- **Learning**: MLX is 1.67x faster, NOT 55x - The claim was never validated
  - **Context**: SPIKE-003 benchmark: MLX 39 chunks/sec, Ollama 23.4 chunks/sec. Wall-clock: 6m10s vs 6m51s (11% difference)
  - **Implication**: The "55x faster" claim was fabricated; actual difference is marginal
  - **Action**: Ollama is now default; MLX opt-in via `--backend=mlx`

- **Learning**: Cross-platform > marginal speed for developer tools
  - **Context**: Ollama works everywhere; MLX is Apple Silicon only. 1.67x speed isn't worth platform lock-in
  - **Implication**: "Just works" beats "slightly faster on some platforms" for adoption
  - **Action**: Made Ollama default, keep MLX as opt-in for power users

- **Learning**: Observability enables data-driven decisions
  - **Context**: Couldn't validate 55x claim because there was no stage timing or throughput logging
  - **Implication**: Without observability, claims go unvalidated; decisions are based on assumptions
  - **Action**: Implemented FEAT-OBS1 (stage timing, throughput, backend info in logs)

- **Learning**: "Lazy engineer UX" drives adoption
  - **Context**: Multiple manual steps to set up MLX created friction; one-command install removes barrier
  - **Implication**: Developer tools succeed when they minimize steps to get started
  - **Action**: Created `make install-mlx` (one command) and `--backend` flag (simple switching)

- **Learning**: Logging requires explicit slog.SetDefault() in CLI commands
  - **Context**: Logs were going to stderr instead of file because slog.SetDefault wasn't called
  - **Implication**: Each CLI command needs explicit logging setup if it wants structured file logging
  - **Action**: Added slog.SetDefault(logger) after creating file handler

### Session 2026-01-12-004 (SPIKE-003 4-Track Benchmark Execution)

- **Learning**: EmbeddingGemma is WORSE than Qwen3-0.6B for code search (75% vs 83% Tier 1)
  - **Context**: SPIKE-003 4-track benchmark - EmbeddingGemma (308M, 768d) vs Qwen3 (595M, 1024d)
  - **Implication**: Despite 50% smaller size, EmbeddingGemma loses 8 Tier 1 points AND is 74% slower
  - **Action**: Keep Qwen3-0.6B as default; do NOT switch to EmbeddingGemma

- **Learning**: MTEB-Code scores don't directly translate to real-world performance
  - **Context**: Qwen3 (74.57) vs EmbeddingGemma (68.14) = 6.4pt MTEB gap, but 8pt Tier 1 gap
  - **Implication**: Benchmark scores are directionally correct but magnitude differs on real codebases
  - **Action**: Always run domain-specific validation, not just rely on MTEB scores

- **Learning**: Backend choice (MLX vs Ollama) doesn't affect search quality
  - **Context**: Track 1 (MLX) and Track 2 (Ollama) both achieved 83% with Qwen3-0.6B
  - **Implication**: Model weights are identical; only latency differs (MLX ~33% faster indexing)
  - **Action**: Use MLX on Apple Silicon for speed; Ollama is valid cross-platform fallback

- **Learning**: EmbeddingGemma on MLX requires `mlx_embeddings` library, not `mlx_lm`
  - **Context**: Track 3 (MLX + EmbeddingGemma) failed - architecture incompatibility
  - **Implication**: Different embedding model architectures need different loading libraries
  - **Action**: To support EmbeddingGemma on MLX, need to integrate `mlx_embeddings` (FEAT-MLX2)

- **Learning**: Clean reindex is REQUIRED when switching embedding models
  - **Context**: BUG-042 lesson - dimension metadata must match, stale index causes silent failures
  - **Implication**: `rm -rf .amanmcp` before each model test; can't reuse index across models
  - **Action**: Always clean slate when changing embedding model or dimensions

### Session 2026-01-12-001 (SPIKE-003 Research + Model Comparison)

- **Learning**: nomic-embed-text is NOT suitable for code search
  - **Context**: During DEBT-025/SPIKE-003 planning, nomic-embed-text kept being mentioned as fallback
  - **Implication**: It's a general text model, NOT optimized for code (no MTEB-Code benchmark)
  - **Action**: Removed from testing matrix; use Qwen3-0.6B (74.57) or EmbeddingGemma (68.14) for code+docs

- **Learning**: EmbeddingGemma offers 50% size reduction with 6.4 point quality drop
  - **Context**: Grounded research found Qwen3-0.6B (595M, 74.57) vs EmbeddingGemma (308M, 68.14)
  - **Implication**: Trade-off is size vs quality; MRL truncation can further reduce index size
  - **Action**: SPIKE-003 3-track testing will empirically validate this trade-off

- **Learning**: CodeRankEmbed (137M) is code-only, can't handle documentation
  - **Context**: Nomic's small code model (77.9 CSN) but only for code files
  - **Implication**: Our project has code + markdown docs; need models that handle both
  - **Action**: Excluded from testing; focus on Qwen3 and EmbeddingGemma which handle both

- **Learning**: Both Qwen3-0.6B and EmbeddingGemma are available on Ollama
  - **Context**: Grounded search confirmed `ollama pull qwen3-embedding:0.6b` and `ollama pull embeddinggemma`
  - **Implication**: Full 4-track testing is possible (2 models × 2 backends)
  - **Action**: SPIKE-003 expanded to 4-track framework for comprehensive comparison

### Session 2026-01-11-001 (FEAT-RR1 Implementation + BUG-040)

- **Learning**: Cross-encoder reranking doesn't solve test/implementation file confusion
  - **Context**: Generic queries like "Search function" still return test files with reranking enabled
  - **Implication**: Test and implementation files contain identical keywords; reranker can't distinguish
  - **Action**: SPIKE-003 explores alternatives (EmbeddingGemma, BM25F, query intent) since 8B model is NOT viable

- **Learning**: 8B embedding model is NOT an option due to system constraints
  - **Context**: 24GB RAM laptop; 8B model causes 15-35GB real usage, system freeze
  - **Implication**: 0.6B model is the ceiling; must find quality improvements within this constraint
  - **Action**: SPIKE-003 tests smaller models (EmbeddingGemma 308M) and techniques (MRL truncation, BM25F)

- **Learning**: New CLI commands need explicit logging initialization
  - **Context**: Daemon command was missing `logging.Setup()`, causing logs to go to stderr not file
  - **Implication**: Every command entry point that should log needs explicit setup
  - **Action**: Add logging init checklist to new command template

- **Learning**: Blocking initialization calls need timeout contexts
  - **Context**: Embedder health check could block indefinitely, making index appear stuck
  - **Implication**: Any network call in init path should have timeout
  - **Action**: Added 15s timeout to embedder init; apply pattern to other init code

### Session 2026-01-10-002 (CR-1 Investigation + Tuning)

- **Learning**: 0.6B embedding model is insufficient for code semantics
  - **Context**: Vector search returns docs instead of code, even with correct model
  - **Implication**: Small embedding space (1024 dims) can't distinguish code from verbose docs
  - **Action**: Accept BM25-heavy weights as workaround; future work needs larger model

- **Learning**: BM25 is the reliable fallback for code search
  - **Context**: When vector search failed, BM25 returned correct code files
  - **Implication**: Keyword matching works where semantic fails for code
  - **Action**: Changed default weights to BM25=0.65, Semantic=0.35

- **Learning**: CR-1 prefixes hurt more than help for code chunks
  - **Context**: Natural language prefixes like "From file: X. Defines: Y." cluster together
  - **Implication**: Verbose metadata dominates embedding space, code signals lost
  - **Action**: Added `contextual.code_chunks` config (default: false for code)

- **Learning**: Qwen3 requires asymmetric embedding (instruction prefix for queries)
  - **Context**: Documents embedded without prefix, queries need task instruction
  - **Implication**: Missing instruction can cause 1-5% quality loss
  - **Action**: Added `Qwen3QueryInstruction` constant to search engine

- **Learning**: Config merge pattern can't set values to zero
  - **Context**: `if other.Field != 0 { c.Field = other.Field }` ignores explicit zeros
  - **Implication**: Use pointers or env vars when zero is a valid user value
  - **Action**: Added env var overrides for search weights (BUG-RR1)

### Session 2026-01-10-001 (PM System Completion)

- **Learning**: Tracking files can drift significantly from actual progress
  - **Context**: Sprint items.yaml showed 7% complete when actual was 87%
  - **Implication**: Need regular sync between work files and tracking
  - **Action**: Run `/aman-pm sync` frequently, especially before session end

- **Learning**: Documentation replacement beats documentation updates
  - **Context**: ai-native-pm-guide.md (708 lines) fully replaced amanmcp_pms.md
  - **Implication**: When docs are comprehensively rewritten, archive the old
  - **Action**: Check for stale docs at session end, archive replaced ones

- **Learning**: Verify actual file state, not just task descriptions
  - **Context**: Tasks described "updates needed" but full implementations existed
  - **Implication**: Task descriptions can become stale; actual code is truth
  - **Action**: Read actual files before marking items as incomplete

### Session 2026-01-09-003 (AI-Native PM Guide)

- **Learning**: The core insight is simple - "If AI can't remember it, write it to a file"
  - **Context**: Writing the comprehensive PM guide crystallized the philosophy
  - **Implication**: Every design decision flows from this one principle
  - **Action**: Made this the central theme of the publishable guide

- **Learning**: Memory is the key innovation that differentiates AI-Native PM
  - **Context**: The `knowledge/` directory is what makes this different from traditional PM
  - **Implication**: Without explicit memory, you're just doing traditional PM with AI tools
  - **Action**: Emphasized memory system as "core innovation" throughout the guide

- **Learning**: Graduated complexity serves different user needs
  - **Context**: Created both comprehensive guide (2300 lines) and quick start (350 lines)
  - **Implication**: Quick start gets people going; full guide provides depth when needed
  - **Action**: Maintain both documents for different audiences

### Session 2026-01-09 (PM System Implementation)

- **Learning**: AI agents need explicit memory systems
  - **Context**: Without memory, each session starts from scratch
  - **Implication**: PM system must BE the memory, not just track tasks
  - **Action**: Added memory section to index.yaml, learnings.md

- **Learning**: "Don't over-engineer" is wrong advice for AI workflows
  - **Context**: Full backlog destructuring IS the minimum viable system
  - **Implication**: All roadmap items need full structure for impact analysis
  - **Action**: Migrating all 27 ROADMAP.md items to backlog.yaml

### Prior Sessions (Pre-PM System)

- **Learning**: CR-1 contextual prefixes dilute code signals
  - **Context**: Dogfood Tier 1 dropped 75% → 33% after v0.1.61
  - **Implication**: May need code-specific chunking strategy
  - **Action**: Flagged for investigation in EPIC-001

- ~~**Learning**: MLX is 55x faster than Ollama~~
  - **CORRECTED 2026-01-12**: Actual benchmark shows 1.67x (39 vs 23.4 chunks/sec)
  - **Context**: Original claim was never validated; Session 2026-01-12-005 ran proper benchmarks
  - **Implication**: Ollama is now default; cross-platform benefits > marginal speed
  - **Action**: See Session 2026-01-12-005 for correct data

---

## How to Add Learnings

At session end, `/session-end` will prompt:

```
Any learnings from this session to persist?

Examples:
- Technical discoveries
- Patterns that work/don't work
- Decisions and their rationale
- Things to remember for future sessions
```

Format:
```markdown
- **Learning**: [What was learned]
  - **Context**: [Why this came up]
  - **Implication**: [What this means for the project]
  - **Action**: [What was done or should be done]
```

---

## Learnings Index

Quick reference for common topics:

| Topic | Key Learning | Sprint |
|-------|--------------|--------|
| Go Methods | Method receiver syntax `func (e *Engine)` breaks naive patterns | 2026-W03 |
| Multi-Query | Fusion works best for "How does X work", less for "X function" | 2026-W03 |
| Auto-Recovery | Better UX than error-and-die for corrupted indices | 2026-W03 |
| Validation | Use official baseline scripts, not manual stricter matching | 2026-W03 |
| Memory | AI agents need explicit memory systems | 2026-W02 |
| Backlog | Full destructuring IS minimum viable | 2026-W02 |
| CR-1 | Prefixes hurt code; skip with `code_chunks: false` | 2026-W02 |
| Embeddings | 0.6B model can't distinguish code semantics | 2026-W02 |
| BM25 | Reliable fallback when vector search fails | 2026-W02 |
| Qwen3 | Queries need instruction prefix (asymmetric) | 2026-W02 |
| Config | Zero values need pointers or env vars | 2026-W02 |
| MLX | 1.67x faster (NOT 55x); Ollama is default | 2026-W02 |
| Observability | Essential for data-driven decisions | 2026-W02 |
| UX | One-command solutions drive adoption | 2026-W02 |
| UX Audit | "Lazy engineer" audits reveal hidden friction | 2026-W02 |
| Release | Atomic automation prevents version drift | 2026-W02 |
| Docs | Contradictions erode trust; standardize ruthlessly | 2026-W02 |
| AI Behavior | Can't remember to be proactive - encode in instructions | 2026-W02 |

## Week of 2026-01-13

### FEAT-AI5 Analysis: Batched Incremental Updates

- **Learning**: Verify claimed inefficiencies with code analysis before building optimization features
  - **Context**: FEAT-AI5 claimed "sequential processing with separate embedding operations" was inefficient
  - **Implication**: Code analysis revealed embedding batching (the 10x optimization) already exists; file-level batching adds latency
  - **Action**: Closed FEAT-AI5 as won't implement; similar pattern to FEAT-SC1 closure

- **Learning**: The dominant cost determines where optimization matters
  - **Context**: Embedding is 10-100x more expensive than file I/O; embedding already batched at 32 chunks
  - **Implication**: Adding file-level batching provides <5% gain while adding 500ms latency for common case
  - **Action**: Document cost breakdown to prevent future misguided optimization attempts

### BUG-060 Resolution: MLX Config Validation Missing

- **Learning:** When adding a new embedder, ALL code references must be updated - not just the factory, but also validators, documentation, and tests
- **Context:** MLX was added as an embedder option but `config.Validate()` wasn't updated, causing silent fallback to default config
- **Implication:** Silent failures are dangerous - always log validation errors prominently
- **Action:** Fix applied. Consider creating an embedder addition checklist.

- **Learning:** Debug logging through the entire flow is essential for config-related bugs
- **Context:** Without tracing config through Load() -> loadFromFile() -> Validate() -> return, root cause would be very hard to find
- **Implication:** Add strategic debug points at each transformation stage
- **Action:** Consider permanent debug logging for config loading (disabled by default)

- **Learning:** Hardcoded lists in validators diverge from authoritative sources
- **Context:** Config validator had `{"llama", "static", "ollama"}` while embed factory has `embed.ValidProviders()` which includes 'mlx'
- **Implication:** Use single source of truth for valid values
- **Action:** Consider refactoring to use `embed.ValidProviders()` in config validation
