# Model deep-dive — 2026-10-13 (night)
Nemotron 3.5 Bolt 74B-A9B + VeriLoop E2, researched for Mark's local benchmark candidacy.

---

## MODEL 1 — NVIDIA Nemotron 3.5 Bolt 74B-A9B (launches Oct 15, 2026 — tomorrow)

**Correction to the earlier sweep note:** it is 74B (not 70B) and the footprint is **24GB**, not under 20GB.

### What it is (verified from NVIDIA's Oct 13 announcement + NVIDIA blog)
- 74B-total / **9B-active-per-token** mixture-of-experts, purpose-built for always-on local agents (coding help, monitoring, tool calls, background tasks).
- Compressed to 24GB via **2-bit quantization with quantization-aware training** (QAT).
- Launch claimed through llama.cpp, vLLM, **Ollama**, Unsloth, LM Studio on Oct 15.
- NVIDIA promises full open release: weights, training data, recipes.
- **License: NVIDIA Open Model License** — permissive for commercial use and modification, but with field-of-use and redistribution restrictions. NOT Apache 2.0. Fine for Mark's free/local use; not as clean as Apache.

### What is NOT verified
- **No independent evals exist.** Every speed/quality claim is NVIDIA's own marketing ("faster than popular dense models, smarter than similar-footprint MoE models"). Mark's rule applies: it's marketing until a local run.
- GGUF provenance at launch is unclear. "Ollama support" likely means Ollama will host a build, and Unsloth (a launch partner) typically ships dynamic GGUFs — but no GGUF file exists yet to download. Community quants will follow within days of launch.
- The exact 2-bit scheme is NVIDIA's own (not GGUF Q2_K); how it maps to Ollama's quant ladder is TBD.

### The fit math for Mark's 7900 XT (20GB VRAM)
- **24GB does not fit in 20GB VRAM.** Ollama would partially offload to system RAM (he has 128GB, so it *runs*).
- Speed estimate: for MoE, all 74B weights stream through memory per token even though only 9B compute. ~55 GB/s system RAM ÷ ~24GB weights ≈ **~2 tok/s optimistic**, realistically 1.5–2.5 tok/s. That sits right on/below his large-tier gate (>2 tok/s).
- Quality: only 9B params are active per token — the per-token "brain" is closer to a 9B model than a 70B one. Against qwen3-coder:30b (dense, all 30B working) on raw coding, it is unlikely to win. Its pitch is agentic tool-calling efficiency, not raw code quality.

### Bottom line: WAIT
Do not pull on day one. Let the Oct 15 launch happen, let community GGUFs and independent benchmarks land (give it a week), then reassess. The 24GB footprint makes it a poor fit for the 20GB card anyway — it would live half in system RAM at gate-borderline speed. Revisit only if independent runs show surprising quality or a smaller quant appears.

---

## MODEL 2 — VeriLoop E2 (released ~Sept 23, 2026)

### What it is (verified from the official HF model card + release thread)
- **Maker:** Tsinghua SIGS Robot Lab (Libo Wang).
- **Base:** Qwen3.8-27B. **License: Apache 2.0.** Clean.
- **Post-training:** 1,841,831 records across software engineering, code-agent trajectories, terminal/tool use, math, scientific reasoning, and "verifier-sensitive repair."
- **Core idea — VeriLoop-Governed Recurrence (VGR):** generation and verification are separated; the model proposes, external evidence decides whether a candidate state is committed or rolled back. Honest-failure-adjacent philosophy: "Fail Loud, Never Fake Success" is literally one of their 14 public engineering rules. That should resonate with Mark.
- **262K native context** in the released tokenizer config.
- **Official GGUF ladder** (first-class release, not community): `tsinghua-sigs-robot-lab/VeriLoop-E2-GGUF`
  - BF16 50.1 GiB | Q8_0 26.6 | Q6_K 20.6 (their "overall sweet spot") | Q5_K_M 19.0 | **Q4_K_M 18.3** | Q3_K_M 16.8 | IQ2_S 16.8 | IQ1_M 16.8
  - Each low-bit tier is quantized directly from the canonical BF16 with paired logit-reference quality measurement (not requantized from another low-bit file). Unusually careful quant work.
  - Also ships MTP (speculative decoding) draft files for BF16/Q8_0/Q6_K/Q5_K_M.

### Benchmarks (vendor-reported — treat as marketing until local run)
- SWE-bench Pro **76.2%** (vs Qwen3.8-Max 67.7 — beats its own base family flagship by ~8.5 pts; vs Claude Fable 5.1 81.2)
- Terminal-Bench 2.1 **88.8%** (ties GPT-5.6 Sol)
- DeepSWE v1.1 64.6% | Terminal-Bench 3.0 29.7% | Terminal-Bench 4.0 37.9%
- AIME 2026 98.3% | GPQA Diamond 93.9% | MathArena Apex 89.6%
- Task-level evaluation evidence is published per benchmark (inspectable, not just declared).

### The honest caveats
- The README states an explicit **attribution boundary**: scores characterize the E2 *system configuration* — the checkpoint **inside their internal VeriLoop Harness** where the task required it. The bare checkpoint outside that runtime may not reproduce the numbers. The production harness itself is NOT released.
- No independent bare-checkpoint evals found yet (release is ~3 weeks old). Community reception is thin so far.
- Nothing published about honesty/citation discipline — Mark's trust criteria. The VGR philosophy *suggests* good instincts (external verification, fail loud), but that is not evidence.

### Bull case vs qwen3-coder:30b (Mark's deep/edit seat)
- Purpose-built for exactly the seat's criteria: code, terminal trajectories, tool use, long-horizon agentic work.
- Beats the Qwen3.8 flagship on SWE-bench Pro — this is not a random finetune; the post-training moved the needle over its base.
- Apache 2.0, official measured GGUF ladder, 262K context, 27B class = direct head-to-head with the 30B seat on the same hardware.
- The "fail loud, never fake success" engineering culture aligns with what Mark wants from the seat.

### Bear case
- All headline numbers are vendor numbers run with their harness — Mark's standing rule says they don't count.
- The seat was won on tools + honesty + coding *as Mark measured them*, not on leaderboard scores. E2 has to win the same local battery.
- Post-training on top of Qwen3.8 could have *narrowed* it (code/math specialist, weaker general chat) — the seat does general deep work too.

### Bottom line: WORTH PULLING for a local benchmark run
- **File:** `VeriLoop-E2-Q4_K_M.gguf` from `tsinghua-sigs-robot-lab/VeriLoop-E2-GGUF`
- **Size:** 18.3 GiB ≈ **~19.6 GB download** — fits the 7900 XT 20GB VRAM about like his current 30B Q4 seat model.
- **Why Q4_K_M:** Q6_K (their sweet spot) is 20.6 GiB ≈ 22.1 GB — too big for 20GB VRAM. Q5_K_M (19.0 GiB ≈ 20.4 GB) is also just over. Q4_K_M is the largest quant that fully fits.
- **Suggested run:** the same battery Mark used for the seat (tools/honesty/coding), plus a tok/s + VRAM check. This is the first genuine deep/edit-seat challenger since qwen3-coder:30b took the seat.

---

## Net recommendation
1. **VeriLoop E2 — pull Q4_K_M (~19.6 GB) and benchmark when there's a window.** Genuine challenger for the deep/edit seat; everything about the release (official ladder, measured quants, Apache 2.0, agentic code focus) says it's worth a local run.
2. **Nemotron 3.5 Bolt 74B-A9B — wait.** Launches tomorrow; nothing to download or verify yet. Even when it lands, 24GB on a 20GB card means gate-borderline speed via partial offload, and 9B-active is unlikely to out-code the 30B dense seat. Revisit in a week on independent evidence.
