# Foundry (Cheat Sheet)

Foundry comes with four main binaries [`forge`](https://book.getfoundry.sh/forge),
[`cast`](https://book.getfoundry.sh/cast), [`anvil`](https://book.getfoundry.sh/anvil), and
[`chisel`](https://book.getfoundry.sh/chisel).

> Forge tests, builds, and deploys your smart contracts.

> Cast is Foundry's command-line tool for performing Ethereum RPC calls. You can make smart contract
> calls, send transactions, or retrieve any type of chain data - all from your command-line!

> Anvil is a local testnet node shipped with Foundry. You can use it for testing your contracts from
> frontends or for interacting over RPC.

> Chisel is an advanced Solidity REPL shipped with Foundry. It can be used to quickly test the
> behavior of Solidity snippets on a local or forked network.

Here are links to the commands you can run with each of these binaries:

-   [`forge` commands](https://book.getfoundry.sh/reference/forge/)
-   [`cast` commands](https://book.getfoundry.sh/reference/cast/)
-   [`anvil` commands](https://book.getfoundry.sh/reference/anvil/)
-   [`chisel` commands](https://book.getfoundry.sh/reference/chisel/)

## Installation

Running `foundryup` by itself will install the latest
(nightly) [precompiled binaries](https://book.getfoundry.sh/getting-started/installation#precompiled-binaries): `forge`, `cast`, `anvil`,
and `chisel`. See `foundryup --help` for more options, like installing from a specific version or
commit.

Source: [book.getfoundry.sh](https://book.getfoundry.sh/getting-started/installation)

## `cast`

### Generate event signature

Source:
[book.getfoundry.sh](https://book.getfoundry.sh/reference/cast/cast-sig-event#cast-sig-event)

```sh
$ cast sig-event "Transfer(address indexed from, address indexed to, uint256 amount)"
0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef

$ cast sig-event "event ValidatorGroupVoteActivated(address indexed account,address indexed group,uint256 value,uint256 units)"
0x45aac85f38083b18efe2d441a65b9c1ae177c78307cb5a5d4aec8f7dbcaeabfe
```

For example, when filtering event logs using `topic0` on [dune.com](https://dune.com/).

```sql
SELECT *
FROM celo.logs
WHERE contract_address = 0x8d6677192144292870907e3fa8a5527fe55a7ff6 -- ElectionProxy
    AND topic0 = 0x45aac85f38083b18efe2d441a65b9c1ae177c78307cb5a5d4aec8f7dbcaeabfe -- ValidatorGroupVoteActivated
```

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

### Convert number (from one base) to decimal

Source: [book.getfoundry.sh](https://book.getfoundry.sh/reference/cast/cast-to-dec)

```sh
$ cast to-dec "0x0000000000000000000000000000000000000000000000000000000000000012"
18
```

For example, convert result of a contract read call into decimal:

```sh
$ cast call 0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1 "decimals()" --rpc-url='https://alfajores-forno.celo-testnet.org' | cast to-dec
18
```

### Call smart contract function

Call the `getWhitelist()` function on the
[`FeeCurrencyWhitelist.sol`](https://github.com/celo-org/celo-monorepo/blob/cc8c3448938f7ff3e1f4e7a5ab692904729dcdc9/packages/protocol/contracts/common/FeeCurrencyWhitelist.sol#L4)
contract deployed at
[`0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c`](https://celoscan.io/address/0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c).

```sh
$ cast call 0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c "getWhitelist() (address[] memory)" --rpc-url='https://forno.celo.org'
[0x765DE816845861e75A25fCA122bb6898B8B1282a, 0xD8763CBa276a3738E6DE85b4b3bF5FDed6D6cA73, 0xe8537a3d056DA446677B9E9d6c5dB704EaAb4787, 0x73F93dcc49cB8A239e2032663e9475dd5ef29A08]
```

> [!TIP] Find RPC URLs for Celo at
> [docs.celo.org/network/node/forno](https://docs.celo.org/network/node/forno)https://docs.celo.org/network/node/forno

### Get event logs

Source: [`cast logs`](https://book.getfoundry.sh/reference/cast/cast-logs)

```sh
$ cast logs --rpc-url 'https://forno.celo.org' 'event ValidatorEpochPaymentDistributed(address indexed validator, uint256 validatorPayment, address indexed group, uint256 groupPayment)' --from-block 24883200 --to-block 24883200
```

Example response:

```sh
# ...
- address: 0xaEb865bCa93DdC8F47b8e29F40C5399cE34d0C58
  blockHash: 0xc4c566ca9e7a494567f5076d0cd814fc6cd8408d5a675959efc8fc2ca9126a70
  blockNumber: 24883200
  data: 0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004fe01f72f47dbef70
  logIndex: 453
  removed: false
  topics: [
        0x6f5937add2ec38a0fa4959bccd86e3fcc2aafb706cd3e6c0565f87a7b36b9975
        0x000000000000000000000000e8302a78c4eb56ac1cd1d6aee25719dc54c63e59
        0x000000000000000000000000d25c6a9fef4744e8d4f90bf6bdfaf7686909d799
  ]
  transactionHash: 0xc4c566ca9e7a494567f5076d0cd814fc6cd8408d5a675959efc8fc2ca9126a70
  transactionIndex: 7
- address: 0xaEb865bCa93DdC8F47b8e29F40C5399cE34d0C58
  blockHash: 0xc4c566ca9e7a494567f5076d0cd814fc6cd8408d5a675959efc8fc2ca9126a70
  blockNumber: 24883200
  data: 0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004f5e53ac258c8b63c
  logIndex: 456
  removed: false
  topics: [
        0x6f5937add2ec38a0fa4959bccd86e3fcc2aafb706cd3e6c0565f87a7b36b9975
        0x000000000000000000000000989f3f2684f96b8cdec308b4e7538a5a062890f0
        0x000000000000000000000000d25c6a9fef4744e8d4f90bf6bdfaf7686909d799
  ]
  transactionHash: 0xc4c566ca9e7a494567f5076d0cd814fc6cd8408d5a675959efc8fc2ca9126a70
  transactionIndex: 7
# ...
```

### Get transaction receipt for a transaction

Source:
[`cast receipt`](https://book.getfoundry.sh/reference/cli/cast/receipt?highlight=receipt#cast-receipt)

For example, to get transaction receipt of [`0xe6b1eda9...`](https://explorer.celo.org/alfajores/tx/0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a) on the Alfajores testnet.

```sh
$ cast receipt 0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a --rpc-url 'https://alfajores-forno.celo-testnet.org'

blockHash               0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f
blockNumber             23389301
contractAddress
cumulativeGasUsed       362425
effectiveGasPrice
gasUsed                 81747
logs                    [{"address":"0x2f25deb3848c207fc8e0c34035b3ba7fc157602b","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x000000000000000000000000303c22e6ef01cba9d03259248863836cb91336d5","0x0000000000000000000000000000000000000000000000000000000000000000"],"data":"0x0000000000000000000000000000000000000000000000000000000000000487","blockHash":"0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f","blockNumber":"0x164e475","transactionHash":"0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a","transactionIndex":"0x1","logIndex":"0x2","removed":false},{"address":"0x2f25deb3848c207fc8e0c34035b3ba7fc157602b","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x000000000000000000000000303c22e6ef01cba9d03259248863836cb91336d5","0x0000000000000000000000005111a8caca3366389eeaaad8a49027d573588bbb"],"data":"0x0000000000000000000000000000000000000000000000000000000000002710","blockHash":"0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f","blockNumber":"0x164e475","transactionHash":"0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a","transactionIndex":"0x1","logIndex":"0x3","removed":false},{"address":"0x2f25deb3848c207fc8e0c34035b3ba7fc157602b","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x0000000000000000000000000000000000000000000000000000000000000000","0x000000000000000000000000303c22e6ef01cba9d03259248863836cb91336d5"],"data":"0x0000000000000000000000000000000000000000000000000000000000000487","blockHash":"0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f","blockNumber":"0x164e475","transactionHash":"0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a","transactionIndex":"0x1","logIndex":"0x4","removed":false},{"address":"0x2f25deb3848c207fc8e0c34035b3ba7fc157602b","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x000000000000000000000000303c22e6ef01cba9d03259248863836cb91336d5","0x0000000000000000000000001443326496c9775c50adc6e8a26ccb79ad4d00ff"],"data":"0x00000000000000000000000000000000000000000000000000000000000000cc","blockHash":"0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f","blockNumber":"0x164e475","transactionHash":"0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a","transactionIndex":"0x1","logIndex":"0x5","removed":false},{"address":"0x2f25deb3848c207fc8e0c34035b3ba7fc157602b","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x000000000000000000000000303c22e6ef01cba9d03259248863836cb91336d5","0x000000000000000000000000eaaff71ab67b5d0ef34ba62ea06ac3d3e2daaa38"],"data":"0x00000000000000000000000000000000000000000000000000000000000001bb","blockHash":"0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f","blockNumber":"0x164e475","transactionHash":"0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a","transactionIndex":"0x1","logIndex":"0x6","removed":false}]
logsBloom               0x00000000000000000001000800000400000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000000000000000000000000000008000000000000000000000000000000000000000000000000020000000000830000000800000000800000000080000011000000000000000000000000000000000000000000000000000000000008000000000000000000000000000000000000000000000000000000000000000000000000000000000202000000000000008008000000000000000000000000000000000020000000000000000800000000000000000000000000000000000000000000000000
root
status                  1
transactionHash         0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a
transactionIndex        1
type                    123
```

## `forge`

### Deploy contract

```sh
forge create [OPTIONS] `<path>:<contractname>`
```
