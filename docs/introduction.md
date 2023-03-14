---
layout: default
title: Introduction
description: |
  assay-it is testing tool for distributed application implemented with Go or other languages. It proposes a type safe and pure functional style for implementation of testing strategy. 
permalink: /introduction
nav_order: 3
has_children: true
---

# Introduction

`assay-it` is testing tool for distributed application implemented with Go or other languages.  
{: .fs-6 .fw-300 }


## Why has it been made?

Microservices have become a design style to evolve system architecture in parallel, implement stable and consistent interfaces. This architecture style brings additional complexity and new problems -- quality assessment. 

Engineers uses **mocking and staging** environments as a primary testing strategy, which is more complex when your application is distributed. Therefore, teams **spend twice as much time** maintaining these imitations around genuine cloud services. The best effort simulation of the real environment is the real environment itself. 

How to implement “testing in production”? What would be the expressive language to implement a testing suites?

So far, `curl -v` implements the most human-friendly syntax to depict networking I/O:

```
> GET / HTTP/1.1
> Host: assay.it
> User-Agent: curl/7.54.0
> Accept: text/html
>
< HTTP/1.1 200 OK
< Content-Type: text/html; charset=UTF-8
< Server: ECS (phd/FD58)
< ...
```

The given semantic provides an intuitive approach to specify requests and responses, using Input/Process/Output paradigm. Adoption of this syntax as Go native code provides rich capabilities for programming quality assurance pipelines. 


## Key features

Standard Golang library implement a low-level protocol interfaces, which requires knowledge about the protocol itself, understanding of Golang implementation aspects, and a bit of boilerplate coding. It also misses standardized chaining (composition) of individual requests.

`assay-it` uses [ᵍ🆄🆁🅻 library](https://github.com/fogfish/gurl) that provides type and and pure functional languages to express communication behavior by hiding the networking complexity. It implements a declarative approach for testing of network protocols. Domain Specific Language declared by the library is genue and pure Golang code so that standard tools are in use.

The composition of individual networking operation to complex networking computations and represent them as pure computation is a philosophy casted on the test suite development.   


## Use cases

tbd

## Usage

tbd

