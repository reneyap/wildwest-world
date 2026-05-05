# Wild West — DONE

> **Last updated:** 20260505-1720Z (13:20 EDT)
> **Scope:** Cross-county completed work and architectural decisions  
> **Not committed to any repo**

---

## feat/session-export-pipeline — v0.14.0 Shipped ✓ 2026-05-05

Delta pipeline designed by RA (session `3ecc35f4`) and implemented by TM(ww-vscode) as PR #1. Merged and shipped as v0.14.0. Pipeline confirmed working:
- `staged/packets/` — 144 delta packets (wwsid-keyed, seq-indexed)
- `staged/storage/index.json` — 134 sessions indexed across all tools
- `staged/storage/sessions/` — 135 per-session detail files
- Packet shape: `schema_version`, `wwsid`, `tool`, `actor`, `device_id`, `seq_from/seq_to`, `turns`
- Storage index shape: `schema_version`, `updated_at`, `sessions[]` with `tool`, `tool_sid`, `project_path`, `created_at`
- Note: legacy flat `staged/*.json` files (260) remain alongside new structure — cleanup is a separate open item

---

## wwTerritory Rename — wildwest-vscode Code Complete ✓ 2026-05-05

All three pending code items confirmed complete as of v0.14.0:
- `HeartbeatMonitor.beatWorld()` already `beatTerritory()` + `'territory'` scope
- World registry `"scope"` field already `"territory"` in `~/wildwest/.wildwest/registry.json`
- README.md + package.json description + HeartbeatMonitor log updated this session (uncommitted)

---

## World Root Configurability — v0.11.0 ✓ 2026-05-05

All 6 items shipped in v0.11.0 (current: v0.14.0). Confirmed working:
1. `wildwest.worldRoot` VSCode setting (default `~/wildwest`)
2. `wildwest.countiesDir` VSCode setting (default `counties`)
3. `wildwest.sessionsDir` VSCode setting (default `sessions`)
4. `HeartbeatMonitor.beatCounty()` — uses worldRoot + countiesDir path derivation
5. `HeartbeatMonitor.beatTerritory()` — renamed from `beatWorld()`
6. `path` fields removed from territory + county registries

---

## wwWorld → wwTerritory Rename ✓ 2026-05-05

Governor governs a Territory — the Wild West analogy term and role name are inseparable. `wwWorld` was misleading (implies absolute top; role at that level is Governor, not "World"). `Territory` was already in `background.md` but incorrectly tied to Sheriff (county scope). Corrected to Governor.

**Scope of change:** `wwTerritory` replaces `wwWorld` in all framework docs; scope enum `"world"` → `"territory"` in registry discriminator; `background.md` Territory definition fixed; `mcp.md` multi-territory model updated. **Still pending:** wildwest-vscode code (`HeartbeatMonitor.beatWorld()` → `beatTerritory()`, `scope === "world"` checks), world registry `"scope"` field, MEMORY.md.

---

## World Registry Commit Decision ✓ 2026-05-05

Registry committed at all scopes (world, county, town) for committed instances. wwWorld is an instance of wildwest-framework — its `.wildwest/registry.json` is a governance artifact, not a machine-local index. "World gitignored" assumption was pre-instance-model and is superseded. `.last-beat` sentinels remain gitignored (runtime-only). Framework spec should codify: gitignore only if the world root is purely local and not a versioned instance.

---

## TODO.md Reorganization + Doc Cleanup ✓ 2026-05-05

| Item | Resolution |
|---|---|
| TODO.md reorganized by area | Sections: Now (5-item prioritized queue) → Open Decisions → wwFramework → wildwest-vscode → nx-icouponads. Items grouped by owner, not chronology. |
| Missing backlog items added to TODO.md | Registry validator, model identity gap in staged, command palette split, MCP integration, role-awareness warning, public release prep, frontier onboarding — all surfaced from doc review. |
| Stale snapshots archived | `1300Z` (obsolete), `1305Z`, `1136Z` moved to `archive/`. |
| Reference docs moved to `docs/` | `1435Z`, `1443Z`, `1155Z`, `1207Z` — isolation probe, sessions pipeline, alignment plan, compact behavior. |
| Root cleaned up | `README.md` and `DONE.md` committed. `docs/` and `archive/` established as world-root conventions. |

---

## Registry & Extension Cleanup ✓ 2026-05-03

| Item | Resolution |
|---|---|
| wildwest-vscode: bootstrap `.wildwest/registry.json` | Written manually with `wwuid: 83b09a8d-6587-46bb-9e98-880d56db39b2`, `alias: "wildwest-vscode"`, `remote`, `mcp: null` |
| wildwest-ai county registry: stale `~/counties/` paths | Both town `path` fields updated to `~/wildwest/counties/wildwest-ai/...`; `last_updated` bumped |
| v0.9.0 commit message mismatch | Amended `2d224fd → 9424dbd`; subject and body now correctly describe v0.9.0 content; force-pushed to origin/main |

---

## `~/wildwest/` World Migration ✓ 2026-05-02

Single-trunk migration complete. `~/wildwest/` is the sole root; all symlinks removed.

| Move | Status |
|---|---|
| `~/frontier/` → `~/wildwest/frontier/` | Done |
| `~/counties/` → `~/wildwest/counties/` | Done (copied then symlinks removed 2026-05-02) |
| Sessions → `~/wildwest/sessions/` | Done (v0.4.0 sessionExporter.ts path) |
| `~/counties/` and `~/frontier/` symlinks | Removed 2026-05-02 |

**Remaining from migration:** CLAUDE.md files and `.code-workspace` files still reference `~/counties/` — tracked as open item.

---

## nx-icouponads Onboarding Survey — Decisions ✓ 2026-04-30

| Q | Decision |
|---|---|
| **Q1 — initTown: run or bypass?** | Bypass. initTown bails if `.wildwest/` exists. Add `_heartbeat` worktree manually — surgical, zero risk. |
| **Q2 — `docs/branches/` migration** | Defer (Option C). Get heartbeat alive first. Migration as `chore/wwtownmigration`. |
| **Q3 — `drafts/` state** | Add to spec. nx-icouponads' 5-state lifecycle is battle-tested. Final order: `drafts/ → planned/ → active/ → merged/ \| abandoned/`. |
| **Q4 — `dailies/` BOD/EOD** | Formalize as canonical. `dailies/` is a canonical `.wildwest/` subdir; content format is town-defined. |
| **Q5 — registry.json at town vs. county** | Keep both. Cascading, recursive: county tracks towns, town tracks branches/sessions/actors. Not a conflict — a hierarchy. |
| **Q6 — session exporter path** | Relocate to `~/wildwest/sessions/`. Done in v0.4.0. |

---

## Architectural Decisions — Identity & Federation ✓ 2026-04-30

### wwuid

All first-class entities (County, Town, Session) get a `wwuid` (UUID v4). Globally unique from day one for federation readiness.

**Identity block — four fields (canonical):**

```json
{
  "wwuid": "550e8400-e29b-41d4-a716-446655440000",
  "alias": "wildwest-vscode",
  "remote": "https://github.com/wildwest-ai/wildwest-vscode",
  "mcp": null
}
```

| Field | Mutable | Purpose |
|---|---|---|
| `wwuid` | Never | Canonical identity — what systems and federation reference |
| `alias` | Yes | Human-friendly slug — renameable without breaking refs |
| `remote` | Rarely | Path-independent, machine-independent network address |
| `mcp` | When live | `.wwMCP` server address — `null` until implemented |

**Where it lives:** Town `.wildwest/registry.json` and county `registry.json` (by wwuid reference). Session headers reference town wwuid + actor devPair code.

### .wwMCP

Each town/county will expose a `.wwMCP` MCP server when the framework matures — exposing registry, board state, telegraph, heartbeat as queryable resources. Federation over MCP enables cross-county coordination without shared filesystem. wwuid and clean registry schema designed for this from day one; .wwMCP implementation deferred.

---

## `~/wildwest/` Full Trunk — Architectural Decision ✓ 2026-04-30

`~/wildwest/` is the single root of the entire Wild West world on this machine. Cascade:
- `~/wildwest/` — world root (local runtime; cross-county)
- `~/wildwest/counties/<county>/` — county instance
- `~/wildwest/counties/<county>/<town>/.wildwest/` — town governance domain
- `~/wildwest/frontier/` — ungoverned; projects graduate frontier → counties
- `~/wildwest/sessions/<actor>/` — session exporter output (all tools)

---

## Session Handoffs ✓ 2026-04-30

> Historical checkpoints; work complete.

**12:57 UTC** — Phase 1 done (frontier moved, sessions path updated to `~/wildwest/sessions/`).  
**13:25 UTC** — Phase 2 planned; `wildwest.code-workspace` written; registry.json identity model finalized; `getTownRoot()` marker decision (key on `.wildwest/registry.json`, not `.wildwest/scripts/`).
