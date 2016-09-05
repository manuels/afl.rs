# Using it

First, add this project as a [Cargo][] dependency:

```toml
[dependencies]
afl = "0.1"
afl-plugin = "0.1"
```

Then you can add afl instrumentation to one or more crates:

```rust
#![feature(plugin)]
#![plugin(afl_plugin)]
```

You will also need a test executable that exercises the instrumented functions,
in a deterministic way based on input from stdin. This executable should link
the `afl` run-time library:

```rust
extern crate afl;
```

This will produce a binary that you can pass to `afl-fuzz` in the usual manner.

C++ code will be compiled by default with `g++`, though one can specify a different C++ compiler by setting the `CXX` environment variable to point to a different compiler binary.
