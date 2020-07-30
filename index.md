---
layout: default
title: assay.it
nav_order: 2
description: |
  https://assay.it makes a formal quality assessment of microservices in the distributed environment using Behavior as a Code paradigm.
permalink: /
---

# Objective proof of microservice quality
{: .fs-9 }

We build [**https://assay.it**](https://assay.it) to help developers with quality assessment of microservices in the distributed environment. The service is designed to perform a formal and objective proof of the quality using Behavior as a Code paradigm.
{: .fs-6 .fw-300 }

[Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/assay-it/sample.assay.it){: .btn .fs-5 .mb-4 .mb-md-0 }

---

Microservices have become a design style to evolve systems architecture in parallel,
implement stable and consistent interfaces. This architecture style brings additional
complexity and new problems. One of them is the assessment of system behavior while its
components communicate over the network - like integration testing but for distributed
environment. We need an ability to quantitatively evaluate and trade-off architecture
to ensure quality of the solutions.

[**https://assay.it**](https://assay.it) is designed to perform a formal (objective)
proofs of the quality using Behavior as a Code (BaC) paradigm. It connects cause-and-effect
(Given/When/Then) to the networking concepts (Input/Process/Output). The expected
behavior of each network component is declared using simple Golang program.


## Getting started

Want to jump right into using [**https://assay.it**](https://assay.it)? Follow the steps below to have an environment up and running. You can also continue with example on GitHub

[View it on GitHub](https://github.com/assay-it/sample.assay.it){: .btn .fs-5 .mb-4 .mb-md-0 }

1. Sign Up for [https://assay.it](https://assay.it) with your GitHub account.

2. To start quality assessments with [https://assay.it](https://assay.it), you need a git repository. This repository will host your BaC suites either create a new dedicated one or collocate with your application.

3. The suite is simple Golang program build around [http combinator library](https://github.com/fogfish/gurl). Please check  details about [syntax and api](/docs/core).

4. Use standard Golang import declaration to include core libraries and its peer dependencies
```go
import (
	"github.com/fogfish/gurl"
	ƒ "github.com/fogfish/gurl/http/recv"
	ø "github.com/fogfish/gurl/http/send"
)
```

5. Each suite implements few quality assessments. Each is a function of the form `func TestXxx() gurl.Arrow`.
```go
func TestOk() gurl.Arrow {
	return gurl.HTTP(
		// ...
	)
}
```

6. The quality assessments uses cause-and-effect concept to declare HTTP I/O and expected response. 
```go
func TestOk() gurl.Arrow {
	return gurl.HTTP(
		ø.GET("https://assay.it"),
		ƒ.Code(gurl.StatusCodeOK),
		ƒ.Header("Content-Type").Is("text/html"),
	)
}
```  

7. See complete example of [suite.go](https://github.com/assay-it/sample.assay.it/blob/master/suite.go). 

8. Create a configuration file [`.assay.json`](/docs/core)
```json
{
  "suites": ["suite.go"]
}
```

9. Integrate your repository with [https://assay.it](https://assay.it) at Settings of Your Account.

7. Enjoy the results!

---

## Next Steps

Look into advanced example on GitHub. It shows more complex examples, talks about integration with CI\CD and depict the typical workflow.

[View it on GitHub](https://github.com/assay-it/example.assay.it){: .btn .fs-5 .mb-4 .mb-md-0 }

Study How To code "Behavior as a Code"

[Syntax and API](/docs/core){: .btn .fs-5 .mb-4 .mb-md-0 }
