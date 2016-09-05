# Setup

## Requirements

* [rustup](https://rustup.rs/)
* Nightly build of Rust from any time after January 24, 2016 ([this issue](https://github.com/rust-lang/rust/pull/31176) prevented compatibility with previous builds of Rust)
* C++ compiler that supports C++11

LLVM 3.8

OSX
 
```
brew update
brew install homebrew/versions/llvm38
```

Linux

```
sudo apt-get update;
sudo apt-get install curl file g++ g++-multilib g++-4.9 g++-4.9-multilib libstdc++-4.8-dev llvm-3.8;
```

```
rustup default nightly-2016-07-30
```
