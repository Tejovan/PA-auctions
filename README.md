# Screening Externalities in Auctions with Participant Audits: code and supplements

Code and supplementary material for the empirical evaluation (Section 7 and Appendix A) of the paper *Screening Externalities in Auctions with Participant Audits*.

The model has n bidders, k identical goods, and bidder types (e, v), where e is the externality and v the valuation. For a type distribution and a threshold function τ(e) common to all bidders, the package `PA_auctions` computes exactly:

- the expected outcomes of four mechanisms: VCG without audits, VCG-PA, IVCG-PA (the ironed VCG auction with participant audits), and the first best;
- the penalties r(e) that induce τ.

It searches for the τ that maximizes exact expected welfare (threshold-response ascent, and grid search over the best common welfare cutoff and the best clipped affine τ), checks the results, and draws figures.

## Contents

| Path | Contents |
|---|---|
| [empirical_supplement/empirical_supplement_v2.pdf](empirical_supplement/empirical_supplement_v2.pdf) | **Empirical supplement**: type distributions, exact computation, optimization, checks, the experiment, and results in every market and distribution. Source, data and figure data in [empirical_supplement/](empirical_supplement/). |
| [supplement/code_supplement_v6.pdf](supplement/code_supplement_v6.pdf) | **Code supplement**: package layout, running experiments, outputs. |
| [docs/user_guide.md](docs/user_guide.md) | user guide: command line, every configuration key, outputs of a run, distributions, Python interface |
| [PA_auctions/](PA_auctions/) | the package; [PA_auctions/README.md](PA_auctions/README.md) documents the methods at the level of functions |
| [run.py](run.py) | command-line entry point |
| [configs/](configs/) | `paper_run.toml` (the experiment of the paper), `smoke_test.toml` (a 15-second test run), `evaluate_example.toml` (evaluating given threshold functions) |
| [scripts/](scripts/) | data scripts for the figures and tables of the paper and the empirical supplement; previews of the distributions |
| [tests/](tests/) | test suite (`pytest`) |

## Install

Python ≥ 3.12.

```bash
uv sync                                   # with uv, from uv.lock
# or
python3 -m venv .venv
.venv/bin/pip install numpy scipy pandas matplotlib pygad pytest
```

## Test

```bash
.venv/bin/python -m pytest tests -q
.venv/bin/python run.py configs/smoke_test.toml --yes      # about 15 s; results not meaningful
```

## Reproduce the experiment of the paper

```bash
.venv/bin/python run.py configs/paper_run.toml --dry-run   # plan and time estimate
.venv/bin/python run.py configs/paper_run.toml --yes --workers N
```

The run has 716 combinations: four synthetic distributions, 20 markets (n, k), and for each market the optimized τ for VCG-PA and IVCG-PA, the best common welfare cutoff, the best clipped affine τ, the zero-welfare threshold τ⁰ and the ex ante welfare cutoff τ^EA. It took 51 minutes on 28 cores. It writes the run folder `runs/<date>_<time>_optimize_paper-run/` (`RUN` below), with `summary.csv` (exact outcomes and shares of the first-best gain of every combination), `checks.csv`, and figures and README files for every combination.

The figures and tables of the paper and the empirical supplement are drawn in LaTeX from data written by two scripts:

```bash
.venv/bin/python scripts/web27_paper_data.py RUN OUT                       # paper: Figures 3 and 4
.venv/bin/python scripts/empirical_supplement_data.py RUN empirical_supplement
cd empirical_supplement && pdflatex empirical_supplement_v2 && bibtex empirical_supplement_v2 \
  && pdflatex empirical_supplement_v2 && pdflatex empirical_supplement_v2
```

All reported quantities are exact expectations on the given distributions, so a rerun reproduces them up to floating-point differences.
