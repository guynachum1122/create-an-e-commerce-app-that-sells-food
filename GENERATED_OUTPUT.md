Applied **P0/P1 fixes** from the security and QA reports to the existing `harvest-basket/` codebase. Summary of what changed:

## Security (P0/P1)

| Fix | Files |
|-----|-------|
| **GDPR erasure** — redacts order shipping addresses, email log recipients, compliance audit log | `app/api/account/delete/route.ts`, `prisma/schema.prisma` (`ComplianceLog`) |
| **Production rate limiting** — requires Upstash on Vercel production | `lib/rate-limit.ts` |
| **Verified reviews** — only `DELIVERED` + paid orders | `app/api/reviews/route.ts` |
| **JWT role refresh** — re-fetches role every 5 min | `auth.ts` |
| **`/api/admin/*` middleware guard** | `middleware.ts` |
| **Account delete rate limit** | `app/api/account/delete/route.ts` |
| **Marketing consent persisted** | `marketingConsent` on `User`, `app/api/account/consent/route.ts` |
| **Rate limits** on search suggest + wishlist | `app/api/search/suggest/route.ts`, `app/api/wishlist/route.ts` |
| **CSP tightened** — removed `unsafe-eval` | `vercel.json` |
| **Seed admin password** from `SEED_ADMIN_PASSWORD` | `prisma/seed.ts` |

## QA (P0/P1)

| Fix | Files |
|-----|-------|
| **Admin product CRUD UI** — categories, tags, nutrition, images, variant attrs/discounts/stock | `components/admin/admin-product-form.tsx`, `app/admin/products/new|/[id]/page.tsx`, `app/api/admin/products/[id]/route.ts`, `app/api/admin/catalog-options/route.ts` |
| **Dashboard date range** — API returns `topByRevenue` / `topByUnits` | `app/api/admin/dashboard/route.ts` |
| **Analytics revenue filter** — paid + non-cancelled only | `app/admin/analytics/page.tsx` |
| **Cart drawer pricing** — uses `unitPrice` | `components/cart/cart-drawer.tsx` |
| **Wishlist on category/search cards** | `components/product/product-card.tsx`, category + search pages |
| **Search relevance sort** | `lib/products/service.ts`, `components/catalog/category-filters.tsx`, search page |
| **Checkout tax line** + server tax calc | `lib/checkout/calculate-totals.ts`, checkout page, `app/api/checkout/route.ts` |
| **Checkout step persistence** via `sessionStorage` | `app/(storefront)/checkout/page.tsx` |
| **Removed unused Zustand** | `package.json`, `README.md` |
| **Seed**: multi-category product, gallery images, `deals` category | `prisma/seed.ts` |
| **`.env.example`** — `NEXTAUTH_*` aliases, seed + Upstash notes | `.env.example` |

## Schema migration required

New fields/models need a DB push:

```bash
cd harvest-basket
npm install
npx prisma db push
npm run db:seed
npm run dev
```

**Demo credentials (dev):**
- Admin: `admin@harvestbasket.com` / `admin12345` (or `SEED_ADMIN_PASSWORD`)
- Customer: `customer@example.com` / `customer123`

**Production:** set `UPSTASH_REDIS_REST_URL`, `UPSTASH_REDIS_REST_TOKEN`, and a strong `AUTH_SECRET` before deploying to Vercel.

The full app (catalog, cart, auth-gated checkout, mock payments, admin, observability, `/docs`) was already in place; these changes address the reported security and QA gaps. I couldn't run `npm run build` in this environment — run it locally to confirm.