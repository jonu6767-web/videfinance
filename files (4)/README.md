# VibeFinance (PS 04)

Three specialist agents (technical, fundamental/RAG, sentiment) feed a synthesis engine that turns the
same market signals into different advice for Conservative, Moderate and Aggressive investors.

```
index.html  (GitHub Pages)  ->  Supabase Edge Functions  ->  Postgres + pgvector
                                 synthesis -> technical-agent
                                           -> fundamental-agent (RAG, citations verified against source text)
                                           -> sentiment-agent  (Gemini scores news headlines)
```

## Deploy the dashboard (GitHub Pages)
1. Create a public GitHub repository and upload `index.html` to its root.
2. Settings -> Pages -> Source: "Deploy from a branch", branch `main`, folder `/ (root)`.
3. Open `https://<your-username>.github.io/<repo-name>/`.

`index.html` already contains your project URL and the public `anon` key (safe to expose; row-level
security protects the data). Never put the Gemini key or the service-role key in this file.

## Backend (already deployed in the Supabase project)
Functions: `synthesis`, `technical-agent`, `fundamental-agent`, `sentiment-agent`, `load-prices`,
`load-news`, `ingest-document`. Database tables and risk rules were created with migrations.
Secret required: `GEMINI_API_KEY` (Edge Functions -> Secrets).

## Tests (Node 22+)
```
cd tests/technical-agent && python3 gen_cases.py && node --experimental-strip-types run_tests.mjs
cd tests/synthesis      && node --experimental-strip-types run_tests.mjs && node --experimental-strip-types run_handler_tests.mjs
cd tests/sentiment      && node --experimental-strip-types run_tests.mjs
cd tests/fundamental    && node --experimental-strip-types run_tests.mjs
cd tests/load-prices    && node --experimental-strip-types test_yahoo.mjs
```

## Demo data
`DEMOEV` is a fictional company with synthetic prices, headlines and a synthetic earnings-call transcript
(`sample-docs/`). It exists only to demonstrate the full pipeline. Real stocks use real prices and news;
add real filings for them to enable the fundamental agent.
