# SmallBiz Job Profit & Pricing Calculator (Multi-User Freemium Web App)

A Vanilla HTML, CSS, and modular ES JavaScript web app converted from `SmallBiz_Job_Profit_and_Pricing_Calculator.xlsx` with multi-user accounts, strict per-user isolation, a `2 saved quotes/month` free tier, and a one-time Lifetime Pro unlock.

## Features

- **Guest-First Onboarding**: Visitors can calculate prices immediately with sample jobs; clicking **Save Quote** opens signup/login and automatically saves their draft to their account.
- **Multi-User Isolation**: Each account (e.g., User A landscaper vs. User B cleaner) has completely separate business settings, saved quotes, completed actuals, and monthly usage counters—enforced by Postgres Row-Level Security (RLS) in Supabase.
- **Freemium Quota (`2 quotes/month`) + Lifetime Unlock**: Creating a new quote runs through an atomic server-side quota check (`create_job_with_quota`). Editing existing quotes, recording actuals, printing customer PDFs, and exporting CSV/JSON are always unlimited. One-time payment via Lemon Squeezy unlocks unlimited quote storage forever via a signed webhook.
- **Zero-Key Local Multi-User Mode**: Runs out-of-the-box with `localMultiUserProvider.js` so you can test User A vs. User B isolation, hitting the `2/2` limit, and unlocking Lifetime Pro before adding live Supabase & Lemon Squeezy keys.

## Project Structure

```text
smallbiz-pricing-app/
├── index.html                                  # Semantic HTML5 shell + Auth & Upgrade modals
├── styles/
│   ├── tokens.css                              # Curated HSL design tokens
│   ├── app.css                                 # Responsive UI, account header & modal styles
│   └── print.css                               # Clean Customer Quote @media print styles
├── js/
│   ├── config.js                               # Supabase, Lemon Squeezy & pricing config
│   ├── calculator.js                           # Pure calculation engine (Excel parity)
│   ├── format.js                               # Intl.NumberFormat currency, tax % & date helpers
│   ├── demoData.js                             # Default assumptions & 4 sample jobs
│   ├── storage.js                              # Normalization, JSON backup & formula-safe CSV
│   ├── dataService.js                          # Pluggable facade (Cloud vs. Local Multi-User)
│   ├── providers/
│   │   ├── localMultiUserProvider.js           # Offline multi-tenant test provider
│   │   └── supabaseProvider.js                 # Production Supabase Auth/RLS/RPC provider
│   └── app.js                                  # Reactive async UI controller
├── supabase/
│   ├── migrations/001_init.sql                 # Postgres tables, triggers, RLS & quota RPC
│   └── functions/lemon-webhook/
│       ├── verify.js                           # Web Crypto HMAC-SHA256 verification helpers
│       └── index.ts                            # Supabase Edge Function for lifetime unlocks
├── tests/
│   ├── calculator.test.mjs                     # Formula, fuzz, normalization & CSV tests
│   ├── freemium.test.mjs                       # Multi-tenant isolation & quota rollover tests
│   └── webhook.test.mjs                        # HMAC-SHA256 webhook security tests
├── scripts/bundle-singlefile.mjs               # Zero-dependency single-HTML packager
└── SETUP_CLOUD.md                              # 15-minute Supabase + Lemon Squeezy launch guide
```

## Running Tests

```bash
node tests/calculator.test.mjs
node tests/freemium.test.mjs
node tests/webhook.test.mjs
```

## Going Live

See [SETUP_CLOUD.md](./SETUP_CLOUD.md) for the 3-step Supabase + Lemon Squeezy + static hosting deployment guide.
