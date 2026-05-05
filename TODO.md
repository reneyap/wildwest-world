<!-- updated: 20260505-0151Z (21:51 EDT) -->

# Wild West — TODO

> **Created:** 2026-04-30 12:17 UTC
> **Last updated:** 2026-05-05 01:51Z
> **Scope:** Cross-county planning — not committed to any repo

---

## Now

> Max 5 items. What S(R) touches next. Promote from area sections; remove when done.

| # | Item | Area | Why Now |
|---|---|---|---|
| 1 | **Resolve identity block shape** | Open Decision | Blocking town registry template + `TownInit.ts` fix |
| 2 | **Fix `SoloModeController.hasBranchDoc()` path** | wildwest-vscode | T2 solo mode is fully broken for all compliant towns |
| 3 | **Add `scope: "town"` to wildwest-vscode `.wildwest/registry.json`** | wildwest-vscode | Own town not canonically detected without it |
| 4 | **Add cold-start checklist to wildwest-framework CLAUDE.md** | wwFramework | Every actor opening that workspace starts blind |
| 5 | **Fix `TownInit.ts` — write `scope` field** | wildwest-vscode | Depends on #1; every future town bootstrapped by initTown has the gap |

---

## Open Decisions — Blocking

| Decision | Options | Blocker For |
|---|---|---|
| **Identity block shape** | A) Flat — `scope`, `wwuid`, `alias`, `remote`, `mcp` at top level (ICA town style) vs. B) Wrapped — `"identity": { wwuid, alias, remote, mcp }` (county template style). Currently mixed: county template uses B; ICA town uses A; wildwest-vscode registry.json uses partial-A. | Town registry template; `TownInit.ts` fix |
| **`heartbeat.sh` fate in framework `scripts/`** | Retire (delete or archive) vs. annotate as deprecated vs. replace with lifecycle script templates | Framework `scripts/` cleanup |
| **Registry `path` fields + gitignore policy** | **Decided: Option A + instance clarification** — remove `path` fields from world + county registries; derive paths from `alias` + world root + counties dir. Registry committed at all scopes (world, county, town) for committed instances — world registry is a governance artifact, not a machine-local index. "World gitignored" assumption was pre-instance-model and is superseded. Gitignore only `.last-beat` sentinels (runtime-only). Requires `HeartbeatMonitor.ts` changes + new VSCode settings. | County registry commit status; federation readiness |

---

## wwFramework

### Immediate

| Item | Detail |
|---|---|
| **Add cold-start checklist to wildwest-framework CLAUDE.md** | wildwest-vscode CLAUDE.md has section 3 (Cold-Start Checklist). wildwest-framework CLAUDE.md has no equivalent. Any actor opening that workspace starts blind. |
| **CLAUDE.town.md template must include cold-start section** | Every town CLAUDE.md scaffolded by `TownInit.ts` must include a Copilot cold-start checklist. Without it, cold-start failure is the default for every new town. |

### Spec & Templates

| Item | Detail |
|---|---|
| **Update README paths** | Still references `~/counties/<county>/` — update to `~/wildwest/counties/<county>/`. Getting Started section + repo structure diagram. |
| **Create `registry.town.json` template** | Framework has a county registry template but no town equivalent. ICA registry + identity block = the full shape. Depends on identity block shape decision. |
| **Lift ICA lifecycle scripts → framework templates** | `plan-branch.sh`, `activate-branch.sh`, `retire-branch.sh`, `spawn-branch.sh`, `abandon-branch.sh`, `generate-branch-index.sh` exist in ICA `scripts/` — parameterize for any town (`$TOWN_ROOT/.wildwest/`) and add to `wildwest-framework/scripts/`. |
| **Retire / annotate `heartbeat.sh`** | The bash heartbeat in `scripts/` is superseded by native Node.js in wildwest-vscode. Misleading as a "template." Depends on `heartbeat.sh` fate decision. |
| **Spec county + world directory level** | `wildwest-directory-spec.md` covers town exhaustively; county and world are described by analogy. Add explicit canonical structure for each. |
| **Document `dailies/` BOD/EOD format** | Framework declares `dailies/` as canonical (Q4) but has no example. ICA's entries are the reference implementation. |

### Town Bootstrap Templates

| Item | Detail |
|---|---|
| **Spec: town CLAUDE.md minimum viable content** | Document what every town CLAUDE.md must contain at minimum: town scope, active roles + devPairs, telegraph rules, board path, key paths, cold-start checklist. Lives in `wildwest-framework/docs/`. |
| **Create `CLAUDE.town.md` template** | Starter town CLAUDE.md with placeholders (`<town-alias>`, `<county>`, `<wwuid>`, actor model). Pattern: county CLAUDE.md adapted for town scope. |
| **Update `TownInit.ts` to scaffold `CLAUDE.md`** | On `initTown`: generate `CLAUDE.md` from `CLAUDE.town.md` template, substituting `alias`, `wwuid`, `remote`, county from registry. Eliminates cold-start briefing gap for all future towns. |
| **Repo memory baseline per town** | `/memories/repo/` is empty everywhere. Each town should have baseline repo memory: town identity, active roles, telegraph path, board path. Note: repo memory is machine-local — not a portability layer; town CLAUDE.md remains the portable source of truth. |

### Session Continuity Gaps

| Item | Detail |
|---|---|
| **`session-continuity.md` is not scope-aware** | Protocol written from town scope perspective. A `.Cld` session can operate at world, county, or multi-town scope. Branch lifecycle rules differ by scope — need explicit per-scope rules. |
| **Multi-town session protocol undefined** | When CD operates at world/county scope and touches multiple town repos in one session, no protocol defines: whether to branch per-town-touched, how to sequence commits across repos, or how session dumps reference cross-repo work. |
| **World/county scope branch rules** | Town scope: branch lifecycle (plan/activate/retire). World/county scope: coordination artifacts (`TODO.md`, `DONE.md`, session dumps). Current working assumption: no branch needed at world root for coordination artifacts; branch per-town when making town changes within a world-scope session. Needs codification. |
| **Session memory protocol undefined** | No convention for when/what to write to `/memories/session/`. Spec should define: write session memory at cold-start with current task context; update at each major step; guidance on when it's worth writing vs. leaving empty. |

### Telegraph Memo Convention + Script

| Item | Detail |
|---|---|
| **Codify memo filename convention in spec** | Canonical format: `<timestamp>-to-<role>(<actor>).<channel>-from-<role>(<actor>).<channel>--<subject>.md`. Lives in `wildwest-framework/docs/` or `wildwest-directory-spec.md`. Note: pre-channel memos (`2026-05-02`) omit `.channel` suffix — both forms valid. |
| **Create `send-memo.sh` script** | Args: `--to`, `--from`, `--subject`, `--town`. Generates filename from timestamp + args; opens editor for body; writes to `<town>/.wildwest/telegraph/`. Lives in `wildwest-framework/scripts/`. |

### Spec Gaps

> Found 2026-05-01 — surfaced during first strictly-compliant wwFramework session in nx-icouponads.

| Gap | Description | Action |
|---|---|---|
| **TM-absent protocol** | No fallback defined when TM window unavailable — CD cannot run TM lifecycle scripts; no documented escalation | Add to `wildwest-framework/docs/` or role CHARTERs |
| **Role-channel enforcement** | Nothing prevents CD from running TM scripts; framework relies on actor discipline only | Spec: clarify honor-system model. wwVscode: design role-awareness warning |
| **CD town visit protocol** | No spec language defining which tools CD may touch during a town visit | Add "CD town visit protocol" to CD CHARTER.md |
| **Stale `active/` doc detection** | Branch merged on remote but `active/` doc still present; TM retirement failure mode | wwVscode: board integrity check |

### nx-icouponads — Framework Reference

> ICA is the richest implementation. Source material for framework templates. Onboarding deferred — see nx-icouponads section.

| Artifact | Location | Notes |
|---|---|---|
| `.wildwest/` | repo root | Cowboy Era layout — not canonical Wild West spec |
| `.wildwest/telegraph/` | live | Real inter-actor memos present |
| `.wildwest/scripts/` | live | Session scripts — NOT governance scripts |
| `.wildwest/dailies/` | live | BOD/EOD summaries; canonical in wwFramework spec (Q4) |
| `.wildwest/registry.json` | live | Branch/actor registry |
| `docs/branches/` | repo root | 5-state lifecycle: drafts/planned/active/merged/abandoned |
| `scripts/` | repo root | Lifecycle scripts: `plan-branch.sh`, `activate-branch.sh`, etc. |

### World-Scope Governance

| Item | Detail |
|---|---|
| **Instantiate RA (Ranger) role** | RA is the world-scope operator — right hand to G, world equivalent of CD. Not yet instantiated. Needs: devPair assignment, CHARTER reference, entry in world registry, world CLAUDE.md section. |
| **World CLAUDE.md** | `~/wildwest/CLAUDE.md` — world-scope law and cold-start context for any actor operating at world scope. Equivalent of county CLAUDE.md one level up. Should cover: G/RA roles, world registry, county map, telegraph rules at world scope. |
| **Update world registry with G/RA identity** | `~/wildwest/.wildwest/registry.json` currently has no `governor` or `ranger` fields. Add world-scope actor entries consistent with the full authority gradient. |
| **Spec: G county list = G's authority relationships** | Codify in framework spec: a world instance's county list = counties where G holds authority. Not framework-defined. Different world instances have different counties. |

### Deferred — World Repo

| Item | Notes |
|---|---|
| **Create `wildwest-ai/wildwest-world` on GitHub** | Template/reference world instance. Do after `reneyap/wildwest-world` is stable and structure is proven. |
| **wwTrust SSOT — `~/wildwest/.wildwest/trust-policy.json`** | World-scope trust-policy.json. Cascade: county + town extend/revoke. Sync script writes projection → `.code-workspace` autoApprove. Deferred — do after ICA feature pressure subsides. |
| **Public release prep (wildwest-framework)** | Review pass, MIT license, wildwest-county template repo, CONTRIBUTING.md. Prerequisite for `wildwest-ai/wildwest-world` on GitHub. |
| **Frontier onboarding** | `agentic-handbook`, `devflow`, `teamgpt` identified as candidates in `~/wildwest/frontier/`. Many others unclassified (`nx-teststream`, `nx-tictactoe`, etc.). No timeline — do in a gap between active county work. |
| **wwMCP federation** | When implemented: each world instance exposes a G endpoint over MCP. Multiple G instances federate cross-machine without shared filesystem. `mcp: null` in all registries is the placeholder. |

---

## wildwest-vscode

### Immediate — Blocking Framework Behavior

| # | Item | Detail |
|---|---|---|
| 1 | **Fix `SoloModeController.hasBranchDoc()` path** | Currently checks `docs/branches/active/<branch>/README.md` — the pre-spec path. Canonical: `.wildwest/board/branches/active/<branch>/README.md`. T2 solo mode never activates correctly for any compliant town. |
| 2 | **Add `scope: "town"` to `.wildwest/registry.json`** | Bootstrapped without `scope`. Without it, extension falls back to `.wildwest/scripts/` presence — own town not canonically detected. |
| 3 | **Fix `TownInit.ts` — write `scope` field** | `initTown` writes `{wwuid, alias, remote, mcp}` but omits `scope: "town"`. Every future town bootstrapped by initTown will have the same gap. Depends on identity block shape decision. |

### World Root Configurability

> Decided 2026-05-03. Required for Option A (registry path removal) and framework portability. Memo sent to TM(RHk).Cpt: `20260503-1233Z-CD-to-TM--registry-path-removal-and-world-root-config.md`

| # | Item | Detail |
|---|---|---|
| 1 | **Add `wildwest.worldRoot` VSCode setting** | Default `~/wildwest`. Passed into HeartbeatMonitor + sessionExporter as constructor param. |
| 2 | **Add `wildwest.countiesDir` VSCode setting** | Default `counties`. Relative to worldRoot. Used to derive county + town paths. |
| 3 | **Add `wildwest.sessionsDir` VSCode setting** | Default `sessions`. Relative to worldRoot. Used by sessionExporter output path. |
| 4 | **Update `HeartbeatMonitor.beatCounty()`** | Replace `t.path` read with `path.join(worldRoot, countiesDir, countyAlias, t.alias)` |
| 5 | **Update `HeartbeatMonitor.beatWorld()`** | Replace `c.path` read with `path.join(worldRoot, countiesDir, c.alias)` |
| 6 | **Remove `path` fields from world + county registries** | After extension ships the setting; world registry stays gitignored; county registry becomes committable. |

### Near-term

| Item | Detail |
|---|---|
| **Write CLAUDE.md at wildwest-vscode town level** | TM(RHk).Cpt needs handoff context. Minimum: town scope, role, telegraph rules, board path. Interim fix — superseded once `TownInit.ts` scaffolds CLAUDE.md from template. |
| **T3 exploration fork in SoloModeController** | `getTier()` is assessment-only. T3 (fork creation at `explore/<branch>--<reason>--<date>`) is the core solo mode value proposition. |
| **`TownInit.ts`: scaffold `CLAUDE.md` from template** | Depends on `CLAUDE.town.md` template in wildwest-framework. Eliminates cold-start briefing gap for all towns bootstrapped by initTown. |
| **Registry validator utility** | `validateRegistry()` — validates registry.json shape, detects stale paths, confirms wwuid format. Recommended by TM during isolation probe (2026-05-02). Deferred at the time. |
| **Model identity gap in staged sessions** | No canonical capture of which model ran a given session. Current best proxy: Copilot `thinking` field often contains model self-declaration (e.g. `"Claude Haiku 4.5"`). Needs a proper model identity field in the staged schema. Affects all three tools. |

### Backlog

| Item | Detail |
|---|---|
| **Command palette split** | Separate "Wild West: Sessions" and "Wild West: Governance" command categories in `package.json`. UX only — no functional change. |
| **MCP integration** | Migrate `.wildwest/` artifacts to an MCP server; expose `sendMessage`, `readTelegraph`, `reportHeartbeat`. Enables cross-machine and remote-scope operations. Long-term architectural direction. |
| **Role-awareness warning** | Extension should know which devPair opened the workspace and warn when an actor attempts an out-of-role action (e.g. CD running a TM lifecycle script). Tooling complement to the framework honor-system model. |

### Telegraph Visibility — Multi-Scope Badge

> Surfaced from TM incident report (2026-05-04) + world-scope observer discussion.
> Spec written into `wildwest-framework/docs/telegraph-delivery.md` — "Visibility Layer" section.

| Item | Detail |
|---|---|
| **Status bar telegraph badge** | `TelegraphWatcher` aggregation mode: scan all workspace folders with `.wildwest/`, count unread per town, show `$(mail) N` total + tooltip breakdown. Single-folder = single count (existing behavior); multi-root = aggregate (world-observer mode). Target: v0.14.x |
| **Badge reads from `inbox/`** | Badge should read `inbox/` (post-delivery model) not telegraph root. Inbox/outbox split (v0.13.x delivery model) should land first. |
| **Telegraph ≠ Heartbeat** | Decision recorded: keep separate. `TelegraphWatcher` = event-driven (fs watch), badge = read surface. `HeartbeatMonitor` = write/delivery. Do not merge. |
| **World-scope aggregation → MCP boundary** | Cross-machine or remote scope visibility = wwMCP job. Same badge interface; data source swaps. Local FS aggregation = extension handles natively. |

---

## nx-icouponads — Deferred Onboarding

> Deferred 2026-05-01 — product time pressure. Activate as `chore/wwtownmigration` in a gap between feature branches.

### What's Missing for Full Wild West

| Missing | Notes |
|---|---|
| `.wildwest/worktrees/_heartbeat/` | Only thing initTown must add — surgical `git worktree add` only; initTown is a no-op (`.wildwest/` exists) |
| `.wildwest/board/` | Branch lifecycle at `docs/branches/` — migration planned as `chore/wwtownmigration` |
| `.last-beat` sentinel | Writes automatically once `_heartbeat` worktree exists and workspace opens |

### Recommended Onboarding Sequence

1. **Open nx-icouponads in VSCode** — heartbeat starts writing `.last-beat` immediately
2. **Manually add `_heartbeat` worktree** — bypass initTown (already has `.wildwest/`)
3. **Create `.wildwest/board/`** — stub board/README.md and board/branches/ with 5-state structure
4. **Plan migration branch** — `chore/wwtownmigration`; dogfoods the lifecycle
5. **Migrate `docs/branches/` → `.wildwest/board/branches/`** on that branch

### Migration Items

| Item | Notes |
|---|---|
| Move wwLifecycle scripts | `scripts/activate-branch.sh`, `retire-branch.sh`, etc. → `.wildwest/scripts/`. Update all callers and branch docs. |
| Audit `.wildwest/` layout | Align to canonical wwGovernance spec. Remove/relocate Cowboy Era artifacts. |
| Update autoApprove paths | `nx-icouponads.code-workspace` autoApprove: update paths to `.wildwest/scripts/` after script migration. Interim fix until wwTrust SSOT + sync script is built. |
