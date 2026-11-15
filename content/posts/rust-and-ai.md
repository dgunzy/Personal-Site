---
title: "Rust and AI-assisted development"
date: 2026-11-14
draft: false
description: "A few thoughts on why Rust's compiler is useful when working with AI-generated code."
tags: ["rust", "ai", "development", "platform-engineering"]
ShowToc: false
---

I chose Rust for flux9s partly because I was learning it at the time. Go probably would have been quicker to get started with, but kube-rs had the Watch API support I needed, and it seemed like a good project to learn on. In the [GA post on the Flux blog](https://fluxcd.io/blog/2026/10/flux9s-ga/), I also mentioned that Rust works well with AI-assisted development.

I think a lot of that comes down to the compiler. An agent can write something that looks reasonable but uses the wrong type, calls an API that doesn't exist in the installed version of a crate, or tries to use a value after moving it. Running `cargo check` gives it something specific to fix. That is useful when you're learning the language too, since you can work through the error and understand why the first attempt didn't compile.

Rust's [ownership rules](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html) put some limits on what an agent can get away with. It has to account for who owns a value and how that value is borrowed. The compiler checks those rules whether the code came from a person or an agent.

There are still ways to make a compiler error go away without improving the code. Adding `.clone()` everywhere can avoid having to think about ownership. Using `unwrap()` on a [`Result`](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html) can turn an error that should be handled into a panic. Both might compile, so those changes still need a look.

For a Kubernetes tool, there's also a lot the compiler can't tell you. A namespace is often just a string. Code can use a perfectly valid string and still fetch resources from the wrong namespace. Switching namespaces while a watch is running is the kind of behavior that needs testing, including what happens to the resources already on screen.

The same applies to the tests an agent writes. If the code and the test both assume the wrong namespace, a passing test doesn't help much. The expected behavior needs to come from the requirement, and some things still need to be tried against a cluster.

I wouldn't choose Rust just because an AI agent can work with it. But if it fits the project, having the compiler catch some of these mistakes before running the code is a good reason to consider the combination. It leaves plenty to review, but it does catch things that would otherwise turn up later.
