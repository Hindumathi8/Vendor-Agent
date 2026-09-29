# VendorIQ – Vendor Issue & Resolution Intelligence Agent (MERN)
## Setup
Requires Node 18+ and MongoDB running locally.
```
brew services start mongodb-community   # macOS
cp server/.env.example server/.env   # set JWT_SECRET
cp client/.env.example client/.env   # VITE_API_URL=http://localhost:5001 (dev proxy target)
npm install      # installs root, server and client
npm run seed     # demo data
npm run dev      # API :5001, UI :5173
npm test         # retrieval unit tests
```
Demo logins (Password123!): admin@demo.com · manager@demo.com · employee@demo.com
## How the AI works
1. `server/ai/similarity.js` – synonym-normalised TF-IDF cosine retrieval over `HistoricalCase` (same-type/vendor boost, min threshold; returns nothing rather than inventing cases).
2. `server/ai/recommend.js` – if `ANTHROPIC_API_KEY` is set, an LLM reasons over ONLY the retrieved cases (supporting IDs are validated against retrieval). Otherwise recommendations are derived from the retrieved cases' actual resolutions. No cases ⇒ explicit "not evidence-based" output.
3. `POST /api/issues/:id/decide` – human Approve/Modify/Reject is mandatory; approval writes a `Resolution` and a new `HistoricalCase`.
## API (all under /api, Bearer JWT except auth)
POST auth/register, auth/login · GET auth/me · GET/POST vendors, GET/PUT/DELETE vendors/:id, GET vendors/:id/risk · GET/POST issues, GET/PATCH issues/:id, POST issues/:id/comments|reanalyze|decide · GET cases, dashboard, search?q= · GET notifications, POST notifications/read · GET reports/issues.csv · Admin: GET admin/users, PATCH admin/users/:id, GET admin/audit
## Notes / not yet built
Reports page UI (CSV endpoint exists), global search UI (endpoint exists), file-upload storage (attachments field only), overdue notification cron, frontend tests.

## Ports
Frontend http://localhost:5173 → (Vite proxy `/api`, target from `client/.env` `VITE_API_URL`) → API http://localhost:5001 → MongoDB 127.0.0.1:27017/vendor_agent. Health check: `GET /api/health`.
# Vendor-Agent
