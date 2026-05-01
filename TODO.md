# Wild West — TODO

> **Created:** 2026-04-30 12:17 UTC
> **Scope:** Cross-county planning — not committed to any repo

---

## World Repo — Open Items

| Item | Notes |
|---|---|
| Create `wildwest-ai/wildwest-world` on GitHub | Template/reference world instance for the framework org. Do after `reneyap/wildwest-world` is stable and the structure is proven. `wildwest-ai/wildwest-world` = canonical example; `reneyap/wildwest-world` = live instance. |

---

## nx-icouponads Onboarding — Findings & Open Questions

> Survey conducted 2026-04-30. Town is the only active Cowboy Era town; onboarding to Wild West is the priority.

### What Already Exists (Cowboy Era)

| Artifact | Location | Notes |
|---|---|---|
| `.wildwest/` | repo root | Exists — but Cowboy Era layout, not canonical Wild West spec |
| `.wildwest/telegraph/` | live | Real inter-actor memos present; ready for `.last-beat` |
| `.wildwest/scripts/` | live | Session scripts: `session-start.sh`, `session-end.sh`, `validate-timestamps.sh`, `cleanup-telegraph.sh`, etc. — NOT governance scripts |
| `.wildwest/dailies/` | live | BOD/EOD summaries (no Wild West equivalent yet) |
| `.wildwest/dev-pairs/` | live | Dev pair tracking |
| `.wildwest/models/` | live | Model tracking |
| `.wildwest/registry.json` | live | Branch/actor registry |
| `.wildwest/living-docs.json` | live | Living docs tracker |
| `docs/branches/` | repo root | Mature branch lifecycle — 5 states: drafts/planned/active/merged/abandoned |
| `scripts/` | repo root | Lifecycle scripts: `plan-branch.sh`, `activate-branch.sh`, `retire-branch.sh`, `spawn-branch.sh`, `generate-branch-index.sh`, `abandon-branch.sh`, etc. |
| `~/wildwest-vscode/reneyap/` | home | Session exporter already capturing — extension partially live |

### What's Missing for Full Wild West

| Missing | Impact | Notes |
|---|---|---|
| `.wildwest/worktrees/_heartbeat/` | No heartbeat | The only thing initTown must add |
| `.wildwest/board/` | No board | Branch lifecycle at `docs/branches/` — needs migration |
| `.last-beat` sentinel | No liveness signal | Will write automatically once _heartbeat worktree exists and workspace opens |

### Key Facts

- **getTownRoot() works today** — `.wildwest/scripts/` exists → extension detects nx-icouponads as governed town correctly
- **Session exporter already running** — `~/wildwest-vscode/reneyap/` confirms extension is capturing
- **`docs/branches/` has 5 states** — includes `drafts/` which our spec doesn't have yet (open question: add to spec?)
- **`scripts/` at repo root** — mature lifecycle scripts; point to `docs/branches/` paths; need path updates on migration
- **`nx-icouponads.code-workspace`** — single root (just `.`); no multi-root complexity

---

## Open Questions / Decisions Needed

### Q1 — initTown: run or bypass?
**RESOLVED 2026-04-30 — Bypass.**

initTown checks `fs.existsSync('.wildwest/')` — bails immediately with "already initialized" if the dir exists at all. nx-icouponads already has `.wildwest/` → initTown is a no-op; it won't touch anything.

`_heartbeat` worktree must be added manually:
```bash
git checkout -b _heartbeat
git worktree add .wildwest/worktrees/_heartbeat _heartbeat
git checkout main
# add .wildwest/worktrees/ to .gitignore
```
Zero risk. Surgical.

### Q2 — `docs/branches/` → `.wildwest/board/branches/` migration
**RESOLVED 2026-04-30 — Defer (Option C).**

Get heartbeat alive first. Migration planned as a named branch (`chore/wildwest-migration`) — dogfoods the lifecycle process itself.

### Q3 — `drafts/` state
**RESOLVED 2026-04-30 — Add to spec.**

nx-icouponads' branch lifecycle is battle-tested through many real scenarios in the Cowboy Era. `drafts/` earned its place — it captures rough ideas that are committed enough to put on the board but not yet specced enough for `planned/`. Skipping it loses a stage that proved necessary in practice.

Final state order: `drafts/ → planned/ → active/ → merged/ | abandoned/`

**Action:** Add `drafts/` to wildwest-directory-spec.md and wildwest-vscode board/branches/ structure.

### Q4 — `dailies/` BOD/EOD
**RESOLVED 2026-04-30 — Formalize as canonical.**

nx-icouponads' Cowboy Era implementation is the source material for the Wild West framework — proven in practice, lifted as the starting point. `dailies/` earned its place through real daily usage.

`dailies/` is a canonical `.wildwest/` subdir. Content format (BOD/EOD templates) is town-defined; spec declares the dir exists, not what's in it.

**Action:** Add `dailies/` to wildwest-directory-spec.md.

### Q5 — `registry.json` at town level vs. county level
**RESOLVED 2026-04-30 — Keep both. Cascading, recursive.**

Registry pattern repeats at each level of the hierarchy — same shape, different scope:

| Level | Location | Tracks |
|---|---|---|
| County | `~/counties/<county>/registry.json` | Authors, towns, county-wide roles |
| Town | `.wildwest/registry.json` | Branches, sessions, activation metadata, per-branch actor assignments |

Not a conflict — a hierarchy. Each level owns its scope. Lower levels can reference up; upper levels don't reach down. This is a core architectural principle of the Wild West framework.

**Action:** Document cascading registry pattern in wildwest-framework spec.

### Q6 — `~/wildwest-vscode/` session exporter path
**RESOLVED 2026-04-30 — Relocate to `~/wildwest/sessions/`.**

`~/wildwest/` is the framework home for all artifacts that belong to the Wild West framework but live outside any specific county or town. Same cascading, recursive pattern:

```
~/wildwest/              ← framework home (local runtime; cross-county)
~/counties/<county>/     ← county instance
  <town>/
    .wildwest/           ← town governance domain
```

Session logs are framework-level (one session can span multiple repos) — they belong at `~/wildwest/sessions/<actor>/`, not scoped to a tool name or repo.

**Current:** `~/wildwest-vscode/reneyap/`, `~/wildwest-vscode/unknown/`
**Target:** `~/wildwest/sessions/reneyap/`, `~/wildwest/sessions/unknown/`

**All framework-level artifacts → `~/wildwest/`:**
- `sessions/` — session exporter output
- `TODO.md` — already here
- Cross-county tooling, config, future MCP config
- Framework-level logs and registries

**Actions:**
- Update wildwest-vscode `sessionExporter.ts` output path → `~/wildwest/sessions/`
- Migrate existing `~/wildwest-vscode/` content → `~/wildwest/sessions/`
- Update wildwest-framework spec to document `~/wildwest/` as framework home
- `~/wildwest-vscode/` retired as a path

---

## `~/wildwest/` — Full Trunk (Architectural Principle, updated)

`~/wildwest/` is the single root of the entire Wild West world on this machine. One world, one trunk. Everything Wild West lives here.

```
~/wildwest/
  counties/              ← was ~/counties/ — governed county instances
    <county>/
      registry.json
      <town>/
        .wildwest/
          registry.json  ← cascading; town-level scope
  frontier/              ← was ~/frontier/ — experimental, ungoverned, pre-Wild West
    _ARCHIVE/            ← Cowboy Era archives
    devflow/             ← Cowboy Era devflow framework
    <project>/           ← experimental projects (graduate to counties/ when governed)
  sessions/              ← was ~/wildwest-vscode/ — session exporter output (all actors)
  TODO.md                ← cross-county planning (this file)
```

**Cascade is complete:**
- `~/wildwest/` — framework root (this machine)
- `~/wildwest/counties/<county>/` — county instance
- `~/wildwest/counties/<county>/<town>/.wildwest/` — town governance domain
- Frontier = inside the world, ungoverned edge; projects graduate frontier → counties

**Migration required (do as a planned, single-session operation):**

| Move | From | To |
|---|---|---|
| Counties | `~/counties/` | `~/wildwest/counties/` |
| Frontier | `~/frontier/` | `~/wildwest/frontier/` |
| Sessions | `~/wildwest-vscode/` | `~/wildwest/sessions/` |

**Migration risks:**

| Risk | Severity | Mitigation |
|---|---|---|
| Hardcoded `~/counties/` paths in CLAUDE.md files | Medium | Find + replace across all county/town CLAUDE.md |
| VSCode `.code-workspace` files | Medium | Update folder paths |
| Scripts hardcoding `~/counties/` (nx-icouponads, etc.) | Medium | Grep + update |
| Claude Code memory is path-scoped | High | Memory for wildwest-framework keyed to current path — orphaned on move; rebuild in new path |
| `git remote` URLs | None | Remote refs unaffected by local path |

**Transition strategy:** Symlink `~/counties/` → `~/wildwest/counties/` and `~/frontier/` → `~/wildwest/frontier/` during migration window while references are updated. Remove symlinks once clean.

---

## Migration Plan — Graft `~/counties/` and `~/frontier/` into `~/wildwest/`

> **Decision:** 2026-04-30 — `~/wildwest/` is the single trunk. One world.

### Phase 1 — Safe moves (no active session dependency)

| Step | Action | Risk |
|---|---|---|
| 1a | Move `~/frontier/` → `~/wildwest/frontier/` | Low — nothing active |
| 1b | Symlink `~/frontier` → `~/wildwest/frontier/` | Keeps old refs working |
| 1c | ~~Move `~/wildwest-vscode/` → `~/wildwest/sessions/`~~ | **Skipped — exporter rebuilds from source. Old content left in place; clean up later.** |
| 1d | Update sessionExporter.ts output path → `~/wildwest/sessions/` | **Done — v0.4.0 shipped.** |

### Phase 2 — `~/counties/` move (requires clean session break)

| Step | Action | Notes |
|---|---|---|
| 2a | End current Claude Code session | Memory is path-scoped — must end cleanly |
| 2b | Move `~/counties/` → `~/wildwest/counties/` | Core move |
| 2c | Symlink `~/counties` → `~/wildwest/counties/` | Keeps all existing refs working during transition |
| 2d | Update CLAUDE.md files | Find + replace `~/counties/` → `~/wildwest/counties/` |
| 2e | Update VSCode `.code-workspace` files | Folder paths break on move |
| 2f | Update scripts (nx-icouponads, etc.) | Grep for hardcoded `~/counties/` |
| 2g | Start new session from `~/wildwest/counties/wildwest-ai/wildwest-framework/` | Memory rebuilds at new path |
| 2h | Remove symlinks when all references clean | Final cutover |

### Memory impact

Claude Code memory is path-scoped. Current memory location:
`~/.claude/projects/-Users-reneyap-counties-wildwest-ai-wildwest-framework/`

After Phase 2, new path:
`~/.claude/projects/-Users-reneyap-wildwest-counties-wildwest-ai-wildwest-framework/`

**Cost:** Memory orphaned — accepted. Rebuild in new session from new path.
**Mitigation:** Before ending session, dump key memory to `~/wildwest/TODO.md` or a handoff doc so nothing critical is lost.

---

## Recommended Onboarding Sequence (updated)

1. **Open nx-icouponads in VSCode** — heartbeat starts writing `.last-beat` immediately
2. **Manually add `_heartbeat` worktree** — surgical; bypass initTown (Q1)
3. **Relocate session exporter → `~/wildwest/sessions/`** — update sessionExporter.ts, migrate existing content (Q6)
4. **Create `.wildwest/board/`** — stub board/README.md and board/branches/ with 5-state structure (drafts/planned/active/merged/abandoned)
5. **Plan migration branch** — `chore/wildwest-migration` as a planned branch; dogfoods the lifecycle (Q2)
6. **Migrate `docs/branches/` → `.wildwest/board/branches/`** on that branch
7. **Distill all decisions → wildwest-framework spec** — Q3 (drafts/), Q4 (dailies/), Q5 (cascading registry), Q6 (~/wildwest/ home)

---

## Architectural Decisions — Identity & Federation

### wwuid — Wild West Unique ID

**Decision 2026-04-30 — All first-class entities get a wwuid. Format: UUID v4.**

Federation is in scope. wwuid must be globally unique from day one — no retrofitting.

**Entities that get wwuid:**

| Entity | wwuid | Reason |
|---|---|---|
| County | Yes | Can rename; framework refs must survive |
| Town | Yes | Can rename; county refs must survive; same town on two machines = distinct identities |
| Session | Yes | Each session instance needs a unique ID |
| Branch | TBD | Rename is rare; name may be sufficient natural key |
| DevPair instance | TBD | Useful for tracking concurrent instances (TM(RHk) vs aCD(RHk)) |
| Author | No | Already has permanent code (single char, never reused) |
| DevPair class | No | Already has permanent code (RSn, RHk) |

**Format:** Four identity fields. Same pattern as Git (SHA = canonical, branch name = renameable alias).

| Field | Value | Mutable | Purpose |
|---|---|---|---|
| `wwuid` | UUID v4 — e.g. `550e8400-e29b-41d4-a716-446655440000` | Never | Canonical identity — what systems and federation reference |
| `alias` | Human-friendly slug — e.g. `wildwest-vscode` | Yes | Display name — renameable without breaking refs |
| `remote` | Git remote URL — e.g. `https://github.com/wildwest-ai/wildwest-vscode` | Rarely | Network address — path-independent, machine-independent |
| `mcp` | MCP endpoint URL — e.g. `https://mcp.wildwest-vscode.example.com` | When live | .wwMCP server address — `null` until implemented |

**Complete town registry.json identity block:**
```json
{
  "wwuid": "550e8400-e29b-41d4-a716-446655440000",
  "alias": "wildwest-vscode",
  "remote": "https://github.com/wildwest-ai/wildwest-vscode",
  "mcp": null
}
```

- `wwuid` set at `initTown`, never changes — what MCP/federation references
- `alias` renameable — rename the town → update alias; wwuid stays; all cross-references remain valid
- `remote` path-independent stable network address — federation can reach the town without knowing local path; doesn't break when `~/counties/` moves to `~/wildwest/counties/`
- `mcp` null today — populated when `.wwMCP` is live; county registry can query all towns where `mcp != null`

**Where it lives:**
- Town: `.wildwest/registry.json` — identity block + governance data (branches, sessions, actors)
- County: `~/wildwest/counties/<county>/registry.json` — same identity block per town entry
- County registry references towns by `wwuid`, not name or local path
- Session: session file header → `wwuid` of the town + actor devPair code

**Implication:** `registry.json` schema must be designed for MCP queryability now — clean fields, stable keys, no ad-hoc structure.

---

### .wwMCP — Wild West MCP Server

**Decision 2026-04-30 — Each town/county will expose a `.wwMCP` server when the framework matures.**

MCP (Model Context Protocol) is the federation protocol. Towns and counties expose their governance data as MCP resources — queryable by AI actors, other towns, and the Sheriff's county-wide view.

**What .wwMCP exposes:**

| Resource | Data |
|---|---|
| Town registry | actors, branches, sessions |
| Board state | active branches, queue, shipped |
| Telegraph | inter-actor memos |
| Heartbeat | liveness signal |
| County registry | towns, authors, county-wide roles |

**What federation enables:**
- AI in one town queries another town's board over MCP
- Cross-county coordination without shared filesystem
- Sheriff gets county-wide view by querying all town MCPs
- `wildwest.code-workspace` becomes a local convenience, not the only view into the world

**The stack (when mature):**
```
.wwMCP server (per town)  <->  MCP client (extension, Claude Code, other actors)
     |
registry.json + board/ + telegraph/   <- local source of truth
     |
wwuid  <- stable identity layer
```

**Phasing:** wwuid and clean registry schema now. .wwMCP implementation deferred until framework is mature and federation use cases are concrete.

---

## Session Handoff — 2026-04-30 12:57 UTC

### Phase 1 status
- ✅ 1a: `~/frontier/` → `~/wildwest/frontier/`
- ✅ 1b: symlink `~/frontier` → `~/wildwest/frontier/`
- ✅ 1c: skipped — exporter rebuilds sessions from source
- ✅ 1d: v0.4.0 shipped — sessions now write to `~/wildwest/sessions/`

### Phase 2 status
- ✅ `~/counties/` COPIED to `~/wildwest/counties/` — original still at `~/counties/` as fallback
- ⬜ Verify repos, git, extension all work from new path
- ⬜ Update CLAUDE.md files, workspace files, scripts — replace `~/counties/` with `~/wildwest/counties/`
- ⬜ Delete `~/counties/` original once confident
- ⬜ Symlink `~/counties` → `~/wildwest/counties/` for remaining stale refs (optional)

---

## Session Handoff — 2026-04-30 13:25 UTC

### What was decided this session

**wildwest.code-workspace** — written at `~/wildwest/wildwest.code-workspace`
- Multi-root: wildwest-framework, wildwest-vscode, nx-icouponads, vscode-auto-chat-export, wooqb, frontier
- Named folders with relative paths from `~/wildwest/`
- `files.exclude` + `search.exclude` for node_modules, dist, build, .wildwest/worktrees

**getTownRoot() marker** — keying on `.wildwest/scripts/` is wrong (arbitrary proxy)
- Correct marker: `.wildwest/registry.json` (presence = governed town; content = version manifest)
- `getTownRoot()` and `getRepoRoot()` should key on `.wildwest/registry.json`
- `initTown` creates registry.json; extension updates `extensionVersion` on activation

**Identity model — registry.json** — four fields, decided and finalized:
```json
{
  "wwuid": "550e8400-e29b-41d4-a716-446655440000",
  "alias": "wildwest-vscode",
  "remote": "https://github.com/wildwest-ai/wildwest-vscode",
  "mcp": null
}
```
- `wwuid` = UUID v4, set at initTown, never changes — canonical identity for systems/federation
- `alias` = human-friendly, renameable — display name; rename town → update alias only
- `remote` = git remote URL — path-independent, machine-independent network address
- `mcp` = null until .wwMCP ships — county can query all towns where mcp != null

**Federation** — in scope. .wwMCP deferred but designed for from day one.
- See "Architectural Decisions — Identity & Federation" section above for full spec

**Memory warning** — Claude Code memory is path-scoped. Old memory at:
`~/.claude/projects/-Users-reneyap-counties-wildwest-ai-wildwest-framework/`
New session from wildwest.code-workspace will key on the first governed folder's path.
This TODO.md is the handoff doc — memory rebuilds from here.

### Next session checklist
1. Open `~/wildwest/wildwest.code-workspace` in VSCode
2. Confirm heartbeat alive — `.last-beat` writing to wildwest-vscode (not wildwest-framework)
3. Confirm sessions writing to `~/wildwest/sessions/`
4. Fix `getTownRoot()` + `getRepoRoot()` → key on `.wildwest/registry.json` not `.wildwest/scripts/`
5. Design `registry.json` schema (identity block + governance data) — update initTown to write it
6. Update CLAUDE.md files, workspace files, scripts — replace `~/counties/` with `~/wildwest/counties/`
7. Delete `~/counties/` original once confident
8. Then: nx-icouponads onboarding (`_heartbeat` worktree + board)
