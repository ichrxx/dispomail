# Webmail Client

A webmail reader that fetches emails for any address at your custom domain via Yopmail's mail infrastructure.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/webmail run dev` — run the frontend (port 23342)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5
- Frontend: React + Vite, Tailwind CSS, TanStack Query
- Email fetching: axios + cheerio (scraping Yopmail)
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract (source of truth)
- `lib/api-client-react/src/generated/` — generated React Query hooks
- `lib/api-zod/src/generated/` — generated Zod schemas for the server
- `artifacts/api-server/src/lib/yopmail.ts` — Yopmail scraping service
- `artifacts/api-server/src/routes/mail.ts` — inbox and email routes
- `artifacts/webmail/src/pages/inbox.tsx` — main UI page

## Architecture decisions

- Backend proxies all Yopmail requests (avoids CORS, hides scraping complexity from frontend)
- Token is fetched fresh per request from yopmail.com (no session persistence needed)
- Email body rendered in `<iframe srcdoc>` on frontend for safe HTML email display
- Frontend polls inbox every 30 seconds via `refetchInterval`

## Product

- Enter any email address at your connected subdomain in the sidebar
- View your inbox — from, subject, date, read/unread status
- Click any email to read it in the full reading pane
- Auto-refreshes every 30 seconds

## Setup for custom domain

To use your own subdomain with this webmail:
1. Go to your DNS provider and set an MX record:
   - Name: `yourdomain.com` (or `subdomain.yourdomain.com`)
   - Value: `mx1.yopmail.com` (priority 10)
   - Value: `mx2.yopmail.com` (priority 20)
2. Wait for DNS propagation (up to 48 hours)
3. Any email sent to `anything@yourdomain.com` is now accessible via this webmail

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Yopmail's HTML structure may change — the parser has multiple selector fallbacks
- Yopmail does not require authentication; any email at a yopmail-connected domain is publicly readable by anyone who knows the address
- Do NOT use this for sensitive emails — Yopmail is a disposable mail service

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
