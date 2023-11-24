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

### Generate event signature

Source: [book.getfoundry.sh](https://book.getfoundry.sh/reference/cast/cast-sig-event#cast-sig-event)

```sh
$ cast sig-event "Transfer(address indexed from, address indexed to, uint256 amount)"
0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
```

For example, when filtering event logs using `topic0`.


### Convert decimal to hex 

Source: [book.getfoundry.sh](https://book.getfoundry.sh/reference/cast/cast-to-hex#cast-to-hex)

```sh
$ cast to-hex "22584960"
0x1589e80
```

For example, block numbers must be in hex representation in JSON-RPC requests:

```json
{
    "jsonrpc": "2.0",
    "method": "eth_getLogs",
    "params": [
        {
            "fromBlock": "0x1589e80",
            "toBlock": "0x1589e80"
        }
    ],
    "id": 0
}
```