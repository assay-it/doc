---
layout: default
title: Suite structure
description: |
  The suite is a Golang program of certain structure. It implements collection of function, each declares cause-and-effect of networking I/O.
parent: core
permalink: /core/suite
nav_order: 1
---

# Suite structure

The suite implements a quality assessments as function of the form
```go
func MySuite() assay.Arrow
```

Where `MySuite` is a unique name of the assessment. Each assessment declares cause-and-effect using [category pattern](./category):
* "Given" specifies the communication context and the known state of the expected behavior;
* "When" executes key actions about the interaction with remote component;
* "Then" observes output of remote component, validates its correctness and outputs results.

Quality assessments functions are executed sequentially one after another. Here is a typical suite structure, which is a pure Golang module:

```go
package mysuites

import (
  "github.com/assay-it/sdk-go/assay"
  c "github.com/assay-it/sdk-go/cats"
  "github.com/assay-it/sdk-go/http"
  ƒ "github.com/assay-it/sdk-go/http/recv"
  ø "github.com/assay-it/sdk-go/http/send"
)

func MyFoo() assay.Arrow { /* ... */ }

func MyBar() assay.Arrow { /* ... */ }
```
