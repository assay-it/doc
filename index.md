---
layout: default
title: assay.it
nav_order: 2
description: |
  Learn with doc about quality assessments of Serverless applications with assay.it using typesafe pure functional Behavior as a Code paradigm.
permalink: /
---


# Confirm Quality, Eliminate Risks
{: .fs-9 }

Learn about quality assessments of Serverless applications with assay.it
{: .fs-6 .fw-300 }

[Sign In with GitHub](https://github.com/login/oauth/authorize?client_id=6941f2acf659df65f37e&response_type=code&scope=repo%3Astatus&state=%7B%22vsn%22%3A%22v6%22%2C%22cid%22%3A%226941f2acf659df65f37e%22%2C%22url%22%3A%22https%3A%2F%2Fapi.assay.it%2Fauth%2Fhook%2Fgithub%22%2C%22acc%22%3A%22oss%22%2C%22upg%22%3Afalse%7D){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }

---

## Quick Start

[**https://assay.it**](https://assay.it) is a Software as a Service for developers to perform formal proofs of quality using type safe Behavior as a Code. It automates validation of cause-and-effect in loosely coupled topologies such as serverless applications, microservices and other systems that rely on interface syntaxes and its behaviors. It emphasizes deployment and quality assessment as a key feature along the development pipelines. Continuous proofs of the quality helps to eliminate defects at earlier phases of the feature lifecycle. It impacts on engineering teams philosophy and commitments, ensuring that your microservice(s) are always in a release-ready state.

1. **Sign up for [assay.it](https://assay.it)** with your GitHub developer account. Initially, the service requires only access to your public profile, public repositories and access to commit status of connected repositories. Later, you can enable quality assessments of private repositories.  

2. **Fork [assay-it/blueprint-suite](https://github.com/assay-it/blueprint-suite)** to your own GitHub account and then add to the service workspace. The example implements a minimal quality assessment job using [category pattern](./core/category) to connect cause-and-effect (Given/When/Then) with the networking concepts (Input/Process/Output). Just write [pure functional code](./core) instead of clicking through UI or maintaining endless XML, YAML or JSON documents.
```go
func TestOk() assay.Arrow {
	return http.Join(
		ø.GET("https://assay.it"),
		ƒ.Code(http.StatusCodeOK),
		ƒ.Header("Content-Type").Is("text/html"),
	)
}
```

3. **Launch the quality assessment** through the user interface. The service schedules the job and returns results of assessments in a few seconds. Here, a manual job trigger is used for ad-hoc and illustration purposes. [assay.it](https://assay.it) supports integration with CI/CD so that [continuous quality evaluation](./case-study/everything-is-continuous) is a part of the development culture. 
![](/doc/assets/images/screen.png)


## Further Reading

Please continue to [the core](/doc/core) sections for details about Behavior as a Code development and see [our advanced example on GitHub](https://github.com/assay-it/blueprint-continuous-quality).

