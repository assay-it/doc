---
layout: default
title: Setup Testing for Golang modules
description: |
  Setup testing of public or private cloud application developed with Golang
parent: Cookbooks
nav_order: 2
permalink: /cookbooks/setup-testing-for-golang-modules
---

# Setup Testing for Golang modules

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

The example of usage `assay-it` utility with Golang model is shown by [the blueprint](https://github.com/fogfish/blueprint-serverless-golang). 

[See Example](https://github.com/fogfish/blueprint-serverless-golang/blob/main/http/suites/petshop.go){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

