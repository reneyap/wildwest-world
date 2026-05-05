# Wild West — DONE

> **Scope:** Cross-county completed work and architectural decisions  
> **Not committed to any repo**

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
