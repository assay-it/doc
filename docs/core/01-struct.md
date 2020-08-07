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

The suite implements a quality assessments as function of the form `func TestAbc() gurl.Arrow`. Where `Abc` is a unique name of the assessment. Each assessment declares cause-and-effect using [category pattern](/docs/core/category):
* "Given" specifies the communication context and the known state of the expected behavior;
* "When" executes key actions about the interaction with remote component;
* "Then" observes output of remote component, validates its correctness and outputs results.

Quality assessments functions are executed sequentially one after another. Here is a typical suite structure:

```go
package main

import (
  "github.com/fogfish/gurl"
  ƒ "github.com/fogfish/gurl/http/recv"
  ø "github.com/fogfish/gurl/http/send"
)

func TestFoo() gurl.Arrow { /* ... */ }

func TestBar() gurl.Arrow { /* ... */ }

func main() {}
```

<span class="label label-yellow">Important</span> features to implement
* Each suite is always declared as main package. 
* Empty main function is a required for each suite, otherwise we cannot compile it.
