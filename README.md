# Replication package

**Paper:** *Uncertainty-aware credit decisions in peer-to-peer lending: conformal prediction under temporal drift, label delay and human review*
**Authors:** Saanvi Goel, Syamasudha Veeragandham (corresponding author: syamasudha.v@vit.ac.in)
**Archive DOI:** [to be added after deposit]

All analyses use **real loan data**; nothing is simulated. The package contains the analysis code, the Lending Club data extracts we used, the result tables, figures and executed notebooks, and SHA-256 checksums of every file. Three inputs are **not** included; section 2 says why and where to get them.

## 1. Contents

```
README.md, LICENSE, LICENSE_results.txt, requirements.txt, CHECKSUMS.txt
code/                       the notebooks (stored without outputs) and make_paper_figures.py
workspace/                  working folder ("Q2") the notebooks read from; contains the Lending Club extracts
results/
  lending_club_main/        tables + figures from Conformal_Prediction_Credit_Risk_RUN.ipynb
  feature_sets/             tables + figures from Feature_Set_Analysis.ipynb
  prosper/                  tables from Prosper_Replication.ipynb
  extensions/               tables + figures from Extensions.ipynb (segments, monitoring, calibration age)
  audit/                    tables from Audit_and_Robustness.ipynb
  executed_notebooks/       executed copies (outputs printed in the notebooks, incl. Mega_Analysis and Final_Analysis)
paper_figures/              Figure 1-7 as submitted (png + pdf)
```

## 2. Not included, and why

| item | why | how to obtain |
|---|---|---|
| Raw Lending Club file `accepted_2007_to_2018Q4` (about 1.6 GB) | size | Kaggle dataset "All Lending Club loan data" (https://www.kaggle.com/datasets/wordsforthewise/lending-club; listed as CC0 on its page). SHA-256 of the csv is in `workspace/extra_data/raw_manifest.json`. Put it in `workspace/archive/`. |
| `workspace/extra_data/payments.csv`, `label_dates.csv` | derived from the raw file | created automatically by `Mega_Analysis.ipynb` / `Final_Analysis.ipynb` on their first run when the raw file is in `workspace/archive/` |
| Prosper `prosperLoanData.csv` | no licence or terms issued by Prosper for this snapshot were found, so we do not redistribute it | see `workspace/extra_data/prosper/README.txt` (source links and SHA-256 `fce13408ac22e8201363e71e1a16d9795305f4aaf8c0f67efcd80424bc5b9e02`); the notebooks download it from a public GitHub mirror if it is absent |

The original synthetic-data draft of the main notebook is deliberately not included.

## 3. Data provenance and terms

* **Lending Club extracts** (`workspace/extra_data/lending_club_36m_wide.csv`, `lending_club_60m_wide.csv`, `workspace/lending_club_real_36m.csv`) are derived from the Kaggle file above: resolved loans only (fully paid = 0; charged off/default = 1); 36-month loans issued through Dec 2015 and 60-month loans issued through Dec 2013, so that every loan had its full term to resolve before the 2018-Q4 extract. SHA-256 values of the extracts are in `raw_manifest.json` and `CHECKSUMS.txt`. The extracts contain loan-level public data only. They are included so that the results can be reproduced without the 1.6 GB raw file; they remain subject to the terms of the original source (the Kaggle dataset page lists CC0; the underlying data are published by Lending Club). If the rights holder objects to redistribution, write to the corresponding author and the files will be removed.
* **Prosper** data: publisher Prosper Marketplace, Inc.; snapshot of loans originated Nov 2005 to Mar 2014 (113,937 records, 81 variables). Source links, the checksum and how to verify your copy are in `workspace/extra_data/prosper/README.txt`.
* **Licences:** code (`code/`) is MIT (`LICENSE`); results, tables, figures and documentation are CC BY 4.0 (`LICENSE_results.txt`). The Lending Club extracts keep the terms of their source (see above).

## 4. How to run

1. Install the packages in `requirements.txt` (Python 3.10 or newer).
2. Tell the notebooks where the workspace is, then start Jupyter from the same shell:
   `export Q2_DIR=/full/path/to/Replication_Package/workspace`   (or copy `workspace` to your Desktop and rename it `Q2`).
3. Run the notebooks in `code/` with **Run All**. The newer notebooks (Mega, Final, Prosper, Extensions, Audit) isolate their sections, print a list of failed sections at the end and zip their outputs; if an input file is missing they say which one.

| order | notebook | needs | time | produces |
|---|---|---|---|---|
| 0 | `Extract_Extra_Data.ipynb` (optional) | raw Kaggle file in `workspace/archive/` | minutes | the wide Lending Club extracts (already included) |
| 1 | `Conformal_Prediction_Credit_Risk_RUN.ipynb` | `workspace/lending_club_real_36m.csv` (or the raw file) | about 10 min | main per-month-sample experiments, DtACI / recency-weighted / label-shift baselines |
| 2 | `Mega_Analysis.ipynb` | wide file; raw file for `payments.csv` | about 2-5 min | model zoo, label delay, decisions on realised profit, robustness, 60-month replication |
| 3 | `Final_Analysis.ipynb` | wide file; `payments.csv` and `label_dates.csv` (from the raw file) | about 2-5 min | profit frontier, expected-return model, reviewer break-even, realistic label arrival, deployment-realistic training |
| 4 | `Prosper_Replication.ipynb` | Prosper file (downloaded if absent) | about 2 min | second-lender replication |
| 5 | `Extensions.ipynb` | wide file, Prosper file | about 3 min | segment coverage, monitoring, calibration age (Section 4.7) |
| 6 | `Audit_and_Robustness.ipynb` | wide file, Prosper file (`payments.csv` optional) | about 3 min | data audit, independent recomputation, robustness |
| 7 | `Feature_Set_Analysis.ipynb` | wide file | about 3 min | feature-set comparison, grade mix |
| 8 | `python code/make_paper_figures.py` | `results/prosper/tables` | seconds | Figures 1-5 |

`Final_Analysis.ipynb` stops if `payments.csv` is missing. Without the raw file, run steps 4-7 only (they do not need payments).

## 5. Where each part of the paper comes from

| paper | source |
|---|---|
| Table 1, grade mix (Section 3.1), Section 4.4 expected-return and frontier results | `Final_Analysis` (`F1`, `A1`, `A2`, `B1`-`B3`), `Feature_Set_Analysis` (`F5_grade_mix_by_year`) |
| Tables 2-3 (model zoo, calibration year), Table 4 (alpha), uniform delays in Table 5, Table 8 (decisions) | `Mega_Analysis` (`M1`, `M2`, `M3`, `M4a`-`M4c`) |
| Realistic arrival in Table 5, Table 7 (label selection), reviewer break-even (Table 9), labels known at deployment (Table 13) | `Final_Analysis` (`D1`-`D3`, `C1`, `E1`) |
| Table 6 (DtACI, recency-weighted, label-shift weighted) | `Conformal_Prediction_Credit_Risk_RUN` (`19a_dtaci`, `19b_barber_weighted`, `15c_label_shift_conformal`); per-month sample |
| Section 3.2, 4.5, Tables 10-13, Prosper columns of Tables 4, 7, 9 | `Prosper_Replication` (`P1`-`P13`) |
| Section 4.7, Tables 14-15, Figures 6-7 | `Extensions` (`E1`-`E3`) |
| Robustness paragraph in Section 4.1 and in 4.5, independent recomputation | `Audit_and_Robustness` (`B`, `C1`-`C4`) |
| Figures 1-5 | `code/make_paper_figures.py` |

## 6. Reproducibility status

* The notebooks were executed in two environments. `Prosper_Replication` and `Extensions` were run in both and agree exactly, except for the logistic-regression rows of the Prosper model-zoo table (differences in the 4th decimal; not used in the paper).
* In `results/executed_notebooks/`, the copies of `Mega_Analysis`, `Final_Analysis` and `Extensions` come from the first environment (the authors' machine). The copies of `Conformal_Prediction_Credit_Risk_RUN`, `Feature_Set_Analysis` and `Audit_and_Robustness` were run in the second environment (Python 3.12) on the same data files. `Audit_and_Robustness` was run only in the second environment; it finds the files and runs without failures when pointed at this package via `Q2_DIR`.
* The Lending Club tables of `Mega_Analysis` and `Final_Analysis` exist here only as printed output in the executed notebooks; their CSVs are regenerated by running them.
* The Lending Club winsorised/trimmed-profit check in `Audit_and_Robustness` needs `payments.csv` and was **not** run for Lending Club (stated in the paper's Limitations). The executed audit notebook therefore skips it.
* An independently written implementation of split conformal prediction (p-value formulation) reproduces 12 headline numbers (`results/audit/tables/B_manuscript_checks.csv`).
* Random elements: fixed seeds throughout (42); a fixed-seed sample of at most 2,500 loans per issue month is used only by the row-by-row online methods; intervals use resampling of real loans.
* Method details and assumptions (profit definition, approximate charge-off lag for Lending Club, exact label dates for Prosper) are in the paper's Sections 3 and 5.

## 7. Citation

Goel S, Veeragandham S (2026) Replication package for "Uncertainty-aware credit decisions in peer-to-peer lending: conformal prediction under temporal drift, label delay and human review". [Repository], [DOI to be added].
