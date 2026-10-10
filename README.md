# Nitrix Trader PRO

Professional, mobile-friendly AI trading analysis and setup-tracking platform.

Built with **Next.js (App Router)**, server-side Gemini integration, and a shared-password access gate.

> **Important limitation:** The access password is a basic shared gate, **not** a complete multi-user identity system. Anyone who knows the password has full access. Do not share it publicly.

## Features

- **Password gate** — HTTP-only, SameSite cookie session; server-validated; rate-limited
- **Server-side Gemini** — API key never reaches the browser
- **Chart camera / upload** — JPEG, PNG, WebP; client-side compression
- **AI chart analysis** — structure, BOS/CHOCH, FVGs, order blocks, liquidity, R:R, invalidation
- **PRO setup tracker** — persistent records (IndexedDB), status lifecycle, AI conversation per setup
- **Risk calculator** — account risk %, R:R (position size requires instrument specs)
- **Trade journal** — searchable history of setups and outcomes
- **Mobile-first** — large touch targets, responsive tab bar

## Security notes

- `NITRIX_APP_PASSWORD` and `GEMINI_API_KEY` are **server-only** environment variables.
- Never use `NEXT_PUBLIC_` prefixes for secrets.
- Password is compared with timing-safe equality; sessions are HMAC-signed.
- Protected API routes return 401 when unauthenticated.
- Rate limiting on login (default 10 attempts / 15 min per IP, in-memory).
- Chart images are processed in memory and sent to Gemini from the server; they are not permanently stored on the server by default (previews live in IndexedDB on the device).

## Quick start (local)

```bash
cp .env.example .env.local
# Edit .env.local and set:
#   NITRIX_APP_PASSWORD=your-private-password
#   GEMINI_API_KEY=your-gemini-key
#   SESSION_SECRET=a-long-random-string

npm install
npm run dev
```

Open http://localhost:3000

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `NITRIX_APP_PASSWORD` | Yes | Shared access password |
| `GEMINI_API_KEY` | Yes (for AI) | Google AI Studio / Gemini API key |
| `GEMINI_MODEL` | No | Default `gemini-2.0-flash` |
| `SESSION_SECRET` | Recommended | HMAC secret for session cookies |
| `AUTH_RATE_LIMIT` | No | Login attempts per IP window (default 10) |

## Deploy to Vercel

1. Push the repo (without `.env*` files).
2. In Vercel project → Settings → Environment Variables, add the variables above (Production + Preview).
3. Deploy.
4. After deploy, verify:
   - Visiting the site shows the password gate.
   - Wrong password is rejected.
   - Correct password sets an `HttpOnly` cookie.
   - `/api/analyze` returns 401 without the cookie.
   - View page source / Network: no `GEMINI_API_KEY` or password appears in JS bundles or responses.

## Tests & verification

```bash
npm run build    # production build
npm run lint     # eslint
```

Manual checks (required):

1. Correct password → session cookie set.
2. Incorrect password → 401 + error message.
3. Logout clears cookie.
4. Unauthenticated call to `/api/analyze` → 401.
5. Missing `GEMINI_API_KEY` → clear 503 configuration message (no fake AI reply).
6. Image upload validation (type / size).
7. Setup persistence survives refresh (IndexedDB).
8. Status can be changed only to defined lifecycle values.

## Live market data

No live feed is connected out of the box. Synthetic indices, forex and metals require authorized broker / data-provider APIs with different symbol mappings. The UI clearly labels monitoring as manual / screenshot-based. Do not enable a “live” indicator until a verified feed is integrated.

## Remaining / optional integrations

- Durable multi-device server database (Vercel Postgres / Neon / Turso) — currently setups live in browser IndexedDB.
- Real-time price feeds and automatic zone proximity alerts.
- Broker order execution (explicitly out of scope; AI never places trades).
- Full unit-test suite (core auth & analyze routes are structured for easy testing).

## Disclaimer

Not financial advice. AI can misread charts. Always verify levels yourself. Past structure does not guarantee future results.
# Ai-trader-pro
