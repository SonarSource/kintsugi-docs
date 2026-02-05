# Quick Start Guide

## View Documentation Locally

Your documentation is currently running at:

**🌐 http://127.0.0.1:8080/kintsugi-docs/**

The development server is already started and will automatically reload when you make changes to:
- Documentation files (`.md`)
- Configuration (`mkdocs.yml`)
- Stylesheets (`docs/stylesheets/extra.css`)

## What Changed?

✨ Your documentation now has a **professional, SonarSource-inspired design**:

- 🎨 Modern color scheme with purple & teal branding
- 📝 Beautiful typography with Inter and JetBrains Mono fonts
- 🌗 Optimized light and dark themes
- 🎯 Professional navigation with tabs
- 📦 Enhanced code blocks with copy button
- 🎪 Modern callouts/admonitions
- 📊 Beautiful tables and components
- 📱 Fully responsive mobile design
- ♿ Accessibility improvements

## Test the Features

### 1. Theme Toggle
Click the sun/moon icon in the header to switch between light and dark modes.

### 2. Navigation
- Top tabs for main sections
- Sticky navigation that follows you
- Sidebar with page hierarchy
- Footer navigation (previous/next)

### 3. Search
Press `/` or click the search icon to search the documentation.

### 4. Code Blocks
Hover over any code block to see the copy button.

### 5. Responsive Design
Resize your browser or view on mobile to see the responsive layout.

## Make Changes

### Edit Content
Edit any `.md` file in the `docs/` folder and save - the browser will auto-reload.

### Customize Styling
Edit `docs/stylesheets/extra.css` to adjust colors, spacing, or any other styles.

### Change Configuration
Edit `mkdocs.yml` to modify navigation, features, or site settings.

## Build for Production

When you're ready to deploy:

```bash
# Build the site
mkdocs build --clean

# The built site is in the `site/` folder
# Ready to deploy to GitHub Pages or any static host
```

## Stop the Server

To stop the development server, press `Ctrl+C` in the terminal where it's running.

## Need Help?

- **Full details**: See `STYLING_CHANGES.md`
- **MkDocs docs**: https://www.mkdocs.org
- **Material theme**: https://squidfunk.github.io/mkdocs-material/

---

**Enjoy your new documentation! 🚀**
