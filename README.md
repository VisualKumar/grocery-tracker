# Grocery Run Estimator

A mobile-friendly web app for planning grocery trips across Costco and Walmart, estimating the cost before I shop, and tracking what I actually spend over time.

**Live demo:** https://visualkumar.github.io/grocery-tracker/
Tap **Load sample trips** in Spending history to see the charts with 12 weeks of generated data.

## Why I built it

I shop at two stores with different pack sizes and prices, and I wanted to know what a trip would cost before I got to the register. The item list started from a real Costco receipt, where I decoded abbreviated line items (for example, `ORGNC BS THG` became organic boneless skinless chicken thighs, priced per pound).

The app is also the data-collection layer for a larger analysis project (see Roadmap).

## Features

- Tap items to build a list, with a running estimated total
- Per-pound pricing for meat, with an editable pack weight
- Per-store prices: switch an item between Costco, Walmart, or another store and it remembers the price at each
- Filter the list by store and see a total per store
- Log a trip with the date and the actual amount paid
- Spending history: monthly totals by store, spending by category, and average gap between estimated and actual cost
- CSV export at trip level and line-item level for analysis in Python

## Data model

Each logged trip is one record per store:

| Field | Description |
|---|---|
| `trip_id` | Unique ID |
| `date` | Shopping date (YYYY-MM-DD) |
| `store` | Costco, Walmart, or Other |
| `item_count` | Total units bought |
| `est_total` | Sum of estimated line totals, before tax |
| `actual_total` | Amount paid including tax (optional) |
| `is_sample` | `true` for generated demo trips, `false` for real ones |

Line-item export adds `item_id`, `item_name`, `category`, `qty`, `unit_price`, `price_per_lb`, `lbs`, `price_is_estimate`, `line_total`, and `is_sample`.

## Tech

- **Front end:** a single HTML file with vanilla JavaScript and CSS, no build step. Charts are inline SVG. Hosted on GitHub Pages.
- **Back end:** [Supabase](https://supabase.com) (Postgres) with magic-link email sign-in. Three tables: `user_state` (item list and prices as JSONB), `trips`, and `trip_lines`, linked by foreign keys with cascading deletes.
- **Security:** row level security on every table, so a signed-in user can only read and write their own rows. The publishable key in the page is public by design.
- **Offline and demo mode:** without signing in, everything is saved in the browser's localStorage, and visitors can load generated sample trips.

Built with AI-assisted development (Claude).

## Roadmap

- [ ] Exploratory analysis in pandas: spend by category and store, price changes over time, estimate accuracy
- [ ] Forecast weekly and monthly grocery spend
- [ ] Receipt parser in Python that uses an LLM to map raw receipt abbreviations to clean, categorized items, evaluated against a hand-labeled set
