---
layout: default
title: Manual Testing of Microservices
description: |
  Start manual on-demand testing of public or private cloud application
parent: Cookbooks
nav_order: 2
permalink: /cookbooks/manual-testing-of-microservices
---

# Manual, on-demand testing of microservices

With `assay-it` you can check quality of any HTTP(S) endpoints deployed either to public to private clouds. Manual, on-demand testing implies starting of testing suites by engineers whenever is needed.  

**Requirements**:
* deployed target HTTP(S) endpoints


## Designing test suite

Make decision what is testing intent of the application (e.g. api specification helps on this). For example, our intent test existence of the following resources:

```
https://assay.it/doc/
https://assay.it/doc/introduction
https://assay.it/doc/how-it-works
https://assay.it/doc/user-guide
```

Use `assay-it` to generate test specification

```bash
assay-it testspec -f go \
  https://assay.it/doc/ \
  https://assay.it/doc/introduction \
  https://assay.it/doc/how-it-works \
  https://assay.it/doc/user-guide > suites.go
```

Alternatively, you can implement suites

```go
package suites

import (
  "github.com/fogfish/gurl/v2/http"
  ƒ "github.com/fogfish/gurl/v2/http/recv"
  ø "github.com/fogfish/gurl/v2/http/send"
)

func TestDocHowItWorks() http.Arrow {
  return http.GET(
    ø.URI("https://assay.it/doc/how-it-works"),
    ø.UserAgent.Set("gurl/v2"),
    ƒ.Status.OK,
  )
}
```

## Run testing

```
assay-it eval suites.go

...
==> testing
==> PASS: TestDoc (213.525539ms)
==> PASS: TestDocIntroduction (31.773183ms)
==> PASS: TestDocHowItWorks (213.525539ms)
==> PASS: TestDocUserGuide (26.340668ms)
PASS	main (271.63939ms)
```
