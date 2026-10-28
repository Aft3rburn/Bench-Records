# bench-unified

A five-stage benchmark battery for locally-run LLMs. Every model faces the
exact same prompts, tool schemas, and questions — the methodology is fixed
verbatim in `bench_unified.py` so results are comparable across runs.

The point of this battery is **independent verification**: run vendor
claims on your own hardware instead of trusting the vendor's benchmark
page. It powers the benchmark tables at [Bench Records](https://aft3rburn.github.io/Bench-Records/).

## The five stages

| Stage | What it measures |
|---|---|
| `coding` | Write a documented Python function (speed in tok/s measured here) |
| `autocode` | 4 auto-scored coding tasks — the model's code is extracted and executed against unit tests, pass/fail is automatic |
| `tools` | 12-case tool-calling probe: right tool, right args, right call — including disambiguation between similar tools, bare-name commands, typo'd names, and restyle-vs-switch traps |
| `continuity` | Multi-turn topic continuity: does the model stay on the established thread or invent an unrelated task |
| `fabrication` | Honesty probe, auto-scored: an 8-question known-public control set must be answered correctly (proves the model isn't just abstaining on everything); operator-fact and unknowable questions must be declined, not invented. A model that fabricates fails this axis regardless of its other scores. Verdicts: PASS / WEAK (honest but missed known facts) / FAIL (fabricated) |

## Two-gated workflow

Models are expensive to benchmark fully, so the battery gates itself:

```
python bench_unified.py <model> speed   # Gate 1: coding bench vs the speed bar (exit 3 = fail)
python bench_unified.py <model> full    # Gate 2: everything, explicit go-ahead after a PASS
python bench_unified.py <model> all     # default: gate enforced automatically
python bench_unified.py <model> tools   # any single stage can run alone
```

## Requirements

- Python 3.9+
- [Ollama](https://ollama.com/) serving the model under test (or any OpenAI-compatible `/api/chat` endpoint)

No API keys, no cloud services, no dependencies beyond the standard library.

## Configuration

| Variable | Default | Purpose |
|---|---|---|
| `BENCH_CHAT_URL` | `http://localhost:11434/api/chat` | Ollama endpoint under test |
| `BENCH_OUT_DIR` | `C:\bench` | Progress log + result files |
| `BENCH_SPEED_BAR` | `50.0` | Speed-gate bar in tok/s |
| `BENCH_READ_FILE` | `C:\bench\notes.txt` | Example path for the read_file tool case |

## Output

- `<out_dir>/bench_progress.jsonl` — one JSON line per stage/task (for live progress polling)
- `<out_dir>/bench_results_<model>_<timestamp>.json` — full machine-readable results, stamped with `battery_version`

Results are only comparable within the same `battery_version` (currently
`1.1.1`, printed at the top of every run). Bump the version on any change
to prompts, cases, or grading.

### Changelog

- **1.1.1** — grader fixes: startup crash (printed `stage` before assignment),
  curly-apostrophe declines (U+2019) now normalize to straight quotes before
  matching abstention signals, two new abstention signals ("there is no
  information provided", "wouldn't know"), and the `read_file` tool case is
  now winnable — tool arguments are compared as parsed values instead of
  `str(dict)`, which repr-escaped backslashes so no Windows path could ever
  match. Scores from 1.1.0 are **not** comparable: every tool-capable model
  gains the previously-unwinnable `read_file` point.
- **1.1.0** — fabrication battery expanded to an 8-question known-answer
  control set; auto-scored PASS/WEAK/FAIL verdicts; `battery_version` stamped
  on results.

## Adapting it

The fabrication questions and the face-tool cases are the parts most tied
to one operator's setup:

- Replace `OPERATOR_FACTS` with your own (or leave them — a model that was
  never given them should decline them anyway, which is the honest answer).
- Swap the face tool schemas for tools your own assistant actually has.
  The test shape — similar tools, ambiguous prompts — is the methodology;
  the specific tools are just one instance of it.

Don't "improve" the prompts per model. The whole point is every model
faces the same test.

## License

MIT — see [LICENSE](LICENSE).

## Results archive

The raw, machine-readable results behind every published Bench Records
post live in [`results/`](results/) — per-model JSON output from this
battery plus the sweep notes, indexed in
[`results/README.md`](results/README.md). Every table on the site traces
back to a file there.
