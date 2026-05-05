# wwWorld State — 20260503T1136Z

> **Captured:** 2026-05-03 11:36 UTC  
> **Scope:** Full world snapshot — structure, registry health, extension state, open items  
> **Author:** CD(RSn).Cld

---

## World Root

```
~/wildwest/                       ← wwWorld root (single trunk)
  counties/
    wildwest-ai/
      wildwest-framework/         ← town | spec repo
      wildwest-vscode/            ← town | VSCode extension v0.9.0
    icouponads/
      nx-icouponads/              ← town (Cowboy Era; onboarding deferred)
    visibleteam/
      vscode-auto-chat-export/    ← directory present; no .wildwest/ (not a town)
    wpicity/
      wooqb/                      ← directory present; no .wildwest/ (not a town)
  frontier/                       ← ungoverned / Cowboy Era repos
  sessions/
    reneyap/
      raw/
      staged/                     ← 245+ sessions across claude-code, github-copilot, chatgpt-codex
  TODO.md
  README.md
  wildwest.code-workspace
```

**Symlinks removed 2026-05-02:** `~/counties/` and `~/frontier/` no longer exist.

---

## Counties

| County | Path | Towns (governed) | Status |
|--------|------|-----------------|--------|
| wildwest-ai | `~/wildwest/counties/wildwest-ai/` | wildwest-framework, wildwest-vscode | Active |
| icouponads | `~/wildwest/counties/icouponads/` | nx-icouponads | Active (Cowboy Era) |
| visibleteam | `~/wildwest/counties/visibleteam/` | _(none — vscode-auto-chat-export not onboarded)_ | Dormant |
| wpicity | `~/wildwest/counties/wpicity/` | _(none — wooqb not onboarded)_ | Dormant |

---

## Registry Health

### World — `~/wildwest/.wildwest/registry.json`
- **Exists:** Yes
- **Stale:** `last_updated: 2026-04-30T14:00:00Z` (pre-symlink-removal)
- **County paths in file:** Use `~/wildwest/counties/<county>` — correct
- **Action needed:** Bump `last_updated` after any structural change

### County — `~/wildwest/counties/wildwest-ai/.wildwest/registry.json`
- **Exists:** Yes
- **Stale paths:** Town entries reference `~/counties/wildwest-ai/...` — **NOT updated to `~/wildwest/counties/`**
- **Action needed:** Update `path` fields for both towns; this is the "CLAUDE.md path staleness" open item

### Town — `~/wildwest/counties/wildwest-ai/wildwest-vscode/.wildwest/registry.json`
- **Exists:** No — **ABSENT**
- **Context:** initTown was fixed in v0.9.0 to write registry.json, but this fix is forward-only. The wildwest-vscode town predates the fix and never had one written.
- **Action needed:** Manually bootstrap `registry.json` with identity block (`wwuid`, `alias`, `remote`, `mcp: null`)

### Town — `~/wildwest/counties/wildwest-ai/wildwest-framework/`
- No `.wildwest/` directory — this is a spec-only repo, not governed as a town via the extension

---

## wildwest-vscode (Extension)

- **Version:** 0.9.0
- **Branch:** main — clean, pushed to origin/main
- **Last commit:** `2d224fd feat(town): v0.8.0 — registry identity, town root detection, session filter, copilot fallback`
  - _(Note: commit message says v0.8.0 but was amended and contains v0.9.0 content — Copilot response extraction fix)_

### Capabilities as of v0.9.0

| Feature | State |
|---------|-------|
| Heartbeat | `setInterval` scheduler; `_heartbeat` worktree per town |
| Solo Mode | T1–T4 tiers (Maintenance / Continuation / Exploration / Hard stop) |
| Session export | raw/ → staged/; auto-sync on watcher startup and activity |
| Empty session filter | `requests.length === 0` stubs excluded from staged/ |
| Town root detection | Keys on `.wildwest/registry.json` (fixed from `.wildwest/scripts/`) |
| initTown | Writes registry.json identity block (`wwuid`, `alias`, `remote`, `mcp: null`) |
| Copilot response capture | `kind=None` parts → `response`; `kind=thinking` → `thinking` (both fields) |
| Telegraph | `.wildwest/telegraph/` inbox; Rule 23 enforcement via processInbox command |

### Session Export Pipeline

- **Tool coverage:** claude-code, github-copilot, chatgpt-codex
- **Staged count:** 245+ sessions in `sessions/reneyap/staged/`
- **Known limitation:** Copilot `thinking` field is only populated for Claude model (Haiku/Sonnet); ChatGPT and Codex have no equivalent

---

## Telegraph State — wildwest-vscode

All memos are from the 2026-05-02 TM isolation probe session. No unread memos outstanding.

| Memo | Direction | Subject | Resolved |
|------|-----------|---------|----------|
| 1324Z | CD → TM | Filter empty sessions from staged | ✅ (v0.8.0) |
| 1331Z | CD → TM | Copilot response text missing at source (superseded) | ✅ (v0.9.0 — response IS in JSON) |
| 1347Z | TM → CD | Town-level isolation findings | ✅ reviewed |
| 1402Z | TM → CD | v0.8.0 shipped | ✅ reviewed |
| 1406Z | CD → TM | Amend commit then push | ✅ done |
| 1417Z | CD → TM | Fix Copilot response extraction (kind=None) | ✅ (v0.9.0) |
| 1427Z | TM → CD | v0.9.0 Copilot response fix shipped | ✅ reviewed |

---

## Frontier

`~/wildwest/frontier/` contains ungoverned Cowboy Era repos. Notable candidates for onboarding or disposition:

- `agentic-handbook`, `devflow`, `teamgpt` — identified for onboarding (deferred)
- `_ARCHIVE/` — historical archive; no action
- Many others (`nx-teststream`, `nx-tictactoe`, `rytfxn`, `xydek`, etc.) — unclassified

---

## Open Items (Prioritized)

### P1 — Registry stale paths
- `~/wildwest/counties/wildwest-ai/.wildwest/registry.json`: town `path` fields still reference `~/counties/wildwest-ai/...`
- Fix: update to `~/wildwest/counties/wildwest-ai/...`

### P1 — wildwest-vscode missing registry.json
- `~/wildwest/counties/wildwest-ai/wildwest-vscode/.wildwest/registry.json` absent
- Fix: write identity block manually (wwuid, alias, remote, mcp: null)

### P2 — wildwest-vscode: no CLAUDE.md at town level
- Copilot TM sessions lack handoff context; must re-brief each time
- Fix: write minimal CLAUDE.md covering town scope, role, and telegraph rules

### P2 — wildwest-vscode: registry validator utility
- Deferred from TM isolation probe
- Validates registry.json shape, detects stale paths, confirms wwuid format

### P3 — CLAUDE.md path staleness (wildwest-ai county)
- `~/wildwest/counties/wildwest-ai/CLAUDE.md` may reference `~/counties/` — needs audit
- `.code-workspace` files may also have stale folder paths

### P3 — nx-icouponads onboarding
- Add `_heartbeat` worktree; activate `chore/wwtownmigration` in a gap between feature branches
- Note: initTown bypassed (`.wildwest/` exists); surgical `git worktree add` only

### P4 — wwFramework spec gaps (found 2026-05-01)
1. TM-absent protocol — no fallback defined when TM window unavailable
2. Role-channel enforcement — honor system; no tooling enforcement yet
3. CD town visit protocol — which tools CD may touch during a town visit
4. Stale `active/` doc detection — board integrity check after branch merge

### P5 — wwTrust SSOT
- `~/wildwest/.wildwest/trust-policy.json` — world-scope trust policy, cascading to county + town
- Deferred until ICA feature pressure subsides

### P5 — GitHub: wildwest-ai/wildwest-world
- Create canonical example world instance on GitHub
- Blocked on: public release review pass, MIT license, CONTRIBUTING.md

### P5 — Public release (wildwest-framework)
- Review pass, MIT license, wildwest-county template repo, CONTRIBUTING.md

### P5 — wwVscode future
- Command palette split; status bar enhancements; MCP integration

### P6 — Frontier onboarding
- agentic-handbook, devflow, teamgpt — identified but no timeline

---

## Actor Model (current window)

- **S(R)** — Sheriff. Rene (reneyap). Sole authority.
- **CD(RSn).Cld** — Chief Deputy. This window. County-level scope.
- **TM(RHk).Cpt** — Town Marshal. Copilot chat window. Town-level impl.

---

## Key Architectural Facts

- **Cascading principle:** `wwuid` + `alias` + `remote` + `mcp` at every scope level; `.wildwest/registry.json` is the spec marker for a governed entity
- **devPair class principle:** codes are classes; `Role(Actor)` = instance; `aCD(RHk)` ≠ `TM(RHk)`
- **Session path:** `~/wildwest/sessions/<actor>/raw/` → `staged/` (all actors, all tools)
- **World registry:** `~/wildwest/.wildwest/registry.json` — gitignored, local only
- **County registry:** `~/wildwest/counties/<county>/.wildwest/registry.json`
- **Town registry:** `<town>/.wildwest/registry.json` (created by initTown or manually)
- **Repo lifecycle:** Cowboy Era → Outpost → Town
