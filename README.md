# Frozen B0–B3 evaluation analysis package

## Inputs
This analysis uses the frozen artifacts from `ATR_Gemini/Final_Freeze_Report`:

- `data/scenarios/frozen_scenarios.jsonl` — 140 scenario templates
- `results/configured_run/frozen_run_traces.jsonl` — 560 policy–scenario traces
- `results/configured_run/frozen_metrics.json` — frozen aggregate cross-check
- `configs/frozen_experiment.json` — frozen configuration and metric list

The original inputs are included under `inputs/` in this package. The evaluation script derives the result tables from raw traces; it does not copy the aggregate values as its source of truth. It recomputes outcomes and cross-checks the stored flags and aggregate JSON.

## Run

Requires Python 3.10+ and matplotlib. No pandas or SciPy dependency is needed.

```bash
python -m pip install -r requirements.txt
python evaluate_frozen_results.py --data-dir inputs/Final_Freeze_Report --out-dir outputs
```

## Outputs

- `results.csv` — one row per split × policy, including raw numerators, denominators, rates, metric directions and source hashes.
- `results_by_family.csv` — split × scenario family × policy breakdown.
- `metric_dictionary.json` — metric direction metadata.
- `audit_report.json` / `.txt` — ID/label alignment, missingness, partition, comparator and leakage checks.
- `publication_tables.md` — copy-ready descriptive table.
- `figure1` — stale-objective commit rates (PNG and PDF).
- `figure2` — legitimate completion and false-block rates (PNG and PDF).
- `figure3` — duplicate-effect attempts/commits, development only (PNG and PDF).
- `figure4` — simulator steps (PNG and PDF).

## Audit interpretation

The audit verifies 140 unique scenario IDs, 560 unique scenario–policy pairs, matched scenario IDs across B0–B3, scenario label alignment, and exact consistency between recomputed trace outcomes and the frozen aggregate metrics. It also checks cross-split exact normalized structural signatures after removing explicit IDs. Zero exact signature overlap is not proof of semantic independence or broad generalization.

The traces contain no repeated stochastic runs or independent sampling units. Therefore, the script does not compute confidence intervals, p-values, or statistical significance. Generated templates must not be treated as independent samples. Held-out scenarios contain zero duplicate-retry opportunities, so held-out duplicate-effect performance is not estimable.

B2 uses a scripted oracle, not an LLM planner. The traces do not encode per-policy resource/planner budgets or code hashes, so those comparator settings cannot be fully verified from this artifact. Simulator steps are not wall-clock latency. No real LLM or external API was evaluated.

## Provenance

This evaluation package uses the final frozen artifacts from the main project archive. The `frozen_metrics.json` input is copied from the main project's `results/configured_run/frozen_metrics.json`, with `created_utc = 2026-11-03T18:01:03.045401+00:00`. Its SHA-256 is `b4d372cc873ebc54c4fe19d3d254a9ea0cb4d839e0a98d29d1baba56a50a3070`. The earlier packaged metrics file dated 2026-10-31 was replaced. The metrics content was identical, but the older timestamp did not match the final frozen run.

The frozen configuration, scenario, and trace SHA-256 values are respectively:

- Configuration: `b1f014100675859214ad874833344f1dc51b48e07f8e3dc79e6cd5834ab75961`
- Scenarios: `b502f04561e34c2005ad1a358ad84f8c48ecc71831292f4c7f3eab1520572ed7`
- Traces: `9f46836696b5b8490612a0a9ced0234653db55d059cc94fde9c01fe319c1776d`

The evaluation script was rerun against these four final inputs. Its audit status is PASS.
