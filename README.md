# afl.rs

Fuzzing [Rust][] code with [american fuzzy lop (AFL)][american-fuzzy-lop]

<img src="https://i.imgur.com/RUZRyTO.gif" width="563" height="368" alt="Screen recording of afl">

Screen recording of AFL running on Rust code. The code under test is [`examples/hello.rs`][example] in this repository.

## What is it?

[Fuzz testing][] is a software testing technique used to find security and stability issues by providing pseudo-random data as input to the software. [American fuzzy lop][american-fuzzy-lop] is a popular, effective, and modern fuzz testing tool. This library, afl.rs, allows one to run AFL on code written in [the Rust programming language][rust].


### Treat panics as crashes

To look for logic errors in safe Rust code, use the `no-landing-pads` rustc flag
upon compilation of the AFL entrypoint.  This causes the fuzzer to treat any
Rust panic as a crash. Examples of usage:

* `rustc -Z no-landing-pads`
* `cargo rustc -- -Z no-landing-pads`

### Deferred init

If your program has a slow set-up phase that does not depend on the input data,
you can set `AFL_DEFER_FORKSRV=1` for a substantial speed-up, provided that you
insert a call to `afl::init()` after the set-up and before any
dependence on input. There are various other caveats, described in the section
"Bonus feature: deferred instrumentation" in `llvm_mode/README.llvm`
distributed with afl. See also [`examples/deferred-init.rs`][example-defer] in
this repository.

### Conditional compilation

afl instrumentation adds some run-time overhead, so it's a good candidate for
[conditional compilation][], perhaps through a [Cargo feature][]:

```toml
# You may need to add `optional = true` to the above dependencies.
[features]
afl = ["afl-plugin", "afl"]
```

```rust
// Active only with `cargo [...] --feature afl`
#![cfg_attr(feature = "afl", feature(plugin))]
#![cfg_attr(feature = "afl", plugin(afl_plugin))]
```


### AFL configuration

See the afl documentation for other configuration variables. Some of these are
set at compile time in `config.h`. For the most part they only affect
`afl-fuzz` itself, and will work fine with this library. However, if you change
`SHM_ENV_VAR`, `MAP_SIZE`, or `FORKSRV_FD`, you should update this library's
`src/config.h` to match.

## Upcoming changes

- [ ] [✨ Logo  ✨](https://github.com/frewsxcv/afl.rs/issues/66)
- [ ] [Simpler API](https://github.com/frewsxcv/afl.rs/issues/31)
- [ ] [Don't require users to compile Rust](https://github.com/frewsxcv/afl.rs/issues/41)
- [ ] Don't require users to have AFL installed (utilize afl-sys crate)

## Trophy case

* brotli-rs: [#2](https://github.com/ende76/brotli-rs/issues/2), [#3](https://github.com/ende76/brotli-rs/issues/3), [#4](https://github.com/ende76/brotli-rs/issues/4), [#5](https://github.com/ende76/brotli-rs/issues/5), [#6](https://github.com/ende76/brotli-rs/issues/6), [#7](https://github.com/ende76/brotli-rs/issues/7), [#8](https://github.com/ende76/brotli-rs/issues/8), [#9](https://github.com/ende76/brotli-rs/issues/9), [#10](https://github.com/ende76/brotli-rs/issues/10), [#11](https://github.com/ende76/brotli-rs/issues/11), [#12](https://github.com/ende76/brotli-rs/issues/12)
* flac: [#3](https://github.com/sourrust/flac/issues/3)
* httparse: [#9](https://github.com/seanmonstar/httparse/issues/9)
* image: [#414](https://github.com/PistonDevelopers/image/issues/414), [#473](https://github.com/PistonDevelopers/image/issues/473), [#474](https://github.com/PistonDevelopers/image/issues/474), [#477](https://github.com/PistonDevelopers/image/issues/477)
* jpeg-decoder: [#38](https://github.com/kaksmet/jpeg-decoder/issues/38)
* mp4parse-rust: [#2](https://github.com/mozilla/mp4parse-rust/issues/2), [#4](https://github.com/mozilla/mp4parse-rust/issues/4), [#5](https://github.com/mozilla/mp4parse-rust/issues/5), [#6](https://github.com/mozilla/mp4parse-rust/issues/6)
* rustc: [#24275](https://github.com/rust-lang/rust/issues/24275), [#24276](https://github.com/rust-lang/rust/issues/24276)
* rust-url: [#108](https://github.com/servo/rust-url/pull/108)
* regex: [#84](https://github.com/rust-lang/regex/issues/84)
* rust-asn1: [#32](https://github.com/alex/rust-asn1/issues/32)
* rustc-serialize: [#109](https://github.com/rust-lang/rustc-serialize/issues/109), [#110](https://github.com/rust-lang/rustc-serialize/issues/110)
* serde: [#75](https://github.com/serde-rs/serde/issues/75), [#77](https://github.com/serde-rs/serde/issues/77), [#82](https://github.com/serde-rs/serde/issues/82)
* tar-rs: [#23](https://github.com/alexcrichton/tar-rs/issues/23)
* xml-rs: [#93](https://github.com/netvl/xml-rs/issues/93)
* Logic errors in [tendril](https://github.com/kmcallister/tendril) and its [html5ever](https://github.com/servo/html5ever) integration

These bugs aren't nearly as serious as the [memory-safety issues afl has
discovered](http://lcamtuf.coredump.cx/afl/#bugs) in C and C++ projects.
That's because Rust is memory-safe by default, but also because not many people
have tried afl.rs yet! Over time we will update this section with the most
interesting bugs, whether they're logic errors or memory-safety problems
arising from `unsafe` code. Pull requests are welcome!

[conditional compilation]: https://doc.rust-lang.org/reference.html#conditional-compilation
[american-fuzzy-lop]: http://lcamtuf.coredump.cx/afl/
[Cargo feature]: http://doc.crates.io/manifest.html#the-[features]-section
[example-defer]: https://github.com/frewsxcv/afl.rs/blob/master/examples/deferred-init.rs
[build script]: https://github.com/frewsxcv/afl.rs/blob/master/plugin/build.bash
[from source]: https://github.com/rust-lang/rust#building-from-source
[LLVM pass]: https://github.com/frewsxcv/afl.rs/blob/master/plugin/src/afl-llvm-pass.o.cc
[example]: https://github.com/frewsxcv/afl.rs/blob/master/examples/hello.rs
[Cargo]: http://doc.crates.io/
[unresolved issue]: https://github.com/frewsxcv/afl.rs/issues/11
[fuzz testing]: https://en.wikipedia.org/wiki/Fuzz_testing
[Rust]: https://www.rust-lang.org
[rustup]: https://rustup.rs/
