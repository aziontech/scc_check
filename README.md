# SCC Complexity Check

This repository contains a GitHub Action that automatically analyzes code complexity using the SCC tool when a Pull Request is opened, providing metrics about complexity and lines of code differences between branches.

## How to Use in Your Project

Create a workflow file in your repository (e.g., `.github/workflows/complexity_check.yml`) with the following content:

```yaml
name: SCC Complexity Check

on:
  pull_request:
    branches: [main, master]

jobs:
  scc-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - uses: aziontech/scc_check@main
```

This will automatically run the SCC complexity check on your pull requests and add a comment with the complexity metrics.
