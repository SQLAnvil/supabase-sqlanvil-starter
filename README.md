# supabase-sqlanvil-starter

**Analytics engineering for Supabase, in ~5 minutes.** A ready-to-run [SQLAnvil](https://github.com/SQLAnvil/sqlanvil)
project that builds derived tables, an incremental daily rollup, a materialized view, and a
data-quality assertion — right inside your Supabase Postgres database. No data warehouse required.

> SQLAnvil is a [Dataform](https://github.com/dataform-co/dataform) fork with first-class Postgres &
> Supabase support — SQLX models, `${ref()}` dependency tracking, assertions, incremental tables, and
> materialized views. Think "dbt for Supabase."

## Quickstart

```bash
# 1. Install the CLI
npm i -g @sqlanvil/cli

# 2. Use this template (GitHub "Use this template" button), then clone your copy. Or:
git clone https://github.com/SQLAnvil/supabase-sqlanvil-starter.git && cd supabase-sqlanvil-starter

# 3. Add your Supabase connection
cp .df-credentials.example.json .df-credentials.json
#    then edit .df-credentials.json (see "Connecting to Supabase" below)

# 4. Compile (no DB needed) and run (applies to your Supabase DB)
sqlanvil compile .
sqlanvil run . --credentials .df-credentials.json
```

You'll get a `sqlanvil_starter` schema with `raw_orders`, `daily_revenue`, `revenue_rollup`, and a
passing assertion. Re-run any time — it's idempotent (the incremental appends; the matview refreshes).

## Connecting to Supabase

Get the connection from your project's **Connect** button in the Supabase dashboard. **On most
networks (IPv4) use the Session pooler**, and **copy the host verbatim** — don't construct it:

```json
{
  "host": "aws-1-<region>.pooler.supabase.com",
  "port": 5432,
  "database": "postgres",
  "user": "postgres.<your-project-ref>",
  "password": "<your-db-password>",
  "sslMode": "require",
  "defaultSchema": "public"
}
```

Full details + troubleshooting: [Getting Started with Supabase](https://sqlanvil.com/docs/guides/supabase/).

## What's in here

| File | Demonstrates |
| :--- | :--- |
| `definitions/raw_orders.sqlx` | a seed `table` (stands in for a source — replace with a `declaration` of your real table) |
| `definitions/daily_revenue.sqlx` | an `incremental` table with an upsert key + a one-time primary key |
| `definitions/revenue_rollup.sqlx` | a **materialized view** that refreshes in place on each run |
| `definitions/assert_revenue_non_negative.sqlx` | a data-quality `assertion` |
| `.github/workflows/scheduled-run.yml` | run your models on a schedule via GitHub Actions (free) |

## Using your real data

Swap the seed `raw_orders` table for a **declaration** of a table you already have in Supabase:

```sqlx
config { type: "declaration", schema: "public", name: "orders" }
```

Then point `daily_revenue` at `${ref("orders")}`. Declarations are exempt from `--schema-suffix`, so
a `--schema-suffix dev` run still reads your real source while writing to a `_dev` sandbox.

## Scheduled runs

`.github/workflows/scheduled-run.yml` runs `sqlanvil run` on a daily cron. Add your credentials JSON
as a repository secret named `SQLANVIL_CREDENTIALS` (Settings → Secrets and variables → Actions).

## Links
- Docs: https://sqlanvil.com/docs/
- SQLAnvil: https://github.com/SQLAnvil/sqlanvil
<!-- release-pinning e2e touch 2026-07-02 -->
