---
layout: default
title: Command Line Application
description: Built automation pipelines and CI/CD integrations with assay command line utility
parent: usage and integrations
nav_order: 3
permalink: /usage-and-integrations/command-line-application
---

# Command Line Application

Built automation pipelines and CI/CD integrations with assay cli utility. 
{: .fs-6 .fw-300 }

[View on GitHub](https://github.com/assay-it/assay){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

---

Use `go get` to download and **install** the cli into `$GOPATH/bin`.

```bash
go get github.com/assay-it/assay
```

The application requires personal access key to access [assay.it](https://assay.it) api, you can generate a key from your profile.

The built-in help shows how to use supported features:

```bash
assay help
```

**Confirm quality** of existing deployment with `run` command. It takes a latest snapshot of suites from the repository and runs them against specified endpoint.

```bash
assay run facebadge/sample.assay.it \
  --url https://example.com \
  --key Z2l0aHV...bWhaQQ
```

**Integrate with CI/CD** using `webhook` command. The command supports pull-request like integration, allowing to run suites from explicitly defined reference at the repository. 

```bash
assay webhook \
  --base facebadge/sample.assay.it/main/8c7ec...dc59 \
  --head facebadge/sample.assay.it/main/9d8fd...ed60 \
  --number '#123' \
  --title 'awesome feature' \
  --url https://example.com \
  --key Z2l0aHV...bWhaQQ
```
