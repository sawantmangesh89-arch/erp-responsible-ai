# Deploy to GitHub + Render + Namecheap

This project is prepared for GitHub-based deployment to Render Free with a custom domain from Namecheap.

## 1) Push code to GitHub

Create an empty GitHub repository first (do not initialize it with README or .gitignore), then run:

```powershell
git init -b main
git add .
git commit -m "Prepare Streamlit app for Render deployment"
git remote add origin <YOUR_GITHUB_REPO_URL>
git push -u origin main
```

If this folder is already a Git repository, skip `git init` and just commit + push.

## 2) Deploy on Render (Free)

This repo includes `render.yaml`, so Render can create the service from code.

1. In Render Dashboard, click `New` -> `Blueprint`.
2. Connect your GitHub account and select this repository.
3. Render reads `render.yaml` and creates the web service.
4. Wait for first deploy to become `Live`.

Default app entrypoint in `render.yaml`:

```text
voice_erp_coach.py
```

If you want to run `responsible_erp_app.py` instead, update `startCommand` in `render.yaml` and push again.

## 3) Add custom domain from Namecheap

In Render service settings:

1. Open `Settings` -> `Custom Domains`.
2. Add:
   - `askyacham.com`
   - `www.askyacham.com`

In Namecheap `Advanced DNS`:

1. Remove conflicting old records for `@` and `www`.
2. Add `A` record:
   - Host: `@`
   - Value: `216.24.57.1`
3. Add `CNAME` record:
   - Host: `www`
   - Value: `<your-service>.onrender.com`
4. Remove any `AAAA` records for root/www to avoid verification issues.
5. Back in Render, click `Verify` on both domains.

Render will provision TLS certificates automatically after DNS verification.

## 4) Security checklist

1. Keep `JWT_SIGNING_KEY` as an environment variable (already configured in `render.yaml` with generated value).
2. Never commit secrets files. `.gitignore` already excludes:
   - `.streamlit/secrets.toml`
   - `streamlit/secrets.toml`
3. Do not commit local DB and logs (`*.db`, `*.log` are ignored).
4. Use private GitHub repo unless public access is required.

## 5) Important free-tier behavior

Render Free web services spin down after inactivity and can take up to about a minute to wake on the next request. This is expected behavior on Free.

## 6) Persistence note (recommended)

This app currently uses local SQLite files. On free hosting, local disk should be treated as non-durable. If you need reliable long-term user data, move storage to a managed external database.
