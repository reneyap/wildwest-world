<!-- 20260502-1305Z (09:05 EDT) -->

# wildwest-vscode — Memory Snapshot

> Captured from live wwTown scan. Reflects repo state as of 2026-05-02.

---

## Identity

- **GitHub:** `https://github.com/wildwest-ai/wildwest-vscode`
- **Local:** `~/wildwest/counties/wildwest-ai/wildwest-vscode/`
- **Stack:** TypeScript / VSCode extension
- **Current version:** 0.7.1
- **Activation:** `onStartupFinished`
- **Main entry:** `./dist/extension.js` (esbuild bundle)

---

## Source Files (12 files, ~4,219 lines)

| File | Purpose |
|---|---|
| `extension.ts` | Main entry; registers 11 commands; initializes all subsystems; auto-start on activation |
| `HeartbeatMonitor.ts` | Multi-scope heartbeat engine — town/county/world cascading timers; registry-based config; sentinel writes |
| `SoloModeController.ts` | Autonomy tier gating T1–T4; delegates to HeartbeatMonitor + branch doc check |
| `TelegraphWatcher.ts` | chokidar watcher on all `.wildwest/telegraph/` dirs; prompts on incoming memos |
| `TownInit.ts` | Onboarding wizard; creates `.wildwest/` structure + `_heartbeat` branch/worktree |
| `WorktreeManager.ts` | Parses `git worktree list --porcelain`; resolves main checkout via git-common-dir |
| `TelegraphInbox.ts` | Rule 23 enforcement; interactive memo processing; archives to `telegraph/history/` |
| `sessionExporter.ts` | Core export pipeline; polls VSCode chat storage; writes raw/; fires batchConvert on activity |
| `batchConverter.ts` | raw/ → staged/ pipeline; mtime-based idempotency; JSON + markdown output |
| `chatSessionConverter.ts` | Single-session converter → raw-chat.log + chatreplay.json |
| `jsonToMarkdown.ts` | JSON → Markdown with timestamps; standalone CLI + library |
| `generateIndex.ts` | Generates index.md summary table across all staged/ sessions |

---

## Features — All Shipped (v0.7.1)

### Heartbeat (v0.5.0+)
- Three scopes: town / county / world — independent timers per scope
- Intervals: town 5 min idle / 2 min active; county 30/15 min; world 60/30 min
- Sentinel: town → `.wildwest/telegraph/.last-beat`; county/world → `.wildwest/.last-beat`
- Scope detection: walks workspace folders, finds `.wildwest/registry.json`, walks ancestors for county/world
- Config resolution chain: `registry.heartbeat.interval_ms` → extension settings → floor (5 min)
- Staleness check: if sentinel age ≥ 2× idle interval → 'stopped'

### Session Export (v0.4.0+, v0.7.1 fix)
- Polls VSCode chat storage every 5s; detects changes; exports to `raw/`
- v0.7.1: fires `batchConvertSessions(true)` at end of each poll cycle when activity detected (was startup-only before)
- Output path: `~/wildwest/sessions/<actor>/`
- Pipeline: `raw/*.json` → `staged/<ts>_<8id>.{raw-chat.log, chatreplay.json, json, md}` → `index.md`

### Telegraph (v0.6.0+)
- TelegraphWatcher: chokidar on all worktree telegraph dirs; prompts on `to-*` memos or attention patterns
- TelegraphInbox: Rule 23 enforcement; opens memo in editor; action picker (done/blocked/question); writes ack file; archives original

### Town Init (v0.2.4+)
- Wizard-driven; creates `.wildwest/telegraph`, `scripts`, `docs`; creates `_heartbeat` branch + worktree; updates `.gitignore`
- Idempotent; skips existing branch/worktree

### Status Bar (v0.5.4+)
- Single consolidated item: heartbeat state icon + branch name + worktree count + solo tier (T1–T4)

### Solo Mode (v0.3.0+)
- T1 Maintenance / T2 Continuation / T3 Exploration / T4 Hard stop
- T4 if heartbeat not alive; T2 if branch doc exists at `.wildwest/board/branches/active/<branch>/README.md`; T1 otherwise

---

## .wildwest/ State (live)

- `.wildwest/board/branches/` — planned/, active/ (main + _heartbeat), merged/, abandoned/
- `.wildwest/telegraph/` — live; `.last-beat` present; rule 23 memos active
- `.wildwest/worktrees/_heartbeat/` — worktree live; gitignored
- No `drafts/` state yet (spec decision: add drafts/ — not yet applied here)

---

## Commands (11)

`startWatcher`, `stopWatcher`, `exportNow`, `batchConvert`, `convertToMarkdown`, `generateIndex`, `viewTelegraph`, `soloModeReport`, `initTown`, `processInbox`, `menu`

---

## Open Work (from TODO.md)

| Item | Notes |
|---|---|
| Fix `getTownRoot()` / `getRepoRoot()` | Still keys on `.wildwest/scripts/` — should key on `.wildwest/registry.json` |
| `initTown` registry.json | Must write identity block: `wwuid`, `alias`, `remote`, `mcp: null` |
| MCP integration | Migrate `.wildwest/` artifacts to MCP server; expose sendMessage, readTelegraph, reportHeartbeat |
| Command palette split | "Wild West: Sessions" + "Wild West: Governance" categories (package.json only) |
| Status bar enhancements | Telegraph unread count badge; last beat age tooltip |

---

## Git State (as of scan)

- Branch: `main` — clean, up to date with origin
- Latest commit: `4f5c199` — `fix(sessions): staged/ incremental sync on activity; vsix to build/ only`
- VSIX builds in `build/` — 0.5.5, 0.6.0, 0.7.0, 0.7.1

---

## Design Docs

Full extension design: `~/wildwest/counties/wildwest-ai/wildwest-framework/docs/wildwest-vscode.md`
