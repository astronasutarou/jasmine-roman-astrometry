# jasmine-roman-astrometry

## GitHub Pages

This repository is configured to automatically build and deploy documentation to GitHub Pages using MkDocs when changes are pushed to the `main` or `master` branch.

The deployment workflow can also be triggered manually from the Actions tab.

### How it works

1. When changes are pushed to the `main` or `master` branch, the workflow automatically runs
2. Python packages from `requirements.txt` are installed
3. The website is generated using `mkdocs build` from the `docs/` directory
4. The generated site is deployed to GitHub Pages

### To update the documentation

1. Edit markdown files in the `docs/` directory
2. Update `mkdocs.yml` to configure the site structure and theme
3. Push changes to the `main` or `master` branch
4. The workflow will automatically build and deploy your site

### Local development

To build and preview the site locally:
```bash
pip install -r requirements.txt
mkdocs serve
```