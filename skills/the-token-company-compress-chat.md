---
name: Compress a chat conversation in one request
description: Compress a whole OpenAI/Anthropic message array with The Token Company before a multi-turn LLM call, keeping assistant turns cache-warm.
api: openapi/the-token-company-openapi.yml
operations: [compressChat]
---

# Compress a chat conversation

Use this for multi-turn conversations: instead of compressing each message separately, send the entire message array once to `compressChat` (`POST /v1/chat/compress`). Assistant messages pass through unchanged so the downstream provider's KV cache stays warm.

## Auth
- `Authorization: Bearer ttc-...`.

## Steps
1. Build your OpenAI- or Anthropic-format `messages` array as usual.
2. Call `compressChat` with `messages`, `fmt` (`openai` or `anthropic`), optional `model` (default `bear-2`) and `aggressiveness` — either a single float or a `{role: float}` map (roles absent from the map are left uncompressed).
3. For Anthropic, pass the top-level `system` prompt to compress it too; use `strip_server_tool_results` / `skip_tool_use_ids` to control tool blocks.
4. Read the compressed `messages` back and send them to your LLM; inspect `original_input_tokens`, `output_tokens`, `cache_hits`, `cache_misses`.

## Rules
- Only non-assistant messages are compressed; do not re-compress assistant history.
- Prefer a per-role `aggressiveness` map to compress bulky user/system context harder than short turns.

## Errors
See `errors/the-token-company-problem-types.yml`.
