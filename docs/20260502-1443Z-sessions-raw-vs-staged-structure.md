<!-- 20260502-1443Z (10:43 EDT) -->

# Sessions — Raw vs Staged Structure Comparison
## claude-code / github-copilot / chatgpt-codex

**Author:** CD(RSn)
**Scope:** wwWorld sessions pipeline — `sessions/reneyap/raw/` vs `sessions/reneyap/staged/`
**Extension version at time of capture:** wildwest-vscode v0.9.0

---

## Raw Structure

| | **claude-code** | **github-copilot** | **chatgpt-codex** |
|---|---|---|---|
| **Format** | JSON | JSON | JSONL (event stream) |
| **Top-level keys** | `sessionId`, `projectPath`, `creationDate`, `lastMessageDate`, `messages` | `version`, `sessionId`, `creationDate`, `lastMessageDate`, `requests`, `pendingRequests`, `modelId`, `hasPendingEdits` | Per-line events; no single top-level object |
| **Turn container** | `messages[]` | `requests[]` | Event types: `event_msg`, `response_item`, `turn_context` |
| **User prompt** | `messages[].content` (string) | `requests[].message` (string) | `event_msg/user_message` → `payload.message` (string) |
| **Assistant response** | `messages[].content` (string) | `requests[].response[]` — typed parts array | `event_msg/agent_message` → `payload.message` (string) |
| **Thinking / CoT** | absent | `response[]{kind:'thinking', value}` — interleaved in parts array | `response_item/reasoning` → `payload.summary` (encrypted in practice) |
| **Tool calls** | not stored (handled by extension) | `response[]{kind:'toolInvocationSerialized'}` | `response_item/function_call` → `{name, arguments, call_id}` |
| **Tool results** | absent | embedded in `toolSpecificData` within serialized part | `response_item/function_call_output` |
| **Session metadata** | `sessionId`, `projectPath` | `sessionId`, `responderUsername`, `modelId`, `agent` | `session_meta` event → `{cwd, cli_version, model_provider, git}` |
| **Token tracking** | absent | `completionTokens`, `elapsedMs` per request | `event_msg/token_count` events |
| **File edits** | absent | `response[]{kind:'textEditGroup'}` | `event_msg/patch_apply_end` |
| **MCP** | absent | `response[]{kind:'mcpServersStarting'}` | `event_msg/mcp_tool_call_end` |

---

## Staged Structure

| | **claude-code** | **github-copilot** | **chatgpt-codex** |
|---|---|---|---|
| **Format** | JSON | JSON | JSON |
| **Top-level keys** | `exportedAt`, `provider`, `github_userid`, `user_timezone_offset`, `totalPrompts`, `totalLogEntries`, `sourceSession`, `prompts` | same | same |
| **Turn container** | `prompts[]` | `prompts[]` | `prompts[]` |
| **`prompt` field** | string | string | string |
| **`response` field** | string (full text) | string — concatenated `kind=None` parts (v0.9.0) | string (agent_message text) |
| **`thinking` field** | absent | string — concatenated `kind:'thinking'` parts (v0.9.0) | absent |
| **`logs` field** | `[]` (unused) | `[]` (unused) | `[]` (unused) |
| **`hasSeen`** | bool | bool | bool |
| **Tool activity** | not captured | not captured | not captured |
| **Model identity** | not captured | not captured | not captured |

---

## Key Observations

**1. Staged schema is unified.**
All three tools normalize to the same `prompts[]` shape regardless of raw format. Loss of tool call detail and model identity is intentional — staged is for human review, not replay.

**2. `thinking` only on Copilot.**
Copilot is the only tool that exposes internal CoT in its raw storage. Claude Code and Codex have no equivalent field that survives to staged. This makes Copilot staged sessions uniquely rich for model assessment.

**3. Codex is an event stream.**
Unlike the other two, raw Codex is JSONL with discrete typed event lines. Richer in execution detail (token counts, patch events, MCP calls, reasoning items) but requires assembly to reconstruct a conversation. `chatSessionConverter` does this assembly at staging time.

**4. Model identity gap in staged.**
None of the three staged formats capture which model was active for a given session. The only available signal is Copilot's `thinking` field, which often contains the model's self-declaration (e.g. `"Claude Haiku 4.5"`). This is currently the best proxy for model identity in session review.

**5. Copilot response was hardest to extract.**
Raw Copilot response text uses `kind=None` (no kind tag) — not a named kind like `markdownContent`. Missed in v0.7.1 (no extraction), wrong in v0.8.0 (`[thinking]` fallback), correct in v0.9.0 (`kind=None` concatenation). See observation `20260502-1435Z` for full investigation record.

---

## Copilot Raw Response Parts Reference

For reference — all known `kind` values in `requests[].response[]`:

| kind | Content |
|---|---|
| `null` / `undefined` | **Actual response text** — concatenate `value` fields in order |
| `thinking` | Internal chain-of-thought; `value` is CoT text; empty entries have `metadata.vscodeReasoningDone: true` (sentinels — skip) |
| `toolInvocationSerialized` | Tool call + result; `toolId`, `toolCallId`, `toolSpecificData` |
| `mcpServersStarting` | MCP server lifecycle; `didStartServerIds` |
| `textEditGroup` | File edits applied by the agent |
| `inlineReference` | File references cited in response |
| `codeblockUri` | Code block file references |
| `undoStop` | Undo markers |

---

## Codex JSONL Event Reference

| Event type / subtype | Content |
|---|---|
| `session_meta` | Session start: `id`, `cwd`, `cli_version`, `model_provider`, `git` |
| `event_msg/user_message` | User prompt: `payload.message` (string) |
| `event_msg/agent_message` | Agent response: `payload.message` (string) |
| `event_msg/task_started` | Turn lifecycle start |
| `event_msg/task_complete` | Turn lifecycle end |
| `event_msg/token_count` | Token usage per turn |
| `event_msg/exec_command_end` | Terminal command result |
| `event_msg/patch_apply_end` | File patch applied |
| `event_msg/mcp_tool_call_end` | MCP tool result |
| `response_item/message` | Message item |
| `response_item/reasoning` | CoT reasoning (`summary`, `encrypted_content`) |
| `response_item/function_call` | Tool call: `name`, `arguments`, `call_id` |
| `response_item/function_call_output` | Tool result |
| `response_item/custom_tool_call` | Custom tool invocation |
| `turn_context` | Turn context snapshot |
