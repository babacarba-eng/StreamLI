# ⚡ StreamLI — Digital Subscriptions & Gift Cards Marketplace

A full-stack SaaS marketplace for selling digital subscriptions and gaming gift cards instantly. Built with Next.js 14, Supabase, and Stripe.

![StreamLI](https://img.shields.io/badge/Next.js-14-black?style=for-the-badge&logo=next.js)
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)
![Stripe](https://img.shields.io/badge/Stripe-626CD9?style=for-the-badge&logo=Stripe&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white)

---

## 🚀 Features

- **Landing Page** — Hero, categories, features, testimonials, CTA
- **Marketplace** — Filter by category (Gaming, Streaming, AI Tools, Design Tools), search
- **Auth** — Supabase email/password authentication
- **Currency Switcher** — MAD (🇲🇦), FCFA (🌍), USD — prices update live
- **Stripe Checkout** — Secure payment processing with webhooks
- **User Dashboard** — Order history, copy delivery codes
- **Admin Dashboard** — Manage products (add/edit/delete), view all orders, update order status
- **13 Products** — PSN, Xbox, Nintendo, Steam, Roblox, V-Bucks, Spotify, Apple Music, Netflix, Prime Video, ChatGPT Plus, Canva Pro, CapCut Pro

---

## 📦 Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 14 (App Router) |
| Styling | Tailwind CSS + custom design system |
| Database | Supabase (PostgreSQL) |
| Auth | Supabase Auth |
| Payments | Stripe Checkout |
| Deployment | Vercel |
| CI/CD | GitHub Actions |
| State | Zustand |
| Fonts | Syne + Plus Jakarta Sans |

---

## 🛠️ Local Development

### 1. Clone & Install

```bash
git clone https://github.com/your-username/streamli.git
cd streamli
npm install
```

### 2. Set Up Environment Variables

```bash
cp .env.example .env.local
```

Fill in `.env.local`:

```env
# Supabase (get from https://app.supabase.com → Settings → API)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# Stripe (get from https://dashboard.stripe.com/apikeys)
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Admin
ADMIN_EMAIL=admin@yourdomain.com
```

### 3. Set Up Supabase Database

1. Go to [Supabase Dashboard](https://app.supabase.com) → your project → SQL Editor
2. Copy the content of `supabase/migrations/001_initial.sql`
3. Paste and run it in the SQL Editor
4. This creates all tables, RLS policies, and seeds the 13 products

### 4. Set Admin User

After signing up through the app:

```sql
-- Run in Supabase SQL Editor
UPDATE public.profiles 
SET is_admin = true 
WHERE email = 'your-admin@email.com';
```

### 5. Set Up Stripe Webhook (local)

```bash
# Install Stripe CLI
brew install stripe/stripe-cli/stripe

# Login
stripe login

# Forward webhooks to local server
stripe listen --forward-to localhost:3000/api/webhook

# Copy the webhook secret (whsec_...) to .env.local
```

### 6. Run Development Server

```bash
npm run dev
```

Visit `http://localhost:3000`

---

## 🚢 Deployment on Vercel

### Method 1: Deploy with Vercel CLI

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Follow prompts to link your project
# Add environment variables when prompted
```

### Method 2: Deploy via Vercel Dashboard

1. Push your code to GitHub
2. Go to [vercel.com](https://vercel.com) → **New Project**
3. Import your GitHub repository
4. Add all environment variables (from `.env.example`)
5. Click **Deploy**

### Method 3: GitHub Actions (Automated CI/CD)

The `.github/workflows/deploy.yml` workflow automatically:
- ✅ Runs lint + TypeScript check on every push/PR
- ✅ Builds the app
- 🚀 Deploys to Vercel production on `main` branch pushes
- 🔍 Creates preview deployments for PRs and `develop` branch

**Required GitHub Secrets** (add at `Settings → Secrets → Actions`):

| Secret | Where to find it |
|--------|-----------------|
| `VERCEL_TOKEN` | [vercel.com/account/tokens](https://vercel.com/account/tokens) |
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase → Settings → API |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase → Settings → API |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase → Settings → API |
| `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` | Stripe Dashboard |
| `STRIPE_SECRET_KEY` | Stripe Dashboard |
| `STRIPE_WEBHOOK_SECRET` | Stripe Dashboard → Webhooks |
| `NEXT_PUBLIC_APP_URL` | Your production URL |

### Configure Stripe Webhook in Production

1. Go to [Stripe Dashboard → Webhooks](https://dashboard.stripe.com/webhooks)
2. Click **Add endpoint**
3. URL: `https://your-domain.vercel.app/api/webhook`
4. Events to listen for:
   - `checkout.session.completed`
   - `checkout.session.expired`
   - `payment_intent.payment_failed`
   - `charge.refunded`
5. Copy the **Signing secret** → add as `STRIPE_WEBHOOK_SECRET`

---

## 📁 Project Structure

```
streamli/
├── app/
│   ├── page.tsx              # Landing page
│   ├── layout.tsx            # Root layout + fonts
│   ├── globals.css           # Global styles
│   ├── marketplace/
│   │   └── page.tsx          # Marketplace with filters
│   ├── login/
│   │   └── page.tsx          # Login page
│   ├── signup/
│   │   └── page.tsx          # Signup page
│   ├── dashboard/
│   │   └── page.tsx          # User dashboard
│   ├── admin/
│   │   └── page.tsx          # Admin control panel
│   ├── auth/callback/
│   │   └── route.ts          # Supabase auth callback
│   └── api/
│       ├── checkout/route.ts # Stripe checkout session
│       ├── webhook/route.ts  # Stripe webhook handler
│       ├── products/route.ts # Products API
│       └── orders/route.ts   # Orders API
├── components/
│   ├── Navbar.tsx            # Navigation + currency switcher
│   ├── ProductCard.tsx       # Product card + skeleton
│   └── Footer.tsx            # Site footer
├── hooks/
│   └── useCurrency.ts        # Zustand currency store
├── lib/
│   ├── supabase.ts           # Supabase clients
│   ├── stripe.ts             # Stripe client
│   └── utils.ts              # Utility functions
├── types/
│   └── index.ts              # TypeScript types + product data
├── supabase/
│   └── migrations/
│       └── 001_initial.sql   # Database schema + seed data
├── .github/
│   └── workflows/
│       └── deploy.yml        # GitHub Actions CI/CD
├── .env.example              # Environment template
├── next.config.js            # Next.js config
├── tailwind.config.js        # Tailwind config
├── vercel.json               # Vercel deployment config
└── package.json
```

---

## 💱 Currency System

| Currency | Code | Rate (vs USD) | Flag |
|----------|------|--------------|------|
| Moroccan Dirham | MAD | 10.05 | 🇲🇦 |
| CFA Franc | FCFA | 612 | 🌍 |
| US Dollar | USD | 1 | 🇺🇸 |

Prices are stored in USD in the database and converted client-side using Zustand persisted state.

---

## 🔐 Security

- Row Level Security (RLS) enabled on all Supabase tables
- Admin-only pages check `is_admin` flag before rendering
- Service role key only used server-side in API routes
- Stripe webhook signature verification
- User sessions managed by Supabase Auth

---

## 📋 Database Schema

```
profiles    — User profiles (linked to Supabase auth.users)
products    — Digital products catalog
orders      — Purchase records with Stripe session links
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

**Built with ⚡ by StreamLI**
