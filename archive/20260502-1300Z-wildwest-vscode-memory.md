<!-- 20260502-1300Z (09:00 EDT) -->

# wildwest-vscode — Memory Snapshot

> Captured from Claude Code session memory. Does NOT reflect current repo state — verify against wwTown(wildwest-vscode).

---

## Identity

- **GitHub:** `https://github.com/wildwest-ai/wildwest-vscode`
- **Local:** `~/wildwest/counties/wildwest-ai/wildwest-vscode/`
- **Stack:** TypeScript / VSCode extension
- **Origin:** Scaffolded from scratch 2026-04-28; source material from `vscode-auto-chat-export` (now obsolete)

---

## Purpose — Three Main Features

1. **Heartbeat scheduler** — `setInterval` drives `heartbeat.sh` per town; `_heartbeat` permanent worktree per town is the trigger point; extension is scheduler, script is logic (pluggable)
2. **Telegraph watcher** — monitors `.wildwest/telegraph/` for inter-actor memos
3. **Session exporter** — captures devPair activity; v0.4.0 updated output path to `~/wildwest/sessions/<actor>/`

---

## Known Open Work

| Item | Notes |
|---|---|
| Fix `getTownRoot()` / `getRepoRoot()` | Currently keys on `.wildwest/scripts/` — wrong proxy; correct marker is `.wildwest/registry.json` (presence = governed town) |
| Update `initTown` | Must write `registry.json` with identity block: `wwuid`, `alias`, `remote`, `mcp: null` |
| Role-awareness feature | Extension should know which devPair opened the workspace; warn when actor attempts out-of-role action (e.g. CD running a TM lifecycle script) |

---

## wildwest.code-workspace

- Written at `~/wildwest/wildwest.code-workspace`
- Multi-root: wildwest-framework, wildwest-vscode, nx-icouponads, wooqb, frontier
- Named folders with relative paths from `~/wildwest/`
- `files.exclude` / `search.exclude` set for: `node_modules`, `dist`, `build`, `.wildwest/worktrees`

---

## Design Docs

Full extension design: `~/wildwest/counties/wildwest-ai/wildwest-framework/docs/wildwest-vscode.md`
