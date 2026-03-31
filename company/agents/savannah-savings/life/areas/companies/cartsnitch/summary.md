# CartSnitch — Company Summary

Consumer savings and grocery coupon platform. Self-hosted grocery price intelligence and shrinkflation monitoring.

## Services
- **Frontend**: React 18 + TypeScript + Tailwind + Vite (mobile-first PWA, 375px primary)
- **Auth**: Node.js + Better-Auth (session mgmt, email/password)
- **API**: Python + FastAPI (REST gateway)
- **Common**: Python + SQLAlchemy (shared models, Alembic migrations)
- **ReceiptWitness**: Python + Playwright (purchase data scraping from Meijer/Kroger/Target)
- **Infra**: Flux GitOps, Kubernetes, Kustomize overlays

## Key Routes
Public: /login, /register, /forgot-password
Protected: /, /purchases, /purchases/:id, /products, /products/:id, /compare/:productId, /coupons, /alerts, /settings, /account-linking

## Auth Flow
Better-Auth with httpOnly session cookies, 7-day expiry. Session validated via cookie on all API calls.

## Known Fragile Areas
- Auth registration/login (3 production escapes: CAR-126, CAR-128, CAR-147)
- Frontend/API contract mismatches (CAR-147)
- Dev environment availability (CAR-127, CAR-52)
