# WASI exploration

This repository was born for the purpose of structuring my learning of WebAssembly, specifically...

1. For Rust programming language;
2. For [WASI interface] (`wasm32-wasi` cargo/rustc target);
3. For [`wasmtime`] runtime.

## Concepts

* Command modules vs Reactor modules. See [Reactor modules support](#reactor-modules-support) for more details.
* [WIT] file format. Wasm Interface Type (WIT) format is an IDL to provide tooling for the WebAssembly Component Model.

## Useful crates and repositories

* [`cargo-wasi`] - A Cargo subcommand for convenient building and running of Rust programs for the `wasm32-wasi` target.
* [`wasi`] - Raw API bindings to the WebAssembly System Interface (WASI), providing also `wasi_snapshot_preview1` but not [WASI snapshot preview2](https://github.com/nodejs/uvwasi/issues/59).
* [`binaryen`] - Optimizer and compiler/toolchain library for WebAssembly.
* [`wabt`] - WABT: The WebAssembly Binary Toolkit.
* [`witx-codegen`] - A WITX code and documentation generator. WITX is a way to describe types and function interfaces for WebAssembly modules.

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

[`cargo-wasi`]: https://crates.io/crates/cargo-wasi
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
