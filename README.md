"# Intern-Data-Engineer-Task-4" 
# Store Performance Dashboard

A pandas-based analysis of five book stores, with a standalone BI-style
dashboard for revenue, customers, and author performance. Each store is
analyzed separately, and the dashboard lets you switch between stores.

## Dashboard

After running the analysis, open [`output/dashboard.html`](./output/dashboard.html)
in a browser. The dashboard includes:

- **Overview:** revenue, books sold, order count, reconciled users, daily
  revenue chart, and top five revenue days.
- **Customers & Authors:** unique user and author-set counts, the best-selling
  author set, and the top buyer's spending and associated user IDs.
- **Revenue:** daily revenue chart and top five revenue days.
- **Ask the data:** a local, rule-based chat helper for questions about the
  displayed metrics. It does not call an AI service.

Generated per-store summaries and chart images are saved in `output/`.

## Data

The analysis expects a ZIP archive containing these files for each store:

```text
STORE1/users.csv
STORE1/orders.parquet
STORE1/books.yaml
...
STORE5/users.csv
STORE5/orders.parquet
STORE5/books.yaml
```

The source data archive is not included in this repository. Keep raw customer
data out of public repositories. The generated dashboard includes top-buyer
user IDs, so review sharing permissions before publishing it.

## Run locally

Requires Python 3.10 or newer.

```powershell
python -m pip install -r requirements.txt
python analyze.py --archive C:\path\to\data.zip --output-dir output
```

Then open `output\dashboard.html` in your browser. To run the unit tests:

```powershell
python -m unittest -v
```

## Cleaning and metric definitions

- IDs and quantities are converted to numeric types. Orders with missing or
  malformed required values, non-positive or non-integral quantities, invalid
  prices, or invalid timestamps are excluded. Duplicate user and order IDs
  keep their first row; blank optional user fields remain missing.
- Mixed timestamp formats are parsed. Ambiguous slash-formatted dates use
  month-first interpretation. If a timestamp includes a timezone, it is
  converted to UTC before deriving the date.
- Prices are calculated in integer cents to avoid floating-point rounding
  issues. USD is assumed when the currency is not marked. EUR is converted at
  **€1 = $1.20** and rounded to the nearest cent (half up). A cent sign
  between digits is treated as a decimal separator when normalizing malformed
  price text.
- `paid_price` is quantity multiplied by the converted unit price.
- User profiles are reconciled within each store when three of the four
  normalized fields—name, address, phone, and email—match. This follows the
  task's assumption that at most one field changes. It does not use fuzzy
  matching.
- An author set is a distinct combination of co-authors in the book catalog.
  Popularity is measured by the total quantity sold for each author set.
- Daily revenue is the sum of `paid_price` grouped by the date extracted from
  the order timestamp. The best buyer is the reconciled customer with the
  greatest total spending; all linked source user IDs are reported.

## Results from the supplied data

| Store | Unique users | Unique author sets | Most popular author set | Books sold by set | Best buyer user IDs |
|---|---:|---:|---|---:|---|
| STORE1 | 3,221 | 324 | Darron Ernser & Miss Lorina Kertzmann | 173 | `[49954]` |
| STORE2 | 3,166 | 281 | Kenneth Hamill & Marcus McKenzie | 176 | `[49476, 49910]` |
| STORE3 | 2,955 | 307 | Loreta Metz | 155 | `[52998, 55135]` |
| STORE4 | 2,850 | 299 | Emil Rempel, Heriberto Kutch & Merle Rice | 198 | `[53949]` |
| STORE5 | 2,987 | 311 | Dulcie Bode | 169 | `[51827]` |


The dashboard is self-contained HTML with its chart images embedded, so it
does not require an API, server, or separate static assets at runtime.

