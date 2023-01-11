# spartan-ecdsa

ECDSA verification with a fork of Spartan that operates over the secq256k1 curve.

### What we use

**Spartan**

- We use a fork of Spartan that operates over the secq256k1 curve.

**Nova-Scotia**

- We use a fork of Nova-Scotia to compile Circom circuits into a binary that can be loaded by Spartan. We slightly modify Nova-Scotia to be compatible with secp256k1.

### About proving

**Witness generation**
We use the wasm witness generator generated by Circom to compute the witness. More specifically, the witness generation is done by running `snarkJs.wtns.calculate` (the actual code here).

**Proof generation**
The prover is a SpartanNIZK prover in wasm, which reads a circuit compiled by Nova-Scotia.

## Browser proving benchmarks

We use circuits with varying number of Poseidon hash instance (e.g. poseidon5.circom has 5 instances of Poseidon hashes).

**Prover**

- MacBook Pro (M1 Pro)
- Internet download speed: 170Mbps
- Browser: Brave

Spartan NIZK

| Circuit     | Constraints | Full proving time | Circuit (i.e. proving key ) size | Circuit download time | Proof size |
| ----------- | ----------- | ----------------- | -------------------------------- | --------------------- | ---------- |
| poseidon5   | 3045        | 1s                | 6.8 MB                           | 320ms                 | 12.9KB     |
| poseidon32  | 19488       | 4.5s              | 43.2 MB                          | 2s                    | 17.4KB     |
| poseidon256 | 155904      | 31s               | 345.9 MB                         | 16s                   | 32.8KB     |

Groth16

| Circuit     | Constraints | Full proving time | Zkey size |
| ----------- | ----------- | ----------------- | --------- |
| poseidon5   | 3045        | 950ms             | 4.6 MB    |
| poseidon32  | 19488       | 3.7s              | 29.8 MB   |
| poseidon256 | 155904      | 24s               | 238.1 MB  |

- The prover keys are hosted on Google Cloud Storage.
- We don't present the zkey download time for Groth16 due to the difficulty of benchmarking the processes inside snarkjs.

- The time required to download the circuit (i.e. proving key) is a major contributor to the full proving time. For mobile applications, we could download the circuit at app installation, relieving the burden of downloading the circuit at proving time.
- Spartan NIZK circuit serialization is not optimized; which is why Spartan NIZK has larger “proving keys”.

## Development

### Compile prover to wasm

Install wasm-pack

```
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```

Run compile script

```
sh ./scripts/build_wasm.sh
```

## Compile Circom R1CS to serialized Spartan circuit instance

```
cargo run --release --bin gen_spartan_inst
```

## Run compiled wasm in browser

### Get into browser_benchmark dir

```
cd ./packages/browser_benchmark
```

### Install dependencies

```
yarn
```

### Start server

```
yarn dev
```

## Build

Switch to nightly Rust using `rustup`:

```text
rustup default nightly
```

build

```
cargo build
```

## Run Circom circuit tests

Install [this](https://github.com/DanTehrani/circom-secq) fork of Circom that supports compiling to the secp256k1 base field.

```
git clone https://github.com/DanTehrani/circom-secq
```

```
cd circom && cargo build --release && cargo install --path circom
```

Install dependencies and run tests

```
cd packages/circuits
```

```
yarn
```

```
yarn jest
```
