---
layout: default
title: Testing in Production with GitHub Actions
description: |
  Start manual on-demand testing of public or private cloud application
parent: Cookbooks
nav_order: 4
permalink: /cookbooks/testing-in-production-with-github-actions
---

# Testing in Production with GitHub Actions

With `assay-it` you can embed quality checks of HTTP(S) endpoints into CI\CD pipelines.

The utility is accomplished with [GitHub Actions](https://github.com/assay-it/github-actions-quality-check). The action relies that project is configured with testing suites compatible with `assay-it` utility. 

Configure the job

```yaml
jobs:
  steps:
    - uses: actions/checkout@v2

    ## use your favorite method of deploying microservice(s)
    - id: deploy
      # ...

    - uses: assay-it/github-actions-quality-check@latest
      with:
        install-go: false
        system-under-test: ${{ steps.deploy.outputs.deployed-api }}
```

The example of usage `assay-it` utility with GitHub is shown by [the blueprint](https://github.com/fogfish/blueprint-serverless-golang). 

[GitHub Example](https://github.com/fogfish/blueprint-serverless-golang/blob/main/.github/workflows/check-spawn.yml){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

