# Handover · Recipe for High-Grossing Movies

DTU course 02467 Computational Social Science · Spring 2026 · Project Assignment B.
Website: https://alfred-aqraou.github.io/CSS-final-project/

This file is the entry point for a new collaborator (or a fresh Claude session) picking up the project cold. Read this first, then open `Analysis-3.ipynb`.

---

## What this project is

A dual-track analysis of **3,781 US theatrical films released between 2000 and 2026**. We combine TMDb metadata with Wikipedia-scraped plot summaries and critical-reception sections. The deliverables are:

- a **self-contained website** (`index-3.html`, ~9.9 MB, opens directly in a browser, no internet required) telling the story to a non-technical reader;
- an **explainer notebook** (`Analysis-3.ipynb`) covering the end-to-end pipeline, structured per the four sections required by Project Assignment B (Motivation / Basic stats / Tools-theory-analysis / Discussion).

The headline methodological choice: every analysis is run under two tracks.

- **Track A** = absolute inflation-adjusted profit (`box_office_surplus_adj_2026`).
- **Track B** = each film's rank within its release year (time-invariant by construction).

Patterns that show up under one track but not the other are flagged as inflation- or recency-driven rather than real.

---

## Files in this folder

### Required for the deliverable

| File | Purpose |
| --- | --- |
| `Analysis-3.ipynb` | Explainer notebook · 56 cells · 4-section structure · runs end-to-end, regenerates `index-3.html` from the template. |
| `index-3.html` | The shipped website. Self-contained (d3 v7 inlined, no CDN). |
| `index-3_v4_template.html` | Template the notebook fills in. Contains all markup, CSS, JS, and a placeholder `/*__SITE_DATA_OBJ__*/null` that the final cell of the notebook replaces with computed data. |
| `top_10k_movies_2000_min_1m_adj_filtered.csv` | Primary data — TMDb extraction + CPI inflation adjustment. 3,781 rows. |
| `movies_full_enriched.csv` | Wikipedia-enriched data joined on `tmdb_id`. Adds `wiki_plot` and `wiki_critical_response`. |
| `data_extraction 16.20.25.ipynb` | Original TMDb extraction notebook. Referenced from §2.1 of the explainer; kept for provenance. |
| `wiki.ipynb` | Original Wikipedia scraping notebook. Referenced from §2.2 of the explainer; kept for provenance. |
| `fig_*.png` (5 files) | Figure exports from the notebook (degree distribution, Zipf, null distributions, track comparison). Useful when the notebook is viewed on nbviewer. |

### Safe to delete manually (the sandbox cannot delete; do it in Finder)

| File | Why it can go |
| --- | --- |
| `.DS_Store` | macOS clutter; not part of the project. |
| `test2.txt` | Empty leftover from a sandbox write-test. |
| `Analysis-3.backup-20260517-1752.ipynb` | Snapshot taken before the May 17 4-section rewrite; superseded by the current `Analysis-3.ipynb`. |
| `Analysis-3.executed.ipynb` | `nbconvert --execute` intermediate. Regenerated automatically next time you run the notebook. |
| `index-3.backup-20260517-1752.html` | Pre-rewrite snapshot of the site. |
| `index-3_v4_template.backup-20260517-1752.html` | Pre-rewrite snapshot of the template. |
| `claude_handoff_v4.md` | This file replaces it. |

Optionally, the parallel folder at `/Users/<you>/.../Final project/` (the one outside the `02467 Computational social science` parent) is the older copy from a previous session and is now stale. Either un-share it from Cowork or delete its contents — the canonical project lives inside `02467 Computational social science/Final project/`.

---

## How to rebuild the site from scratch

```
cd "02467 Computational social science/Final project/"
jupyter nbconvert --to notebook --execute Analysis-3.ipynb \
    --output Analysis-3.executed.ipynb \
    --ExecutePreprocessor.timeout=1800
```

That writes `index-3.html`. Runtime ≈ 3–4 min, dominated by the 100 configuration-model nulls in §3.2.

The extraction cells in §2.1–2.3 (TMDb + Wikipedia + inflation) are **gated by `RUN_API_EXTRACTION = False`** and disabled by default. Set the flag to `True` only if you want to re-scrape from scratch (≈ 6 hours on a home connection); otherwise the cached CSVs in this folder are used.

A short smoke-test script `/sessions/zen-tender-volta/smoke_test_v2.py` exercises every tab on both tracks and reports any JS errors.

---

## Notebook structure (Project Assignment B compliance)

`Analysis-3.ipynb` is organised exactly as the rubric requires:

- **§1 Motivation** — what the dataset is, why we chose it, what the end user should learn.
- **§2 Basic stats** —
  - §2.1 TMDb data acquisition (function bodies from `data_extraction 16.20.25.ipynb`, gated by `RUN_API_EXTRACTION`)
  - §2.2 Wikipedia enrichment (function bodies from `wiki.ipynb`, same gate)
  - §2.3 Filter pipeline + CPI inflation adjustment
  - §2.4 Loading the final dataset · descriptive table
  - §2.5 Collaboration-network construction (edge weight ≥ 2; 2,520 nodes / 7,203 edges)
  - §2.6 Log-log degree distribution (week-5 convention)
  - §2.7 Text descriptive stats + Zipf rank-frequency plot
- **§3 Tools, theory & analysis** —
  - §3.1 Community detection (weighted greedy modularity, 145 communities, modularity 0.7052)
  - §3.2 Topology null-model test (100 configuration + 100 ER nulls; both produce ~0 modularity using the observed partition)
  - §3.3 Newman degree assortativity (bidirectional formula — Assignment-2 fix), z ≈ 9 against the configuration null
  - §3.4 Newman attribute assortativity by track (r=+0.31 Track A, +0.39 Track B; both p ≈ 0.005 against 200-shuffle null)
  - §3.5 Per-community plot-text TF-IDF prep
  - §3.6 Corpus-wide TF-IDF with bigrams (`ngram_range=(1,2)`)
  - §3.7 Group-vs-rest TF-IDF with permutation p-values; per-community vocabulary
  - §3.8 VADER sentiment, audience + critic
  - §3.9 Time-fairness series (raw / adjusted / per-year top-10 % counts)
  - §3.10 Spearman correlations, R², genre rankings, budget-quintile breakdown (reproduces every number on the Attributes and Recipe tabs)
  - §3.11 Network export with per-year breakdowns for the canvas
  - §3.12 SITE_DATA assembly + writes `index-3.html`
- **§4 Discussion** — what went well, what's missing, ethics & limitations.

Every number on the website is now produced by code in this notebook. The site loads `SITE_DATA` from the final cell's JSON injection.

---

## Website structure

Eight tabs (top nav, hash-routed):

1. **Intro** — kicker + Q + an "About the dataset" stat block (films, years, network n/m, communities, modularity, Wiki coverage, sources)
2. **Methodology** — the two-track argument + inflation-fairness chart with three series (raw, adjusted, per-year)
3. **Attributes** — Spearman correlation bars, genre ranking, budget U-curve (Track A) / monotonic ramp (Track B); R²/ρ stats now live-computed from SITE_DATA
4. **Network** — interactive canvas (2,520 nodes / 7,203 edges); controls for node-size mode, edge-weight, community filter, year picker, person search; **null-card** with two strips (topology vs random nulls + track-aware attribute assortativity)
5. **Language** — Audience vs Critics TF-IDF, dual sentiment (amber + cyan), Communities×Language strip, Plot themes
6. **Recipe** — synthesis ingredients + caveats
7. **Discussion** — what went well / could improve / ethics & limitations (three cards)
8. **Notebook** — big nbviewer-link button + dataset/notebook download buttons + section guide

Tracks A and B can be toggled top-right at any time; the entire page re-renders.

---

## Methodological fixes made in response to Assignment 2 feedback

All five issues the teacher flagged on Assignment 2 are explicitly fixed:

| Feedback | Fix |
| --- | --- |
| Newman undirected degree assortativity must count each edge in both directions so `mean(k_u) = mean(k_v)`. | `newman_degree_assortativity()` in §3.3 pushes `(k_u, k_v)` *and* `(k_v, k_u)` into both lists. Our value matches `nx.degree_assortativity_coefficient` to 4 decimals. |
| Null distribution plots must use the *same* metric on observed and null. | §3.2 uses `nx_modularity(H_null, COMMS)` — modularity of the *observed partition* under the rewired graph. §3.3 plots degree assortativity against `null_cfg_dassort`, not against a different statistic. |
| Unknown / NaN attribute nodes must be excluded from attribute assortativity. | `newman_attr_assortativity()` filters `np.isnan` before any edge is counted. |
| IDF over only 9 community-aggregate documents made IDF useless. | §3.6 fits `TfidfVectorizer` on the **full 3,781-film corpus**. Per-community TF-IDF (§3.7) reuses that matrix. |
| No collocations in the TF-IDF. | `ngram_range=(1, 2)` on the vectorizer; ~2,400 bigrams enter the vocabulary. (Caveat: in the *top-12 displayed* lists, character names still dominate plot-text — flagged in §4 as a follow-up for an explicit named-entity strip.) |

---

## Known caveats and follow-ups

- **Notebook nbviewer URL is a placeholder.** `NOTEBOOK_NBVIEWER_URL` in §2.4 and the placeholder string in §1 (header) both point to `https://nbviewer.org/github/Vaksth/comsocsci2026_final_project/blob/main/Analysis-3.ipynb`. Swap with the real URL once the repo is public.
- **Contribution statement** in cell 1 is a template — fill in real DTU student IDs before submission. Assignments 1 & 2 required this and dropped formatting points for missing it; the rubric for B doesn't strictly demand it, but no reason to risk losing marks.
- **2026 is a partial year.** Extraction happened in May 2026, so 2026's per-year top-10% counts (4 films in `whole_raw`/`whole_adjusted`, 4 in `per_year`) reflect five months of releases. The Intro stat block flags this; the Methodology chart's right-edge dip is real and explained.
- **Plot-vocabulary character-name dominance.** §4 acknowledges this. An explicit named-entity strip would lift the bigram fraction in the displayed top-12.
- **Inflation index choice.** CPI from FRED `CPIAUCSL`. A ticket-price index would shift the Track-A top-10% membership at the margins; Track B (rank-based) is robust to this.

---

## Where things live for a future Claude session

- The build script for the notebook (so you don't have to read the JSON) is at `/sessions/zen-tender-volta/build_notebook.py` outside the mount.
- The headless smoke test is at `/sessions/zen-tender-volta/smoke_test_v2.py`.
- Backups timestamped `*.backup-20260517-1752.*` exist for the notebook, template, and rendered HTML — listed as "safe to delete" above, but they're cheap rollback points if anything regresses.

If you're a fresh Claude session: read this file, then `Analysis-3.ipynb` cell by cell, then open `index-3.html` in a browser. Everything else flows from there.
