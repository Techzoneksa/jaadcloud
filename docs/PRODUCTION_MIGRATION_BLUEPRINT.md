# JAAD CLOUD — Production Migration Blueprint

> **Status:** Planning Phase Only — No Code Changes
> **Demo Branch:** `phase-1-migration-audit` | **Tag:** `v0.1.0-demo-baseline`
> **Current Tech:** TanStack Start v1 + React 19 + Vite 7 + Tailwind v4 + shadcn/ui
> **Target Tech:** Next.js + Node.js 22.x + Hostinger

---

## 0. Sync Check

| Check | Result |
|-------|--------|
| Branch | `phase-1-migration-audit` ✅ |
| Local HEAD | `5f089fb` ✅ |
| Remote HEAD | `5f089fb` ✅ |
| Tag local | `v0.1.0-demo-baseline` → `220ca3e` ✅ |
| Tag remote | `v0.1.0-demo-baseline` ✅ |
| Working tree | CLEAN ✅ |
| Local == Remote | ✅ In sync |
| Secrets tracked | ❌ None |
| Can proceed | ✅ |

---

## 1. Approved Demo Modules — Migration Priority

| Module | In Demo? | Migrate? | Priority | Notes |
|--------|----------|----------|----------|-------|
| Dashboard | ✅ Full | ✅ | P0 | Charts, summaries, stats |
| Customers | ✅ Full | ✅ | P0 | CRUD + contact info |
| Suppliers | ✅ Full | ✅ | P0 | CRUD + contact info |
| Products & Services | ✅ Full | ✅ | P0 | Items with pricing, tax |
| Quotations | ✅ Full | ✅ | P0 | Create → issue → convert to invoice |
| Sales Invoices | ✅ Full | ✅ | P0 | Core sales document |
| Purchase Invoices | ✅ Full | ✅ | P0 | Core purchasing document |
| Receipts | ✅ Full | ✅ | P0 | Payment collection with auto-apply |
| Payments | ✅ Full | ✅ | P0 | Payment disbursement |
| Chart of Accounts | ✅ Full | ✅ | P0 | Hierarchical tree |
| Journal Entries | ✅ Full | ✅ | P0 | Double-entry posting engine |
| Taxes | ✅ Full (8 rates) | ✅ | P0 | Saudi VAT rates, ZATCA-ready |
| Reports Center | ✅ (11 reports) | ✅ | P0 | TB, BS, P&L, CF, GL, AR, Sales, VAT |
| Audit Log | ✅ | ✅ | P0 | Compliance trail |
| Permissions | ✅ (4 roles) | ✅ | P1 | RBAC middleware |
| Document Templates | ✅ (4 templates) | ✅ | P1 | Print layouts |
| Smart Select / Combobox | ✅ | ✅ | P1 | Entity lookup UX |
| Quick Create | ✅ | ✅ | P1 | Inline entity creation |
| Cost Centers | ✅ | ✅ | P1 | Segmented reporting |
| Projects | ✅ | ✅ | P1 | Project-based accounting |
| Branches | ✅ | ✅ | P1 | Multi-branch support |
| Bank Accounts | ✅ | ✅ | P1 | Cash/bank management |
| HR (Employees) | ✅ Foundation | ⏳ | P2 | Basic CRUD only |
| Inventory | ✅ Foundation | ⏳ | P2 | Basic stock view |
| Fixed Assets | ✅ Foundation | ⏳ | P2 | Basic register |
| Credit Notes | ✅ List only | ⏳ | P2 | Needs full flow |
| Debit Notes | ✅ List only | ⏳ | P2 | Needs full flow |
| Sales Orders | ✅ List only | ⏳ | P2 | Needs full flow |
| Purchase Orders | ✅ List only | ⏳ | P2 | Needs full flow |
| Bank Reconciliation | ✅ Foundation | ⏳ | P2 | Needs full flow |
| Demo Checklist | ✅ | ❌ | — | Demo-only, not needed in production |
| System Data Mode | ✅ | ❌ | — | Demo-only, replaced by real settings |

---

## 2. Current Technology Assessment

### What to Keep

| Technology | Assessment | Action |
|------------|------------|--------|
| React 19 | Core library — keep | Already Next.js compatible |
| TypeScript (strict) | Keep | Already configured |
| Tailwind v4 | Keep | Native Next.js support |
| shadcn/ui | Keep | Re-install via `npx shadcn@latest init` |
| Zustand | Keep (optional) | Replace with React Server State where possible |
| TanStack Query | Keep (optional) | Useful for client-side data fetching |
| Lucide Icons | Keep | Re-install via npm |
| Sonner (toast) | Keep | Re-install via npm |
| Recharts (charts) | Keep | Re-install via npm |
| `date-fns` / `hijri` | Keep | Already pure functions |
| `js-cookie` | Keep | Already pure |
| `qrcode` | Keep | Already pure |

### What Must Be Rewritten

| Component | Reason | Strategy |
|-----------|--------|----------|
| Router (`@tanstack/react-router`) | Meta-framework lock-in | Replace with Next.js App Router |
| Route files (`src/routes/` 74 files) | TanStack-specific format | Port to `app/` directory |
| SSR setup (`@tanstack/start`) | Different SSR model | Replace with Next.js SSR/SSG |
| File-based routing config | Vendor-specific | Use Next.js file conventions |
| `router.tsx` | Router definition | Not needed in Next.js |
| `routeTree.gen.ts` | Auto-generated | Not needed in Next.js |
| Vite plugins (`@lovable.dev/vite-tanstack-config`) | Lovable-specific | Remove entirely |

### What to Port as Components

| Directory | Action |
|-----------|--------|
| `src/components/ui/*` | Port directly (shadcn/ui components) |
| `src/components/shared/*` | Port directly |
| `src/components/accounting/*` | Port with minimal changes |
| `src/components/documents/*` | Port with minimal changes |
| `src/components/quick-create/*` | Port with adapter changes |
| `src/lib/store.ts` (types) | Port entirely |
| `src/lib/validators.ts` | Port entirely |
| `src/lib/services/*` | Port, replace DataAdapter with DB calls |

### What to Rewrite in Service Layer

| Service | Status | New Implementation |
|---------|--------|--------------------|
| `AccountingEngine` | Rewrite | Direct DB posting, immutability checks |
| `JournalService` | Rewrite | DB transactions, audit log |
| `InvoiceService` | Rewrite | DB write + state machine |
| `QuotationService` | Rewrite | DB write + convert flow |
| `ReceiptService` | Rewrite | DB write + auto-apply |
| `PaymentService` | Rewrite | DB write + GL posting |
| `TaxRateService` | Port + rewrite | Read from DB, write via admin |
| `ReportService` | Rewrite | Direct DB aggregation queries |
| `AuditService` | Rewrite | DB insert via middleware |
| `PermissionService` | Rewrite | Role-based middleware |

### What to Remove / Clean Up

| Artifact | Reason | When |
|----------|--------|------|
| `.lovable/` directory | Lovable context | After migration complete |
| `.workspace/` directory | Lovable workspace (contains JWT in `.git/config`) | After migration complete |
| `@lovable.dev/vite-tanstack-config` | Lovable Vite plugin | After migration complete |
| `@lovable.dev/lovable-error-reporting` | Lovable error tracking | After migration complete |
| `nitro` dependency | TanStack/Nitro SSR | After migration complete |
| `vite.config.ts` | Vite config | After migration complete |
| `src/lib/lovable-error-reporting.ts` | Lovable-specific | After migration complete |
| `src/lib/adapters/LocalStorageDataAdapter.ts` | Demo only | After production paths are ready |
| `src/lib/adapters/FutureBackendDataAdapter.ts` | Read-only stub | After production paths are ready |
| `src/lib/adapters/index.ts` (DATA_MODE switch) | Demo only | After production paths are ready |
| Demo Checklist page | Demo-only UI | Remove from production |
| System Data Mode page | Demo-only UI | Remove from production |
| `supabase/planned-migrations/` | Stale docs | Remove after real migration |

### What to Keep (Supabase but Independent)

| Artifact | Action |
|----------|--------|
| `supabase/migrations/` (10 files) | Keep as reference — real DB design is inspired but rewritten independently |
| `src/integrations/supabase/` | Keep as reference — auth patterns |
| `src/lib/supabase/` | Keep as reference — service layer patterns |
| `src/components/backend/` | Port only `BackendAuthSandbox` patterns (not the demo page itself) |

---

## 3. Proposed Next.js Architecture

### Folder Structure

```
jaadcloud/                          # Monorepo root (or standalone app)
  src/
    app/
      (auth)/                       # Auth pages (no sidebar)
        login/
          page.tsx
        register/
          page.tsx
        forgot-password/
          page.tsx
      (dashboard)/                  # Authenticated pages (with sidebar)
        layout.tsx                  # AppShell equivalent
        page.tsx                    # Dashboard
        customers/
          page.tsx
          [id]/
            page.tsx
            edit/
              page.tsx
        suppliers/
          page.tsx
          [id]/
            page.tsx
            edit/
              page.tsx
        items/
          page.tsx
          [id]/
            page.tsx
            edit/
              page.tsx
        quotations/
          page.tsx                  # List
          new/
            page.tsx
          [id]/
            page.tsx
            edit/
              page.tsx
        invoices/
          sales/
            page.tsx
            new/
              page.tsx
            [id]/
              page.tsx
              edit/
                page.tsx
          purchases/
            page.tsx
            new/
              page.tsx
            [id]/
              page.tsx
              edit/
                page.tsx
        receipts/
          page.tsx
          new/
            page.tsx
          [id]/
            page.tsx
        payments/
          page.tsx
          new/
            page.tsx
          [id]/
            page.tsx
        accounting/
          chart/
            page.tsx
          journal/
            page.tsx
            new/
              page.tsx
            [id]/
              page.tsx
              edit/
                page.tsx
          taxes/
            page.tsx
        reports/
          page.tsx                  # Reports Center
          trial-balance/
            page.tsx
          balance-sheet/
            page.tsx
          profit-loss/
            page.tsx
          cash-flow/
            page.tsx
          general-ledger/
            page.tsx
          account-statement/
            page.tsx
          sales-by-customer/
            page.tsx
          sales-by-product/
            page.tsx
          overdue-invoices/
            page.tsx
          unpaid-invoices/
            page.tsx
          vat/
            page.tsx
        audit-log/
          page.tsx
        settings/
          page.tsx
        cost-centers/
          page.tsx
        projects/
          page.tsx
        branches/
          page.tsx
        bank-accounts/
          page.tsx
        document-templates/
          page.tsx
        // Foundation modules
        employees/
          page.tsx
        inventory/
          page.tsx
        fixed-assets/
          page.tsx
        credit-notes/
          page.tsx
        debit-notes/
          page.tsx
        sales-orders/
          page.tsx
        purchase-orders/
          page.tsx
        bank-reconciliation/
          page.tsx
      api/                          # API Routes (if not using Server Actions exclusively)
        auth/
          [...nextauth]/
            route.ts
        webhooks/
          route.ts
    components/
      ui/                           # shadcn/ui components
        button.tsx
        dialog.tsx
        card.tsx
        ...
      shared/                       # Shared UI patterns
        AppShell.tsx
        Sidebar.tsx
        DataTable.tsx
        ConfirmDialog.tsx
        EmptyState.tsx
        StatusBadge.tsx
        LoadingSpinner.tsx
        PageHeader.tsx
        PermissionGate.tsx
        SmartEntityCombobox.tsx
        DocumentEditorLayout.tsx
        LineItemsEditor.tsx
        DocumentPrint.tsx
      accounting/                   # Module-specific components
        JournalEditor.tsx
        ChartTree.tsx
        TaxRateList.tsx
      documents/
        QuotationEditor.tsx
        SalesInvoiceEditor.tsx
        PurchaseInvoiceEditor.tsx
        EmailDialog.tsx
    features/                       # Feature modules with server actions
      auth/
        actions.ts                  # Server actions: login, register, reset, logout
        middleware.ts               # Next.js middleware for auth guard
      customers/
        actions.ts                  # Server actions: CRUD
        queries.ts                  # DB queries
      suppliers/
        actions.ts
        queries.ts
      items/
        actions.ts
        queries.ts
      invoices/
        sales-actions.ts
        purchase-actions.ts
        queries.ts
      accounting/
        journal-actions.ts
        journal-queries.ts
        posting.ts                  # AccountingEngine
      reports/
        queries.ts                  # Aggregation queries
      audit/
        middleware.ts               # Audit log middleware
        queries.ts
    lib/
      db.ts                         # Database client singleton
      schema.ts                     # Drizzle/Prisma schema exports
      types.ts                      # Shared types (ported from store.ts)
      validators.ts                 # Zod schemas (ported)
      permissions.ts                # RBAC helpers
      env.ts                        # Environment validation
      utils.ts                      # cn(), formatters, etc.
      middleware.ts                 # Tenant resolution
    server/                         # Server-only code
      services/
        accounting-engine.ts
        journal-service.ts
        invoice-service.ts
        receipt-service.ts
        payment-service.ts
        tax-service.ts
        report-service.ts
        audit-service.ts
        permission-service.ts
      guards/
        auth-guard.ts
        permission-guard.ts
    db/                             # Database concerns
      migrations/
      seeds/
      queries/
  public/
    assets/
  docs/
  tests/
  next.config.ts
  tailwind.config.ts
  tsconfig.json
  package.json
```

### Key Architectural Decisions

1. **App Router** — All routes under `app/`, use `layout.tsx` for shared shells
2. **Server Components by default** — Only add `'use client'` where interactivity is needed
3. **Server Actions** for mutations — Replace DataAdapter writes
4. **Route Handlers** (`api/`) only for third-party webhooks
5. **Middleware** (`middleware.ts`) for auth guard + tenant resolution
6. **Feature-based modules** under `features/` — Each module has actions + queries
7. **Pure service layer** under `server/services/` — Business logic, no I/O coupling
8. **DB queries** are separated from services for testability

---

## 4. Database & ORM Recommendation

### Recommendation: Drizzle ORM + PostgreSQL

| Criteria | Drizzle | Prisma |
|----------|---------|--------|
| Bundle size (server) | Tiny (0kb client) | Large (client bundle) |
| Type safety | ✅ Full | ✅ Full |
| Migration generation | ✅ `drizzle-kit` | ✅ `prisma migrate` |
| Multi-schema / multi-tenant | ✅ Native | ⚠️ Requires `multiSchema` preview |
| Raw SQL support | ✅ Excellent | ⚠️ Verbose via `$queryRaw` |
| Relation queries | ✅ Good | ✅ Excellent |
| Join performance | ✅ Direct SQL | ⚠️ Can generate N+1 |
| Server Actions friendly | ✅ | ✅ |
| Learning curve | Medium (SQL-first) | Low (declarative) |
| Transaction support | ✅ `db.transaction` | ✅ `prisma.$transaction` |
| PostgreSQL specific | ✅ Full | ✅ Full |
| Monorepo support | ✅ Good | ✅ Good |

### Why Drizzle (Not Prisma)

1. **Performance** — Drizzle generates more predictable SQL
2. **Bundle size** — Drizzle is tree-shakeable, no client library in browser
3. **SQL dialect** — Accounting queries (trial balance, P&L) need complex aggregation — Drizzle's raw SQL helpers are superior
4. **Migration control** — Drizzle migrations are plain SQL files, easier to review and modify
5. **No vendor lock-in** — Drizzle wraps SQL directly

### Why Not Prisma

1. Heavier dependency
2. Generated client adds build complexity
3. Complex accounting queries require workarounds
4. Migration history management is less transparent

### PostgreSQL Hosting Options

| Provider | Cost | Managed? | Backup | Notes |
|----------|------|----------|--------|-------|
| Hostinger VPS + self-hosted PG | Low | ❌ | Manual | Full control, more ops work |
| Neon (Serverless PG) | Free tier → Paid | ✅ | Automated | Great for SaaS |
| Supabase (standalone, not Lovable) | Free tier → Paid | ✅ | Automated | Good if we reuse auth/infra |
| Railway | Low cost | ✅ | Automated | Easy deployment |
| Render | Low cost | ✅ | Automated | Good for small teams |

**Recommendation**: Start with **Neon** (serverless PG, free tier, great DX) or **Supabase PG** (standalone, not via Lovable). Self-host on Hostinger VPS only after revenue justifies ops overhead.

### Schema Guidelines (Production)

```
organizations (tenants)
users
organization_members          # RBAC membership
customers
suppliers
items (products & services)
item_categories
tax_rates
quotations
quotation_lines
sales_invoices
sales_invoice_lines
purchase_invoices
purchase_invoice_lines
receipts
receipt_applications          # Link receipt to invoices
payments
payment_applications          # Link payment to bills
chart_of_accounts             # Hierarchical (parent_id)
journal_entries               # Immutable after posting
journal_entry_lines           # Double-entry lines
projects
cost_centers
branches
bank_accounts
bank_reconciliation
audit_logs
document_sequences            # Sequential numbering per org
settings                      # Org-level settings
```

---

## 5. Auth & Multi-Tenant Strategy

### Recommendation: Auth.js (NextAuth) + Custom Tenant Model

| Option | Assessment | Decision |
|--------|------------|----------|
| **Auth.js v5 (NextAuth)** | ✅ Mature, Next.js native, supports email/password, OAuth, adapters | **Primary choice** |
| Supabase Auth (standalone) | ✅ Good, but adds external dependency | Backup option |
| Custom credentials (bcrypt + JWT) | ⚠️ More work, security risk if done wrong | ❌ Avoid |
| Clerk | ✅ Good SaaS, expensive for self-hosted | ❌ Not needed |
| Lucia | ✅ Lightweight, but v3 unstable | ⚠️ Wait for stable |

### Auth Architecture

```
User visits page
  → middleware.ts checks session (JWT cookie)
  → If not authenticated & trying protected route → redirect to /login
  → If authenticated → extract org_memberships
  → Add user + org to request headers
  → Server Components read from headers / cookies
  → Server Actions verify permissions before mutations
```

### Tenant Model

- **Multi-tenant via `organization_id`** on every table
- Each user can belong to **multiple organizations**
- One **active organization** per session (stored in JWT)
- Roles per organization membership: `owner`, `accountant`, `sales`, `viewer`
- Owner can invite users and assign roles

### Middleware Guard Flow

```
Request
  → middleware.ts (Next.js Middleware)
  → Verify session (JWT decode)
  → If protected path → check org_id in session
  → Set x-user-id, x-org-id headers
  → Route handler / Server Action reads headers
  → PermissionGate decorates the action
```

---

## 6. API & Service Migration Strategy

### Current Data Flow

```
UI Component
  → Service Function (src/lib/services/)
  → DataAdapter (LocalStorage | FutureBackend)
  → LocalStorage | Supabase (read-only)
```

### Target Data Flow

```
UI Component (Server Component)
  → Server Action (features/*/actions.ts)
  → Service Layer (server/services/*.ts)
  → DB Query (db/queries/*.ts)
  → PostgreSQL (via Drizzle)

UI Component (Client Component)
  → Server Action (via useActionState / direct call)
  → Service Layer
  → DB Query
  → PostgreSQL
```

### Service Migration Map

| Current Service | Target | Strategy |
|----------------|--------|----------|
| `AccountingEngine.postJournalEntry` | `server/services/accounting-engine.ts` | Rewrite with DB transaction + immutability + audit |
| `JournalService` (CRUD + list) | `server/services/journal-service.ts` | Rewrite with posted check on edits |
| `InvoiceService.issueInvoice` | `server/services/invoice-service.ts` | Rewrite with state machine (draft → posted) |
| `InvoiceService.createReceipt` | `server/services/receipt-service.ts` | Rewrite with auto-posting to GL |
| `PaymentService` | `server/services/payment-service.ts` | Rewrite with payable reconciliation |
| `TaxRateService` | `server/services/tax-service.ts` | Port + DB reads |
| `ReportService.getTrialBalance` | `server/services/report-service.ts` | Rewrite with SQL aggregation |
| `AuditService` | `server/services/audit-service.ts` | Rewrite — insert on every mutation |
| `PermissionService` | `server/services/permission-service.ts` | Rewrite as middleware + DB query |

### Critical Accounting Rules

1. **Journal entries are immutable after posting** — No updates allowed. Corrections via reversal + new entry.
2. **Sales invoices lock after posting** — No direct edits. Must use credit note + new invoice.
3. **Quotations are mutable until issued** — Once converted to invoice, quotation becomes read-only.
4. **Receipts auto-apply to invoices** — Application is tracked in `receipt_applications`.
5. **Trial balance must balance** — Every posting must have `SUM(debits) = SUM(credits)`.
6. **Audit log on every mutation** — Insert-only audit table with user, action, timestamp, diff.

### Idempotency Strategy

- Use `idempotency_key` (UUID) on critical mutations (posting, payment)
- Server checks for existing key before processing
- Prevents double-posting on network retry

---

## 7. Routing Migration Mapping

| Current Route (TanStack) | Proposed Next.js Route | Type | Notes |
|-------------------------|----------------------|------|-------|
| `/` | `/` | Server | Landing redirect |
| `/login` | `/login` | Auth | Auth.js pages |
| `/forgot-password` | `/forgot-password` | Auth | |
| `/register` | `/register` | Auth | |
| `/dashboard` | `/dashboard` | Protected | Server Component |
| `/customers` | `/customers` | Protected | Server: list |
| `/customers/:id` | `/customers/[id]` | Protected | Server: detail |
| `/customers/:id/edit` | `/customers/[id]/edit` | Protected | Client: form |
| `/suppliers` | `/suppliers` | Protected | |
| `/items` | `/items` | Protected | |
| `/quotations` | `/quotations` | Protected | |
| `/quotations/new` | `/quotations/new` | Protected | Client: form |
| `/quotations/:id` | `/quotations/[id]` | Protected | |
| `/quotations/:id/edit` | `/quotations/[id]/edit` | Protected | |
| `/invoices/sales` | `/invoices/sales` | Protected | |
| `/invoices/sales/new` | `/invoices/sales/new` | Protected | |
| `/invoices/sales/:id` | `/invoices/sales/[id]` | Protected | |
| `/invoices/sales/:id/edit` | `/invoices/sales/[id]/edit` | Protected | |
| `/invoices/purchases` | `/invoices/purchases` | Protected | |
| `/receipts` | `/receipts` | Protected | |
| `/payments` | `/payments` | Protected | |
| `/accounting/chart` | `/accounting/chart` | Protected | |
| `/accounting/journal` | `/accounting/journal` | Protected | |
| `/accounting/journal/new` | `/accounting/journal/new` | Protected | |
| `/accounting/journal/:id` | `/accounting/journal/[id]` | Protected | |
| `/accounting/taxes` | `/accounting/taxes` | Protected | |
| `/reports` | `/reports` | Protected | Reports Center |
| `/reports/trial-balance` | `/reports/trial-balance` | Protected | Server: aggregation |
| `/reports/balance-sheet` | `/reports/balance-sheet` | Protected | |
| `/reports/profit-loss` | `/reports/profit-loss` | Protected | |
| `/reports/cash-flow` | `/reports/cash-flow` | Protected | |
| `/reports/general-ledger` | `/reports/general-ledger` | Protected | |
| `/reports/account-statement` | `/reports/account-statement` | Protected | |
| `/reports/sales-by-customer` | `/reports/sales-by-customer` | Protected | |
| `/reports/sales-by-product` | `/reports/sales-by-product` | Protected | |
| `/reports/overdue-sales-invoices` | `/reports/overdue-sales-invoices` | Protected | |
| `/reports/unpaid-sales-invoices` | `/reports/unpaid-sales-invoices` | Protected | |
| `/reports/vat` | `/reports/vat` | Protected | |
| `/audit-log` | `/audit-log` | Protected | |
| `/settings` | `/settings` | Protected | |
| `/cost-centers` | `/cost-centers` | Protected | |
| `/projects` | `/projects` | Protected | |
| `/branches` | `/branches` | Protected | |
| `/bank-accounts` | `/bank-accounts` | Protected | |
| `/document-templates` | `/document-templates` | Protected | |
| `/employees` | `/employees` | Protected | Foundation |
| `/inventory` | `/inventory` | Protected | Foundation |
| `/fixed-assets` | `/fixed-assets` | Protected | Foundation |
| `/credit-notes` | `/credit-notes` | Protected | Foundation |
| `/debit-notes` | `/debit-notes` | Protected | Foundation |
| `/sales-orders` | `/sales-orders` | Protected | Foundation |
| `/purchase-orders` | `/purchase-orders` | Protected | Foundation |
| `/bank-reconciliation` | `/bank-reconciliation` | Protected | Foundation |
| `/demo-checklist` | ❌ Remove | — | Demo-only |
| `/system-data-mode` | ❌ Remove | — | Demo-only |

**Total**: 68 routes ported, 2 removed, ~4 consolidated.

---

## 8. Hostinger Deployment Plan

### Hostinger Setup

| Setting | Value |
|---------|-------|
| Hosting type | Business / Cloud (Node.js support) |
| Node version | 22.x LTS |
| Framework | Next.js |
| Build command | `npm run build` |
| Start command | `npm start` (Next.js built-in server) |
| Output directory | `.next/` |
| Environment variables | Set via Hostinger dashboard |
| Domain | `jaadcloud.com` (or custom subdomain) |
| SSL | Auto via Let's Encrypt |
| Preview/Staging | Separate subdomain or branch |

### GitHub Integration

1. Push to `main` (production) or `staging` branch
2. Hostinger auto-deploy via GitHub integration
3. Build triggers on push
4. Rollback via Hostinger dashboard (previous deployment)

### Environment Variables

```
NODE_ENV=production
DATABASE_URL=postgresql://...
AUTH_SECRET=...
AUTH_URL=https://...
NEXT_PUBLIC_APP_URL=https://...
NEXT_PUBLIC_SUPPORT_EMAIL=...
```

### Deployment Flow

```
Developer pushes to main
  → GitHub triggers Hostinger webhook
  → Hostinger clones repo
  → npm ci
  → npm run build
  → npm start (Next.js server)
  → Health check → Route traffic
```

### Monitoring

- Hostinger dashboard for basic monitoring
- Sentry or similar for error tracking (future)
- Database backup schedule (automated via PG provider)

---

## 9. Branch Strategy

### Current State

```
phase-1-migration-audit (demo baseline) ← ACTIVE
  └── v0.1.0-demo-baseline (tag)
master (stale local, 4 commits behind)
```

### Proposed Strategy

```
main                          # Production branch (after migration complete)
  └── v1.0.0 (tag — first production release)

nextjs-migration              # Conversion branch — branched from v0.1.0-demo-baseline
  └── Phase 2.1–2.11 commits

phase-1-migration-audit       # Frozen, never deleted — reference only
  └── v0.1.0-demo-baseline (tag — frozen point)

staging                       # Optional: pre-production verification
hostinger-deploy              # Optional: deployment-specific config
```

### Key Decisions

1. **Start migration from `v0.1.0-demo-baseline` tag**, not from `phase-1-migration-audit` HEAD — this ensures a clean known starting point
2. **Create `nextjs-migration` branch** from the tag
3. **Do NOT delete `phase-1-migration-audit`** — keep as reference
4. **`main` branch** = production. Created only when migration is complete and tested
5. **No merge** between demo and production branches — they are independent projects sharing component source

### Recommendation: New Repo vs Same Repo

**Option A: Same repo, new branch** (✅ Recommended)
- Pros: Shared history, single source of truth, references to demo code available
- Cons: Large repo, temptation to merge instead of rewrite

**Option B: New repo** (⚠️ Backup)
- Pros: Clean start, no Lovable artifacts
- Cons: Lose git history, harder to reference demo code patterns

**Decision**: Same repo (`jaadcloud`), branch `nextjs-migration` from tag `v0.1.0-demo-baseline`.

---

## 10. Production Phase Plan

### Phase 2.1: Create Next.js Production Shell

| Aspect | Detail |
|--------|--------|
| **Goal** | Initialize Next.js app with all dependencies, design system, and layout |
| **Action** | `npx create-next-app@latest jaadcloud --typescript --tailwind --eslint --app --src-dir` |
| **Key files** | `next.config.ts`, `tailwind.config.ts`, `src/app/layout.tsx`, `src/app/page.tsx` |
| **Components ported** | shadcn/ui init, AppShell, Sidebar, all `components/ui/*` |
| **Risks** | Tailwind v4 vs v3 compatibility. shadcn/ui defaults to v3 |
| **Success** | Build passes, AppShell renders with sidebar, RTL works |

### Phase 2.2: Port Design System + Layout

| Aspect | Detail |
|--------|--------|
| **Goal** | Port all shared components: layout, navigation, dialog, table, form |
| **Key files** | `components/shared/*`, `components/ui/*` |
| **Components** | AppShell, Sidebar, DataTable, ConfirmDialog, EmptyState, StatusBadge, PermissionGate, SmartEntityCombobox |
| **Risks** | Sidebar menu config + routing differences |
| **Success** | All shared components render without errors |

### Phase 2.3: Database Schema + Migrations

| Aspect | Detail |
|--------|--------|
| **Goal** | Design and apply PostgreSQL schema via Drizzle |
| **Action** | `npm i drizzle-orm postgres` + `npm i -D drizzle-kit` |
| **Key files** | `src/db/schema.ts`, `src/db/migrations/`, `src/lib/db.ts` |
| **Risks** | Schema must match accounting invariants. Migration order matters |
| **Success** | `drizzle-kit push` succeeds, all tables created |

### Phase 2.4: Auth + Multi-Tenancy

| Aspect | Detail |
|--------|--------|
| **Goal** | Auth.js integration with tenant model |
| **Action** | `npm i next-auth@beta` + configure email/password adapter |
| **Key files** | `src/app/api/auth/[...nextauth]/route.ts`, `src/features/auth/middleware.ts`, `src/lib/middleware.ts` |
| **Risks** | TanStack demo uses mock auto-login — production needs real flow |
| **Success** | Login, register, middleware guard all working with tenant context |

### Phase 2.5: Accounting Core Services

| Aspect | Detail |
|--------|--------|
| **Goal** | Rewrite AccountingEngine, JournalService, AuditService |
| **Key files** | `src/server/services/accounting-engine.ts`, `src/server/services/journal-service.ts` |
| **Risks** | Immutability enforcement, double-entry invariants, concurrent posting |
| **Success** | Journal posting balances, audit log records every action, reversal flow works |

### Phase 2.6: Sales Cycle (Quotations → Invoices → Receipts)

| Aspect | Detail |
|--------|--------|
| **Goal** | Full sales document lifecycle with DB persistence |
| **Key files** | `src/features/invoices/sales-actions.ts`, `src/components/documents/SalesInvoiceEditor.tsx` |
| **Risks** | State transitions (draft → posted → paid), receipt auto-apply |
| **Success** | Create quotation → issue → convert to invoice → issue → receive payment → GL posted |

### Phase 2.7: Purchasing + AP/AR

| Aspect | Detail |
|--------|--------|
| **Goal** | Purchase invoices, payments, supplier aging |
| **Key files** | `src/features/invoices/purchase-actions.ts`, `src/features/invoices/queries.ts` |
| **Risks** | AP aging calculation, payment allocation |
| **Success** | Purchase invoice → post → pay → GL updated → supplier balance correct |

### Phase 2.8: Chart of Accounts + Reports

| Aspect | Detail |
|--------|--------|
| **Goal** | Hierarchical COA + 11 reports using DB aggregation |
| **Key files** | `src/server/services/report-service.ts` |
| **Risks** | Trial balance performance with large datasets. Hierarchical COA queries |
| **Success** | All 11 reports produce correct numbers matching demo data |

### Phase 2.9: Taxes + ZATCA Readiness

| Aspect | Detail |
|--------|--------|
| **Goal** | VAT calculation, QR code generation, ZATCA Phase 1 |
| **Key files** | `src/server/services/tax-service.ts` |
| **Risks** | Tax rate selection at line-item level, rounding, ZATCA XML format |
| **Success** | Invoice QR code scans correctly, VAT report matches ZATCA requirements |

### Phase 2.10: Hostinger Staging Deploy

| Aspect | Detail |
|--------|--------|
| **Goal** | Deploy Next.js app to Hostinger staging environment |
| **Action** | Configure Hostinger GitHub integration, set env vars, first deploy |
| **Risks** | Node 22.x availability on Hostinger, environment variable security |
| **Success** | Staging URL returns working app with real database |

### Phase 2.11: Production Hardening

| Aspect | Detail |
|--------|--------|
| **Goal** | Security audit, backup strategy, monitoring, documentation |
| **Actions** | Sentry setup, load testing, backup automation, runbook creation |
| **Risks** | Production data loss, security misconfiguration |
| **Success** | Production URL live, SSL working, backups scheduled, monitoring alerts active |

---

## 11. Migration Risks

| Risk | Severity | Mitigation |
|------|----------|------------|
| **Routing differences** — TanStack nested routes vs Next.js App Router | Medium | Use parallel routes / groups where needed. Most routes are flat (74 pages, few nested layouts) |
| **LocalStorage → DB gap** — Demo has no real data model | High | Schema designed from scratch. Demo data model informs but doesn't dictate DB schema |
| **Accounting consistency** — Double-entry, immutability, reversal | High | Design immutable journal entries. `idempotency_key` for posting. DB constraints enforce balance |
| **Journal posting concurrent access** — Two users post at same time | Medium | DB transactions + row-level locking on journal sequence |
| **Invoice locking** — Posted invoice must not be editable | High | State machine in DB: `draft → posted → (if void, reverse via credit note)`. Server-side checks |
| **ZATCA Phase 2** — Future XML e-invoicing integration | Medium | Keep tax service extensible. Abstract QR generation. Plan for XML signing |
| **Permissions** — 4 roles across all routes | Medium | Middleware guard + Server Action wrapper. Test each role against each action |
| **DB migrations** — Schema changes during active dev cycle | Medium | Use Drizzle migration files. Review before applying to production |
| **Hostinger Node 22.x** — Not all hosts support latest Node | Medium | Check Hostinger docs. Use `.nvmrc`. Fallback to Node 20.x if needed |
| **Environment variables** — Secrets management | Medium | `.env.example` with placeholders. Hostinger dashboard for production vars. Never commit `.env` |
| **Lovable/Supabase cleanup** — Artifacts remain in repo history | Low | Not urgent. Clean up after migration is stable. Git history rewrite not recommended |
| **Prettier noise** — 4167 formatting issues in demo code | Low | Ignore during migration. New code follows Prettier from day one |
| **RTL direction** — Arabic layout | Low | Tailwind RTL support is mature. Test in all layouts |
| **Client vs Server Component boundaries** — Wrong `'use client'` placement | Medium | Strategy: AppShell + sidebar = client. Data display = server. Forms = client. Reports = server with client chart libs |

---

## 12. Key Recommendations Summary

1. **Create Next.js app from scratch** — Do not try to convert TanStack project in-place. Start fresh with `create-next-app`.
2. **Port components, rewrite services** — UI components transfer mostly as-is. Business logic is rewritten for DB.
3. **Drizzle ORM + PostgreSQL (Neon)** — Best fit for accounting SaaS. Drizzle's raw SQL compatibility is essential for complex reports.
4. **Auth.js (NextAuth v5)** — Mature, Next.js-native, supports email/password + OAuth.
5. **Multi-tenant via `organization_id`** — Simple column-based isolation. No schema-per-tenant complexity.
6. **Immutable journal entries** — Once posted, entries are append-only. Corrections via reversal.
7. **Server Actions for mutations** — Simpler than API Routes for this use case. API Routes only for webhooks.
8. **Same repo, new branch** — `nextjs-migration` from `v0.1.0-demo-baseline` tag.
9. **11-phase migration** — Each phase is independently testable and deployable.
10. **Keep demo branch permanently** — Reference for behavior and test data.

---

## 13. Technology Dependencies (Target)

```json
{
  "next": "^15.x",
  "react": "^19.x",
  "react-dom": "^19.x",
  "@auth/core": "^0.x",
  "next-auth": "^5.x (beta)",
  "drizzle-orm": "^0.x",
  "postgres": "^3.x",
  "tailwindcss": "^4.x",
  "typescript": "^5.x",
  "zod": "^3.x",
  "recharts": "^2.x",
  "sonner": "^2.x",
  "lucide-react": "^0.x",
  "date-fns": "^4.x",
  "qrcode": "^1.x",
  "js-cookie": "^3.x"
}
```

---

## 14. What NOT to Do

- ❌ Do not keep `DATA_MODE` or `BACKEND_WRITES_ENABLED` in production
- ❌ Do not keep `LocalStorageDataAdapter` or `FutureBackendDataAdapter`
- ❌ Do not port the `Demo Checklist` or `System Data Mode` pages
- ❌ Do not keep mock auto-login
- ❌ Do not keep `/demo-checklist` or `/system-data-mode` routes
- ❌ Do not use Lovable deployment (Nitro/SSR) for production
- ❌ Do not reference Supabase service_role_key from browser
- ❌ Do not use `@tanstack/react-router` in the new app
- ❌ Do not attempt incremental migration of the same project
