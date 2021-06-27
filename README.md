# hashring

[![GoDoc][godoc-image]][godoc-url]
[![CI][ci-badge]][ci-url]

> Consistent hashing hashring implementation.

# Overview

This is an implementation of the consistent hashing hashring data structure.

In general, consistent hashing is all about mapping objects from a very big set
of values (e.g., request id) to objects from a quite small set (e.g. server
address). The word "consistent" means that it can produce consistent mapping on
different machines or processes without additional state exchange and
communication.

For more theory about the subject please see this [great
document][stanford-doc].

For more info about the package please read the [docs][godoc-url].

# The Why?

This is an approach for load distribution across servers which I found
convinient to be used in a few projects I had worked on in the past. 

I think it is good to implement it from scratch to synthesize all of my
experience of work with it and share it with the community in hope it will help
to build something good.

Two values of this packages are:
1) Efficiency of concurrent read operations
2) Correct handling of write operations (the order of adding items on different
processes doesn't matter; hash collisions are kept in mind).

# Installation

```bash
go get github.com/gobwas/hashring
```

# Usage

```go
package main

import (
	"strings"
	"io"

	"github.com/gobwas/hashring"
)

func main() {
	var ring hashring.Ring
	_ = ring.Insert(StringItem("server01"))
	_ = ring.Insert(StringItem("server02"))
	_ = ring.Insert(StringItem("server03"))
	_ = ring.Insert(StringItem("server04"))

	ring.Get(StringItem("user01")) // server04
	ring.Get(StringItem("user02")) // server04
	ring.Get(StringItem("user03")) // server02
	ring.Get(StringItem("user04")) // server01
}

type StringItem string

func (s StringItem) WriteTo(w io.Writer) (int64, error) {
	n, err := io.WriteString(w, string(s))
	return int64(n), err
}
```


[godoc-image]:  https://godoc.org/github.com/gobwas/hashring?status.svg
[godoc-url]:    https://godoc.org/github.com/gobwas/hashring
[ci-badge]:     https://github.com/gobwas/hashring/actions/workflows/main.yml/badge.svg?branch=main
[ci-url]:       https://github.com/gobwas/hashring/actions/workflows/main.yml
[stanford-doc]: https://theory.stanford.edu/~tim/s16/l/l1.pdf
