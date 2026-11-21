# OilSec-AI

An AI-assisted operational-technology (OT) security **testbed** for oil and gas, used to study whether small language models can act as incident investigators when every claim they make is mechanically verified.

> **Scope.** Everything runs on a *stylised, uncalibrated* two-phase separator simulation with *synthetic* provenance fields. Nothing here supports claims about a real plant, and nothing issues control commands.

Companion to the paper *"Small Language Models as Industrial Incident Investigators: A Verifier-Checked Negative Result on a Simulated Oil-and-Gas Separator"* (M. S. Aljumaily). **Headline results** (all reproducible from the archived runs): no tested model (Qwen2.5-1.5B/3B/7B) beat an always-"attack" baseline by more than two episodes; all three obeyed an injected operator note on every attack episode (69/69, 69/69, 136/136); 16.7-25.7% of structured claims cited the wrong evidence, against 1.6% for a known-true control; and the same generations score 18-45% defective depending on the counting rule. A physics-consistency twin detects weak attacks (0.983; 0.996 replication) that rules and an isolation forest miss, but only while its model is nearly exact.

## What is in the repository
| Path | Contents |
|---|---|
| `oilsec/sim/` | Separator process model, PI loops, SIS, attack/benign/fault scenarios, runner with separate random streams |
| `oilsec/detect/` | D1 rules, D2 CUSUM, D3 isolation forest, D4 physics twin (`OBSERVABLE` columns only) |
| `oilsec/triage/` | Evidence package (24 signal + 3 context items), context generator, deterministic report generator and claim verifier |
| `notebooks/` | Colab notebooks (detector extras, Suricata, LSTM-AE, **real-LLM investigator v2 lite**) and the scripts that assemble them |
| `experiments/` | Simulator validation, detector baselines, exploratory follow-ups, v1 replay diagnosis |
| `analysis/` | Re-scoring of the archived LLM generations (harmonised claim scoring, bootstrap, baselines) |
| `results/` | Archived outputs of every run, including the **failed first LLM run** (see `results/README.md`) |
| `docs/notebook/` | Lab notebook in order: protocols written before runs, results and critiques, the v1 diagnosis and v2 amendment |
| `paper/` | IEEE LaTeX source and figure script |
| `tests/` | 48 unit tests and a 27-mutant mutation check |

## Quick start
```bash
python3 -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt
python3 -m unittest discover -s tests        # 48 tests, ~10 s
python3 analysis/final_numbers.py            # re-scores archived LLM runs; matches analysis/final_numbers.json
```
Full table of commands per paper item: [`docs/REPRODUCING.md`](docs/REPRODUCING.md). Tested with Python 3.13, numpy 2.5, pandas 3.0, scipy 1.18, scikit-learn 1.9, matplotlib 3.11 (Python 3.12 works for the simulator and tests).

## Rules the code enforces
* Detectors may read only `OBSERVABLE` columns; `true_*` / `truth_*` columns are ground truth for scoring (leakage is tested).
* Test seeds are frozen: LLM dev 6000+, LLM test 7000-7019. Do not tune prompts on the test seeds.
* Post hoc changes (pre-filled assistant turn, tolerant parser, claim normalisation, harmonised claim scoring, reduced model ladder) are logged in `docs/notebook/06_llm_v1_diagnosis_and_v2_amendment.md` and in the paper's Section 5.

## Known limitations
Simulator is uncalibrated and the twin is evaluated on the simulator's own equations (inverse crime); one model family, one prompt wording, one injection template; the 7B model is 4-bit and was run on 174 episodes against 89 for the smaller models. See the paper's Section 8 and the research agenda in its Table 7.

## Citation and licence
Cite via [`CITATION.cff`](CITATION.cff). Code: MIT ([`LICENSE`](LICENSE)). Archived results and documentation may be reused with attribution (CC BY 4.0 intended; confirm before release).
