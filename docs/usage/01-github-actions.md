---
layout: default
title: GitHub Actions
description: assay.it provides a GitHub Actions to automated quality assessment pipelines 
parent: use with
nav_order: 1
permalink: /usage/github-actions
---

# GitHub Actions

One line integration with GitHub Actions using ready-made action. 
{: .fs-6 .fw-300 }

[View on GitHub](https://github.com/assay-it/github-actions-webhook){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

---

Please follows [official GitHub Actions documentation](https://docs.github.com/en/actions) for details about pipeline definition. 


[assay.it](https://assay.it) implements the action `assay-it/github-actions-webhook@latest` that triggers a quality assurance for your change. The actions requires explicit **permission** for GitHub actions to run the job at assay.it on your behalf, *an access key is requires*. Go to your profile settings at assay.it and generate a new personal access key. Store this key to secret key vault at settings of your repository (Your Repo > Settings > Secrets) under the name `ASSAY_SECRET_KEY`.

Add the following build step into your pipeline:

{% raw %}
```yaml
- uses: assay-it/github-actions-webhook@latest
  with:
    secret: ${{ secrets.ASSAY_SECRET_KEY }}
    target: http://api.example.com
```
{% endraw %}

The action requires an optional parameter `target` that points to SUT.
