---
layout: default
title: HTTP I/O syntax 
description: |
  The composition of HTTP protocol is achieved from reusable primitive elements. All together they defines DSL syntax and coding style for Behavior as a Code.
parent: core
permalink: /core/style
nav_order: 3
---

# HTTP: coding style and syntax

```go
http.Join(arrows ...http.Arrow) assay.Arrow
```
builds higher-order HTTP closure, so called `assay.Arrow`, from primitive elements.

```go
http.Join(
  ø.GET("https://assay.it"),
  ƒ.Code(http.StatusCodeOK),
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
  "github.com/assay-it/sdk-go/http"
  ƒ "github.com/assay-it/sdk-go/http/recv"
  ø "github.com/assay-it/sdk-go/http/send"
)
```

## Writer

[Writer morphism (Symbol ø)](https://github.com/assay-it/sdk-go/blob/main/http/send/arrows.go) focuses inside and reshapes HTTP protocol request. The writer morphism is used to declare HTTP method, destination URL, request headers and payload.


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

It is possible to use inline query parameters but it is not a type safe approach:
```go
ø.GET("http://example.com/?tag=%s", "test")
```

The SDK implement an arrow `ø.Params`. It takes a struct serializes it to string and appends query params. You are able to define proper types for your api:

```go
type MyParam struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

ø.Params(MyParam{Site: "site", Host: "host"})
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


### Request payload

`ø.Send` transmits the payload to destination URL. The function takes Go data types (e.g. maps, struct, etc) and encodes its to binary using Content-Type as a hint. The function fails if content type is not supported by the library.

```go
type MyType struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

ø.Send(MyType{Site: "site", Host: "host"})
ø.Send(map[string]string{
  "site": "site",
  "host": "host",
})
```

Only following content types supported:
* `application/json`
* `application/x-www-form-urlencoded`


## Reader

[Reader morphism (Symbol ƒ)](https://github.com/assay-it/sdk-go/blob/main/http/recv/arrows.go) focuses into side-effect, HTTP protocol response. The reader morphism is a pattern matcher, is used to match HTTP response code, headers and response payload. 


### Status Code

Each quality assessment have to declare expected status code(s). The primitive `ƒ.Code` takes one or few [HTTP Status Codes](https://github.com/assay-it/sdk-go/blob/main/http/status.go). The assessment fails if microservice responds with other status.

```go
ƒ.Code(http.StatusCodeOK)
```

### Headers

It is possible to match value of HTTP header in the response. The assessment fails if response is missing header or its value do not matches desired one:

```go
// matches Content-Type value 
ƒ.Header("Content-Type").Is("application/json")

// matches any value of Content-Type header
ƒ.Header("Content-Type").Is("*")
ƒ.Header("Content-Type").Any()
```

The assessment statements can lifts the header value to the variable.

```go
var content string
ƒ.Header("Content-Type").String(&content)
```

### Response payload

`ƒ.Recv` decodes the response payload to Golang native data structure using Content-Type header as a hint.

```go
type MyType struct {
  Site string `json:"site,omitempty"`
  Host string `json:"host,omitempty"`
}

var data MyType
ƒ.Recv(&data)
```

The codec only supports:
* `application/json`
* `application/x-www-form-urlencoded`

It is possible to bypass auto-codec and receive raw binary data

```go
var data []byte
ƒ.Bytes(&data)
```

