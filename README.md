# Structural Size Index (SSI): A Language-Agnostic Software Size Metric Derived from Abstract Syntax Trees

This repository contains the extraction pipeline, dataset, and analysis notebooks for the Structural Size Index (SSI), submitted for anonymous peer review. 

## Repository structure

```
.
├── notebooks/                      All notebooks used to produce results in the paper
├── data/                           All CSV datasets, raw and derived
├── excluded-approaches/            Discarded analyses, kept for transparency (see below)
├── requirements.txt                Python dependencies
└── README.md
```


## Reproducing the pipeline in order

1. `notebooks/mp-dataset-download-ast-features.ipynb` - downloads CodeSearchNet, samples 500 functions per language (seed 42), extracts 32 AST features per function via Tree-sitter.
2. `notebooks/loc-and-cyclomatic-complexity.ipynb` - computes LOC and Cyclomatic Complexity on the same sample.
3. `notebooks/ssi-creation.ipynb` - standardizes the 32 features and fits PCA; SSI is the first principal component. This notebook produces the frozen scaler mean/std and PCA loading weights used everywhere downstream.
4. `notebooks/comparativeanalysisofsoftwaremetrics.ipynb` - merges SSI with the LOC/CC baselines and computes RQ1's correlation table.
5. `notebooks/cross-language-validation.ipynb` - leave-one-language-out Random Forest reconstruction.
6. `notebooks/humaneval-x-cross-language-validation.ipynb` - downloads HumanEval-X, computes the matched-task cross-language CV comparison.

## Note:

Two notebooks in this folder train a Random Forest to predict SSI from the same AST features used to construct it via PCA. Since SSI is a deterministic function of those features, a high self-prediction score only shows the PCA projection is well-behaved, not that SSI carries information beyond its own construction. These notebooks are kept for transparency but are not the basis for any result reported in the paper.

## Requirements

See `requirements.txt`. Notable dependencies: `tree-sitter` and `tree-sitter-language-pack` for AST parsing across Java, Python, JavaScript, and Go; `scikit-learn` for PCA, standardization, and the Random Forest checks; `datasets` (Hugging Face) for CodeSearchNet.

Install with:
```
pip install -r requirements.txt
```

## Data

`data/` contains both raw extraction outputs and derived files. `dataset_with_ssi.csv` is the canonical 2,000-function CodeSearchNet reference corpus with SSI computed; all frozen normalization parameters used elsewhere in the paper are fit on this file. `humaneval_x_*.csv` files are derived from the public HumanEval-X benchmark (Java, Python, JavaScript, Go; 164 matched tasks) using those same parameters.

## License

To be added.
