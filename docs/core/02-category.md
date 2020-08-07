---
layout: default
title: Category pattern
description: | 
  A composition of HTTP primitives are expressed by the rules of category pattern. It represents networking as pure and re-usable computation.
parent: core
permalink: /core/category
nav_order: 2
---

# Category pattern

> Composition is the essence of programming...

The composition is a style of development to build a new things from small reusable elements. There is a challenge with standard Golang HTTP package. It implements low-level interface, which requires boilerplate code, and absence of advanced requests compositions.   

[**https://assay.it**](https://assay.it) uses a [gurl](https://github.com/fogfish/gurl) library. This library inherits an ability of pure functional languages to express communication behavior by hiding the networking complexity using category pattern. This pattern helps us to compose a chain of network operations and represent them as pure computation, build a new things from small reusable elements. This pattern is well know in functional programming languages such as Haskell and Scala but it ia a new thing for Golang.


## Background

The category theory formalizes principles that help us to define own abstraction applicable in functional programming through composition. The composition becomes a fundamental operation: the codomain of 𝒇 be the domain of 𝒈 so that the composite operation `𝒇 ◦ 𝒈` is defined.

A **category** is a concept that is defined in abstract terms of **objects**, **arrows** together with two functions **composition** `◦` and **identity** `𝒊𝒅`. These functions shall be compliant with category laws

1. Associativity : `(𝒇 ◦ 𝒈) ◦ 𝒉 = 𝒇 ◦ (𝒈 ◦ 𝒉)`
2. Left identity : `𝒊𝒅 ◦ 𝒇 = 𝒇`
3. Right identity : `𝒇 ◦ 𝒊𝒅 = 𝒇`

The category leaves the definition of *object*, *arrows*, *composition* and *identity* to us, which gives a powerful abstraction! 


## IO Category

A composition of HTTP primitives within the category are written with the following syntax:

```go
  gurl.HTTP(arrows ...gurl.Arrow) gurl.Arrow
```

Here, each arrow is a morphism applied to HTTP protocol. The implementation defines an abstraction of the protocol environments and lenses to focus inside it. In other words, the category represents the environment as an "invisible" side-effect of the composition. The example definition of HTTP I/O within the category becomes

```go
gurl.HTTP(
  ø..., 
  ø...,

  ƒ...,
  ƒ...,
)
```

Symbol ø (option + o) is an convenient alias to module [gurl/http/send](https://github.com/fogfish/gurl/blob/master/http/send/arrows.go), which defines writer morphism that focuses inside and reshapes HTTP protocol request. The writer morphism is used to declare HTTP method, destination URL, request headers and payload.

Symbol ƒ (option + f) is an convenient alias to module [gurl/http/recv](https://github.com/fogfish/gurl/blob/master/http/recv/arrows.go), which defines reader morphism that focuses into side-effect, HTTP protocol response. The reader morphism is a pattern matcher, is used to match HTTP response code, headers and response payload. It helps us to declare our expectations on the response. The evaluation of "program" fails if expectations do not match actual response.

The composition is one of major reason why we deviate from standard Golang HTTP interface. Instances of `gurl.Arrow` type are composable "promises" of HTTP I/O. The composition of `𝒇 ◦ 𝒈 ⟼ gurl.Arrow` leads results of same type and so on.

```go
gurl.Join(
  gurl.HTTP(/* ... */),
  gurl.HTTP(/* ... */),
)
```

Essentially, the quality assessment is just set of `gurl.Arrow` functions - Behavior as a Code in other words.

```go
func TestFoo() gurl.Arrow {
  return gurl.HTTP(/* ... */)
}

func TestBar() gurl.Arrow {
  return gurl.Join(
    gurl.HTTP(/* ... */),
    gurl.HTTP(/* ... */),
  )
}
```
