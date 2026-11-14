# Assignment 4: Reweighing (Kamiran & Calders, 2012) on Adult, German and COMPAS

This folder was added to a fork of [Trusted-AI/AIF360](https://github.com/Trusted-AI/AIF360). The AIF360 code itself is used unchanged (`aif360.algorithms.preprocessing.Reweighing`). Everything specific to the assignment is in `assignment4/`.

| File | Purpose |
|---|---|
| `reweighing_evaluation.ipynb` | Full pipeline for Task 2 (run top to bottom) |
| `results/` | CSV tables and figure written by the notebook |

## What is evaluated

- **Datasets and protected attribute:** Adult (`sex`), German Credit (`sex`), COMPAS (`race`), loaded with the default AIF360 loaders and pre-processing.
- **Privileged group** is value 1 of the protected attribute in AIF360 (Adult: Male, German: male, COMPAS: Caucasian). **Favourable label** is 1.
- **Measures:** demographic parity difference (`DP_Diff` = P(fav | unprivileged) - P(fav | privileged)), disparate impact ratio (`DIR`), consistency (k = 5, standardised features), plus accuracy, balanced accuracy and F1.
- **Before mitigation:** measures on the original labels of each full dataset.
- **After mitigation:** measures on the predicted labels of the held-out test split, for a baseline logistic regression and for logistic regression trained with the Reweighing weights.

## Parameter settings

| Parameter | Value |
|---|---|
| Train/test split | 70 / 30, stratified on (label, protected attribute) |
| Seeds | 0 to 9 (10 splits; results are mean ± std) |
| Feature scaling | `StandardScaler`, fitted on the training part only |
| Classifier | scikit-learn `LogisticRegression(max_iter=1000)`, other settings default |
| Reweighing | AIF360 defaults, privileged = `{attr: 1}`, unprivileged = `{attr: 0}` |
| Consistency | k = 5 nearest neighbours (own brute-force helper, validated against AIF360 on German in Section 2b) |

## How to reproduce

Tested on a Linux/macOS-style shell with Python 3.10 or newer. On Windows, activate the environment with `.venv\Scripts\activate`.

1. **Fork** https://github.com/Trusted-AI/AIF360 with the *Fork* button on GitHub.
2. **Clone your fork** and enter it:
   ```bash
   git clone https://github.com/<your-username>/AIF360.git
   cd AIF360
   ```
3. **Create an environment and install AIF360 from the fork:**
   ```bash
   python -m venv .venv
   source .venv/bin/activate
   pip install -e .
   pip install jupyter matplotlib
   ```
4. **Open the notebook** from the repository root:
   ```bash
   jupyter notebook assignment4/reweighing_evaluation.ipynb
   ```
5. Run **Kernel > Restart & Run All**. The first run downloads the three raw datasets into `aif360/data/raw/{adult,german,compas}/`. Runtime is a few minutes, mostly the nearest-neighbour searches on Adult.
6. The results appear in the notebook and are written to `assignment4/results/` (relative to where Jupyter was started, so start it from the folder you want the files in).
7. After a successful run, record the exact versions: `pip freeze > assignment4/requirements.txt`.

### If a download fails

Put these files by hand into the matching folder under `aif360/data/raw/` of your clone:

- `adult/`: `adult.data`, `adult.test`, `adult.names` from https://archive.ics.uci.edu/ml/machine-learning-databases/adult/
- `german/`: `german.data`, `german.doc` from https://archive.ics.uci.edu/ml/machine-learning-databases/statlog/german/
- `compas/`: `compas-scores-two-years.csv` from https://github.com/propublica/compas-analysis

Do not commit the raw data files.

## Expected outputs

`results/original_labels.csv`, `results/results_per_seed.csv`, `results/results_summary.csv`, `results/paired_change.csv`, `results/comparison.png`. The notebook is saved with its outputs, so the tables can be read directly on GitHub.
