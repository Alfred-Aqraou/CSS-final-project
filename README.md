# Recipe for High-Grossing Movies

DTU 02467 Computational Social Science · Spring 2026 · Project Assignment B · Group 16

**Website:** https://alfred-aqraou.github.io/CSS-final-project/
**Notebook:** https://nbviewer.org/github/Alfred-Aqraou/CSS-final-project/blob/main/Analysis.ipynb

---

## What this is

A dual-track analysis of 3,781 US theatrical films (2000–2026) combining TMDb metadata with Wikipedia plot summaries and critical-reception text. Every finding is tested under two success definitions:

- **Track A** — inflation-adjusted profit (CPI-deflated to 2026 dollars)
- **Track B** — per-year percentile rank (time-invariant by construction)

Signals that hold under both tracks are treated as robust; signals present only under Track A are flagged as inflation- or recency-driven.

---

## Files

| File | Description |
|------|-------------|
| `Analysis.ipynb` | Explainer notebook. Runs end-to-end and regenerates `index.html`. |
| `index_template.html` | HTML/CSS/JS template. The notebook injects `SITE_DATA` JSON at `/*__SITE_DATA_OBJ__*/null`. |
| `index.html` | Generated website. Do not edit directly — regenerate via the notebook. |
| `top_10k_movies_2000_min_1m_adj_filtered.csv` | Primary dataset: 3,781 films, TMDb + CPI adjustment. |
| `movies_full_enriched.csv.zip` | Same films + Wikipedia plot and critical-reception text. |
| `Data_Extraction.ipynb` | Original TMDb extraction pipeline (provenance only). |
| `wiki.ipynb` | Original Wikipedia scraping pipeline (provenance only). |
| `wc_plot_*.png` | Word-cloud exports (four variants: Track A/B × high/low performers) for nbviewer rendering. |

---

## Rebuilding the site

```bash
pip install -r requirements.txt
python -m spacy download en_core_web_sm

jupyter nbconvert --to notebook --execute --inplace \
    --ExecutePreprocessor.timeout=600 Analysis.ipynb
```

Runtime ≈ 3 minutes, dominated by 100 null-model iterations in §3.2.

The TMDb and Wikipedia extraction cells (§2.1–2.2) are disabled by default (`RUN_API_EXTRACTION = False`). Set to `True` only to re-scrape from scratch (~6 hours).

---

## Notebook structure

Organised per Project Assignment B requirements:

- **§1 Motivation** — dataset description, research questions, user experience goals
- **§2 Basic stats** — acquisition, cleaning, inflation adjustment, network and text descriptive statistics
- **§3 Tools, theory & analysis** — community detection, centrality (degree/betweenness/closeness), null-model permutation tests, degree and attribute assortativity, per-community plot vocabulary (network × text), corpus-wide TF-IDF with bigrams and chi-squared collocations, group-vs-rest TF-IDF, VADER sentiment (audience and critic), time-fairness series, Spearman correlations, R² regressions, genre ranking, budget-tier breakdown
- **§4 Discussion** — what worked, limitations, ethical caveats
