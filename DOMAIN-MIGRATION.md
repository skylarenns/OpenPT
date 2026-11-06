# API domain migration to skylar.sh

This change prepares the API default `https://openptapi.skylar.sh`; it does not prove a production cutover. The public frontend remains `https://openpt.dev`. Retire only the `openpt.skylarenns.com` frontend alias after checking its traffic; preserve its Vercel project, API service, database and object storage.

1. Preserve Cloudflare Access applications/policies and DNS snapshots. Add the new API hostname to the existing Main tunnel with the same origin `http://127.0.0.1:5175`, keeping `openptapi.skylarenns.com`. Port 5175 comes from the live systemd override; the application default is 5173.
2. Confirm DNS/TLS, API responses and credentialed CORS from openpt.dev on the new route before deploying this frontend default. Preserve the existing OPENPT_ALLOWED_ORIGINS list, including transitional old frontend origins. No new frontend origin is required.
3. Preserve OPENPT_PUBLIC_URL=https://openpt.dev for account email links, OPENPT_SECURE_COOKIES, cookie secrets, SMTP configuration and persistent state. Never replace email addresses or SMTP hostnames as a side effect of this migration.
4. Build and deploy the static frontend through its existing Vercel project. `window.OPENPT_API_BASE` and localStorage `openpt:api-base` overrides still take precedence; old overrides continue to work while the legacy API remains.
5. Verify login, CSRF, project read/write and email links using a disposable account. API cookies belong to each hostname: users may need to sign in on the new API; old sessions remain available through the old API. Do not delete legacy routes during this rollout.

Run `npm test` and `npm run build`. Verify both API hosts, allowlisted frontend Origin and a rejected foreign Origin. The sync-client tests cover new API defaults, both same-origin API aliases and explicit overrides. Before using the updated SMTP setup helper, verify `ssh.skylar.sh` connects to Main; the old SSH endpoint remains a rollback path.

Rollback the frontend artifact/default or set OPENPT_API_BASE to the old API while retaining both routes. No database migration is involved. Actual deployment, authenticated smoke tests, and old-alias retirement must be recorded separately; source changes alone do not complete them.
