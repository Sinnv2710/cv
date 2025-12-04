# Portfolio — quick deploy and free domain

This repository contains a minimal static portfolio site (in the `docs/` folder) and instructions for deploying it for free using GitHub Pages or Netlify, plus how to attach a free domain from a provider such as Freenom.

Short options recap:

- Host for free: GitHub Pages, Netlify, or Vercel (all have free tiers). GitHub Pages is simplest for static sites inside `docs/`.
- Free domain providers: Freenom (offers .tk, .ml, .ga, .cf, .gq), or use a free subdomain from Netlify/Vercel/GitHub Pages.

I recommend: Start with GitHub Pages (docs/), and optionally add a Freenom domain and configure DNS.

---

## Files added

- `docs/index.html` — the portfolio landing page
- `docs/styles.css` — simple styling
- `SinNguyenVan_2026.pdf` — (your CV already in repository)

## Quick local preview

To preview locally you can use Python's simple HTTP server:

```bash
# from the repo root
cd docs
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deploy to GitHub Pages (docs folder)

1. Push this branch to GitHub (if not already pushed):

```bash
git add docs README.md
git commit -m "Add docs/ portfolio site"
git push origin portfolio
```

2. On GitHub: Settings → Pages → Source: choose branch `portfolio` and folder `/docs` → Save.

3. GitHub Pages will publish at `https://<your-user>.github.io/<repo>/` (or at a user site if configured). It usually takes a minute to become available.

4. Optional: to use a custom domain, add a `docs/CNAME` file with your domain (e.g. `example.tk`) and configure DNS records at your DNS provider (steps below).

## Deploy to Netlify (alternative)

1. Create an account at Netlify and click "New site from Git" → connect to GitHub → choose this repo.
2. Build command: none; publish directory: `docs`.
3. Netlify will provide a free subdomain `yoursite.netlify.app`. You can then add a custom domain in the Netlify dashboard.

### Configure this repo for Netlify

- I added a `netlify.toml` to this repo so Netlify will publish the `docs` folder and handle single-page fallback. You do not need a build command — just pick this repo and set publish directory to `docs` when Netlify asks.

### Quick Netlify deploy steps (copy/paste)

1. Log into Netlify and click "New site from Git" → Authorize GitHub if necessary.
2. Select the `Sinnv2710/cv` repo and the `portfolio` branch.
3. When Netlify asks for build settings:
  - Build command: leave blank
  - Publish directory: `docs`
4. Click Deploy site. After deployment, Netlify gives you `https://<random>.netlify.app`.

After the site is deployed, open the Site settings -> Domains to add a custom domain, and Netlify will show the required DNS entries.

---

## Free domain + Netlify (Freenom)

Here are two good approaches to attach a Freenom domain to Netlify.

Option A — Use Netlify DNS (recommended if you want simplicity):

1. In the Netlify dashboard for your site, go to Domain settings → Add custom domain → Add the domain you registered at Freenom (e.g. `myportfolio.tk`).
2. Netlify will suggest using Netlify DNS: it will give a set of nameservers (e.g. `dns1.p05.nsone.net`, `dns2.p05.nsone.net`, etc.).
3. At Freenom: Services → My Domains → Manage Domain → Management Tools → Nameservers, set the nameservers to Netlify's values and save. (This hands DNS control to Netlify and is the easiest way to get HTTPS.)
4. Wait up to a few minutes for nameserver changes to propagate. Netlify will automatically request and provision an HTTPS certificate after DNS is verified.

Option B — Keep Freenom DNS and add Netlify records manually (when you can't change nameservers):

1. In Netlify, after adding the custom domain, Netlify will show the records you must create at Freenom.
  - Usually you add a CNAME `www` that points to `<your-site>.netlify.app`.
  - For the apex (root) domain, Netlify may ask for A records or an ALIAS. Freenom doesn’t support ALIAS; the practical workaround is:
    - Point `www` → CNAME to `<your-site>.netlify.app` and use forwarding/redirect from the apex root (Freenom provides forwarding) to `www.yourdomain.tk`.
2. After adding the recommended records, return to Netlify and verify DNS. Netlify will issue an HTTPS certificate once DNS is correct.

Notes & troubleshooting:
- DNS propagation can take minutes to hours. Use `dig +short yourdomain.tk` and `dig +short www.yourdomain.tk` or online checkers like dnschecker.org.
- If using Netlify DNS (nameservers), Netlify automates certificate issuance and renewals — easiest and most reliable.

### Freenom → Netlify detailed step-by-step

Example: you registered `myportfolio.tk` at Freenom and you want Netlify to host and provide HTTPS.

1) In Netlify – add your domain
  - Open your Netlify site → Site settings → Domains → Add custom domain → enter `myportfolio.tk` → click Save.
  - Netlify will show a recommended action: either (A) Change nameservers to Netlify DNS or (B) Add DNS records at your current DNS provider.

2) Option A: change nameservers at Freenom (recommended)
  - In Freenom, open Services → My Domains → Manage Domain → Management Tools → Nameservers.
  - Choose "Use custom nameservers" and copy exactly the Netlify nameservers shown in the Netlify dashboard (e.g. `dns1.p05.nsone.net`, `dns2.p05.nsone.net`, ...). Save.
  - After nameserver change propagates, Netlify will automatically verify and provision an HTTPS certificate.

3) Option B: keep Freenom DNS and add records yourself
  - In Freenom, open Services → My Domains → Manage Domain → Management Tools → Manage Freenom DNS.
  - Add a CNAME record for `www` with value: `<your-site>.netlify.app` (Netlify will show exact site hostname).
  - For apex root (`myportfolio.tk`): Freenom often can't create ALIAS/ANAME. Workarounds:
    - Use Freenom's URL forwarding: forward the root `myportfolio.tk` to `https://www.myportfolio.tk` and set `www` CNAME to Netlify (this is the simplest workaround).
    - (Advanced) Use a third-party DNS provider that supports ANAME/ALIAS or use Netlify DNS nameservers.

4) Verify DNS and HTTPS
  - Use `dig` to check records (replace `example.tk` with your domain):

```bash
dig +short NS example.tk
dig +short CNAME www.example.tk
dig +short A example.tk
```

  - Once Netlify reports the domain verified and shows HTTPS enabled in the Domains panel, test in the browser:

```bash
curl -I https://www.example.tk
# Expect a 200 or a 301 redirect depending on settings
```

If anything looks stuck, tell me the domain you register and I can walk through the Freenom control panel steps with exact fields and check DNS for you.


## Free domain with Freenom (example)

1. Go to https://www.freenom.com and search for an available free domain (.tk, .ml, .ga, .cf, .gq).
2. Register the domain and go to `Services` → `My Domains` → `Manage Domain` → `Management Tools` → `Nameservers` or `Manage Freenom DNS`.

DNS settings examples:

- For GitHub Pages (apex domain example `example.tk`):
  - Add A records pointing to GitHub Pages IPs:
    - 185.199.108.153
    - 185.199.109.153
    - 185.199.110.153
    - 185.199.111.153
  - Add CNAME for `www` -> `<your-user>.github.io` (or `your-site.netlify.app` for Netlify)

- For Netlify (recommended for easy HTTPS):
  - Add CNAME for `www` -> `<your-site>.netlify.app`
  - For apex domain, use an ALIAS/ANAME if supported or point A records to Netlify's load balancer addresses (see Netlify docs).

3. On the hosting provider (GitHub Pages/Netlify), add the custom domain and enable automatic HTTPS (Netlify provides automatic LetsEncrypt certificates; GitHub Pages also supports HTTPS for custom domains once DNS is correct).

Notes / cautions:

- Freenom free domains can expire or have usage restrictions; keep access and renewal in mind.
- If you prefer a permanently reliable domain, consider buying a cheap domain from providers such as Namecheap, Porkbun, or Google Domains.

---

If you'd like, I can:

- 1) finalize this repo (branch -> commit -> push) for GitHub Pages
- 2) walk you through registering a Freenom domain and the exact DNS entries for GitHub Pages or Netlify
- 3) optionally set up a simple Netlify deployment pipeline (netlify.toml)

Which option do you prefer? GitHub Pages or Netlify? Or would you like me to prepare both and give step-by-step commands for registering and attaching a free Freenom domain?
