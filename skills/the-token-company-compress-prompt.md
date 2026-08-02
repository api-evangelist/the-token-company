---
name: Compress a prompt before an LLM call
description: Use The Token Company to strip low-signal tokens from a prompt, then send the compressed text to any LLM to cut cost and latency.
api: openapi/the-token-company-openapi.yml
operations: [compressPrompt]
---

# Compress a prompt with The Token Company

Use this to shrink a large prompt before you send it to an LLM (OpenAI, Anthropic, OpenRouter, etc.). One call to `compressPrompt` returns compressed text plus token counts; you pass the compressed `output` on to your model.

## Auth
- Send `Authorization: Bearer ttc-...` (key from https://app.thetokencompany.com; access is invite-only).

## Steps
1. Protect any spans that must survive verbatim by wrapping them in `<ttc_safe>...</ttc_safe>` (identifiers, code, exact quotes).
2. Call `compressPrompt` (`POST /v1/compress`) with `text`, optionally `model` (default `bear-2`) and `aggressiveness` (0.0–1.0, default 0.2 — raise toward 1.0 to compress harder).
3. Read `output` (compressed prompt), `output_tokens`, and `original_input_tokens`; `tokens_saved = original_input_tokens - output_tokens`.
4. Send `output` as the prompt to your downstream LLM.

## Rules
- Compression is lossy by design; raise `aggressiveness` cautiously and verify output quality on your task.
- Tag requests with `app_id` (≤255 chars) for per-app usage tracking.
- Enable gzip for very large inputs.

## Errors
See `errors/the-token-company-problem-types.yml`: 401 bad key, 400 bad params, 402 insufficient balance, 413 too large, 429 rate limited — back off and retry on 429/5xx.
