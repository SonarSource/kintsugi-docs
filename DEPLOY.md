# Deploy to GitHub Pages - Quick Guide

## Prerequisites

- Git repository pushed to GitHub
- Python 3.x installed locally

## One-Time Setup

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```

This installs MkDocs and the Material theme.

### 2. Update URLs in mkdocs.yml

Edit `mkdocs.yml` and replace `your-org` with your GitHub username:

```yaml
site_url: https://YOUR-USERNAME.github.io/kintsugi-docs
repo_url: https://github.com/YOUR-USERNAME/kintsugi-docs
```

### 3. Test Locally

```bash
mkdocs serve
```

Open http://127.0.0.1:8000 - you should see your documentation site!

Press `Ctrl+C` to stop the server.

### 4. Deploy to GitHub Pages

```bash
mkdocs gh-deploy
```

You'll see output like:
```
INFO    -  Cleaning site directory
INFO    -  Building documentation to directory: /path/to/site
INFO    -  Copying '/path/to/site' to 'gh-pages' branch and pushing to GitHub.
INFO    -  Your documentation should shortly be available at:
           https://YOUR-USERNAME.github.io/kintsugi-docs/
```

### 5. Enable GitHub Pages

1. Go to: `https://github.com/YOUR-USERNAME/kintsugi-docs/settings/pages`

2. Under **Source**:
   - Branch: `gh-pages`
   - Folder: `/ (root)`

3. Click **Save**

4. Wait 1-2 minutes

5. Visit your site: `https://YOUR-USERNAME.github.io/kintsugi-docs/`

## Verifying Deployment

### Check Deployment Status

**Option 1: GitHub Actions**
- Go to the **Actions** tab in your repository
- You should see a "pages build and deployment" workflow

**Option 2: Environments**
- Look at the right sidebar on your repo homepage
- Under **Environments**, click `github-pages`
- You'll see deployment history and the live URL

**Option 3: Settings**
- Go to **Settings** → **Pages**
- You'll see: "Your site is published at https://..."

## Automatic Deployment (Optional)

Once set up, the GitHub Actions workflow will auto-deploy when you push to `main`/`master`.

### Enable Auto-Deploy

1. Go to **Settings** → **Actions** → **General**
2. Under **Workflow permissions**:
   - Select: ✅ **Read and write permissions**
3. Click **Save**

Now every push to `main` will automatically update your site!

## Common URLs

Replace `YOUR-USERNAME` with your actual GitHub username:

- **Repository**: `https://github.com/YOUR-USERNAME/kintsugi-docs`
- **Settings**: `https://github.com/YOUR-USERNAME/kintsugi-docs/settings/pages`
- **Actions**: `https://github.com/YOUR-USERNAME/kintsugi-docs/actions`
- **Live Site**: `https://YOUR-USERNAME.github.io/kintsugi-docs/`

## Making Changes

### Local Development

```bash
# Start server
mkdocs serve

# Edit files in docs/
# Browser auto-refreshes on save

# Stop server: Ctrl+C
```

### Deploy Changes

```bash
# Manual deploy
mkdocs gh-deploy

# Or just push to GitHub (if auto-deploy enabled)
git add .
git commit -m "Update documentation"
git push origin main
```

## Troubleshooting

### Site Not Showing Up

**Wait a bit**: First deployment can take 5-10 minutes

**Check branch**: Ensure `gh-pages` branch exists
```bash
git branch -a | grep gh-pages
```

**Check Pages settings**: Go to Settings → Pages and verify it's enabled

### 404 Error

**Check site_url**: Make sure `mkdocs.yml` has correct URL

**Clear browser cache**: Try incognito/private window

**Check deployment**: Go to Actions tab and look for errors

### Permission Denied

**GitHub token**: The workflow needs write permissions
- Settings → Actions → General → Workflow permissions
- Enable "Read and write permissions"

### Build Errors

**Test locally first**:
```bash
mkdocs build --strict
```

**Check for**:
- Broken internal links
- Invalid Markdown syntax
- Missing files

## Custom Domain (Optional)

### Setup Custom Domain

1. Go to **Settings** → **Pages**
2. Under **Custom domain**, enter: `docs.yourdomain.com`
3. Click **Save**
4. Add DNS records at your domain provider:
   - Type: `CNAME`
   - Name: `docs`
   - Value: `YOUR-USERNAME.github.io`

5. Wait for DNS propagation (up to 24 hours)

## Quick Reference

```bash
# Test locally
mkdocs serve

# Build only (creates site/ folder)
mkdocs build

# Deploy to GitHub Pages
mkdocs gh-deploy

# Deploy with custom message
mkdocs gh-deploy -m "Update documentation"

# Clean build
mkdocs build --clean
```

## Need Help?

- **MkDocs Docs**: https://www.mkdocs.org/
- **Material Theme**: https://squidfunk.github.io/mkdocs-material/
- **GitHub Pages**: https://docs.github.com/en/pages

---

**Your docs are ready to go live!** 🚀
