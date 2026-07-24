# Order Until Shiok

A Singapore hawker-themed "dopamine site" — browse hawker classics, customise your
spice level, "pay" with a fake NETS card, and track a live courier crossing the
void deck. No real food, no real payment.

Static site — a single `index.html`, no build step, no dependencies — plus one
serverless function (`api/nearby.js`) that proxies Google Places API for the
"food near me" feature, so your API key never reaches the browser.

## "Food near me" setup (Google Places API)
1. In Google Cloud Console, enable **"Places API (New)"** for a project and
   create an API key.
2. Restrict the key (Application restrictions -> None needed since it's only
   ever called server-side now; API restrictions -> limit to Places API (New)
   for safety).
3. In your Vercel project: **Settings -> Environment Variables**, add:
   - Name: `GOOGLE_PLACES_API_KEY`
   - Value: your key
   - Apply to Production (and Preview if you want it working on preview URLs)
4. Redeploy. The site calls `/api/nearby?lat=...&lon=...`, which runs
   `api/nearby.js` on Vercel's servers, calls Google with the hidden key, and
   returns just the place data to the browser.

No billing surprises expected for normal testing — Google gives $200/month
free credit, which covers a large number of Nearby Search calls for a small
site like this.

## Deploy to Vercel

### Option A — GitHub + Vercel (recommended, matches your usual flow)
1. Create a new repo on GitHub (e.g. `order-until-shiok`) and push this folder:
   ```
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/<your-username>/order-until-shiok.git
   git push -u origin main
   ```
2. Go to https://vercel.com/new, import the GitHub repo.
3. Framework preset: "Other" (no build command needed — it's static).
4. Deploy. Vercel will serve `index.html` at the root automatically.

### Option B — Vercel CLI, no GitHub needed
```
npm i -g vercel
cd order-until-shiok
vercel
```
Follow the prompts (link or create a project) and it deploys straight from your
machine.

### Option C — Drag and drop
Go to https://vercel.com/new, and drag this folder onto the page. No CLI, no
GitHub required — good for a quick one-off deploy.

## Notes
- Everything (cart, order history, streak, courier collection) is stored in
  the visitor's `localStorage` — there's no backend or database.
- Dish photos are hotlinked from Wikimedia Commons. If you want them to survive
  independently of Commons (or want to swap any of them), download the images
  into an `/images` folder and update the `img` paths in the `DISHES` array
  inside `index.html`.
