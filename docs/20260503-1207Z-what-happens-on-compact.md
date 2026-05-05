# What Happens on `/compact` — 20260503T1207Z

> **Session type:** .Cld chat window  
> **Channel:** S(R) ↔ CD(RSn).Cld  
> **Date:** 2026-05-03

---

## Technical Sequence

1. **Summary generation** — Claude Code sends the full conversation history to the model with a special system prompt requesting a structured summary. The model produces the large "Summary:" block seen at the top of the continued conversation.

2. **Context replacement** — All raw conversation messages (turns, tool calls, tool results) are discarded from the active in-memory context window. They are replaced with a single synthetic assistant turn containing only the summary text.

3. **Persistent transcript** — Original messages are NOT deleted. They remain in the JSONL file on disk at:
   `~/.claude/projects/-Users-reneyap-wildwest/<session-id>.jsonl`
   The summary references this path so the full transcript can be read post-compaction.

4. **Continuation** — The next user message is appended after the summary turn. The model sees: `system prompt + summary turn + new user message`. No original tool calls or results are present.

---

## What Is Lost

- Raw tool output (exact file contents returned, command stdout)
- Exact code snippets generated mid-session
- Precise reasoning sequence and intermediate deliberation
- Any nuance the summarizer chose not to include

## What Is Preserved

- The summary's structured facts (pending tasks, key technical findings, file paths, code snippets the summarizer chose to include)
- Memory files written to disk (`~/.claude/projects/.../memory/`)
- Files written to the filesystem during the session
- Git commits and their history
- The full JSONL transcript on disk

---

## Why Pre-Compact Memory Dump Matters

The summary is only as accurate as what the model chose to include. Memory files (`MEMORY.md`, `project_*.md`, session dumps) are the authoritative persistent state — they survive compaction with no degradation. The 3-pass audit before compacting ensures the memory files are complete and accurate before the raw context is discarded.

---

## Reference

- Full prior session transcript: `~/.claude/projects/-Users-reneyap-wildwest/b5556e13-496c-47e0-bbc1-2227b67bd613.jsonl`
- Prior session memory dump: `~/wildwest/20260503-1200Z-session-dump.md`
