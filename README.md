# UKBB Literature Review (Parkinson's / Dementia)

This repo is prepared around a single cleaned notebook:

- `UKBB_litreview_clean.ipynb`

## Create environment

Use one of the options below.

### Option A (recommended): `venv`

```bash
cd /Users/ai421/Desktop/GitHub/UKBB_litreview_parkinsons
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r Parkinsons/requirements.txt
pip install jupyterlab ipykernel
python -m ipykernel install --user --name ukbb-litreview --display-name "Python (ukbb-litreview)"
```

### Option B: `conda`

```bash
cd /Users/ai421/Desktop/GitHub/UKBB_litreview_parkinsons
conda create -n ukbb-litreview python=3.11 -y
conda activate ukbb-litreview
pip install -r Parkinsons/requirements.txt
pip install jupyterlab ipykernel
python -m ipykernel install --user --name ukbb-litreview --display-name "Python (ukbb-litreview)"
```

## Quick start

1. Activate your environment:
   ```bash
   source .venv/bin/activate
   ```
   If you used conda:
   ```bash
   conda activate ukbb-litreview
   ```
2. Install dependencies:
   ```bash
   pip install -r Parkinsons/requirements.txt
   ```
3. Open and run:
   - `UKBB_litreview_clean.ipynb`

## Notebook outputs

The notebook writes:

- `data/interim/dementia_related_papers.csv`
- `data/processed/dementia_related_papers_cleaned.csv`
- `data/processed/dementia_related_papers_clustered.csv`
- `outputs/figures/combined_wordclouds.png`
- `outputs/figures/tsne_visualization.png`
- `outputs/figures/stacked_bar_chart.png`
- `outputs/figures/full_figure.png`

## Notes

- Optional scraping/Crossref steps are included but disabled by default (`RUN_SCRAPE=False`, `RUN_CROSSREF=False`).
- Default analysis input is `Parkinsons/UKBB_All_Publications_With_DOI_Abstracts.csv`.
