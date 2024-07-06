# Auto Commit

This GitHub Actions workflow automatically commits changes to the repository on a schedule or when changes are pushed to the master branch.

## Table of Contents

- [Overview](#overview)
- [Workflow Configuration](#workflow-configuration)
- [How It Works](#how-it-works)
- [Customization](#customization)
- [Security Considerations](#security-considerations)
- [Contributing](#contributing)
- [License](#license)

## Overview

The "Auto Commit" workflow is designed to:

1. Run on a schedule (twice daily)
2. Run when changes are pushed to the master branch
3. Update a file with the current timestamp
4. Commit and push the changes back to the repository

This can be useful for maintaining activity on your GitHub profile or for triggering other workflows that depend on repository changes.

## Workflow Configuration

The workflow is defined in `.github/workflows/auto-commit.yml`:

```yaml
on:
  push:
    branches:
      - master
      
  schedule:
  - cron: "30 0,2 * * *" # See https://crontab.guru/

jobs:
  auto_commit:
    runs-on: ubuntu-latest
    steps:
      - name: DEBUG 
        run: echo "::debug::Ref = ${{github.ref}}"
      - uses: actions/checkout@v3      
        with:
         persist-credentials: false
         fetch-depth: 0

      - name: Modify last update
        run: |
          TZ='Asia/Ho_Chi_Minh' d=`date -u -d '+7 hours' '+%Y-%m-%dT%H:%M:%S+07:00'`
          echo -e "This is the first commit of $d" > LAST_UPDATED

      - name: Commit changes
        run: |
          git config --local user.email "taphuc1@gmail.com"
          git config --local user.name "andrew-taphuc"
          git add -A
          git commit -m "andrew-taphuc: make it green"
          
      - name: GitHub Push
        uses: ad-m/github-push-action@v0.6.0
        with:
          directory: "."
          github_token: ${{ secrets.GITHUB_TOKEN }}
