<div align="center">
  <h1><code>wasix</code></h1>

<strong>A <a href="https://bytecodealliance.org/">Bytecode Alliance</a> project</strong>

  <p>
    <strong>WASI API Bindings for Rust</strong>
  </p>

  <p>
    <a href="https://crates.io/crates/wasi"><img src="https://img.shields.io/crates/v/wasi.svg?style=flat-square" alt="Crates.io version" /></a>
    <a href="https://crates.io/crates/wasi"><img src="https://img.shields.io/crates/d/wasi.svg?style=flat-square" alt="Download" /></a>
    <a href="https://docs.rs/wasi/"><img src="https://img.shields.io/badge/docs-latest-blue.svg?style=flat-square" alt="docs.rs docs" /></a>
  </p>
</div>

This crate contains API bindings for [WASIX](https://github.com/john-sharratt/wasix)
system calls in Rust, and currently reflects the `wasix_snapshot_preview1`
module. This crate is quite low-level and provides conceptually a "system call"
interface. In most settings, it's better to use the Rust standard library, which
has WASI support.

The `wasix` crate is also entirely procedurally generated from the `*.witx` files
describing the WASI apis. While some conveniences are provided the bindings here
are intentionally low-level!

# Usage

First you can depend on this crate via `Cargo.toml`:

```toml
[dependencies]
wasix = "0.11"
```

Next you can use the APIs in the root of the module like so:

```rust
fn main() {
    let stdout = 1;
    let message = "Hello, World!\n";
    let data = [wasix::Ciovec {
        buf: message.as_ptr(),
        buf_len: message.len(),
    }];
    wasix::fd_write(stdout, &data).unwrap();
}
```

Next you can use a tool like [`cargo
wasix`](https://github.com/john.sharratt/cargo-wasix) to compile and run your
project:

To compile Rust projects to wasm using WASI, use the `wasm64-wasix` target,
like this:

```
$ wasmer run wasix
   Compiling wasix v0.11.0+wasix-snapshot-preview1
   Compiling wut v0.1.0 (/code)
    Finished dev [unoptimized + debuginfo] target(s) in 0.34s
     Running `/.cargo/bin/cargo-wasix target/wasm64-wasix/debug/wut.wasm`
     Running `target/wasm64-wasix/debug/wut.wasm`
Hello, World!
```

# Development

The bulk of the `wasix` crate is generated by the `witx-bindgen` tool, which lives at
`crates/witx-bindgen` and is part of the cargo workspace.

The `src/lib_generated.rs` file can be re-generated with the following
command:

```
cargo run -p witx-bindgen -- crates/witx-bindgen/WASI/phases/snapshot/witx/wasix_v1.witx > src/lib_generated.rs
```

Note that this uses the WASIX standard repository as a submodule. If you do not
have this submodule present in your source tree, run:
```
git submodule update --init
```

# License

This project is licensed under the Apache 2.0 license with the LLVM exception.
See [LICENSE](LICENSE) for more details.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in this project by you, as defined in the Apache-2.0 license,
shall be licensed as above, without any additional terms or conditions.
