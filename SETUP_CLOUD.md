# Cloud Launch Checklist (Supabase + Lemon Squeezy)

Your app runs out-of-the-box in **Local Multi-User Mode** so you can test User A vs. User B, the 2-quotes/month cap, and the lifetime unlock locally. Follow these 3 steps (~15 minutes) to go live with real user accounts and payments.

---

## Step 1 — Set Up Supabase (Auth + Database)

1. Create a free project at [supabase.com](https://supabase.com).
2. In your Supabase dashboard, open **SQL Editor → New Query**, paste the entire contents of `supabase/migrations/001_init.sql`, and click **Run**.
   - This creates the `profiles`, `jobs`, `monthly_quote_usage`, and `payments` tables, turns on Row-Level Security so users can never access each other's data, and installs the `create_job_with_quota()` function.
3. Go to **Authentication → URL Configuration** and add your live site URL (e.g., `https://yourdomain.com`) to **Site URL** and **Redirect URLs**.
4. Go to **Project Settings → API** and copy:
   - **Project URL** (`https://xxxx.supabase.co`)
   - **anon public key**

---

## Step 2 — Set Up Lemon Squeezy (One-Time Lifetime Payment)

1. Create a **one-time payment product** in [lemonsqueezy.com](https://lemonsqueezy.com) (e.g., *SmallBiz Pricing Calculator — Lifetime Unlimited*, \$29).
2. Copy the product's **Checkout Link** (e.g., `https://yourstore.lemonsqueezy.com/buy/...`).
3. Deploy the webhook Edge Function to Supabase:
   ```bash
   supabase login
   supabase link --project-ref YOUR_PROJECT_REF
   supabase secrets set LEMON_WEBHOOK_SECRET=your_random_signing_secret
   supabase functions deploy lemon-webhook --no-verify-jwt
   ```
4. In Lemon Squeezy, go to **Settings → Webhooks → + Add Webhook**:
   - **Callback URL**: `https://YOUR_PROJECT_REF.supabase.co/functions/v1/lemon-webhook`
   - **Signing Secret**: the same `LEMON_WEBHOOK_SECRET` you set above
   - **Events**: check **`order_created`**

---

## Step 3 — Connect Config & Deploy Frontend

1. Open `js/config.js` and fill in your three values:
   ```js
   export const APP_CONFIG = {
     supabaseUrl: 'https://YOUR_PROJECT_REF.supabase.co',
     supabaseAnonKey: 'YOUR_SUPABASE_ANON_KEY',
     lemonCheckoutUrl: 'https://yourstore.lemonsqueezy.com/buy/YOUR_PRODUCT_ID',
     lifetimePriceLabel: '$29',
     freeMonthlyLimit: 2,
   };
   ```
2. Deploy the `smallbiz-pricing-app/` folder to **Cloudflare Pages**, **Netlify**, or **Vercel** (static site, no build command needed).

You're live—every new user gets isolated cloud storage, 2 free saved quotes/month enforced at the database level, and instant automatic lifetime unlocks upon payment.
