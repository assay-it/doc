---
layout: default
title: Introduction
description: |
  assay-it is testing tool for distributed application implemented with Go or other languages. It proposes a type safe and pure functional style for implementation of testing strategy. 
permalink: /introduction
nav_order: 3
---

# Introduction

`assay-it` is a cross platform tool for testing distributed application in production. 
{: .fs-6 .fw-300 }


## Why has it been made?

Microservices and cloud functions have become a design style to evolve system architecture in parallel, implement stable and consistent interfaces. This architecture style brings additional complexity and new problems -- quality assessment. 

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

`assay-it` uses [ᵍ🆄🆁🅻 library](https://github.com/fogfish/gurl) that provides type safe and and pure functional languages to express communication behavior by hiding the networking complexity. It implements a declarative approach for testing of distributed application. For the simplicity reason, testing suites are implemented using Domain Specific Language, which is genue and pure Golang syntax (actual GOlang code).

The development process of testing suites pitches for the composition of individual networking operations into pure computations.

```go
http.GET(                               // > GET /example HTTP/1.1
  s.URI("http://example.com/example"),  // > Host: example.com
  s.UserAgent.Set("curl/7.54.0"),       // > User-Agent: curl/7.54.0  
  s.Accept.ApplicationJSON,             // > Accept: application/json

  r.Status.OK,                          // < HTTP/1.1 200 OK
  r.ContentType.TextHTML,               // < Content-Type: text/html; charset=UTF-8
  r.Server.Is("ECS (phd/FD58)"),        // < Server: ECS (phd/FD58)
  r.Body(/*...*/),                      // < ... 
)
```

## Use cases

`assay-it` is built with automation to **confirm quality** and **eliminate risks** as a key feature along the development process of distributed application. With this tool engineering teams build continuous proofs of the quality helps to eliminate defects at earlier phases of the feature lifecycle, ensuring that software assets are always in a release-ready state.

`assay-it` implements both local and CI workflows, which are capable to validate quality of every single commit deployed into the cloud. 


Hopefully you find it useful, and the docs easy to follow.
{: .fs-6 .fw-300 }

Feel free to [create an issue](https://github.com/assay-it/assay-it/issues) if you find something that's not clear and [join our discussions](https://github.com/assay-it/assay-it/discussions) to chat with other users and maintainers.
