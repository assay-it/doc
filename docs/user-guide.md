---
layout: default
title: User Guide
description: |
  xxx
nav_order: 5
permalink: /user-guide
---

# User Guide

The combinators fit very well to express intent of communication behavior. It gives rich abstractions to hide the networking complexity and help us to compose a chain of network operations and represent them as pure computation, building new things from small reusable elements.
{: .fs-6 .fw-300 }

`assay-it` is implemented over [ᵍ🆄🆁🅻 library](https://github.com/fogfish/gurl) - a "combinator" library for network I/O. Combinators open up an opportunity to depict computation problems in terms of fundamental elements like physics talks about universe in terms of particles. The only definite purpose of combinators are building blocks for composition of "atomic" functions into computational structures. `assay-it` uses a powerful symbolic expressions of combinators to implement declarative language for testing suite development.

## Background

### Combinators

Let’s formalize principles that help us to define our own abstraction applicable in functional programming through composition. The composition becomes a fundamental operation: the codomain of 𝒇 be the domain of 𝒈 so that the composite operation 𝒇 ◦ 𝒈 is defined. Our formalism uses `Arrow: IO ⟼ IO` as a key abstraction of networking combinators.

```go
// Arrow: IO ⟼ IO
type Arrow func(*Context) error
```

It is a pure function that takes an abstraction of the protocol context and applies morphism as an "invisible" side-effect of the composition.

Following the Input/Process/Output protocols paradigm, the two classes of combinators are defined:
* The first class is **writer** (emitter) morphism combinators, denoted by the symbol `ø` across this guide and example code. It focuses inside the protocol stack and reshapes requests. In the context of HTTP protocol, the writer morphism is used to declare HTTP method, destination URL, request headers and payload. 
* Second one is **reader** (matcher) morphism combinators, denoted by the symbol `ƒ`. It focuses on the side-effects of the protocol stack. The reader morphism is a pattern matcher, and is used to match response code, headers and response payload, etc. Its major property is “fail fast” with error if the received value does not match the expected pattern.


### High-order functions, how to compose protocol primitives 

`Arrow` can be composed with another `Arrow` into new `Arrow` and so on. Only product "and-then" composition style is supported. It builds a strict product `Arrow: A ◦ B ◦ C ◦ ... ⟼ D`. The product type takes a protocol context and applies "morphism" sequentially unless some step fails. Use variadic function `http.Join`, `http.GET`, `http.POST`, `http.PUT` and so on to compose HTTP primitives:

```go
// Join composes HTTP arrows to high-order function
// (a ⟼ b, b ⟼ c, c ⟼ d) ⤇ a ⟼ d
func http.Join(arrows ...http.Arrow) http.Arrow

//
var a: http.Arrow = /* ... */
var b: http.Arrow = /* ... */
var c: http.Arrow = /* ... */

d := http.Join(a, b, c)
```

Ease of the composition is one of major intent why syntax deviates from standard Golang HTTP interface. `http.Join` produces instances of higher order `http.Arrow` type, which is composable “promises” of HTTP I/O and so on. Essentially, the network I/O is just a set of `Arrow` functions. These rules of Arrow composition allow anyone to build a complex HTTP I/O scenario from a small reusable block.


## Combinators and its syntax

### Imports

The combinator domain specific language consists of multiple packages, import them all into Golang module

```go
import (
  // context of http protocol stack
  "github.com/fogfish/gurl/http"

  // Writer (emitter) morphism combinators. It focuses inside the protocol stack
  // and reshapes requests. In the context of HTTP protocol, the writer morphism
  // is used to declare HTTP method, destination URL, request headers and payload.
  // single letter symbol (e.g. ø) makes the code less verbose
  ø "github.com/fogfish/gurl/http/send"

  // Reader (matcher) morphism combinators. It focuses on the side-effects of
  // the protocol stack. The reader morphism is a pattern matcher, and is used
  // to match response code, headers and response payload, etc. Its major
  // property is “fail fast” with error if the received value does not match
  // the expected pattern. 
  // single letter alias (e.g. ƒ) makes the code less verbose
  ƒ "github.com/fogfish/gurl/http/recv"
)

func TestWebSiteOnline() http.Arrow { /* ... */ }
```

### Writer combinators

Writer (emitter) morphism combinators. It focuses inside the protocol stack and reshapes requests. In the context of HTTP protocol, the writer morphism is used to declare HTTP method, destination URL, request headers and payload.

#### Method

Use `http.GET(/* ... */)` combinator declares the verb of HTTP request. The language declares a combinator for most of HTTP verbs: `http.GET`, `http.HEAD`, `http.POST`, `http.PUT`, `http.DELETE` and `http.PATCH`.

```go
func TestGetXxx() http.Arrow {
  return http.GET(/* ... */)
}

func TestPutXxx() http.Arrow {
  return http.PUT(/* ... */)
}
```

Use `ø.Method` combinator to declare other verbs

```go
func TestPutXxx() http.Arrow {
  return http.Join(
    ø.Method("OPTIONS"),
    /* ... */)
}
```