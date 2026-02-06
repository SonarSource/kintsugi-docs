# Kintsugi Documentation

Official documentation for Kintsugi - Automated Development Environment for Claude Code.

## 📚 View the Documentation

**Live Site**: [https://SonarSource.github.io/kintsugi-docs](https://SonarSource.github.io/kintsugi-docs)

## 🚀 Quick Links

- [Getting Started](https://SonarSource.github.io/kintsugi-docs/getting-started/)
- [Features Overview](https://SonarSource.github.io/kintsugi-docs/features/)
- [Troubleshooting](https://SonarSource.github.io/kintsugi-docs/troubleshooting/)
- [Quick Reference](https://SonarSource.github.io/kintsugi-docs/quick-reference/)

## 📖 About Kintsugi

Kintsugi is a comprehensive task management and code analysis platform that seamlessly integrates with Claude Code CLI. It automatically tracks your development workflows in real-time, providing:

- **Automatic Task Tracking** - Tasks created as you work with Claude
- **Visual Kanban Board** - Organize work across five workflow stages
- **Integrated Terminal** - Execute Claude commands directly in the app
- **Code Quality Analysis** - Real-time SonarQube for IDE with SonarQube integration
- **Token Usage Tracking** - Monitor API costs per task or epic
- **Local-First Architecture** - All data stays on your machine

## 🛠️ Local Development

### Prerequisites

- Python 3.x
- pip

### Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/SonarSource/kintsugi-docs.git
   cd kintsugi-docs
   ```

2. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

3. **Run local server**

   ```bash
   mkdocs serve
   ```

4. **View documentation**
   Open [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser

### Building

```bash
mkdocs build
```

The static site will be generated in the `site/` directory.

## 📝 Contributing to Documentation

### Documentation Structure

```
kintsugi-docs/
├── mkdocs.yml              # MkDocs configuration
├── requirements.txt        # Python dependencies
├── docs/
│   ├── index.md           # Homepage
│   ├── getting-started.md # Installation & setup
│   ├── features.md        # Feature documentation
│   ├── workflows.md       # Common workflows
│   ├── configuration.md   # Settings & customization
│   ├── architecture.md    # System architecture
│   ├── integrations.md    # Integration guides
│   ├── troubleshooting.md # Problem solving
│   ├── data-privacy.md    # Data & privacy
│   ├── quick-reference.md # Cheat sheet
│   └── stylesheets/
│       └── extra.css      # Custom styling
└── .github/
    └── workflows/
        └── deploy-docs.yml # Auto-deployment
```

### Making Changes

1. **Create a branch**

   ```bash
   git checkout -b update-docs
   ```

2. **Edit documentation**
   - All documentation files are in the `docs/` directory
   - Files are written in Markdown
   - Use MkDocs Material extensions for enhanced features

3. **Preview changes**

   ```bash
   mkdocs serve
   ```

4. **Commit and push**

   ```bash
   git add .
   git commit -m "Update documentation"
   git push origin update-docs
   ```

5. **Create pull request**

### Writing Guidelines

- **Use clear headings** - Organize content with H2 (##) and H3 (###)
- **Add code examples** - Use fenced code blocks with language specifiers
- **Include admonitions** - Use for tips, warnings, notes
- **Link between pages** - Use relative links: `[text](page.md)`
- **Keep it concise** - Break long pages into sections
- **Use tables** - For comparison or reference data
- **Add screenshots** - Place in `docs/images/` (when available)

### Markdown Extensions

MkDocs Material supports many extensions:

**Admonitions** (callouts):

```markdown
!!! note "Title"
Content here

!!! warning
Warning content

!!! tip
Helpful tip
```

**Tabs**:

```markdown
=== "Tab 1"
Content 1

=== "Tab 2"
Content 2
```

**Code blocks**:

````markdown
```python
def hello():
    print("Hello, World!")
```
````

**Task lists**:

```markdown
- [x] Completed task
- [ ] Incomplete task
```

## 🚀 Deployment

### Automatic Deployment

Documentation is automatically deployed to GitHub Pages when changes are pushed to the `main` or `master` branch.

The GitHub Actions workflow (`.github/workflows/deploy-docs.yml`) handles:

1. Installing Python and dependencies
2. Building the documentation with MkDocs
3. Deploying to GitHub Pages

### Manual Deployment

```bash
mkdocs gh-deploy
```

This builds the documentation and pushes to the `gh-pages` branch.

## 🔧 Configuration

### Site Settings

Edit `mkdocs.yml` to configure:

- Site name and description
- Navigation structure
- Theme settings
- Plugins
- Markdown extensions

### Theme Customization

- **Colors**: Edit `:root` variables in `docs/stylesheets/extra.css`
- **Logo**: Add to `docs/images/logo.png` and reference in `mkdocs.yml`
- **Favicon**: Add to `docs/images/favicon.ico` and reference in `mkdocs.yml`

## 🆘 Need Help?

- **Issues**: [GitHub Issues](https://github.com/SonarSource/kintsugi/issues)
- **Discussions**: [GitHub Discussions](https://github.com/SonarSource/kintsugi/discussions)
- **Documentation**: You're looking at it!

---

Built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
