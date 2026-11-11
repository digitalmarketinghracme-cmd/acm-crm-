# Deployment guide (single server, HTTPS)

## What you need
- A small Linux VPS (1 vCPU / 1 GB RAM is enough) with Docker and the Docker Compose plugin.
- A domain name (e.g. `crm.acmeswitchgears.in`) with an **A record** pointing to the server's IP.
- Ports 80 and 443 open in the firewall.

## 1. Configure
```bash
cp .env.example .env
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"   # paste into SESSION_SECRET
```
Edit `.env`: set `SESSION_SECRET`, `ADMIN_PASSWORD` (12+ characters, used only on first start), and `DOMAIN`.
In production the app **refuses to start** if the secret is weak or any user still has the default password.

## 2. Start
```bash
docker compose up -d --build
docker compose logs -f app
```
Caddy obtains and renews the HTTPS certificate automatically. Open `https://<your-domain>` and sign in as `admin`.
Health check: `https://<your-domain>/healthz`.

## 3. Backups (do this before real use)
```bash
docker compose exec app npm run backup        # writes to ./backups on the host
```
Schedule it daily with cron on the host:
```
0 2 * * * cd /opt/acme-crm && docker compose exec -T app npm run backup
```
Then copy `./backups` off the server (object storage, another machine, etc.). **Test a restore:** stop the app, replace the
`acme.sqlite` file in the `crm-data` volume with a backup, start the app.

## 4. Operations
- Change a password: `docker compose exec app npm run set-password -- admin "new-long-password"`
- Update the app: replace the files, then `docker compose up -d --build`
- Logs: `docker compose logs app`

## Without Docker
Install Node 20+, run `npm install --omit=dev`, set the variables from `.env.example` in the environment (or `.env`),
run `node src/server.js` under systemd or pm2, and put nginx/Caddy in front for HTTPS (keep `TRUST_PROXY=1`).

## What was hardened
Production config checks, secure/HttpOnly cookies, persistent SQLite-backed sessions, session regeneration on login,
login and API rate limiting, CSRF protection (custom header + origin check), security headers (CSP, HSTS, etc.),
CORS off by default, no prefilled login credentials, graceful shutdown, health endpoint, safe error handling.

## Still recommended before heavy business use
- Multiple users and roles (only one admin account exists today) and role checks on the API.
- Server-side validation of quotation inputs, and validation on PUT endpoints.
- Remove inline event handlers in the UI so the CSP can drop `'unsafe-inline'` for scripts.
- Have your accountant verify GST calculations (intra-state CGST/SGST vs inter-state IGST) before issuing documents.
- Single-instance only: the rate limiter is in-memory and SQLite is a single-file database.
- This build was syntax-checked but could not be run end-to-end here (no network to install packages), so do a full smoke test
  (login, create customer/product/quotation, download PDF, restart and confirm you stay logged in) on a staging copy first.
