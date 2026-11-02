# Entity matching — direct / semi-direct entries

Put IDs in `cases.txt` (one per line), then run one of the scripts below from the project root.

## Scripts

| Script | What it does | Output |
|---|---|---|
| `main_exact_match.py` | **New.** Exact match on normalised name + compatible legal form, against the population in `queries/fetch_population.sql`. No Links/BO_links. All propositions kept. | `output/matches.xlsx` (Matched, NoMatch) |
| `main_for_crossRef.py` | Quick. Siblings via Links/BO_links with same % + same name in CrossRef. For countries already handled (Belgium, France, ...). | `output/matches.xlsx`, `output/output.txt` |
| `main_for_crossRef_2.py` | Same as above, fuzzy name check (RapidFuzz ratio >= 96) + drop-reason debug. | same |
| `main_little_countries.py` | Slow. Classic matching (exact key + link propagation + filters) for countries not handled. | same |

## main_exact_match.py rules
- Both names normalised with `src/code/name_normalisation.py` (legal forms from `utils/legal_form_mapping.csv`, stopwords from `utils/config.py`, country hook = first 2 chars of the Id). Compact keys must be 100% equal.
- Legal form (normalised code from the CSV): same → ok; one or both sides without legal form → ok; both present and different → rejected.
- Country: same 2-char prefix, matched `YY`, or allowed pair (`ALLOWED_COUNTRY_PAIRS`, default CN↔HK). Original `YY` → any country.
- OriginalId ≠ MatchedId.
- Edit `queries/fetch_population.sql` to choose the population. Keep `{batch_size}`, `{last_id}` and `ORDER BY Id` for paging.

## Layout
```
.
├── cases.txt
├── main_exact_match.py
├── main_for_crossRef.py
├── main_for_crossRef_2.py
├── main_little_countries.py
├── run_finalize.py               # writes output/manifest.json
├── requirements.txt
├── queries/                      # all SQL
├── src/
│   ├── code/                     # normalisation, matching, filters, cases loader
│   └── db_connexion/             # pyodbc connection (Windows auth)
└── utils/                        # config + legal form / country CSVs
```

## Setup
```
pip install -r requirements.txt
```
Needs Microsoft ODBC Driver 17 or 18 for SQL Server. Server can be overridden with env var `APP_SQL_SERVER`.
