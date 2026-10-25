# Store Performance Dashboard

A BI-style dashboard summarizing book sales, customers, authors, and daily
revenue across five stores.

## Live dashboard

**[Open Store Performance Dashboard](https://store-performance-dashboard-six.vercel.app/)**

The dashboard is also available as [`index.html`](./index.html) in
this repository. Its AI assistant uses the serverless endpoint in
[`api/chat.js`](./api/chat.js); the rest of the dashboard remains a static page.

## Dashboard views

- **Overview:** sales KPIs, daily revenue line chart, and a bar chart comparing
  the five highest-revenue days.
- **Store comparison:** side-by-side revenue and customer comparisons across
  all five stores, data-driven highlights, and a daily revenue calendar heatmap.
- **Customers & Authors:** reconciled unique-user count, unique author-set
  count, most popular author set, and best buyer with associated user IDs.
- **Revenue:** daily revenue line chart and top-five-day comparison bar chart.
- **AI data assistant:** answers natural-language questions about the selected
  store's aggregate revenue, daily trends, user counts, and author metrics.
  Buyer IDs and customer profile records are excluded from its data context.

Use the store selector to view STORE1 through STORE5 separately. Revenue is
reported in USD.

The visual style uses a warm, book-inspired palette, with forest green and
amber accents.

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

### Configure AI chat on Vercel

The AI assistant calls OpenAI from the serverless endpoint; the API key is
never placed in browser code.

1. Create an OpenAI API key in your OpenAI developer account. API usage may be
   billed separately from a ChatGPT subscription.
2. In Vercel, open **Settings → Environment Variables** and add
   `OPENAI_API_KEY` with that key. Do not commit the key to GitHub or paste it
   into `index.html.html`.
3. Optionally set `OPENAI_MODEL` to a model available in your API account.
   The default is `gpt-4o-mini`.
4. Redeploy the project. The deployment must include both `index.html.html`
   and `api/chat.js`.

The assistant sends the submitted question, recent conversation, and selected
store's aggregate metrics (sales totals, daily revenues, popular author names,
and user counts) to OpenAI. Buyer IDs, names, profile records, and best-buyer
spending are not included in the dashboard context; question text and chat
history are sent as typed, so do not enter personal or confidential
information. Requests are limited to 12 per minute per serverless instance;
this is a basic abuse guard, not a global quota.

Run the serverless endpoint tests with Node.js 18 or newer:

```powershell
node --test test_chat_api.js
```
