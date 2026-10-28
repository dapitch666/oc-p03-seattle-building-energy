# Seattle Building Energy

Predicting the energy use of Seattle's non-residential buildings from their
structural characteristics, with supervised regression models (scikit-learn).

## Context

This project is part of the OpenClassrooms **AI Engineer** training program.

The City of Seattle aims to be carbon neutral by 2050, and buildings are a
major source of energy use and emissions. In 2016, city agents carefully
measured the consumption of buildings across the city. These surveys are
expensive, so the city wants to **estimate the energy use of non-residential
buildings that have not been measured yet**, using only information that is
known without a survey: size, use, construction year, location, and so on.

The work covers three questions:

1. **Exploration**: what does the building stock look like, and which
   buildings are relevant or reliable for modeling?
2. **Modeling**: how well can supervised models predict a building's energy
   use, compared with a naive baseline and an energy expert's rule of thumb?
3. **Interpretation**: which characteristics drive the predictions of the
   selected model?

## Requirements

- Python 3.12+
- [uv](https://docs.astral.sh/uv/)

## Setup

```sh
uv sync
```

Then open the notebooks with the `.venv` kernel (in VS Code: *Select Kernel* →
*Python Environments* → `.venv`).

### Development setup

To work on the code after cloning, install the ruff hooks (lint and format,
notebooks included) and the nbstripout git filter:

```sh
uv run pre-commit install

uv run nbstripout --install --attributes .gitattributes
NB="$(pwd)/.venv/bin/python -m nbstripout --keep-output --keep-count"
git config filter.nbstripout.clean "$NB"
git config diff.ipynb.textconv "$NB -t"
git config filter.nbstripout.extrakeys "metadata.kernelspec metadata.language_info.version"
```

The filter strips the metadata that the editor rewrites (kernel, Python
version) from the committed notebooks and from the diffs, but it keeps the
outputs and the execution counts, because the notebooks are deliverables.

- `.gitattributes` is versioned and applies the filter to every notebook. The
  filter itself lives in `.git/config`, which is not versioned: run these
  commands once after cloning.
- `nbstripout --install` does not record any option, hence the `git config`
  lines. Run them again after any new `--install`.
- `git status` may still list a notebook that the editor only re-saved. Its
  `git diff` is empty, and `git add` clears it.
- VS Code compares notebooks without the filter, so its diff view still shows
  the metadata changes. To hide them, add `"notebook.diff.ignoreMetadata": true`
  to the VS Code `settings.json`.

## Data

The dataset is the **2016 Building Energy Benchmarking** survey published by
the City of Seattle on its [open data portal](https://data.seattle.gov/Built-Environment/Building-Energy-Benchmarking-Data-2015-Present/teqw-tu6e/about_data), and provided by OpenClassrooms
with the project brief. It describes 3,376 properties with 46 columns:
identification and location, structure (floor area, floors, construction
year), property uses, ENERGY STAR score, and measured energy use and
greenhouse gas emissions. Energy is expressed in kBtu.

The data is not redistributed in this repository (`data/` is git-ignored).
To run the code, place the file so that you get:

```text
data/
└── raw/
    └── 2016_Building_Energy_Benchmarking.csv
```

`data/processed/` is generated: running `notebooks/01_eda.ipynb` writes the
cleaned dataset (`buildings_cleaned.parquet`) that
`notebooks/02_feature_engineering.ipynb` loads, and that notebook writes
`buildings_feature_engineered.parquet` for `notebooks/03_modeling.ipynb`, so run
the notebooks in order.

## Project structure

```text
.
├── data/                   # git-ignored, see "Data"
├── notebooks/
│   ├── 01_eda.ipynb                  # exploratory data analysis, writes data/processed/
│   ├── 02_feature_engineering.ipynb  # feature engineering, reads data/processed/
│   └── 03_modeling.ipynb             # train/test split, preprocessing, model comparison
├── pyproject.toml
└── uv.lock
```

## Results

- **Reference to beat**: an energy expert's rule of thumb, area × median energy
  use per sq ft of the property type, learned on the training set.
- **Model selection** (5-fold cross-validation, MAPE): Ridge 62% against 66% for
  the rule, ahead on every fold. The random forest (70%) and CatBoost (66%,
  65% once tuned) overfit and do not beat the rule steadily. Ridge is selected
  before opening the test set.
- **Test set**: Ridge reaches 64% MAPE, in line with cross-validation. Its R²
  drops from 0.72 to 0.22, and every model, the rule included, falls to about
  0.2: three properties (a power plant with labs and two data centers) carry
  94% of the squared error.
- **Interpretation**: area and property type carry 94% of Ridge's permutation
  importance. The model rediscovers the rule of thumb. The next step is a
  feature that flags energy-intensive uses, from `ListOfAllPropertyUseTypes`.

## Roadmap

- [x] Exploratory data analysis
- [x] Feature engineering
- [x] Feature preparation for modeling
- [x] Model comparison (cross-validation)
- [x] Hyperparameter tuning and feature importance
