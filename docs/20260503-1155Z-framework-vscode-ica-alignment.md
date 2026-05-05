# wwFramework × wildwest-vscode × ICA Alignment — 20260503T1155Z

> **Session type:** Plan (.Cld chat window)  
> **Scope:** wwWorld + wwCounty + wwTown  
> **Author:** CD(RSn).Cld

---

## 1. wwFramework — Current State

### What's There

| Artifact | State |
|---|---|
| `docs/wildwest-directory-spec.md` | Complete. Town-level canonical structure. Covers board 5-state lifecycle, heartbeat, dailies, docs, scripts, registry discriminator. |
| `docs/heartbeat.md` | v3 cascading scope fully designed (scope walking, interval inheritance, per-scope sentinels). v3 implemented in wildwest-vscode. |
| `docs/solo-mode.md` | Fully designed (T1–T4 tiers, fork pattern). Not yet enforced by tooling. |
| `docs/landscape.md` | Living competitive positioning doc. |
| `roles/*/CHARTER.md` | All seven roles have CHARTER.md: S, CD, aCD, TM, DM, DS, HG. |
| `registry.json` (root) | County template with identity block + towns array. `scope: "county"`. Has `identity` wrapper object. |

### What's Missing or Stale

| Gap | Detail |
|---|---|
| **README paths stale** | Still references `~/counties/<county>/` — pre-migration. Should be `~/wildwest/counties/<county>/`. |
| **No town registry.json template** | `registry.json` at root is a county template. No equivalent town template exists. ICA's registry.json is the reference for what a full town registry looks like. |
| **No lifecycle script templates** | `scripts/` contains Cowboy Era session-management scripts from ICA (`session-start.sh`, `cleanup-telegraph.sh`, `heartbeat.sh`, etc.) — NOT the branch lifecycle scripts. `plan-branch.sh`, `activate-branch.sh`, `retire-branch.sh`, `spawn-branch.sh`, `abandon-branch.sh`, `generate-branch-index.sh` are in ICA but not in the framework. |
| **`heartbeat.sh` in scripts/ is retired** | The bash heartbeat is superseded by native Node.js in wildwest-vscode. The framework script is stale and misleading. |
| **County/world directory spec thin** | `wildwest-directory-spec.md` details town level exhaustively; county and world scope are covered only by analogy. |
| **Framework gaps (found 2026-05-01)** | TM-absent protocol; role-channel enforcement; CD town visit protocol; stale `active/` doc detection. |
| **Registry identity block schema inconsistency** | County template wraps identity in an `"identity": {}` object. ICA town registry has flat `scope` + top-level fields. Town vs county shapes diverge — needs a decision. |

---

## 2. wildwest-vscode — Framework Alignment

### Aligned ✓

| Feature | File |
|---|---|
| Heartbeat v3 cascading scope (scope walking, interval inheritance, per-scope sentinels) | `HeartbeatMonitor.ts` |
| Session export raw → staged; 3-tool pipeline | `sessionExporter.ts`, `batchConverter.ts`, `chatSessionConverter.ts` |
| Telegraph inbox + Rule 23 enforcement | `TelegraphInbox.ts`, `TelegraphWatcher.ts` |
| initTown wizard; `.wildwest/` structure creation | `TownInit.ts` |
| Town root detection on `.wildwest/registry.json` | `HeartbeatMonitor.ts`, `WorktreeManager.ts` |
| `_heartbeat` worktree inside `.wildwest/worktrees/` | `WorktreeManager.ts` |

### Misalignments / Gaps

| Issue | Detail | Impact |
|---|---|---|
| **wildwest-vscode `registry.json` missing `scope: "town"`** | Bootstrapped today with identity block only: `{wwuid, alias, remote, mcp}`. Spec requires `scope: "town"` as the canonical town discriminator. Without it, own extension falls back to `.wildwest/scripts/` presence for this repo. | High — extension can't canonically detect wildwest-vscode as a governed town |
| **`initTown` doesn't write `scope`** | Forward gap — every new town bootstrapped by initTown is also missing the discriminator | High — all future towns affected |
| **`SoloModeController.hasBranchDoc()` uses stale path** | Checks `docs/branches/active/<branch>/README.md` — the pre-spec path. Canonical spec path is `.wildwest/board/branches/active/<branch>/README.md`. T2 detection is always false for compliant towns. | Medium — T2 solo mode never activates correctly |
| **`SoloModeController` is assessment-only** | `getTier()` reports T1/T2/T4. T3 (exploration fork creation) not implemented. No enforcement — caller must act on the tier. | Medium — solo mode is passive; no autonomous action |
| **No CLAUDE.md at town level** | TM(RHk).Cpt has no handoff context at wildwest-vscode town scope; must re-brief every Copilot session. | Medium — operational friction |
| **Session export: thinking field is Copilot+Claude only** | ChatGPT/Codex sessions have no thinking equivalent in schema. Documented as known limitation. | Low — by design |

---

## 3. ICA Cowboy Era — What Can Be Applied to wwFramework

ICA is the most mature implementation. Three categories of contribution:

### A. Complete Town Registry Schema

ICA `registry.json` has the full shape that the framework's town template should document:

```json
{
  "scope": "town",
  "sot": {
    "main_head": "<sha>",
    "last_updated": "<ISO8601>",
    "updated_by": "<author-code>"
  },
  "authors": [
    {
      "code": "R",
      "username": "reneyap",
      "git_username": "reneyap",
      "role": "Sheriff",
      "joined": "founding"
    }
  ],
  "active_branches": [
    {
      "branch": "<name>",
      "owner": "<author-code>",
      "base": "main",
      "main_sot_at_activation": "<sha>",
      "activated": "<ISO8601>",
      "status": "active | merged | abandoned"
    }
  ]
}
```

This schema + the identity block (wwuid, alias, remote, mcp) = the canonical complete town registry. The framework needs a `registry.town.json` template that combines both.

**Open question:** Identity block as flat fields vs. wrapped in `"identity": {}` — county template uses wrapper, ICA uses flat. Needs a decision before templating.

### B. Lifecycle Script Templates

ICA `scripts/` contains the branch lifecycle scripts the framework spec describes but doesn't provide:

| Script | Purpose |
|---|---|
| `plan-branch.sh` | Creates `board/branches/planned/<branch>/README.md`; registers in registry |
| `activate-branch.sh` | Moves planned → active; creates worktree; updates registry `active_branches` |
| `retire-branch.sh` | Moves active → merged; removes worktree; updates registry |
| `spawn-branch.sh` | Creates a new branch from an active one (sub-task spawn) |
| `abandon-branch.sh` | Moves active → abandoned with reason |
| `generate-branch-index.sh` | Regenerates `board/branches/README.md` from folder scan |

These should be lifted into `wildwest-framework/scripts/` as templates — parameterized for any town, not hardcoded to ICA. ICA's hardcoded paths become `$TOWN_ROOT/.wildwest/` substitutions.

### C. `dailies/` BOD/EOD Format

ICA has real BOD/EOD entries in `.wildwest/dailies/`. The framework declares `dailies/` as canonical (Q4) but has no example format. ICA's entries are the reference implementation to document in the framework.

---

## 4. Action Map

### Immediate (blocking)

| # | Action | Scope |
|---|---|---|
| 1 | Add `scope: "town"` to `wildwest-vscode/.wildwest/registry.json` | wwTown(wildwest-vscode) |
| 2 | Fix `TownInit.ts` to write `scope: "town"` in registry.json | wildwest-vscode |
| 3 | Fix `SoloModeController.hasBranchDoc()` path → `.wildwest/board/branches/active/` | wildwest-vscode |

### Near-term

| # | Action | Scope |
|---|---|---|
| 4 | Write `CLAUDE.md` for wildwest-vscode at town level | wwTown(wildwest-vscode) |
| 5 | Create `registry.town.json` template in wildwest-framework | wwFramework |
| 6 | Decide: identity block flat vs. wrapped in `identity: {}` | wwFramework |
| 7 | Lift ICA lifecycle scripts → `wildwest-framework/scripts/` as templates | wwFramework |
| 8 | Update framework README `~/counties/` → `~/wildwest/counties/` | wwFramework |
| 9 | Retire or annotate stale `heartbeat.sh` in framework scripts/ | wwFramework |

### Deferred

| # | Action | Scope |
|---|---|---|
| 10 | Document `dailies/` BOD/EOD format in framework using ICA as reference | wwFramework |
| 11 | Spec county and world directory level in `wildwest-directory-spec.md` | wwFramework |
| 12 | T3 exploration fork implementation in SoloModeController | wildwest-vscode |
| 13 | wwFramework gaps: TM-absent protocol, CD town visit protocol, role enforcement | wwFramework |

---

## 5. Open Decisions

| Decision | Options | Notes |
|---|---|---|
| **Identity block shape** | A) Flat (`scope`, `wwuid`, `alias` at top level) vs. B) Wrapped (`"identity": { wwuid, alias, remote, mcp }`) | County template uses B; ICA town uses A; wildwest-vscode registry.json uses partial-A. Must unify before templating. |
| **`heartbeat.sh` fate** | Retire / annotate / replace with lifecycle scripts | Native Node.js beat is the implementation; bash script is stale. Framework `scripts/` should have lifecycle scripts, not heartbeat. |
| **SoloModeController T3** | Implement fork creation or keep as assessment-only | T3 is the value proposition of solo mode — worth prioritizing |
