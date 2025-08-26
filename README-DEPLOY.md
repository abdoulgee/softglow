# Softglow – Quick Deploy to Render + GitHub

## One-time setup
1. Install Git and Python 3.10+.
2. Create a GitHub repo (empty).

## Prepare project
```bash
cd /path/to/your/softglow_extracted
# create virtual env (optional for local dev)
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r softglow/requirements.txt
# local env (sandbox keys recommended)
cp .env.example softglow/.env
python softglow/app.py  # dev run on http://localhost:5001
```

## Commit to GitHub
```bash
git init
git add .
git commit -m "Initial commit: softglow + render blueprint"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## Deploy to Render (without buying a domain yet)
1. Go to https://dashboard.render.com -> New -> **Blueprint**.
2. Pick your GitHub repo (it contains `render.yaml`).
3. When prompted for env vars with `sync: false`, paste your **sandbox** keys:
   - `SECRET_KEY`
   - `PAYPAL_CLIENT_ID`
   - `PAYPAL_SECRET`
4. Render will build and start at an `onrender.com` URL. Use that to test.
5. When ready for real payments, change `PAYPAL_API_BASE` to `https://api-m.paypal.com` and update credentials to your **live** keys.

## Start command in Render
We use gunicorn with the **app factory**:
```
gunicorn -w 2 -b 0.0.0.0:$PORT app:create_app()
```

## Notes
- The code uses `python-dotenv` to load `softglow/.env` **only in local dev**. On Render, set secrets as environment variables in the dashboard (no `.env` file needed).
- Never commit `softglow/.env`. The `.gitignore` already prevents that.
- To test PayPal end-to-end without charging real money, keep `PAYPAL_API_BASE` as the sandbox URL and use sandbox buyer/merchant accounts.
```