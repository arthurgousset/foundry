# Foundry (Cheat Sheet)

Foundry comes with four main binaries [`forge`](https://book.getfoundry.sh/forge), 
[`cast`](https://book.getfoundry.sh/cast), [`anvil`](https://book.getfoundry.sh/anvil), 
and [`chisel`](https://book.getfoundry.sh/chisel).

> Forge tests, builds, and deploys your smart contracts.

> Cast is Foundry's command-line tool for performing Ethereum RPC calls. You can make smart 
> contract calls, send transactions, or retrieve any type of chain data - all from your 
> command-line!

> Anvil is a local testnet node shipped with Foundry. You can use it for testing your 
> contracts from frontends or for interacting over RPC.

> Chisel is an advanced Solidity REPL shipped with Foundry. It can be used to quickly test 
> the behavior of Solidity snippets on a local or forked network.

Here are links to the commands you can run with each of these binaries:

-   [`forge` commands](https://book.getfoundry.sh/reference/forge/) 
-   [`cast` commands](https://book.getfoundry.sh/reference/cast/)
-   [`anvil` commands](https://book.getfoundry.sh/reference/anvil/)
-   [`chisel` commands](https://book.getfoundry.sh/reference/chisel/)

## Installation

Running `foundryup` by itself will install the latest (nightly) [precompiled binaries](https://book.getfoundry.sh/getting-started/installation#precompiled-binaries): `forge`, `cast`, `anvil`, and `chisel`. See `foundryup --help` for more options, like installing from a specific version or commit.

Source: [book.getfoundry.sh](https://book.getfoundry.sh/getting-started/installation)

## `cast`

### [`cast sig-event`](https://book.getfoundry.sh/reference/cast/cast-sig-event#cast-sig-event)

Generates event signatures from event string.
For example, get the hash for the log 
`Transfer(address indexed from, address indexed to, uint256 amount)`

```sh
cast sig-event "Transfer(address indexed from, address indexed to, uint256 amount)"
```