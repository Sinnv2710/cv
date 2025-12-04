# Deployment — manual and automated

This document explains all the ways you can publish the static portfolio in `docs/` to Netlify, and how to set up automatic deployments triggered by GitHub.

Summary
- Manual: Use the Netlify UI or the Netlify CLI to create a site and deploy your `docs/` folder.
- Continuous: Connect your GitHub repo to Netlify so pushes and pull requests automatically build and deploy (this repository is already linked to Netlify).
- CI (GitHub Actions): Trigger Netlify deploys from a GitHub Actions workflow when you push to a branch.

Table of contents
- Manual deploy (Netlify UI)
- CLI deploy (one-off)
- Continuous auto-deploy (Netlify CI) — how it works
- GitHub Actions: example deploy workflow (optional)
- Verify and troubleshoot

---

## Manual deploy (Netlify UI)

1. Go to https://app.netlify.com and sign in.
2. Click **Add new site** → **Import from Git**.
3. Choose **GitHub** and authorize Netlify to access your account if necessary.
4. Select repository `Sinnv2710/cv` and choose the branch `portfolio`.
5. When prompted for build settings:
   - Build command: (leave empty for this simple static site)
   - Publish directory: `docs`
6. Click **Deploy site**. Netlify will build and publish automatically.

This approach creates a project in Netlify and configures webhooks so future pushes to that branch automatically trigger deployments.

## CLI deploy (one-off)

If you want to deploy immediately from your local machine without CI, use the Netlify CLI.

Install the CLI (if not already installed):

```bash
npm install -g netlify-cli
netlify login
```

One-off production deploy (upload local `docs/` to the existing Netlify site):

```bash
# link / init (if not linked already)
netlify init

# one-off production deploy
netlify deploy --prod --dir=docs --site <your-site-id-or-name>
```

This bypasses CI and uploads the local files directly to Netlify.

## Continuous auto-deploy (Netlify CI) — how it works

When you connect a GitHub repository to Netlify via the UI (or `netlify init`), Netlify adds a deploy key and webhook to the repository. The result:

- Netlify CI builds and publishes when commits are pushed to the configured branch (here: `portfolio`).
- Deploy previews are automatically created for pull requests (if enabled).
- Netlify handles build logs, cached builds, rollbacks, and HTTPS provisioning.

We already configured your site to auto-deploy from GitHub, so pushing to `portfolio` will trigger builds.

## GitHub Actions: example deploy workflow (optional)

You can also use GitHub Actions to trigger Netlify deployments (useful for custom steps in your pipeline or to run pre-deploy checks). Below are two approaches:

A) Use the official Netlify GitHub Action to deploy from the repo after building.

Create `.github/workflows/netlify-deploy.yml` with the following example (this will build the repo and publish `docs/`):

```yaml
name: Deploy to Netlify

on:
  push:
    branches:
      - portfolio

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build (no build step required for plain HTML/CSS)
        run: |
          echo "No build needed for docs/ static site"

      - name: Sync to Netlify (official action)
        uses: netlify/actions/cli@v1
        with:
          args: deploy --dir=docs --prod --site=$NETLIFY_SITE_ID
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
```

Notes for this workflow:
- Set `NETLIFY_AUTH_TOKEN` in GitHub repository secrets. Create a personal access token via Netlify (Netlify user settings → Applications → Personal access tokens). Add it as `NETLIFY_AUTH_TOKEN` in GitHub Settings → Secrets → Actions.
- Set `NETLIFY_SITE_ID` to the site ID shown in Netlify, or you can pass the site `name`.

B) Alternatively, let Netlify's own GitHub integration handle builds. That approach is usually simpler and recommended — you get deploy previews for pull requests and automatic builds without managing tokens.

## Verify and troubleshoot

- Check build logs: Netlify dashboard → Site → Deploys shows recent builds and logs.
- Confirm site URL is live (Netlify will give a `https://<site>.netlify.app` url). Use `curl -I https://<site>.netlify.app` to check quick status.
- If builds are failing, inspect the build logs and check whether a build command is set in Netlify UI (for this project, there should be no build command for a plain `docs` static site).
- When using GitHub Actions, use the Actions tab in GitHub to see the workflow run details.

## Notes / best practices

- For simple static sites (plain HTML/CSS in `docs/`) you can rely on Netlify's auto-deploy directly from GitHub — easiest, zero config.
- Use GitHub Actions when you need custom build steps, tests, or to control deployment timing.
- Keep sensitive tokens (NETLIFY_AUTH_TOKEN) in GitHub Secrets.
- If you want to use Netlify DNS and custom domains (e.g., a Freenom domain), Netlify can manage DNS for you and will issue HTTPS automatically.

---

If you'd like, I can add the sample GitHub Actions workflow file to the repo and help set the `NETLIFY_AUTH_TOKEN` secret in your GitHub repository (I can walk you through creating the token and where to paste it). Would you like me to add the workflow to `.github/workflows/netlify-deploy.yml` now?
