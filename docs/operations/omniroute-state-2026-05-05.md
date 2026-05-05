# OmniRoute Operational State - 2026-05-05

Generated at: 2026-05-05T03:32:56.853Z UTC  
Production URL: http://72.62.108.24:20128  
Dashboard: http://72.62.108.24:20128/dashboard/providers  
Container: `omniroute-inteia`  
Image: `diegosouzapw/omniroute:latest`  
Data mount: `/opt/omniroute/data => /app/data`  
Database: `/opt/omniroute/data/storage.sqlite`

## Current Production State

- OmniRoute is live on port `20128`.
- Main SQLite database is mounted outside the container and persists across image updates.
- Config changes in this document were applied directly to the production database with backups before each major step.
- This repository commit documents the operational state; the live configuration itself is in `storage.sqlite`.

## Backups Created During This Session

- `/opt/omniroute/backups/storage_20260505_022818_pre_model_objectives.sqlite`
- `/opt/omniroute/backups/storage_20260505_030118_pre_deep_review.sqlite`
- `/opt/omniroute/backups/storage_20260505_031415_pre_api_equiv_pricing.sqlite`
- `/opt/omniroute/backups/storage_20260505_032255_pre_feature_potentialize.sqlite`

## Main Work Completed

### 1. Model Objectives And Routing Policy

- Read benchmark file: `mrcr-leaderboard-8-needle-2026-05-03 (1).csv`.
- Registered objective metadata for all 46 routing combos.
- GPT-5.5 is the main premium/general model.
- Claude Opus 4.6 is preferred over Opus 4.7.
- Opus 4.7 references were removed from active routing combos and critical aliases.
- DeepSeek direct route is treated as fallback when credits are exhausted; OpenRouter DeepSeek V3.2 is the preferred healthy DeepSeek path.
- Created automatic model-to-combo mappings for premium, Claude, long-context, coding, research, JSON, free/cheap and similar routing names.

### 2. API-Equivalent Pricing

All subscription/OAuth-backed routes are priced as if they were API calls so cost-benefit calculations are meaningful.

- Pricing unit: USD per 1M tokens unless noted otherwise.
- Subscription routes now have `api_equivalent: true` metadata.
- Effective used-model pricing table: `key_value/pricing_policy/api_equivalent_used_models_2026_05_05`.
- Used routes covered: 38.
- Apify/tool routes are marked separately because they are not token-priced LLM calls.

Core examples:

- `cx/gpt-5.5`: input 5, cached 0.5, output 30.
- `cx/gpt-5.4`: input 2.5, cached 0.25, output 15.
- `cx/gpt-5.4-mini`: input 0.75, cached 0.075, output 4.5.
- `cc/claude-opus-4-6`: input 5, cached 0.5, output 25.
- `cc/claude-sonnet-4-6`: input 3, cached 0.3, output 15.
- `openrouter/deepseek/deepseek-v3.2`: input 0.252, cached 0.0252, output 0.378.
- `openrouter/google/gemma-4-26b-a4b-it`: input 0.06, output 0.33.
- `pplx/sonar-pro`: input 3, output 15.

### 3. Compression And Token Savings

Compression was enabled conservatively to reduce cost without damaging structured prompts.

Settings:

- `compression/enabled`: true
- `compression/defaultMode`: "standard"
- `compression/autoTriggerTokens`: 6000
- `compression/cacheMinutes`: 60
- `compression/preserveSystemPrompt`: true

Compression combos:

- `Standard Savings` (default-caveman) default=1: `[{"engine":"rtk","intensity":"standard"},{"engine":"caveman","intensity":"full"}]`
- `Code Safe Standard` (code-safe-standard) default=0: `[{"engine":"rtk","intensity":"standard"}]`
- `Long Context Aggressive` (long-context-aggressive) default=0: `[{"engine":"rtk","intensity":"aggressive"},{"engine":"caveman","intensity":"full"}]`
- `Ultra Savings Explicit` (ultra-savings-explicit) default=0: `[{"engine":"rtk","intensity":"ultra"},{"engine":"caveman","intensity":"compact"}]`

Compression assignments: 40

- Long-context/research flows use `Long Context Aggressive`.
- Coding/dev flows use `Code Safe Standard`.
- General text flows use `Standard Savings`.
- Vision, scraping and explicit automatic/tool-heavy routes were left unassigned where compression could be harmful.

### 4. Context And Cache Protection

Context cache/model pinning was enabled for 17 critical combos:

- `helena-premium`
- `research-deep`
- `coding-power`
- `thinking-chain`
- `fast-quality`
- `claude-only`
- `long-context`
- `content-persuasive`
- `nexo-tools`
- `nexo-reasoning`
- `osa-elite`
- `BestFREE`
- `inteia-juridico`
- `inteia-pesquisa`
- `inteia-comunicacao`
- `inteia-dev`
- `gpt-5.5`

This reduces context drift when long sessions switch providers or fallback paths.

### 5. Provider Protection And Health

Rate-limit protection and concurrency caps were tuned for sensitive providers:

- Claude: max concurrent 1.
- Codex: max concurrent 1.
- Gemini CLI: max concurrent 2.
- Groq: max concurrent 4.
- OpenRouter: max concurrent 3.
- Perplexity: max concurrent 2.
- Kiro: max concurrent 1.
- ElevenLabs: max concurrent 1.

Provider summary:

- `antigravity`: active=0, healthy=0, statuses=active
- `brave-search`: active=1, healthy=1, statuses=active
- `cerebras`: active=1, healthy=0, statuses=error
- `claude`: active=3, healthy=3, statuses=active
- `cloudflare-ai`: active=2, healthy=2, statuses=active
- `codex`: active=1, healthy=1, statuses=unavailable,active
- `cohere`: active=1, healthy=0, statuses=unavailable
- `deepseek`: active=1, healthy=0, statuses=credits_exhausted
- `elevenlabs`: active=1, healthy=1, statuses=active
- `gemini`: active=0, healthy=0, statuses=unavailable
- `gemini-cli`: active=3, healthy=3, statuses=active,unavailable
- `github`: active=1, healthy=1, statuses=active
- `glm`: active=1, healthy=0, statuses=error
- `groq`: active=2, healthy=2, statuses=active
- `iflow`: active=1, healthy=0, statuses=error
- `kiro`: active=2, healthy=2, statuses=active
- `nanobanana`: active=1, healthy=1, statuses=active
- `nvidia`: active=1, healthy=0, statuses=unavailable
- `openai`: active=1, healthy=0, statuses=unavailable
- `openai-compatible-chat-b8e687ad-2d34-4d78-ba4f-566d41b7b920`: active=1, healthy=1, statuses=active
- `openrouter`: active=1, healthy=1, statuses=active
- `perplexity`: active=1, healthy=1, statuses=active
- `perplexity-search`: active=1, healthy=1, statuses=active
- `sambanova`: active=1, healthy=0, statuses=expired
- `together`: active=1, healthy=0, statuses=credits_exhausted
- `vercel-ai-gateway`: active=1, healthy=1, statuses=active
- `voyage`: active=1, healthy=0, statuses=error
- `xai`: active=1, healthy=1, statuses=active
- `zhipu`: active=1, healthy=0, statuses=error

### 6. Memory, Logs, Fallbacks And Evaluations

- `settings/call_log_pipeline_enabled` enabled.
- `settings/memoryEnabled` remains enabled.
- Memory max tokens increased to `3000`.
- Memory retention increased to `45` days.
- Stale last-known-provider cache entries pointing to removed/problem routes were deleted.
- Fallback chains were registered for `gpt-5.5`, `claude-opus-4-6`, `long-context`, `free`, `coding`, and `research`.
- Eval suite created: `Routing Core Smoke 2026-05-05` (7 cases).

## Validation Snapshot

- Combos: 46.
- Compression combos: 4.
- Compression assignments: 40.
- Model-combo mappings: 38.
- Eval suites: 1.
- Eval cases: 7.
- Skills enabled: 54.
- Memories: 315.
- Semantic cache entries: 1249.
- Compression analytics entries: 13.

## Operational Notes

- Do not expose or commit `.env`, API keys, OAuth tokens or raw database contents.
- Before future direct database changes, create a backup in `/opt/omniroute/backups`.
- The Codex desktop default should remain native ChatGPT/Codex; OmniRoute is configured as an optional complementary router for other models/providers.
- If a future upstream update resets tables or aliases, reapply the policies recorded under `key_value/routing_policy`, `key_value/pricing_policy` and `key_value/compression`.
