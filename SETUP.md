# Documentation Setup Guide

This guide explains how to set up and deploy the Kintsugi documentation using GitHub Pages.

## Initial Setup

### 1. Repository Setup

Make sure you have this repository pushed to GitHub:

```bash
git add .
git commit -m "Initial documentation setup with MkDocs"
git push origin main
```

### 2. Enable GitHub Pages

1. Go to your GitHub repository
2. Click **Settings** → **Pages**
3. Under **Source**, select:
   - **Source**: Deploy from a branch
   - **Branch**: `gh-pages`
   - **Folder**: `/ (root)`
4. Click **Save**

### 3. Initial Deployment

Run the manual deployment once to create the `gh-pages` branch:

```bash
# Install dependencies
pip install -r requirements.txt

# Deploy to GitHub Pages
mkdocs gh-deploy
```

This will:
- Build the documentation
- Create the `gh-pages` branch
- Push the built site to GitHub

### 4. Verify Deployment

After a few minutes, your documentation will be available at:

```
https://your-username.github.io/kintsugi-docs/
```

Replace `your-username` with your GitHub username or organization name.

## Automatic Deployment

Once set up, the GitHub Actions workflow will automatically deploy documentation when you:

1. Push to `main` or `master` branch
2. Merge a pull request

### GitHub Actions Setup

The workflow is already configured in `.github/workflows/deploy-docs.yml`.

**To enable it:**

1. Go to **Settings** → **Actions** → **General**
2. Under **Workflow permissions**, select:
   - ✅ **Read and write permissions**
3. Click **Save**

## Local Development

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/kintsugi-docs.git
cd kintsugi-docs

# Install dependencies
pip install -r requirements.txt
```

### Running Locally

```bash
# Start the development server
mkdocs serve

# Open browser to http://127.0.0.1:8000
```

The server will auto-reload when you save changes.

### Building

```bash
# Build the static site
mkdocs build

# Output will be in site/ directory
```

## Customization

### Update Site URLs

Edit `mkdocs.yml` and update these values:

```yaml
site_url: https://your-org.github.io/kintsugi-docs
repo_url: https://github.com/your-org/kintsugi
```

Replace `your-org` with your GitHub username or organization.

### Update Navigation

Edit `mkdocs.yml` to customize the navigation structure:

```yaml
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - Your New Page: your-page.md
```

### Add Custom Colors

Edit `docs/stylesheets/extra.css`:

```css
:root {
  --md-primary-fg-color: #4f46e5;  /* Your primary color */
  --md-accent-fg-color: #818cf8;   /* Your accent color */
}
```

### Add Logo and Favicon

1. Add images to `docs/images/`:
   - `logo.png` - Your logo (recommend 128x128)
   - `favicon.ico` - Your favicon (16x16 or 32x32)

2. Update `mkdocs.yml`:

```yaml
theme:
  logo: images/logo.png
  favicon: images/favicon.ico
```

## Adding Analytics

### Google Analytics

Edit `mkdocs.yml`:

```yaml
extra:
  analytics:
    provider: google
    property: G-XXXXXXXXXX  # Your GA4 measurement ID
```

### Other Analytics

MkDocs Material supports various analytics providers. See [documentation](https://squidfunk.github.io/mkdocs-material/setup/setting-up-site-analytics/).

## Troubleshooting

### GitHub Pages Not Working

**Check GitHub Actions:**
1. Go to **Actions** tab in your repository
2. Look for failed workflows
3. Click on the failed run to see error details

**Common Issues:**

- **Permissions**: Ensure workflow has write permissions
- **Branch**: Verify `gh-pages` branch exists
- **Settings**: Check Pages settings point to `gh-pages` branch

### Local Server Won't Start

**Install dependencies:**
```bash
pip install --upgrade -r requirements.txt
```

**Check Python version:**
```bash
python --version  # Should be 3.x
```

**Clear cache:**
```bash
rm -rf .cache/
```

### Build Fails

**Check syntax:**
- Ensure all Markdown files are valid
- Check for broken internal links
- Verify code blocks are properly formatted

**Validate configuration:**
```bash
mkdocs build --strict
```

This will fail on warnings, helping identify issues.

## Advanced Configuration

### Versioning

To add version dropdown:

1. Install mike:
   ```bash
   pip install mike
   ```

2. Deploy versions:
   ```bash
   mike deploy 1.0 latest
   mike set-default latest
   ```

3. Enable in `mkdocs.yml`:
   ```yaml
   extra:
     version:
       provider: mike
   ```

### Search Configuration

Customize search in `mkdocs.yml`:

```yaml
plugins:
  - search:
      lang: en
      separator: '[\s\-,:!=\[\]()"/]+|\.(?!\d)|&[lg]t;'
      prebuild_index: true
```

### Social Cards

Auto-generate social media preview images:

1. Install dependencies:
   ```bash
   pip install pillow cairosvg
   ```

2. Enable in `mkdocs.yml`:
   ```yaml
   plugins:
     - social
   ```

## Resources

- **MkDocs**: https://www.mkdocs.org/
- **Material Theme**: https://squidfunk.github.io/mkdocs-material/
- **GitHub Pages**: https://docs.github.com/en/pages
- **Markdown Guide**: https://www.markdownguide.org/

## Getting Help

If you encounter issues:

1. Check the [MkDocs documentation](https://www.mkdocs.org/)
2. Review [Material theme docs](https://squidfunk.github.io/mkdocs-material/)
3. Open an issue in the repository
4. Ask in GitHub Discussions

---

**Happy documenting!** 📚
