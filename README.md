# Structural Size Index (SSI): A Language-Agnostic Software Size Metric Derived from Abstract Syntax Trees

This repository contains the extraction pipeline, dataset, and analysis notebooks for the Structural Size Index (SSI), submitted for anonymous peer review. 

## Repository structure

```
.
├── main.tex                        Paper source
├── notebooks/                      All notebooks used to produce results in the paper
├── data/                           All CSV datasets, raw and derived
├── excluded-approaches/            Discarded analyses, kept for transparency (see below)
├── requirements.txt                Python dependencies
└── README.md
```

## Which notebook produces which result

| Paper item | Notebook | Output file |
|---|---|---|
| Feature extraction (§4.1) | `notebooks/mp-dataset-download-ast-features.ipynb` | `data/codesearchnet_sample.csv` → `data/ast_features_final.csv` |
| SSI computation, §4.2–4.3 | `notebooks/ssi-creation.ipynb` | `data/dataset_with_ssi.csv` |
| LOC / Cyclomatic Complexity baselines | `notebooks/loc-and-cyclomatic-complexity.ipynb` | `data/traditional_metrics.csv` |
| Table 2 (RQ1 correlations) | `notebooks/comparativeanalysisofsoftwaremetrics.ipynb` | `data/correlation_matrix.csv`, `data/unified_metrics.csv`, `data/summary_statistics.csv` |
| Table 4 & 5 (RQ2, PCA loadings and feature-group contribution) | `notebooks/ssi-creation.ipynb` (PCA object) | computed directly from `data/dataset_with_ssi.csv` |
| Table 6 (RQ3, HumanEval-X cross-language CV) | `notebooks/humaneval-x-cross-language-validation.ipynb` | `data/humaneval_x_features.csv`, `data/humaneval_x_with_ssi.csv`, `data/humaneval_x_cv_results.csv` |
| Table 7 (RQ3, leave-one-language-out reconstruction) | `notebooks/cross-language-validation.ipynb` | printed in-notebook |

## Reproducing the pipeline in order

1. `notebooks/mp-dataset-download-ast-features.ipynb` — downloads CodeSearchNet, samples 500 functions per language (seed 42), extracts 32 AST features per function via Tree-sitter.
2. `notebooks/loc-and-cyclomatic-complexity.ipynb` — computes LOC and Cyclomatic Complexity on the same sample.
3. `notebooks/ssi-creation.ipynb` — standardizes the 32 features and fits PCA; SSI is the first principal component. This notebook produces the frozen scaler mean/std and PCA loading weights used everywhere downstream.
4. `notebooks/comparativeanalysisofsoftwaremetrics.ipynb` — merges SSI with the LOC/CC baselines and computes RQ1's correlation table.
5. `notebooks/cross-language-validation.ipynb` — leave-one-language-out Random Forest reconstruction (Table 7).
6. `notebooks/humaneval-x-cross-language-validation.ipynb` — downloads HumanEval-X, applies the **frozen** parameters from step 3 (no refitting), computes the matched-task cross-language CV comparison (Table 6).

## About `excluded-approaches/`

Two notebooks in this folder train a Random Forest to predict SSI from the same AST features used to construct it via PCA. As the paper states in §5.2, this is not a validation of SSI's usefulness — since SSI is a deterministic function of those features, a high self-prediction score only shows the PCA projection is well-behaved, not that SSI carries information beyond its own construction. These notebooks are kept for transparency but are not the basis for any result reported in the paper.

## Requirements

See `requirements.txt`. Notable dependencies: `tree-sitter` and `tree-sitter-language-pack` for AST parsing across Java, Python, JavaScript, and Go; `scikit-learn` for PCA, standardization, and the Random Forest checks; `datasets` (Hugging Face) for CodeSearchNet.

Install with:
```
pip install -r requirements.txt
```

## Data

`data/` contains both raw extraction outputs and derived files. `dataset_with_ssi.csv` is the canonical 2,000-function CodeSearchNet reference corpus with SSI computed; all frozen normalization parameters used elsewhere in the paper are fit on this file. `humaneval_x_*.csv` files are derived from the public HumanEval-X benchmark (Java, Python, JavaScript, Go; 164 matched tasks) using those same frozen parameters.

## License

To be added upon acceptance.
