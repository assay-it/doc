---
layout: default
title: Assert Protocol Payload
description: |
  Quality assessment is not only about status of I/O protocols but also assert of responses. Generic category defines primitives that acts as lense to focus inside the structures and fetches values.
parent: core
permalink: /core/assert-protocol-payload
nav_order: 4
---

# Assert Protocol Payload

Quality assessment is not only about status of I/O protocols but also assert of the response content. The SDK defines [**generic category**](https://github.com/assay-it/sdk-go/blob/main/cats/arrows.go). Its primitives acts as lense they are focuses inside the structure, fetches values and asserts them. These helpers abort the evaluation of "program" if expectations do not match actual response.


Let's look on these primitives:
1. TOC
{:toc}


## Imports

Import asserts from the `cats` module:

```go
import (
  ç "github.com/assay-it/sdk-go/cats"
)
```

## Composition

Each assert is a "generic" arrow of type `assay.Arrow`. Use its compose functions

```go
assay.Join(
  http.Join(/* ... */),
  ç...,
  ç...,
)

http.Join(/* ... */).Then(
  ç...,
  ç...,
)
```

## Focus on the value


```go
var data MyType
http.Join(
  /* ... */
  ƒ.Recv(&data),
).Then(
  // checks if the value is defined (shall use pointer)
  ç.Defined(&data.Site),

  // checks if the value matches expected one
  ç.Value(&data).Is(&MyType{Site: "site", Host: "host"}),
  // checks if the value matches string literal
  ç.Value(&data.Site).String("site"),
  ç.Value(/* ... */).Bytes([]byte{1, 2, 3, 4}),
)
```

### Focus on the sequence

`ç.Value` lens has a limitation while working with sequences. It is not able to focus into distinct element, you should use `ç.Seq` instead.   

```go
var data Seq

// lookups element in the sequence by key
ç.Seq(&data).Has("site")
// lookups element in the sequence and matches it against expected value
ç.Seq(&data).Has("site", MyType{Site: "site", Host: "host"})
```

Your data type needs to implement `assay.Ord` interface.

```go
type Ord interface {
  sort.Interface
  // String return primary key as string type
  String(int) string
  // Value return value at index
  Value(int) interface{}
}
```

`assay.Ord` extends `sort.Interface` with ability to lookup element by string. In this example, the interface returns sequence of elements. The lens `Seq` and `Has` focuses on the required element. A reference implementation of the interface is

```go
  type Seq []MyType

  func (seq Seq) Len() int                { return len(seq) }
  func (seq Seq) Swap(i, j int)           { seq[i], seq[j] = seq[j], seq[i] }
  func (seq Seq) Less(i, j int) bool      { return seq[i].Site < seq[j].Site }
  func (seq Seq) String(i int) string     { return seq[i].Site }
  func (seq Seq) Value(i int) interface{} { return seq[i] }
```


### Custom quality check

Often, you need to write a small function to apply a custom check on the received value. These custom functions shall be composable within the category pattern. Let's consider a previous example.

Your assessment has successfully received the sequence of elements into seq variable. We need to write a small function that extracts first elements from the sequence. A following traditional coding style does not work

```go
var seq Seq
http.Join(
  /* ... */
  ƒ.Recv(&seq)
)
head := seq[0]
```

The sequence is not "materialized" yet at the moment when `http.Join(...)` returns. It only returns a "promise" of HTTP I/O which is materialized later. Therefore, any computation have to be lifted-and-composed with this promise. `ç.FMap` does it. `ç.FMap` takes a closure and applies it to the results of network communication:

```go
var seq Seq
var head MyType
http.Join(
  /* ... */
  ƒ.Recv(&seq)
).Then(
  ç.FMap(func() error {
    head := seq[0]
    return nil
  })
)
```
