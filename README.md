# WASI exploration

This repository was born for the purpose of structuring my learning of WebAssembly, specifically...

1. For Rust programming language;
2. For [WASI interface] (`wasm32-wasi` cargo/rustc target);
3. For [`wasmtime`] runtime.

## Concepts

* Evolution of WASI. WASI evolved from (outdated) `wasi_snapshot_preview0` to (currently-standard) `wasi_snapshot_preview1` to (experimental) `wasi_snapshot_preview2`.
* Command modules vs Reactor modules in `wasi_snapshot_preview2`. See [Reactor modules support](#reactor-modules-support) for more details.
* Guest vs Host. The guest code is the `wasm32-wasi` code that is executed by the host. The host is the runtime that executes the guest code. The host provides the guest with the WASI interface, links it if necessary etc.
* [WIT] file format. Wasm Interface Type (WIT) format is an IDL to provide tooling for the WebAssembly Component Model.

## Useful crates and repositories

* [`cargo-wasi`] - A Cargo subcommand for convenient building and running of Rust programs for the `wasm32-wasi` target. Beware that there is an open issue [#143](https://github.com/bytecodealliance/cargo-wasi/issues/143) about the archiving of `cargo-wasi`.
* [`cargo-component`] - A cargo subcommand for creating WebAssembly components using Rust as the component's implementation language.
* [`wasi`] - Raw API bindings to the WebAssembly System Interface (WASI), providing also `wasi_snapshot_preview1` but not [WASI snapshot preview2](https://github.com/nodejs/uvwasi/issues/59). This crate is meant to be used for low-level WASI programming of guest code.
* [`wasmtime-wasi`] - WASI implementation for Wasmtime. This crate certainly can be used for adding WASI support to your host runtime. However, the author isn't yet sure whether it is possible to use it for low-level WASI programming of guest code.
* [`binaryen`] - Optimizer and compiler/toolchain library for WebAssembly.
* [`wabt`] - WABT: The WebAssembly Binary Toolkit.
* [`witx-codegen`] - A WITX code and documentation generator. WITX is a way to describe types and function interfaces for WebAssembly modules.
* [`wiggle`]  - Wiggle is a code generator for the host side of a witx interface. It is invoked as a Rust procedural macro. Wiggle is not specialized to any particular WebAssembly runtime. It is usable in at least Wasmtime and Lucet.

## Limitations and possible problems

### Reactor modules support

At the moment of writing, compiling WASM libraries for WASI interface using exclusively Cargo is not straightforward. While the support of Command modules for WASI in Rust is amazing, the support of Reactor modules is not. In order to build a Reactor module, you have to build a crate with unstable toolchain

```console
cargo +nightly rustc --release --target=wasm32-wasi -- -Z wasi-exec-model=reactor
```

To learn more about the difference between Command and Reactor modules, see ["WASI Command and Reactor Modules"] article by Dylibso.

#### Mitigation

According to ["WASI Command and Reactor Modules"],

> <...> [Extism] allows developers to create Reactor type WebAssembly modules while eliminating the hardships of memory management. <...>

### Absence of support for socket API

Even though there is a [`wasmedge_wasi_socket`] for [`wasmedge`], there is no such thing in WASI interface at the moment of writing. However, there is a [WASI API proposal for managing sockets] in phase 3/5 championed by [Dave Bakker @badeend].

### Partial support of `wasm32-wasi` target by [`tokio`] asynchronous runtime

At the moment of writing, only some features of [`tokio`] are supported for `wasm32-wasi` target, namely `sync`,`macros`, `io-util`, `rt`, `time`.

### `#[no_std]`  with WASI is tricky

See

* ["`#![no_std]` with WASI is more complicated than I thought it would be"][no_std_with_wasi] article on Medium;
* ["Adding experimental WebAssembly support to Decaton - Part 2"][wasm_support_to_decathon] article from LINE Engineering Blog;
* ["WASM Micro Runtime with Rust"][wasm_micro_runtime] article by Anoop Alias.

Also see <https://github.com/JohnScience/wasi-hello-world> for an example of a `#[no_std]` hello world program for WASI.

## `wasi_snapshot_preview2` is highly experimental

The author didn't find the official `.wit`/`.witx` files for `wasi_snapshot_preview2` in the official repository of the WASI interface. However, there is an archived [`preview2-prototyping`] repository in the Bytecode Alliance organization that contains the `.wit`/`.witx` files for `wasi_snapshot_preview2`.

## Companies with experience in WASI

* [Fastly](https://www.fastly.com/).

[`wasi`]: https://crates.io/crates/wasi
[`cargo-wasi`]: https://crates.io/crates/cargo-wasi
[`cargo-component`]: https://crates.io/crates/cargo-component
[`binaryen`]: https://github.com/webassembly/binaryen
[`wabt`]: https://github.com/WebAssembly/wabt
[`witx-codegen`]: https://crates.io/crates/witx-codegen
[WASI interface]: https://wasi.dev/
[`wasmtime`]: https://wasmtime.dev/
["WASI Command and Reactor Modules"]: https://dylibso.com/blog/wasi-command-reactor/
[Extism]: https://github.com/extism/extism
[`wasmedge_wasi_socket`]: https://crates.io/crates/wasmedge_wasi_socket
[`wasmedge`]: https://wasmedge.org/
[WASI API proposal for managing sockets]: https://github.com/WebAssembly/wasi-sockets
[Dave Bakker @badeend]: https://github.com/badeend
[`tokio`]: https://tokio.rs/
[no_std_with_wasi]: https://dev.to/thepuzzlemaker/nostd-with-wasi-is-more-complicated-than-i-thought-it-would-be-14j7
[wasm_support_to_decathon]: https://engineering.linecorp.com/en/blog/adding-experimental-webassembly-support-to-decaton-part-2
[wasm_micro_runtime]: https://anoopelias.github.io/posts/wasm-micro-runtime-with-rust/
[WIT]: https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md
[`wiggle`]: https://crates.io/crates/wiggle
[`preview2-prototyping`]: https://github.com/bytecodealliance/preview2-prototyping
