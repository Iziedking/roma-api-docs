# GitBook Setup Guide

Complete guide to publishing ROMA API documentation on GitBook.

## Prerequisites

- GitHub account
- GitBook account (free at gitbook.com)

## Step 1: Create GitHub Repository

```bash
git init
git add .
git commit -m "Initial ROMA API documentation"
git remote add origin https://github.com/yourusername/roma-api-docs.git
git push -u origin main
```

## Step 2: Connect to GitBook

1. Go to [gitbook.com](https://gitbook.com)
2. Sign in or create account
3. Click "New Space"
4. Select "Import from GitHub"
5. Choose your repository
6. Select branch: `main`
7. Click "Import"

## Step 3: Configure GitBook

GitBook will automatically detect `SUMMARY.md` and build navigation.

### Custom Domain (Optional)

1. Go to Space Settings
2. Click "Domain"
3. Add custom domain
4. Update DNS records

### Branding

1. Go to Space Settings
2. Upload logo
3. Set colors
4. Configure theme

## Step 4: Verify Publication

Visit your GitBook URL:
```
https://your-space.gitbook.io
```

## Auto-Sync

GitBook syncs automatically when you push to GitHub:

```bash
git add .
git commit -m "Update documentation"
git push
```

Documentation updates within 1-2 minutes.

## Structure

```
roma-gitbook/
├── README.md                    # Landing page
├── SUMMARY.md                   # Table of contents
├── getting-started/
│   ├── request-api-key.md
│   ├── quickstart.md
│   ├── authentication.md
│   ├── configuration.md
│   └── error-handling.md
├── api-reference/
│   ├── README.md
│   ├── create-execution.md
│   ├── get-status.md
│   ├── list-executions.md
│   ├── get-details.md
│   ├── cancel-execution.md
│   ├── get-dag.md
│   ├── schemas.md
│   └── error-codes.md
├── integration-guides/
│   ├── README.md
│   ├── python.md
│   ├── javascript.md
│   ├── react.md
│   ├── nextjs.md
│   ├── vue.md
│   ├── react-native.md
│   └── curl.md
├── examples/
│   ├── README.md
│   ├── qa-agent.md
│   ├── research-assistant.md
│   ├── content-generator.md
│   ├── data-analysis.md
│   ├── customer-support.md
│   └── crypto-analyzer.md
├── postman/
│   ├── README.md
│   ├── import.md
│   ├── environment.md
│   ├── testing.md
│   └── roma-api-collection.json
├── best-practices/
│   ├── performance.md
│   ├── security.md
│   ├── rate-limits.md
│   └── polling.md
└── support/
    ├── faq.md
    ├── troubleshooting.md
    └── contact.md
```

## Customization

### .gitbook.yaml

Create `.gitbook.yaml` in repository root:

```yaml
root: ./

structure:
  readme: README.md
  summary: SUMMARY.md

redirects:
  previous/path: new-path
```

### Search

GitBook includes built-in search. No configuration needed.

### Analytics

Add Google Analytics in Space Settings > Integrations.

## Maintenance

### Update Documentation

```bash
git pull
# Edit files
git add .
git commit -m "Update X section"
git push
```

### Version Control

Create branches for major versions:

```bash
git checkout -b v2.0
git push -u origin v2.0
```

Configure in GitBook Space Settings > Versions.

## Troubleshooting

### Sync Issues

1. Check GitHub connection in GitBook Settings
2. Verify webhook is active
3. Try manual sync: Space Settings > GitHub > Sync

### Build Errors

1. Verify SUMMARY.md syntax
2. Check all links are valid
3. Ensure no broken file references

### Custom Domain Issues

1. Verify DNS records
2. Allow 24-48 hours for propagation
3. Check SSL certificate status

## Support

- GitBook Docs: [docs.gitbook.com](https://docs.gitbook.com)
- GitBook Support: support@gitbook.com
- ROMA Support: iziedking17@gmail.com
