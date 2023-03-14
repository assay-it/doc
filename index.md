---
layout: default
title: assay.it
nav_order: 2
description: |
  Learn about testing Cloud Apps in Production with assay.it using type safe and pure functional test suites.
permalink: /
---


#  Test Cloud Apps in Production. Confirm Quality & Eliminate Risk.
{: .fs-9 }

Construct automated quality check pipelines for your applications, microservices and other endpoints across deployments and environments.
{: .fs-6 .fw-300 }

<!--
[Check on GitHub](https://github.com/assay-it/assay-it){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

[Sign In with GitHub](https://github.com/login/oauth/authorize?client_id=6941f2acf659df65f37e&response_type=code&scope=repo%3Astatus&state=%7B%22vsn%22%3A%22v6%22%2C%22cid%22%3A%226941f2acf659df65f37e%22%2C%22url%22%3A%22https%3A%2F%2Fapi.assay.it%2Fauth%2Fhook%2Fgithub%22%2C%22acc%22%3A%22oss%22%2C%22upg%22%3Afalse%7D){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
-->

---

## Quick Start

`assay-it` is an open source command line utility. The utility is designed for testing of loosely coupled topologies such as serverless applications, microservices and other systems that rely on interfaces, protocols and its behaviors. It does the unit-like testing but in distributed environments.

The utility emphasizes continuous proofs of the quality as a key feature along the deployment pipelines to eliminate defects at earlier phases of the feature lifecycle. It impacts on engineering teams philosophy and commitments, ensuring that your microservice(s) are always in a release-ready state.

Easiest way to install the latest version of utility using brew but binary builds are also available at [GitHub Releases](https://github.com/assay-it/assay-it/releases).

```bash
brew tap assay-it/homebrew-tap
brew install -q assay-it
```

### Test suites

`assay-it` checks the correctness of deployments using **type safe and pure functional test specification** of protocol endpoints. The command requires the suite development using Golang syntax implemented by [ᵍ🆄🆁🅻 library](https://github.com/fogfish/gurl) but limited functionality is supported with Markdown documents.

Use the following example suite to test the utility either generate example test suites using `assay-it testspec` command or copy-paste source code to `suite.go` file.

```bash
assay-it testspec > suite.go
```

```go
package suite

import (
  "github.com/fogfish/gurl/v2/http"
  ƒ "github.com/fogfish/gurl/v2/http/recv"
  ø "github.com/fogfish/gurl/v2/http/send"
)

func TestHttpBinGet() http.Arrow {
  return http.GET(
    ø.URI("http://httpbin.org/get"),

    ƒ.Status.OK,
    ƒ.ContentType.ApplicationJSON,
  )
}
```

### Run 

Run the the quality assessment with assay-it eval suite.go. The utility automatically downloads imported modules, compiles suites and outputs results into console.

```bash
assay-it eval suite.go

==> PASS: TestHttpBinGet (325.187959ms)
PASS	main
```

<!--
1. **Sign up for [assay.it](https://assay.it)** with your GitHub developer account. Initially, the service requires only access to your public profile, public repositories and access to commit status of connected repositories. Later, you can enable quality assessments of private repositories.  

2. **Fork [assay-it/blueprint-suite](https://github.com/assay-it/blueprint-suite)** to your own GitHub account and then add to the service workspace. The example implements a minimal quality assessment job using [category pattern](./core/category-pattern) to connect cause-and-effect (Given/When/Then) with the networking concepts (Input/Process/Output). Just write [pure functional code](./core) instead of clicking through UI or maintaining endless XML, YAML or JSON documents.
```go
func TestOk() assay.Arrow {
	return http.Join(
		ø.GET("https://assay.it"),
		ƒ.Code(http.StatusCodeOK),
		ƒ.Header("Content-Type").Is("text/html"),
	)
}
```

3. **Launch the quality assessment** through the user interface. The service schedules the job and returns results of assessments in a few seconds. Here, a manual job trigger is used for ad-hoc and illustration purposes. [assay.it](https://assay.it) supports integration with CI/CD so that [continuous quality evaluation](./case-study/continuous-quality-automation) is a part of the development culture. 
![](/doc/assets/images/screen.png)


## Further Reading

Please continue to [the core](/doc/core) sections for details about Behavior as a Code development and see [our advanced example on GitHub](https://github.com/assay-it/blueprint-continuous-quality).

-->