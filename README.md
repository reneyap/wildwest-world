# wildwest — wwWorld Root

> **Last updated:** 20260505-0213Z (22:13 EDT)

`~/wildwest/` is a governed instance of [wildwest-framework](counties/wildwest-ai/wildwest-framework/).
One world, one root. Everything Wild West lives here.

---

## What Lives Here

| Path | What it is |
|---|---|
| `counties/` | Governed county instances — each county owns its towns |
| `frontier/` | Ungoverned workspace — experimental repos, Cowboy Era projects |
| `sessions/` | Session exporter output — all actors, all towns |
| `docs/` | World-scope reference docs and session observations |
| `archive/` | Stale snapshots retained for historical reference |
| `.wildwest/` | World governance domain — registry, telegraph |
| `TODO.md` | Cross-county open items |
| `DONE.md` | Cross-county completed work and architectural decisions |
| `wildwest.code-workspace` | Multi-root VSCode workspace — all active towns |
| `README.md` | This file |

**Entry point:** open `wildwest.code-workspace` to get a multi-root view across all active towns.

---

## County Map

```
counties/
  wildwest-ai/                ← reference county (framework governs itself)
    wildwest-framework/       ← governance framework spec + role CHARTERs + scripts
    wildwest-vscode/          ← VSCode extension (heartbeat, telegraph, session export)
  icouponads/
    nx-icouponads/            ← iCouponAds platform (active; Cowboy Era, onboarding deferred)
  visibleteam/
    vscode-auto-chat-export/  ← retired
  wpicity/
    wooqb/                    ← directory present; not yet a governed town
```

County law lives at `counties/<county>/CLAUDE.md`.
Framework spec lives at `counties/wildwest-ai/wildwest-framework/`.

---

## Cascading Principle

The same pattern repeats at every scope — identity, registry, governance context, children.
Authority flows outward → inward. Context cascades inward → outward.

```
~/wildwest/                              ← wwWorld   (.wildwest/registry.json)
  counties/<county>/                     ← County    (.wildwest/registry.json)
    <town>/                              ← Town      (.wildwest/registry.json)
      .wildwest/                         ← town governance domain
        board/        ← branch lifecycle (planned/ active/ merged/ abandoned/)
        telegraph/    ← inter-actor messaging bus
        worktrees/    ← linked git worktrees (gitignored)
  frontier/                              ← inside the world; ungoverned edge
  sessions/                              ← framework-level; spans all towns
```

---

## Actor Model (summary)

| Actor | Role | Scope |
|---|---|---|
| G(R) — reneyap | Governor | World — all counties report up |
| RA — TBD | Ranger | World — operator; right hand to G |
| S(R) — reneyap | Sheriff | County — sole authority per county |
| CD(RSn) — reneyap + Sonnet | Chief Deputy | County — PR gate, scope decisions |
| TM(RHk) — reneyap + Haiku | Town Marshal | Town — branch lifecycle, telegraph |
| HGs | Hired Guns | Impl only; no standing authority |

**Note:** In this single-developer instance G(R) = S(R) = reneyap — same person, different scope hats.

**devPair** = human author code + model code (e.g. `RSn`, `RHk`). Not pair programming. Human author is accountable for all output.

---

## Key Tooling

**wildwest-vscode** (`counties/wildwest-ai/wildwest-vscode/`) — VSCode extension, v0.9.0+:
- Heartbeat monitor — town / county / world cascading timers; writes `.last-beat` sentinel
- Telegraph watcher — detects incoming memos; Rule 23 inbox enforcement
- Session exporter — polls VSCode chat storage → `sessions/<actor>/`
- Solo mode controller — T1–T4 autonomy tier gating
- Town init wizard — `.wildwest/` scaffold + `_heartbeat` worktree
