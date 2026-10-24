# Store Performance Dashboard

A BI-style dashboard summarizing book sales, customers, authors, and daily
revenue across five stores.

## Live dashboard

**[Open Store Performance Dashboard](https://store-performance-dashboard-six.vercel.app/)**

The dashboard is also available as [`index.html`](./index.html) in
this repository. It is a self-contained static page and does not require a
backend.

## Dashboard views

- **Overview:** sales KPIs, daily revenue chart, and the five highest-revenue
  days.
- **Customers & Authors:** reconciled unique-user count, unique author-set
  count, most popular author set, and best buyer with associated user IDs.
- **Revenue:** daily revenue chart and top-five-day breakdown.
- **Ask the data:** a local, rule-based helper for questions about the
  displayed metrics; it does not send data to an AI service.

Use the store selector to view STORE1 through STORE5 separately. Revenue is
reported in USD.

## Metric and data-cleaning notes

- Order timestamps use mixed source formats. Ambiguous slash-formatted dates
  are interpreted month-first.
- Prices without a currency marker are treated as USD. EUR prices use the
  conversion rate **€1 = $1.20** and are rounded to the nearest cent.
- Customer profiles are reconciled when three of the four normalized
  identity fields (name, address, phone, and email) match, following the
  assumption that at most one field changes.
- An author set is a distinct combination of co-authors. Popularity is based
  on the quantity of books sold for that author set.
- The best buyer's displayed IDs are the source user IDs linked to that
  reconciled customer.

## Results

| Store | Unique users | Unique author sets | Most popular author set | Books sold by set | Best buyer IDs |
|---|---:|---:|---|---:|---|
| STORE1 | 3,221 | 324 | Darron Ernser & Miss Lorina Kertzmann | 173 | `[49954]` |
| STORE2 | 3,166 | 281 | Kenneth Hamill & Marcus McKenzie | 176 | `[49476, 49910]` |
| STORE3 | 2,955 | 307 | Loreta Metz | 155 | `[52998, 55135]` |
| STORE4 | 2,850 | 299 | Emil Rempel, Heriberto Kutch & Merle Rice | 198 | `[53949]` |
| STORE5 | 2,987 | 311 | Dulcie Bode | 169 | `[51827]` |

## Deployment

The page is hosted on Vercel and published at the live-dashboard link above.
Because the dashboard displays buyer IDs, confirm that sharing them publicly
is permitted. Do not publish the source archive or raw customer records.
