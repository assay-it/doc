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

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

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


### Combinators and its syntax

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

## Writer combinators

Writer (emitter) morphism combinators. It focuses inside the protocol stack and reshapes requests. In the context of HTTP protocol, the writer morphism is used to declare HTTP method, destination URL, request headers and payload.

### Method

Use `http.GET(/* ... */)` combinator to declare the verb of HTTP request. The language declares a combinator for most of HTTP verbs: `http.GET`, `http.HEAD`, `http.POST`, `http.PUT`, `http.DELETE` and `http.PATCH`.

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
func TestXxx() http.Arrow {
  return http.Join(
    ø.Method("OPTIONS"),
    /* ... */)
}
```

### Target URI

Use `ø.URI(string)` combinator to specifies target URI for HTTP request. The combinator uses absolute URI to specify protocol, target host and path of the endpoint.

```go
func TestXxx() http.Arrow {
  return http.GET(
    ø.URI("http://example.com"),
    /* ... */)
}
```

The `ø.URI` combinator is equivalent to `fmt.Sprintf`. It uses [percent encoding](https://golang.org/pkg/fmt/) to format and escape values.

```go
http.GET(
  ø.URI("http://example.com/%s", "foo bar"),
)

// All path segments are escaped by default, use ø.Authority or ø.Path
// types to disable escaping

// BAD, DOES NOT WORK
http.GET(
  ø.URI("%s/%s", "http://example.com", "foo/bar"),
)

// GOOD, IT WORKS
http.GET(
  ø.URI("%s/%s", ø.Authority("http://example.com"), ø.PATH("foo/bar")),
)
```

### Query Params

Use `ø.Params(any)` combinator to lifts the flat structure or individual values into query parameters of specified URI. 

```go
type MyParam struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

func TestXxx() http.Arrow {
  return http.GET(
    /* ... */
    ø.Params(MyParam{Site: "example.com", Host: "127.1"}),
    /* ... */
  )
}
```

Use `ø.Param` to declare individual query parameters, this combinator is suitable for simple queries, where definition of dedicated type seen as an overhead 

```go
func TestXxx() http.Arrow {
  return http.GET(
    /* ... */
    ø.Param("site", "example.com"),
    ø.Param("host", "127.1"),
    /* ... */
  )
}
```

### Request Headers

Use `ø.Header[T any](string, T)` to declares headers and its values into HTTP requests. The [standard HTTP headers](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields) are accomplished by a dedicated combinator making it type safe and easy to use e.g. `ø.ContentType.ApplicationJSON`.

```go
func TestXxx() http.Arrow {
  return http.GET(
    /* ... */
    ø.Header("Client", "curl/7.64.1"),
    ø.Authorization.Set("Bearer eyJhbGciOiJIU...adQssw5c"),
    ø.ContentType.ApplicationJSON,
    ø.Accept.JSON,
    /* ... */
  )
}
```

### Request payload

Use `ø.Send` to transmits the payload to the destination URI. The combinator takes standard data types (e.g. maps, struct, etc) and encodes it to binary using Content-Type header as a hint. It fails if content type header is not defined or not supported by the library.

```go
type MyType struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

// Encode struct to JSON
http.GET(
  // ...
  ø.ContentType.JSON,
  ø.Send(MyType{Site: "example.com", Host: "127.1"}),
)

// Encode map to www-form-urlencoded
http.GET(
  // ...
  ø.ContentType.Form,
  ø.Send(map[string]string{
    "site": "example.com",
    "host": "127.1",
  })
)

// Send string, []byte or io.Reader. Just define the right Content-Type
http.GET(
  // ...
  ø.ContentType.Form,
  ø.Send([]byte{"site=example.com&host=127.1"}),
)
```

On top of the shown type, it also support a raw octet-stream payload presented after one of the following Golang types: `string`, `*strings.Reader`, `[]byte`, `*bytes.Buffer`, `*bytes.Reader`, `io.Reader` and any arbitrary `struct`.
