# Raw results archive

The machine-readable records behind the published Bench Records posts.
Every table on the site traces back to a file here — checkable, not vibes.

Site: [Bench Records](https://aft3rburn.github.io/Bench-Records/) ·
Battery: [`bench_unified.py`](../bench_unified.py)

JSON files are the direct output of the battery: model name, run
timestamp, and per-stage results (coding speed, autocode tasks, the
12-case tool probe, continuity, and the fabrication answers).

## 2026-09-27 — the overnight battery

*Backs post 2, "The seat holder: qwen3-coder:30b vs five challengers" —
the eight-model overnight run on the RX 7900 XT lane.*

| File | Model | Run date | Contents |
|---|---|---|---|
| `2026-09-27/bench_results_qwen3-coder_30b_20260926-001727.json` | qwen3-coder:30b | 2026-09-26 | Full battery result — the seat holder's baseline (92.4 tok/s, autocode 4/4, tools 5/12) |
| `2026-09-27/bench_results_north-mini-code-1.0_20260926-230705.json` | north-mini-code-1.0 | 2026-09-26 | Full battery result (93.8 tok/s, autocode 4/4, tools 5/12) |
| `2026-09-27/bench_results_gpt-oss_20b_20260926-002847.json` | gpt-oss:20b | 2026-09-26 | Full battery result, first run (101.1 tok/s, tools 3/12) |
| `2026-09-27/bench_results_gpt-oss_20b_20260927-204204.json` | gpt-oss:20b | 2026-09-27 | Full battery result, second run (98.0 tok/s, tools 4/12) |
| `2026-09-27/bench_results_gemma4_12b_20260927-212345.json` | gemma4:12b | 2026-09-27 | Full battery result, first run (50.3 tok/s, tools 5/12) |
| `2026-09-27/bench_results_gemma4_12b_20260928-000614.json` | gemma4:12b | 2026-09-28 | Full battery result, second run (54.0 tok/s, tools 5/12) |
| `2026-09-27/bench_results_deepseek-r1_14b_20260926-221029.json` | deepseek-r1:14b | 2026-09-26 | Full battery result (52.0 tok/s, autocode 4/4, tools 3/12) |
| `2026-09-27/bench_results_granite4.2_8b_20260926-001406.json` | granite4.2:8b | 2026-09-26 | Full battery result (80.0 tok/s, autocode 3/4, tools 3/12) |
| `2026-09-27/bench_results_granite4_tiny-h_20260927-133424.json` | granite4:tiny-h | 2026-09-27 | Full battery result (141.3 tok/s, autocode 3/4, tools 5/12) — the voice-seat holder |
| `2026-09-27/bench_results_granite4_micro-h_20260925-235545.json` | granite4:micro-h | 2026-09-25 | Fabrication-stage run only — this model produced no summary scores in the overnight table |

## 2026-10-24 — Bolt head-to-head

*Backs post 5, "Bolt vs the seat holder" — nemotron-3.5-bolt:latest
challenging qwen3-coder:30b for the deep-lane seat.*

| File | Model(s) | Run date | Contents |
|---|---|---|---|
| `2026-10-24-bolt-h2h/bench_results_nemotron-3.5-bolt_latest_20261024-023318.json` | nemotron-3.5-bolt:latest | 2026-10-24 | Full battery result for the challenger (coding 4/4, tools 4/12 official, honesty clean, continuity failed) |
| `2026-10-24-bolt-h2h/results.json` | qwen3-coder:30b + nemotron-3.5-bolt:latest | 2026-10-23 | Turn-by-turn head-to-head probe log (code-edit, codegen, reasoning, structured-JSON turns with per-turn speed) |

## 2026-10-25/26 — Ornith gate + succession spot-check

*Backs post 6 (upcoming), "Ornith succession" — ornith-1.5:122b taking
the Large-tier seat from llama3.3:70b, gated on speed, then confirmed
(or not) by a quality spot-check.*

| File | Model(s) | Run date | Contents |
|---|---|---|---|
| `2026-10-25-ornith-gate/ornith-1.5-122b-gate.json` | ornith-1.5:122b (baseline: llama3.3:70b) | 2026-10-25 | Large-tier speed-gate record — Ornith 9.28 tok/s median vs the 70B baseline's 1.68, with cold-load timing notes (RX 7900 XT + RAM spillover lane) |
| `2026-10-26-ornith-spotcheck/ornith-vs-llama33-spotcheck-GRADED.md` | ornith-1.5:122b vs llama3.3:70b | 2026-10-26 | Graded nine-probe spot-check (3 coding, 3 tool, 3 honesty) — Ornith passed tools clean but spent full token budgets with empty content on three probes; llama3.3 graded alongside |
| `2026-10-26-ornith-spotcheck/ornith-vs-llama33-spotcheck-raw.json` | ornith-1.5:122b vs llama3.3:70b | 2026-10-26 | Raw probe log behind the graded spot-check — every prompt and response, unedited |

## Model sweeps — challenger research notes

*Behind posts 2 and 3: the research and head-to-head notes the
challenger verdicts were drawn from.*

| File | Models | Date | Contents |
|---|---|---|---|
| `model-sweeps/2026-10-13-sweep.md` | release sweep, Oct 6–13 window | 2026-10-13 | New-model sweep notes — which releases were tier-relevant and worth a local run |
| `model-sweeps/2026-10-13-nemotron-veriloop.md` | Nemotron 3.5 Bolt 74B-A9B, VeriLoop E2 | 2026-10-13 | Deep-dive research on both challengers before their local runs (backs posts 3 and 5) |
| `model-sweeps/2026-10-14-veriloop-vs-granite.md` | VeriLoop E2 (27B) vs granite4:tiny-h | 2026-10-14 | The honesty head-to-head behind post 3 — VeriLoop fabricated an SSN and was skipped; granite held the voice seat |
| `model-sweeps/2026-10-14-lfm-vs-granite.md` | LFM2.5-24B-A2B vs granite4:tiny-h | 2026-10-14 | Speed showdown — LFM ~72% faster (152.1 vs 95.7 tok/s) but short on tools and continuity; granite kept the seat |
