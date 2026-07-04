# Deploying World Monitor on Railway (for the ViperChart embed)

One Railway service runs the whole app: the Dockerfile bundles nginx
(frontend) + the API server under supervisord. Frame headers already allow
embedding from ViperChart (nginx sends `frame-ancestors 'self'
viperchart.up.railway.app *.up.railway.app` instead of X-Frame-Options).

## Steps (Railway dashboard)

1. **New Project → Deploy from GitHub repo** → pick `2betafactor/worldmonitor-k`.
2. In the service settings, set **Builder: Dockerfile** (root `Dockerfile`).
   ⚠ If Railway auto-picks nixpacks it will build the AIS relay instead —
   the root `nixpacks.toml` is the relay's config, not the app's.
3. **Redis** (needed for seeded data): easiest is a free Upstash database →
   set env vars on the service:
   - `UPSTASH_REDIS_REST_URL`
   - `UPSTASH_REDIS_REST_TOKEN`
   (Alternative: a second Railway service from `docker/Dockerfile.redis-rest`
   + Railway Redis, mirroring docker-compose.yml.)
4. Optional API keys (see SELF_HOSTING.md) — the app works with public
   sources without any.
5. **Networking → Generate Domain** → you get `https://<name>.up.railway.app`.
6. In **ViperChart's** Railway service env, set
   `NEXT_PUBLIC_WORLDMONITOR_URL=https://<name>.up.railway.app` and redeploy —
   the World Monitor tab switches from launcher to full embedded mode.

## Notes
- `vercel.json` headers do NOT apply on Railway; the Docker nginx config is
  what serves headers here (already embed-ready).
- If ViperChart ever moves to a custom domain, add it to the
  `frame-ancestors` list in `docker/nginx.conf`.
