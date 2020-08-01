---
layout: default
title: HTTP style and syntax
description: |
  The composition of HTTP protocol is achieved from reusable primitive elements. All together they defines DSL syntax and coding style for Behavior as a Code.
parent: core
permalink: /docs/core/style
nav_order: 3
---

# Coding style and syntax of HTTP protocol

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

Symbol ƒ (option + f) is an convenient alias to module [gurl/http/recv](https://github.com/fogfish/gurl/blob/master/http/recv/arrows.go), which defines reader morphism that focuses into side-effect, HTTP protocol response. The reader morphism is a pattern matcher, is used to match HTTP response code, headers and response payload. It helps us to declare our expectations on the response.

### Status Code

Each quality assessment have to declare expected status code(s). The primitive `ƒ.Code` takes one or few [HTTP Status Codes](https://github.com/fogfish/gurl/blob/master/static.go). The assessment fails if microservice responds with other status.

```go
ƒ.Code(gurl.StatusCodeOK)
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

### Focus on the value

The quality assessment shall not only check the status of HTTP I/O but also assert the response value. There are few helper primitives. These primitives acts as lense they are focuses inside the structure and fetches values.

```go
var data MyType
ƒ.Recv(&data)

// checks if the value is defined (shall use pointer)
ƒ.Defined(&data.Site)

// checks if the value matches excepted one
ƒ.Value(&data).Is(&MyType{Site: "site", Host: "host"})
// checks if the value matches string literal
ƒ.Value(&data.Site).String("site")
ƒ.Value(/* ... */).Bytes([]byte{1, 2, 3, 4})
```

### Focus on the sequence

`ƒ.Value` lens has a limitation while working with sequences. It is not able to focus into distinct element, you should use `ƒ.Seq` instead.   

```go
var data Seq

// lookups element in the sequence
ƒ.Seq(&data).Has("site")
// lookups element in the sequence and matches it against expected value
ƒ.Seq(&data).Has("site", MyType{Site: "site", Host: "host"})
```

Your data type needs to implement `gurl.Ord` interface.

```go
type Ord interface {
  sort.Interface
  // String return primary key as string type
  String(int) string
  // Value return value at index
  Value(int) interface{}
}
```

`gurl.Ord` extends `sort.Interface` with ability to lookup element by string. In this example, the microservice returns sequence of elements. The lens `Seq` and `Has` focuses on the required element. A reference implementation of the interface is

```go
  type Seq []MyType

  func (c Seq) Len() int                { return len(c) }
  func (c Seq) Swap(i, j int)           { c[i], c[j] = c[j], c[i] }
  func (c Seq) Less(i, j int) bool      { return c[i].Site < c[j].Site }
  func (c Seq) String(i int) string     { return c[i].Site }
  func (c Seq) Value(i int) interface{} { return c[i] }
```


### Custom quality check

Often, you need to write a small function to apply a custom check on the received value. These custom functions shall be composable within the category pattern. Let's consider a previous example.

Your assessment has successfully received the sequence of elements into seq variable. We need to write a small function that extracts first elements from the sequence. A following traditional coding style does not work

```go
var seq Seq
gurl.HTTP(
  /* ... */
  ƒ.Recv(&seq)
)
head := seq[0]
```

The sequence is not "materialized" yet at the moment when `gurl.HTTP(...)` returns. It only returns a "promise" of HTTP I/O which is materialized later. Therefore, any computation have to be lifted-and-composed with this promise. `ƒ.FMap` does it. `ƒ.FMap` takes a closure and applies it to the results of network communication:

```go
var seq Seq
var head MyType
gurl.HTTP(
  /* ... */
  ƒ.Recv(&seq)
  ƒ.FMap(func() error {
    head := seq[0]
    return nil
  })
)
```
