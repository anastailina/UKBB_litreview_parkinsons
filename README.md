# UKBB Literature Review (Parkinson's / Dementia)

This repo is prepared around a single cleaned notebook:

- `UKBB_litreview_clean.ipynb`

## Quick start

1. Install dependencies:
   ```bash
   pip install -r Parkinsons/requirements.txt
   ```
2. Open and run:
   - `UKBB_litreview_clean.ipynb`

## Notebook outputs

The notebook writes:

- `data/interim/dementia_related_papers.csv`
- `data/processed/dementia_related_papers_cleaned.csv`
- `data/processed/dementia_related_papers_clustered.csv`
- `outputs/figures/tsne_visualization.png`
- `outputs/figures/stacked_bar_chart.png`

## Notes

- Optional scraping/Crossref steps are included but disabled by default (`RUN_SCRAPE=False`, `RUN_CROSSREF=False`).
- Default analysis input is `Parkinsons/UKBB_All_Publications_With_DOI_Abstracts.csv`.
