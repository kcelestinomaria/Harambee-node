### The Harambee Blockchain Node
This is the node that runs the Harambee Blockchain

### Getting Started 

## Using Nix
Install nix and optionally direnv and lorri for a fully plug and play experience for setting up the development environment. To get all the correct dependencies activate direnv direnv allow and lorri lorri shell.

Rust Setup
First, complete the basic Rust setup instructions. Install Rust and the Cargo package manager

Run
Use Rust's native cargo command to build and launch the template node:

```cargo run --release -- --dev --tmp```

Build
The cargo run command will perform an initial build. Use the following command to build the node without launching it:

```cargo build --release```
Embedded Docs
Once the node has been built(compiled) on your machine, the following command can be used to explore all parameters and subcommands:

```./target/release/node-template -h```

Run
The provided cargo run command will launch a temporary node and its state will be discarded after you terminate the process. After the project has been built, there are other ways to launch the node.

Single-Node Development Chain
This command will start the single-node development chain with persistent state:

```./target/release/node-template --dev```


Purge the development chain's state:

```./target/release/node-template purge-chain --dev```
Start the development chain with detailed logging:

```RUST_LOG=debug RUST_BACKTRACE=1 ./target/release/node-template -lruntime=deb```

### Note 

- This code is still in Hack mode and as such, a few things will change i.e More customized pallets(an EVM pallet customized for Reach, and a Balances pallet) will be added

- Also, for a separation of concerns, most of the codebase 
will be moving to the Harambee Network github org 
