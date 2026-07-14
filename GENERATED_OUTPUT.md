The **Harvest Basket** food e-commerce app is in `harvest-basket/` — **104 source files** covering the full MVP spec.

## Run it

```bash
cd harvest-basket
cp .env.example .env
# Set DATABASE_URL and AUTH_SECRET (openssl rand -base64 32)

npm install
npx prisma db push
npm run db:seed
npm run dev
```

| Role | Email | Password |
|------|-------|----------|
| Admin | `admin@harvestbasket.com` | `admin12345` |
| Customer | `customer@example.com` | `customer123` |

**Mock payment:** `4242 4242 4242 4242` succeeds · ending in `0002` declines.

---

## Architecture highlights

### Payments (mock, swappable)
- `lib/payments/types.ts` — clean `PaymentProvider` interface
- `lib/payments/mock-provider.ts` — always succeeds except test decline card
- Checkout uses `getPaymentProvider()` — swap via `PAYMENT_PROVIDER=STRIPE` later

### Observability (all key-gated)
| Tool | Files | Env var |
|------|-------|---------|
| Sentry | `sentry.*.config.ts`, spans in checkout/auth APIs | `NEXT_PUBLIC_SENTRY_DSN` |
| PostHog | `lib/posthog.ts`, `lib/posthog-server.ts` | `NEXT_PUBLIC_POSTHOG_KEY` |
| Mixpanel | `lib/mixpanel.ts` | `NEXT_PUBLIC_MIXPANEL_TOKEN` |
| Datadog | `instrumentation.ts` | `DD_API_KEY` |
| Vercel Analytics | `app/layout.tsx` | zero config |

Analytics respects GDPR cookie consent before initializing.

### `/docs` page
- `app/docs/page.tsx` — client component with sidebar + `react-markdown` / `remark-gfm` / `rehype-highlight`
- Imports from `@/lib/project-docs` (placeholder stub for upstream injection)
- Linked from header (book icon) and footer

### Storefront
- Homepage hero, categories, featured products, diet tags, trust bar
- Category/search with faceted filters + URL state
- PDP: gallery, variants, nutrition, allergens, reviews, recommendations
- Cart drawer + persistent cart (guest cookie + auth merge on login)
- 4-step checkout → confirmation + order emails

### Admin (`/admin`, RBAC)
- Dashboard KPIs, products list, orders + status/tracking, abandoned carts, analytics, review moderation

### SEO
- `app/sitemap.ts`, `app/robots.ts`
- JSON-LD on homepage + PDP
- `generateMetadata` on product/category pages

---

## Project layout

```
harvest-basket/
├── app/
│   ├── (storefront)/     # Shop pages
│   ├── admin/            # Back office
│   ├── docs/             # AI blueprint viewer
│   └── api/              # Cart, checkout, auth, admin APIs
├── components/           # UI, layout, product, cart, admin
├── lib/
│   ├── payments/         # Mock provider
│   ├── email/            # Resend (no-op in dev)
│   ├── seo/              # Metadata + JSON-LD
│   └── project-docs.ts   # Injected by upstream agents
├── prisma/schema.prisma  # Full data model
├── prisma/seed.ts        # 10 products, tags, categories, demo orders
├── .env.example          # Observability section included
└── README.md
```

---

## Seed catalog

10 food products across 8 categories with variants, health info, tags, discounts, and co-purchase data for recommendations (protein powder ↔ yogurt ↔ granola bar).

---

All files are on disk at `C:\Guy\generated-apps\.agent_workspace\harvest-basket\`. Say if you want a specific file exported in `=== FILE ===` format, or help running `npm install` / verifying the build locally.