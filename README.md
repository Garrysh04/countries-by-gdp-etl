# Countries by GDP — ETL Pipeline

A small but complete extract–transform–load pipeline: scrape a table off the web, clean it, and land it in two places a downstream consumer can actually use.

## Why this one is here

Most portfolio projects are models. This one is the other half of the job — the part that runs on a schedule, has to survive a source that changes shape, and writes somewhere a BI tool can read. It is deliberately unglamorous.

## The pipeline

| Stage | What happens |
|---|---|
| `extract` | Requests the Wikipedia list of countries by nominal GDP (pinned to a Web Archive snapshot for reproducibility) and parses the table with BeautifulSoup, skipping rows with no country link or a missing figure. |
| `transform` | Strips thousands separators, casts to float, converts units, and renames the column to match. |
| `load_to_csv` | Writes `Countries_by_GDP.csv`. |
| `load_to_db` | Writes the `Countries_by_GDP` table into a `World_Economies.db` SQLite database. |
| `log_progress` | Timestamps each stage to `etl_project_log.txt`, so a failed run tells you where it failed. |

Pinning the source to an archived snapshot rather than the live page is intentional: a live Wikipedia table changes structure without warning, and a pipeline that silently reads the wrong column is worse than one that fails.

## Output

The run in the committed notebook produced 191 countries, queried back out of SQLite in two bands:

- 69 economies at or above $100bn nominal GDP, led by the United States at $26,854bn
- 122 economies below it, down to Tuvalu at $0.06bn

## Files

- `countries_by_gdp_etl.ipynb` — the pipeline, GDP in USD billions
- `gdp_in_gold_equivalent.ipynb` — the same pipeline, converting each figure to a gold-equivalent weight

## Stack

Python, BeautifulSoup, requests, pandas, NumPy, SQLite.
