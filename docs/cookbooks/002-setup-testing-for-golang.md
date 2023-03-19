---
layout: default
title: Setup Testing for Golang application
description: |
  Start manual on-demand testing of public or private cloud application
parent: Cookbooks
nav_order: 2
permalink: /cookbooks/manual-testing-of-microservices
---

# Setup Testing for Golang application

With `assay-it` you can embed quality checks of HTTP(S) endpoints into Golang modules.

Create new Golang module
```bash
mkdir myapp && cd myapp
go mod init
```

Initialize `assay-it` config

```bash
assay-it init
go mod tidy
```

It creates
* `.assay-it.json` config files
* `suites/suite.go` example testing suites

Run testing

```bash
assay-it test
```

