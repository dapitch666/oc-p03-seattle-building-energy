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
   use, compared with a naive baseline?
3. **Interpretation**: which characteristics drive the predictions of the
   selected model?

## Requirements

- Python 3.12+
- [uv](https://docs.astral.sh/uv/)

## Setup

```sh
uv sync
uv run pre-commit install   # contributors only: ruff + nbstripout hooks
```

Then open the notebooks with the `.venv` kernel (in VS Code: *Select Kernel* →
*Python Environments* → `.venv`).

## Data

The dataset is the **2016 Building Energy Benchmarking** survey published by
the City of Seattle on its open data portal, and provided by OpenClassrooms
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

## Project structure

```text
.
├── data/                   # git-ignored, see "Data"
├── notebooks/
│   └── 01_eda.ipynb        # exploratory data analysis
├── scripts/                # command-line entry points
├── src/
│   └── building_energy/    # reusable code (loading, cleaning, features, models)
├── pyproject.toml
└── uv.lock
```

The logic lives in the `building_energy` package; notebooks call it and
visualize the results.

## Roadmap

- [ ] Exploratory data analysis
- [ ] Feature engineering
- [ ] Feature preparation for modeling
- [ ] Model comparison (cross-validation)
- [ ] Hyperparameter tuning and feature importance
