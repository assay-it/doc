---
layout: default
title: How it works
description: |
  Construct automated quality assessment pipelines and integrate them with various service, providers and tools.
nav_order: 4
permalink: /how-it-works
---

# How it works

`assay-it` aims to automate most of the boring work you'll have while executing testing of distributed application. 

The utility expects a couple of things
* testing suites implemented using either Markdown or Golang syntax that depicts expected behavior;
* a `.assay-it.json` configuration file that specify what suites to be executed.

Despite testing suites are written using Golang syntax, `assay-it` is proper cross platform utility, which is able to validate microservices or cloud functions developed on any language. 

`assay-it` does multiple things while your are running it
1. Generates necessary sources code required to bootstrap testing;
2. Compiles testing suites listed in the configuration files and links executables;
3. Output test binaries performs networking interactions with the target system according to scenarios defined in the testing suites;
4. After all test finishes, `assay-it` prints a summary line showing the test status ('PASS' or 'FAIL'), package name, and elapsed time.


## Testing suites

Testing suites are secret ingredient of the cookbook. It is a type safe, pure functional Golang source code files. Each file contains one or multiple test functions named `TestXxx` (where Xxx does not start with a lower case letter) and should have the signature,

```go
func TestXxx() http.Arrow { /* ... */ } 
```

These functions declares cause-and-effect for protocols operations:
* "Given" specifies the communication context and the known state of
the expected behavior;
* "When" executes key actions about the interaction against
specified deployment;
* "Then" observes output, validates its correctness and outputs results.

The code below is the minimal implementation of the testing suite that checks if the web site online.

```go
package suite

import (
  "github.com/fogfish/gurl/v2/http"
  ƒ "github.com/fogfish/gurl/v2/http/recv"
  ø "github.com/fogfish/gurl/v2/http/send"
)

func TestWebSiteOnline() http.Arrow {
  return http.GET(
    ø.URI("https://assay.it"),
    ƒ.Status.OK,
  )
}
```

You can generate these example file with 

```bash
assay-it testspec > suite.go
```

We recommend to use **Golang** for test suite development, it is general purpose programming language that allows implementation of any complexity testing scenarios. Any quick ad-hoc testing or unit-like testing might be implemented with **Markdown** and output of `curl -v` command.


The text below is valid test suite made with Markdown syntax

```
## Test HttpBin Get

\`\`\`
GET http://httpbin.org/get
> User-Agent: curl/7.64.1
< 200 OK
< Content-Type: application/json
{
  "headers": {
    "Host": "httpbin.org",
    "User-Agent": "curl/7.64.1"
  },
  "origin": "_",
  "url": "http://httpbin.org/get"
}
\`\`\`
```

You can generate these example file with 

```bash
assay-it testspec -f md > suite.md
```
