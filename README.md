# Foundry (Cheat Sheet)

> [!INFO]  
> This is a cheat sheet on Foundry (mostly notes-to-self). They are incomplete by default.

Foundry has four main binaries:

1.  [`cast`](https://book.getfoundry.sh/reference/cast/): a CLI for performing Ethereum RPC calls,
    smart contract calls, send transactions, or retrieve any type of chain data.
1.  [`forge`](https://book.getfoundry.sh/reference/forge/): a developer tool to test, build, and
    deploy smart contracts.
1.  [`anvil`](https://book.getfoundry.sh/reference/anvil/): a local testnet node for testing
    contracts from frontends or for interacting over RPC.
1.  [`chisel`](https://book.getfoundry.sh/reference/chisel/): a Solidity REPL to test Solidity
    snippets on a local or forked network.

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

### Convert hex to decimal

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

### Convert wei to ether/gwei

Source: [`cast to-unit`](https://book.getfoundry.sh/reference/cli/cast/to-unit)

Convert an ETH amount into another unit (ether, gwei or wei).

```sh
$ cast to-unit 5000000000 gwei
5
```

For example:

```sh
$ cast base-fee -r https://alfajores-forno.celo-testnet.org
5000000000

$ cast to-unit 5000000000 gwei
5
```

```sh
$ curl -s -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":["0x4822e58de6f5e485eF90df51C41CE01721331dC0"],"id":1}' "https://alfajores-forno.celo-testnet.org/" | jq
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x203512c12"
}

$ cast to-dec "0x203512c12"
8645585938

$ cast to-unit 8645585938 gwei
8.645585938
```

### Convert ether/gwei to wei

Source: [`cast to-wei`](https://book.getfoundry.sh/reference/cli/cast/to-wei)

Convert an ETH amount to wei.

```sh
$ cast to-wei 5 ether
5000000000000000000
```

For example:

```sh
$ cast to-wei 5 gwei
5000000000

$ cast to-unit 5000000000 gwei
5
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

> [!TIP]  
> Find RPC URLs for Celo at
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

For example, to get transaction receipt of
[`0xe6b1eda9...`](https://explorer.celo.org/alfajores/tx/0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a)
on the Alfajores testnet.

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

### Call RPC methods

Source: [`cast rpc`](https://book.getfoundry.sh/reference/cast/cast-rpc)

Perform a simple JSON-RPC POST request for the given method and with the params.

For example, for [`eth_getBlockByNumber`](https://www.quicknode.com/docs/celo/eth_getBlockByNumber),
I use QuickNode's JSON-RPC documentation to check what params the method takes.

```sh
$ cast rpc eth_getBlockByNumber "0x164e475" "true" \
--rpc-url https://alfajores-forno.celo-testnet.org | jq

{
  "baseFeePerGas": "0x12a05f200",
  ...
  "size": "0x4e9",
  "stateRoot": "0xf56faac2ca299965f2c6e91f8430060cdae80892bf4392ace1b6770ea07c9abd",
  "timestamp": "0x6610338c",
  "totalDifficulty": "0x164e476",
  "transactions": [
    {
      ...
    },
    {
      "blockHash": "0xb820cfe19395b0652c54d5401e3dc6a1b62e6232fbc61c1d668c99056b08240f",
      "blockNumber": "0x164e475",
      "from": "0x303c22e6ef01cba9d03259248863836cb91336d5",
      "gas": "0x23c90",
      "gasPrice": null,
      "maxFeePerGas": "0x2e90edd00",
      "maxPriorityFeePerGas": "0x9502f900",
      "feeCurrency": "0x4822e58de6f5e485ef90df51c41ce01721331dc0",
      "gatewayFeeRecipient": null,
      "gatewayFee": "0x0",
      "hash": "0xe6b1eda941c32f65c129e286f875afc1c265683d01e1232c696d9ebb1ce9174a",
      "input": "0xa9059cbb0000000000000000000000005111a8caca3366389eeaaad8a49027d573588bbb0000000000000000000000000000000000000000000000000000000000002710",
      "nonce": "0x75",
      "to": "0x2f25deb3848c207fc8e0c34035b3ba7fc157602b",
      "transactionIndex": "0x1",
      "value": "0x0",
      "type": "0x7b",
      "accessList": [],
      "chainId": "0xaef3",
      "v": "0x1",
      "r": "0xcdb39723aa7e176d1a76f2ce54ac13f22d43d7adb43ba8afac8ec69c94b6a005",
      "s": "0x66ad6afc907daa892c64ed38da35ff6ffebcd5b92a79940554e88dd4f381dfc3",
      "ethCompatible": false
    }
  ],
  "transactionsRoot": ...
}
```

### Get base fee from last block

Source: [`cast base-fee`](https://book.getfoundry.sh/reference/cli/cast/base-fee)

```sh
$ cast base-fee -r https://alfajores-forno.celo-testnet.org
5000000000
```

```sh
$ cast base-fee -r https://forno.celo.org
5000000000
```

## `forge`

### Deploy contract

```sh
forge create [OPTIONS] `<path>:<contractname>`
```
