# OpenPT Environment Notes For Agents

Check this file before deployment, server work, or Vercel verification.

## Versioning

- The visible frontend version is defined in `app.jsx` as `OPENPT_VERSION`.
- Bump that value before deployment tests that need to prove Vercel is serving the newest push.
- Current expected version: `0.2.4-sync.20260518`.
- Vercel redeploys automatically after pushes to `main`.
- When testing Vercel, verify the visible title-bar version before testing behavior.
- Vercel serves the static frontend on `https://openpt.dev`. The prepared client
  default is `https://openptapi.skylar.sh`; verify its route before deployment.

## GitHub

- Repository: `https://github.com/skylarenns/OpenPT.git`
- Production branch for this project: `main`
- User currently expects direct pushes to `main` for this MVP.

## Remote Server

- SSH target: `skylarenns@192.168.1.174`
- Password location on this Mac: `~/Desktop/192.168.1.174.rtf`
- Do not commit, paste, print, or summarize the password.
- Requested remote project parent: `/Documents/GitHub/`
- Actual server path in use: `/home/skylarenns/Documents/GitHub/OpenPT`

## Remote Service

- Service name: `openpt.service`
- App command: `npm run dev`
- Default local port on the remote server: `5173`
- Data directory default: `<repo>/.openpt-data`
- Target API hostname: `openptapi.skylar.sh`; legacy `openptapi.skylarenns.com` remains available during migration.
- Add a route on the existing Cloudflare Tunnel mapping `openptapi.skylar.sh` to
  `http://127.0.0.1:5175` (the systemd `port.conf` override).
- The backend CORS allowlist must include the public frontend origin
  `https://openpt.dev`.
- The public tunnel uses HTTPS, so the remote service should set
  `OPENPT_SECURE_COOKIES=1`.
- Spam proofing is handled in-process by `server/abuse-guard.mjs`: Cloudflare-aware
  IP detection, auth/project/share/save throttles, retry-after responses, and a
  hidden signup honeypot field. Restarting the service resets in-memory counters.
- Durable storage is local SQLite plus `.openpt-data/objects`. Use
  `npm run storage:backup`, `npm run storage:cleanup:dry-run`,
  `npm run storage:cleanup`, and `npm run storage:restore -- --backup <id>` for
  production maintenance.
- Optional storage env vars:
  `OPENPT_USER_BYTES_LIMIT`, `OPENPT_PROJECT_BYTES_LIMIT`,
  `OPENPT_MIN_SAVE_INTERVAL_MS`, `OPENPT_LEASE_TTL_MS`, and
  `OPENPT_ADMIN_TOKEN`.
- Account email links should set `OPENPT_PUBLIC_URL` to the public frontend
  origin, for example `https://openpt.dev`.
- Account verification and password reset email use `OPENPT_ACCOUNT_SMTP_*`
  first, then the feedback/report SMTP env names as fallbacks. Only set
  `OPENPT_ACCOUNT_EMAIL_DEBUG=1` in local test/dev contexts; debug mode returns
  account tokens in API responses.
- `POST /api/admin/restore` only writes a pending restore request; the restore is
  applied on the next server start before the live database is opened.

## Local Verification

```sh
npm install
npm test
node --check server/index.mjs
```

Start locally:

```sh
npm run dev
```

## skylar.sh migration

See [DOMAIN-MIGRATION.md](DOMAIN-MIGRATION.md). Retain openptapi.skylarenns.com as a compatibility route; keep the public frontend and email links on openpt.dev. Verify the new API route before deploying the updated frontend default.
