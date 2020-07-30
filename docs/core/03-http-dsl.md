---
layout: default
title: Syntax of HTTP protocol
parent: core
permalink: /docs/core/http
nav_order: 3
---

# Syntax of HTTP protocol

`gurl.HTTP(arrows ...gurl.Arrow) gurl.Arrow` builds higher-order HTTP closure, so called `gurl.Arrow`, from primitive elements.

```go
gurl.HTTP(
  ø.GET("https://assay.it"),
  ƒ.Code(gurl.StatusCodeOK),
  ƒ.Header("Content-Type").Is("text/html"),
)
```

Let's look on these primitives:
1. TOC
{:toc}

## Imports

Any suite requires at least these three modules:

```go
import (
  "github.com/fogfish/gurl"
  ƒ "github.com/fogfish/gurl/http/recv"
  ø "github.com/fogfish/gurl/http/send"
)
```

## Writer

Symbol ø (option + o) is an convenient alias to module [gurl/http/send](https://github.com/fogfish/gurl/blob/master/http/send/arrows.go), which defines writer morphism that focuses inside and reshapes HTTP protocol request. The writer morphism is used to declare HTTP method, destination URL, request headers and payload.


### Method & URL

Like any HTTP request, you have to define method and destination URL: 

```go
// built-in method
ø.GET("http://example.com")
ø.POST("http://example.com")
ø.PUT("http://example.com")
ø.DELETE("http://example.com")

// generic variant 
ø.URL("PATCH", "http://example.com")
```

These functions are able to format URL according to [standard specifiers](https://golang.org/pkg/fmt). 

```go
ø.GET("http://example.com/%s/%d", "test", 10)

lit := "test"
ø.GET("http://example.com/%s", lit)
ø.GET("http://example.com/%s", &lit)
```

<span class="label label-yellow">Important</span> Formatted values are escaped using [percent encoding](https://en.wikipedia.org/wiki/Percent-encoding).

```go
// this ok
ø.GET("https://%s/%s", "example.com", "test")

// this do not work
ø.GET("https://%s", "example.com/test")
```

### Query Params

It is possible to use inline query parameters:
```go
ø.GET("http://example.com/?tag=%s", "test")
```

However, that method do not implement any type safeness. The library implement an arrow `ø.Params`. It takes a struct serializes it to string and appends query params. You are able to define proper types for your api:

```go
type MyParam struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

ø.Params(MyParam{"host", "site"})
```

### Headers

Headers are optional, you define as many headers as needed either using string literals or state variables.

```go
// the literal value is defined using Is 
ø.Header("Accept").Is("application/json")

// the header value is defined from state variable
ø.Header("Authorization").Val(&token)
```

<span class="label label-yellow">Important</span> There are header constants for frequently used headers. 

## Reader

Symbol ƒ (option + f) is an convenient alias to module [gurl/http/recv](https://github.com/fogfish/gurl/blob/master/http/recv/arrows.go), which defines reader morphism that focuses into side-effect, HTTP protocol response. The reader morphism is a pattern matcher, is used to match HTTP response code, headers and response payload. It helps us to declare our expectations on the response.