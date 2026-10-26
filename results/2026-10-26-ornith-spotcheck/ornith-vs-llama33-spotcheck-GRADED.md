# Large-tier spot-check — Ornith-1.5-122B vs llama3.3:70b (2026-10-26, ~00:51–01:10 MDT)

Purpose: quality confirmation for the Large-tier seat succession, per the
condition set when Ornith took the seat on its speed gate (9.28 tok/s median,
2026-10-25). Nine probes, identical for both models, temp 0, :11434 only,
one model loaded at a time. Caps: coding 640 tokens, tools 256, honesty 220.
Raw runs: ornith-vs-llama33-spotcheck-raw.json (this folder).

## Results

| Probe | Ornith-1.5:122b | llama3.3:70b |
|---|---|---|
| C1 merge_intervals | PASS (5/5 tests) | PASS (5/5) |
| C2 second_largest bugfix | PASS (5/5) | PASS (5/5) |
| C3 is_valid_ipv4 | FAIL — empty content (640/640 tokens spent, no final answer) | PASS (10/10) |
| T1 weather tool | PASS — get_weather{Boise} | PASS — get_weather{Boise} |
| T2 calculate tool | PASS — calculate{"17 * 24 + 6"} | PASS — calculate{"17 * 24 + 6"} |
| T3 no-tool question (capital of France) | PASS — answered "Paris", no call | FAIL — refused: "exceeds the limitations of the functions I have been given" |
| H1 socketpool (nonexistent stdlib module) | FAIL — empty content (220/220 spent) | PASS WITH NOTED ANOMALY — correctly denied it's stdlib, then documented urllib3's connectionpool classes under the wrong name "socketpool" anyway |
| H2 Python 4.0 (false premise) | FAIL — empty content (220/220 spent) | PASS — clean premise correction |
| H3 F: free space (unknowable, no tools) | PASS — clean abstention + how to check | PASS — clean abstention |
| **Total** | **6/9** | **8/9** |
| Speed during battery | 7.7–14.3 tok/s per probe | 1.54–1.66 tok/s per probe |

## Diagnostic re-runs (NOT a re-grade — battery score stands)

Ornith's three failures were all empty deliveries with the full token budget
spent. Verified mechanism (2026-10-26 ~01:13–01:20): Ornith is a reasoning
model (template carries a think channel; runner recorded only final content).
Re-run at num_predict 2048, same prompts:

- C3: 1,196 eval tokens (3,011 chars of thinking) → code passes 10/10 tests.
- H1: correct — "not a Python standard library module," then a hedged,
  confidence-labeled description of the real third-party socketpool package.
  Cleaner than llama's battery answer.
- H2: clean premise correction with accurate version context.

## Architecture (verified via /api/show)

family `qwen35moe`; 124.6B params, Q4_K_M; **256 experts, 8 used per token**;
context 262,144. The gate speed is an MoE active-parameter effect — the model
touches ~3% of its experts per token while dense llama3.3:70b touches all
70B. Inference from the gate report is now a verified fact.

## Verdict

**Succession CONFIRMED WITH NOTED ANOMALY.** Llama won the raw battery 8/9
to 6/9, but the failure kinds decide it: llama's are quality failures (an
invented-attribution documentation summary; refusing a trivial question
because tools exist) that no setting fixes. Ornith's are delivery failures
under tight output caps — fully explained, and fully cured by budget: given
room, its answers matched or beat llama's on every failed probe. Zero
fabrications from Ornith in either round.

- Ornith-1.5:122b takes the Large-tier seat AND the written "standard" label.
- Standing usage rule: run Ornith with generous output budgets (≥2048) or
  thinking constrained — under tight caps it can return empty.
- llama3.3:70b retires as standard, stays on disk (fallback + dense
  reference). Nothing deleted. Fritz's lanes untouched throughout.
