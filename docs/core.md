---
layout: default
title: core
description: |
  A pure functional Golang style is core, human-friendly syntax to compose a chain of networking and represent them as pure computation.
permalink: /core
nav_order: 3
has_children: true
---

# Core

An expressive language is required to design the variety of network communication use-cases. A pure functional languages fits very well to express communication behavior because it gives a rich techniques to hide the networking complexity. Functional languages helps us to compose a chain of network operations and represent them as pure computation, build a new things from small reusable elements.

[https://assay.it](https://assay.it) adapts a human-friendly syntax to represent HTTP traffic:

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

This semantic provides an intuitive approach to specify HTTP traffic. Our service has adopted this syntax as Go native code, which provides a rich capability to network programming.

