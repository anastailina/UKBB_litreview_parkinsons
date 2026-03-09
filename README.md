# UKBB Literature Review (Parkinson's / Dementia)

This repository is prepared for paper review around one cleaned notebook:

- `UKBB_litreview_clean.ipynb`

Legacy notebooks are kept for provenance:

- `Parkinsons/Exploring_UKBB_Bibliography.ipynb`
- `Parkinsons/AbstractDownload.ipynb`

## Environment setup

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

## Analysis pipeline (paper review)

### Step 1: Acquire abstracts from PubMed (default path)

1. Launch Jupyter:
   ```bash
   source .venv/bin/activate
   jupyter lab
   ```
2. Open `Parkinsons/AbstractDownload.ipynb`.
3. Set `NCBI_API_KEY` (optional but recommended).
4. Run all cells to generate:
   - `pubmed_abstract_updated.csv` (repo root)
   - live download progress bars (year ranges + records fetched)

Output fields are `Journal`, `Title`, `Abstract`, `Authors`, `Year`, `PMID`, `DOI`, `query_year_range`.

### Step 2: Run cleaned analysis notebook

1. Open `UKBB_litreview_clean.ipynb`.
2. Keep `RUN_PUBMED_DOWNLOAD=True` (default) so the notebook runs the same PubMed acquisition logic internally.
3. Keep `EXACT_REPRO_MODE=True` for legacy-compatible 12-cluster behavior.
4. Run all cells top-to-bottom.

### Use previously downloaded abstracts (no new download)

If you want to reuse the existing dataset already in this repo:

- `pubmed_abstract_updated.csv` (not a current default, but updates the original `pubmed_abstracts.csv` with more recent publications)
- `pubmed_abstracts.csv` (legacy snapshot kept for paper-submission reference)

Set this flag in `UKBB_litreview_clean.ipynb`:

- `RUN_PUBMED_DOWNLOAD = False`

With `EXACT_REPRO_MODE=True`, the notebook will load `pubmed_abstract_updated.csv` first (then legacy `pubmed_abstracts.csv` as fallback) instead of calling PubMed again.

### Snapshot vs updated abstracts

- Figure-submission snapshot (legacy): `pubmed_abstracts.csv`
- Updated refresh file: `pubmed_abstract_updated.csv`

If you want to reproduce the paper-submission snapshot, keep and use `pubmed_abstracts.csv`.

If you want a refreshed dataset with more recent publications, generate `pubmed_abstract_updated.csv` by either:

1. Running `Parkinsons/AbstractDownload.ipynb`
2. Running `UKBB_litreview_clean.ipynb` with `RUN_PUBMED_DOWNLOAD=True`

### Step 3: Review generated artifacts

The cleaned notebook writes:

- `pubmed_abstract_updated.csv`
- `data/interim/dementia_related_papers.csv`
- `data/processed/dementia_related_papers_cleaned.csv`
- `data/processed/dementia_related_papers_clustered.csv`
- `outputs/figures/combined_wordclouds.png`
- `outputs/figures/tsne_visualization.png`
- `outputs/figures/stacked_bar_chart.png`
- `outputs/figures/full_figure.png`

## How the PubMed acquisition logic works

`AbstractDownload.ipynb` is a PubMed ingestion notebook. It does not enrich an existing CSV by fuzzy title matching.

1. Query strategy:
   - Uses PubMed E-utilities query:
     - `(UK Biobank[Title/Abstract]) OR (UK Biobank[Affiliation])`
   - Runs this query in publication-year blocks.
2. Retrieval strategy:
   - Calls `esearch` with `usehistory=y` to obtain `WebEnv` and `QueryKey`.
   - Calls `efetch` with batching (`retmax`) to download XML records.
3. Parsing strategy:
   - Extracts `Journal`, `Title`, `Abstract`, `Authors`, `Year`, `PMID`, `DOI`.
   - Handles multi-section abstracts and variable year fields (e.g., `MedlineDate`).
4. Consolidation strategy:
   - Concatenates all year-block results.
   - Optionally deduplicates by `PMID`.
5. Output contract:
   - Writes canonical source file: `pubmed_abstract_updated.csv`.

This same logic now runs by default in `UKBB_litreview_clean.ipynb` (`RUN_PUBMED_DOWNLOAD=True`) so acquisition and analysis are aligned.

## What happens inside the cleaned notebook

1. Acquire/load publication abstracts from PubMed.
2. Filter records using dementia-related keywords in `Title`, `Abstract`, or `Journal`.
3. Clean abstract text (HTML stripping + section-word removal + abbreviation normalization).
4. Build TF-IDF features (`max_features=1000`).
5. Cluster papers (`KMeans`, 12 clusters for the final figure).
6. Compute t-SNE embedding for visualization.
7. Create per-cluster word clouds, yearly stacked cluster counts, and a combined A/B/C figure panel.

## Optional legacy path (Crossref)

The cleaned notebook still contains optional steps for:

- UK Biobank Showcase scrape (`RUN_SCRAPE=True`)
- Crossref DOI/abstract lookup (`RUN_CROSSREF=True`)

What each option does:

1. `RUN_SCRAPE=True`
- Scrapes the UK Biobank Showcase publication table for `START_YEAR` to `END_YEAR`.
- Extracts bibliographic fields (for example title, journal, authors, year/source_year, publication_id).
- Writes `data/raw/ukbb_publications_by_year.csv`.
- This step does not fetch abstracts.

2. `RUN_CROSSREF=True`
- Requires `CROSSREF_EMAIL` (used in the API user-agent).
- Tries to fill missing DOI/abstract values by querying Crossref (`works` endpoint) using title and journal.
- Writes `data/raw/ukbb_all_publications_with_doi_abstracts.csv`.

3. Combined behavior
- If `RUN_SCRAPE=True` and `RUN_CROSSREF=True`: Crossref enrichment is applied to scraped UKBB records.
- If `RUN_SCRAPE=False` and `RUN_CROSSREF=True`: Crossref enrichment is applied to the current input dataset (`INPUT_WITH_ABSTRACTS`, which is typically `pubmed_abstract_updated.csv` in the default setup).

These are disabled by default and are not the preferred path for the PubMed-based figure workflow.
