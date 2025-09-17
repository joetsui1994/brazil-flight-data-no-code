# Brazil International Passenger Arrivals Pipeline

This repository holds a reproducible, notebook-driven workflow for summarising ANAC's
flight statistics into dengue-relevant passenger arrival metrics.

The current dataset covers 2021–2025 and is distributed by ANAC as
`Dados_Estatisticos_2021_a_2030.json.tar.gz`. Download that archive (kept out of git)
into the repository root before running the notebook. The pipeline filters the raw file
down to international arrivals into Brazil and aggregates them by month, Brazilian
region, and origin country.

## Repository structure

```
├── data
│   ├── processed                               # CSV outputs from the pipeline
│   └── raw                                     # place for extracted JSON (git-ignored)
├── figures                                     # publication-friendly plots
├── notebooks                                   # analysis notebooks
├── reports                                     # QA summaries (e.g., missing origin countries)
└── requirements.txt                            # minimal Python dependencies
```

## Setup

1. **Download and extract the raw JSON (one time)**
   ```bash
   mkdir -p data/raw/anac
   wget "https://sistemas.anac.gov.br/dadosabertos/Voos%20e%20opera%C3%A7%C3%B5es%20a%C3%A9reas/Dados%20Estat%C3%ADsticos%20do%20Transporte%20A%C3%A9reo/Dados_Estatisticos_2021_a_2030.json.tar.gz"
   tar -xzf Dados_Estatisticos_2021_a_2030.json.tar.gz -C data/raw/anac
   ```

2. **Create a Python environment and install dependencies**
   ```bash
   python -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```

3. **Launch Jupyter** (or execute with Papermill):
   ```bash
   jupyter lab  # or jupyter notebook
   ```

## Running the pipeline notebook

Open and run `notebooks/international_arrivals_pipeline.ipynb`. The parameter cell near
the top lets you change the analysis window (default: 2005–2025) and the maximum number
of origin countries to highlight per region when plotting. The notebook will:

1. Stream the ANAC JSON (multiple arrays concatenated within the tar) without loading it
   entirely into memory.
2. Filter for flights arriving into Brazil from countries other than Brazil.
3. Track and report any records with missing origin-country information.
4. Aggregate passenger counts by month, region, and origin country.
5. Produce a stacked-bar overview figure (one subplot per Brazilian region).

## Outputs

Running the notebook will create/update the following artifacts:

- `data/processed/monthly_international_arrivals_by_region_origin.csv`
- `data/processed/yearly_international_arrivals_by_region_origin.csv`
- `reports/missing_origin_summary.csv`
- `figures/international_arrivals_by_region.png` (ignored by git; regenerate via the notebook)

These files are safe to check into version control because they are lightweight summaries
compared to the raw source.

## Notes

- The raw JSON file extracted into `data/raw/anac/` is intentionally ignored by git to
  prevent large commits.
- The ANAC extract occasionally lists domestic flights; the notebook filters them out by
  destination country and origin country name.
- Missing origin-country entries are logged in `reports/missing_origin_summary.csv` so
  you can quantify potential data gaps in downstream analyses.
