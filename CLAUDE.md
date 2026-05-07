# CLAUDE.md — wildwest territory

> **Last updated:** 2026-05-07 12:08 UTC (08:08 EDT)
> **Scope:** territory
> **wwuid:** `4cc9535c-ecd4-492b-8dd6-794b34fd08a1`
> **Alias:** `wildwest`
> **Framework:** `~/wildwest/counties/wildwest-ai/wildwest-framework/` — role CHARTERs, script templates, meta model
> **Registry:** `~/wildwest/.wildwest/registry.json`

This file is territory law. It governs all counties and all towns under `~/wildwest/`.
County-specific rules live in each county's `CLAUDE.md`. Town-specific rules live in each town's `CLAUDE.md`.

The framework (`wildwest-framework/CLAUDE.md`) describes the *pattern*. This file is the *instance* — the live territory.

---

## Territory Scope

These rules apply territory-wide — to all counties and all towns.

**Active counties:**

| Alias | Sheriff | Remote | Status |
|---|---|---|---|
| `wildwest-ai` | R | https://github.com/wildwest-ai/wildwest-county.git | active |
| `icouponads` | R | https://github.com/iCouponAds/wildwest-county.git | active |
| `visibleteam` | R | https://github.com/visibleteam/wildwest-county.git | active |
| `wpicity` | R | https://github.com/WPicity/wildwest-county.git | active |

**Known towns (per county):**

| County | Town | Path |
|---|---|---|
| `wildwest-ai` | `wildwest-framework` | `~/wildwest/counties/wildwest-ai/wildwest-framework/` |
| `wildwest-ai` | `wildwest-vscode` | `~/wildwest/counties/wildwest-ai/wildwest-vscode/` |
| `icouponads` | `nx-icouponads` | `~/wildwest/counties/icouponads/nx-icouponads/` |
| `wpicity` | `wooqb` | `~/wildwest/counties/wpicity/wooqb/` |

---

## Standing Rules — Territory Law

> **These rules apply to ALL actors territory-wide.** County and town law may extend but not contradict territory law.

1. **Scope hierarchy:** Territory → County → Town. Authority flows outward → inward. Context cascades inward → outward.
2. **"World" scope is deferred** — the scope above territory is planned for the wwMCP era. Until then, territory is the outermost implemented scope. Do not use "world" in governance docs.
3. **All actors must declare identity at session start** — devPair class, role, channel, model, version. No exceptions.
4. **No `git push` without explicit authorization from the devPair lead (author)** — the author authorizes directly; no external authority loop required.
5. **Branch + PR for all code changes** — no direct pushes to `main` except TM-role work within a town.
6. **No cross-county destructive operations** without explicit written approval from G(R).
7. **Registry (`~/.wildwest/.wildwest/registry.json`) is the SSOT for territory-scope identity** — county aliases, remotes, sheriff codes. Do not edit it without RA or G authority.
8. **Telegraph bus (`~/.wildwest/.wildwest/telegraph/`) is territory-scope only** — county and town actors write to their own scope's telegraph; territory-scope memos are routed here by RA.
9. **CLAUDE.md files are scope-gated** — territory CLAUDE.md is G(R)/RA(RSn) territory; county CLAUDE.md is S(R)/CD territory; town CLAUDE.md is TM territory. Impl actors (HG, DS) do not edit CLAUDE.md directly at any scope.
10. **All living docs must have a `> **Last updated:**` timestamp** — update whenever the file is materially changed; format: `2026-05-07 12:08 UTC (08:08 EDT)`.
11. **Actor self-identification at every new chat window** — if misidentified, correct immediately before proceeding; silence = confirmation.

---

## Actor Model — Territory Scope

**Authority gradient (territory → town):**
```
G (Governor)           ← territory scope
└── RA (Ranger)        ← territory scope, right hand to G
    └── S (Sheriff)    ← county scope
        └── CD / aCD   ← county scope
            └── TM     ← town scope
                └── HG / DS  ← impl only
```

**Territory-scope roles:**

| Role | Code | Holder | Authority |
|---|---|---|---|
| Governor | G | R (reneyap) | Ultimate authority. Territory law. County onboarding. wwMCP governance when it exists. |
| Ranger | RA | RSn (reneyap + Sonnet) | Right hand to G. Territory-scope ops: registry, telegraph, cross-county coordination, framework governance. |

**Author registry** (permanent; codes never reused):

| Author | Code | Username | Current role |
|---|---|---|---|
| reneyap | R | reneyap | G (Governor) — territory scope; S (Sheriff) — all counties |

**devPair model codes:**

| Model | Code | Channels |
|---|---|---|
| Claude Sonnet | Sn | Claude Code, GitHub Copilot |
| Claude Haiku | Hk | Claude Code, GitHub Copilot |
| GPT (Copilot chat) | Gp | GitHub Copilot |
| GPT (Codex) | Cx | Codex CLI |
| Gemini | Gm | GitHub Copilot |
| Grok | Gk | GitHub Copilot |
| Raptor / Raptor Mini | Rp | GitHub Copilot |

**Channel architecture:**

| Channel | Code | Primary occupants |
|---|---|---|
| Claude Code | CLD | RA(RSn), TM(RHk) — strategic RC work |
| GitHub Copilot | CPT | RA(RSn) overflow, HGs (RGp, RRp, RGm, RGk) |
| Codex CLI | CDX | RCx — async/independent |

---

## Cold-Start Protocol — RA(RSn).Cpt

When a new RA(RSn) session opens in GitHub Copilot at territory scope:

1. **Self-identify** — `RA(RSn).Cpt — Claude Sonnet 4.6, GitHub Copilot. Ranger, territory scope.`
2. **Read the latest memory dump** — `~/wildwest/archive/` — sort by name, read the most recent `*-CPT.md` or `*-wwworld-state*.md`
3. **Read this file (territory CLAUDE.md)** — you're reading it now ✓
4. **Check territory telegraph** — `ls ~/wildwest/.wildwest/telegraph/` — scan for memos addressed to RA or broadcast
5. **Verify registry** — `cat ~/wildwest/.wildwest/registry.json` — confirm county roster matches expectations
6. **Cross-check repo state** — `git log --oneline -3` in each active repo; surface any unpushed commits
7. **Surface open items** — from the memory dump + any telegraph memos; report to G(R) before other work
8. **Ask for push authorization** if any commits are pending

---

## Current Repository State

> Update this section at session close.

- **Territory registry:** `~/wildwest/.wildwest/registry.json` — schema_version 2; 4 active counties
- **Framework (`wildwest-framework`):** HEAD `580b034` — `law(scope): world scope deferred — rename to territory throughout` — **1 commit ahead of origin; push auth pending**
- **wildwest-vscode:** HEAD `e10f2d9` on `feat/telegraph-delivery-v2` — **1+ commits unpushed; PR/push decision pending**
- **nx-icouponads:** `686caa98` + `7ed006ac` local on `main` — **2 commits unpushed; push decision pending**
- **Territory repo (`~/wildwest/`):** untracked files (TODO.md, DONE.md, README.md, archive/, docs/) — not yet committed
- **Scope terminology:** "world" → "territory" migration complete in Governor CHARTER, Ranger CHARTER, framework CLAUDE.md (committed `580b034` 2026-05-07)

---

## Paths Quick Reference

| Item | Path |
|---|---|
| Territory root | `~/wildwest/` |
| Territory registry | `~/wildwest/.wildwest/registry.json` |
| Territory telegraph | `~/wildwest/.wildwest/telegraph/` |
| Framework | `~/wildwest/counties/wildwest-ai/wildwest-framework/` |
| County: wildwest-ai | `~/wildwest/counties/wildwest-ai/` |
| County: icouponads | `~/wildwest/counties/icouponads/` |
| County: visibleteam | `~/wildwest/counties/visibleteam/` |
| County: wpicity | `~/wildwest/counties/wpicity/` |
| Archive / memory dumps | `~/wildwest/archive/` |
| Sessions export | `~/wildwest/sessions/` |
| Frontier (ungoverned) | `~/wildwest/frontier/` |
