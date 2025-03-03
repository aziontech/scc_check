# SCC Complexity Check

This repository contains a GitHub Actions workflow that automatically analyzes code complexity using the SCC tool when a Pull Request is opened, providing metrics about complexity and lines of code differences between branches.

## How to Use in Your Project

```yaml
name: SCC Complexity Check

on:
  pull_request:
    branches: [main, master]

jobs:
  scc-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: aziontech/gmud_brief@main
```

Or copy the `.github/workflows/scc_check.yml` file from this repository to the same path in your repository and customize as needed.
