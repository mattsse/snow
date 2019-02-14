# Snow (with I2P Noise extensions)

[![Crates.io](https://img.shields.io/crates/v/i2p_snow.svg)](https://crates.io/crates/i2p_snow)
[![Docs.rs](https://docs.rs/i2p_snow/badge.svg)](https://docs.rs/i2p_snow)

![totally official snow logo](https://i.imgur.com/gFgvo49.jpg?1)

An implementation of Trevor Perrin's [Noise Protocol](https://noiseprotocol.org/) that is designed to be
Hard To Fuck Up™.

This codebase contains additional Noise extensions used by [I2P](https://geti2p.net) for the
[NTCP2 protocol](https://geti2p.net/spec/ntcp2). Look [here](https://snow.rs) for the unmodified crate.

🔥 **Warning** 🔥 This library has not received any formal audit.

## What's it look like?
See `examples/simple.rs` for a more complete TCP client/server example.

```rust
let mut noise = i2p_snow::Builder::new("Noise_NN_25519_ChaChaPoly_BLAKE2s".parse()?)
                    .build_initiator()?;
 
let mut buf = [0u8; 65535];
 
// write first handshake message
noise.write_message(&[], &mut buf)?;
 
// receive response message
let incoming = receive_message_from_the_mysterious_ether();
noise.read_message(&incoming, &mut buf)?;
 
// complete handshake, and transition the state machine into transport mode
let mut noise = noise.into_transport_mode()?;
```

See the full documentation at [https://docs.rs/i2p_snow](https://docs.rs/i2p_snow).


## Implemented

Snow is currently tracking against [Noise spec revision 34](https://noiseprotocol.org/noise_rev34.html).

However, a not all features have been implemented yet (pull requests welcome):

- [ ] [The `fallback` modifier](https://noiseprotocol.org/noise_rev34.html#the-fallback-modifier)

## Crypto
Cryptographic providers are swappable through `Builder::with_resolver()`, but by default it chooses select, artisanal
pure-Rust implementations (see `Cargo.toml` for a quick overview).

### Providers

#### ring

[ring](https://github.com/briansmith/ring) is a crypto library based off of BoringSSL and is significantly faster than most of the pure-Rust implementations.

If you enable the `ring-resolver` feature, Snow will include a `ring_wrapper` module as well as a `RingAcceleratedResolver` available to be used with `Builder::with_resolver()`.

If you enable the `ring-accelerated` feature, Snow will default to choosing `ring`'s crypto implementations when available.

#### HACL*

[HACL*](https://github.com/mitls/hacl-star) is a formally verified cryptographic library, accessed via the [`rust-hacl-star`](https://github.com/quininer/rust-hacl-star) wrapper crate.

If you enable the `hacl-resolver` feature, Snow will include a `hacl_wrapper` module as well as a `HaclStarResolver` available to be used with `Builder::with_resolver()`.

Similar to ring, if you enable the `hacl-accelerated` feature, Snow will default to choosing HACL* implementations when available.

### Resolver primitives supported

|            | default | ring | hacl* |
|-----------:|:-------:|:----:|:-----:|
| CSPRNG     | ✔       |      |       |
| 25519      | ✔       | ✔    | ✔     |
| 448        |         |      |       |
| AESGCM     | ✔       | ✔    |       |
| ChaChaPoly | ✔       | ✔    | ✔     |
| SHA256     | ✔       | ✔    | ✔     |
| SHA512     | ✔       | ✔    | ✔     |
| BLAKE2s    | ✔       |      |       |
| BLAKE2b    | ✔       |      |       |
