# Foundry (Cheat Sheet)

> [!NOTE]  
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

Other cheat sheets:

- [github.com/dabit3/foundry-cheatsheet](https://github.com/dabit3/foundry-cheatsheet)

## Installation (`foundryup`)

Running `foundryup` by itself will install the latest
(nightly) [precompiled binaries](https://book.getfoundry.sh/getting-started/installation#precompiled-binaries): `forge`, `cast`, `anvil`,
and `chisel`. See `foundryup --help` for more options, like installing from a specific version or
commit.

Source: [book.getfoundry.sh](https://book.getfoundry.sh/getting-started/installation)

### Check the version

```sh
$ cast --version
cast 0.2.0 (6b72a8c 2023-11-06T00:23:40.259531000Z)
```

### Install a specific version

From a specific commit:

```sh
$ foundryup -C 2885b0d
```

Syntax:

```sh
$ foundryup --help
The installer for Foundry.

Update or revert to a specific Foundry version with ease.

USAGE:
    foundryup <OPTIONS>

OPTIONS:
    -h, --help      Print help information
    -v, --version   Install a specific version
    -b, --branch    Install a specific branch
    -P, --pr        Install a specific Pull Request
    -C, --commit    Install a specific commit
    -r, --repo      Install from a remote GitHub repo (uses default branch if no other options are set)
    -p, --path      Install a local repository
    --arch          Install a specific architecture (supports amd64 and arm64)
    --platform      Install a specific platform (supports win32, linux, and darwin)
```

## `cast`

### Call smart contract function

Source: [`cast call`](https://book.getfoundry.sh/reference/cast/cast-call)

### Read contract call

Source: [`cast call`](https://book.getfoundry.sh/reference/cast/cast-call)

For example:

```sh
$ cast call \
0x000000000000000000000000000000000000ce10 \
"getAddressForStringOrDie(string calldata identifier)" \
"FeeCurrencyDirectory" \
--rpc-url=http://127.0.0.1:8546

0x00000000000000000000000042fe5a2a61ed9705eb2f08a04a58ceb606d22f6a

$ cast abi-decode \
"getAddressForStringOrDie(string calldata identifier)(address)" \
"0x00000000000000000000000042fe5a2a61ed9705eb2f08a04a58ceb606d22f6a"

0x42Fe5a2A61ed9705eb2F08a04A58CEB606D22f6a
```

```sh
cast call \
0xe6774BE4E5f97dB10cAFB4c00C74cFbdCDc434D9 \
"name()" \
--rpc-url=http://127.0.0.1:8546
0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000b43656c6f20446f6c6c6172000000000000000000000000000000000000000000

cast abi-decode \
"name()(string memory)" \
"0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000b43656c6f20446f6c6c6172000000000000000000000000000000000000000000"
"Celo Dollar"
```

Source: PR "[Add FeeCurrencyDirectory to Anvil migrations#10992](https://github.com/celo-org/celo-monorepo/pull/10992)"

Call the `getWhitelist()` function on the
[`FeeCurrencyWhitelist.sol`](https://github.com/celo-org/celo-monorepo/blob/cc8c3448938f7ff3e1f4e7a5ab692904729dcdc9/packages/protocol/contracts/common/FeeCurrencyWhitelist.sol#L4)
contract deployed at
[`0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c`](https://celoscan.io/address/0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c).

```sh
$ cast call \ 
0xbb024e9cdcb2f9e34d893630d19611b8a5381b3c \
"getWhitelist()(address[] memory)" \
--rpc-url='https://forno.celo.org'

[0x765DE816845861e75A25fCA122bb6898B8B1282a, 0xD8763CBa276a3738E6DE85b4b3bF5FDed6D6cA73, 0xe8537a3d056DA446677B9E9d6c5dB704EaAb4787, 0x73F93dcc49cB8A239e2032663e9475dd5ef29A08]
```

> [!TIP]  
> Find RPC URLs for Celo at
> [docs.celo.org/network/node/forno](https://docs.celo.org/network/node/forno)https://docs.celo.org/network/node/forno

### Write contract call

Source: [`cast send`](https://book.getfoundry.sh/reference/cast/cast-send)

For example:

1. writing `setExchangeRate` function `FeeCurrencyDirectory.sol`

    ```sh
    cast send \
    0x42Fe5a2A61ed9705eb2F08a04A58CEB606D22f6a \
    "setExchangeRate(address,uint256,uint256)" \
    0xe6774BE4E5f97dB10cAFB4c00C74cFbdCDc434D9 5 10 \
    --private-key $PRIVATE_KEY \
    --gas-limit 100000 \
    --rpc-url=http://127.0.0.1:8546

    blockHash               0x563f59930ea8dbbfda8d1076f5ed8ebee876ba45aeee9f4a7a103a68eee779ad
    blockNumber             267
    contractAddress
    cumulativeGasUsed       27284
    effectiveGasPrice       3000000008
    gasUsed                 27284
    logs                    []
    logsBloom               0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    root
    status                  0
    transactionHash         0xae17f552d46eb6a580ed41722ea7cac844c61717b00cb505ddf0d3dae68bcc40
    transactionIndex        0
    type                    2
    ```

    You can see the values have been written correctly:

    ```sh
    cast call \
    0x42Fe5a2A61ed9705eb2F08a04A58CEB606D22f6a \
    "getExchangeRate(address)(uint256, uint256)" \
    "0xe6774BE4E5f97dB10cAFB4c00C74cFbdCDc434D9" \
    --rpc-url=http://127.0.0.1:8546
    10
    5
    ```

### Make native transfer

Source: [`cast send`](https://book.getfoundry.sh/reference/cast/cast-send)

For example:

```sh
cast send \
0x2ee6F1cB802695F64D0A81284b36179f2886E7C2 \
--value 1ether \
--private-key $PRIVATE_KEY \
--rpc-url http://127.0.0.1:8545/
```

### Decode 

Source: [`cast abi-decode`](https://book.getfoundry.sh/reference/cast/cast-abi-decode)

Decode ABI-encoded input or output data.

Syntax:

```sh
$ cast abi-decode [options] sig calldata
```

The signature (*sig*) is a fragment in the form `<function name>(<types...>)(<types...>)`.

For example:

```sh
$ cast abi-decode \
"getAddressForStringOrDie(string calldata identifier)(address)" \
"0x00000000000000000000000042fe5a2a61ed9705eb2f08a04a58ceb606d22f6a"

0x42Fe5a2A61ed9705eb2F08a04A58CEB606D22f6a
```

Source: PR "[Add FeeCurrencyDirectory to Anvil migrations#10992](https://github.com/celo-org/celo-monorepo/pull/10992)"

```sh
$ cast abi-decode \
"getCurrencies()(address[] memory)" \
"0x00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000003000000000000000000000000e6774be4e5f97db10cafb4c00c74cfbdcdc434d9000000000000000000000000b7a33b4ad2b1f6b0a944232f5c71798d27ad92720000000000000000000000002a3733dbc31980f02b12135c809b5da33bf3a1e9"

[0xe6774BE4E5f97dB10cAFB4c00C74cFbdCDc434D9, 0xb7a33b4ad2B1f6b0a944232F5c71798d27Ad9272, 0x2A3733dBc31980f02b12135C809b5da33BF3a1e9]
```

Source: PR "[Add FeeCurrencyDirectory to Anvil migrations#10992](https://github.com/celo-org/celo-monorepo/pull/10992)"

### Get balance of an address

Source: [`cast balance`](https://book.getfoundry.sh/reference/cast/cast-balance)

`--ether` to display the balance in `ether` instead of `wei`.

For example:

```sh
$ cast balance \
0x2ee6F1cB802695F64D0A81284b36179f2886E7C2 \
--rpc-url http://127.0.0.1:8545/ \
--ether
10001.000000000000000000
```

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

## `anvil`

### Fork Alfajores testnet locally

Source: [`anvil`](https://book.getfoundry.sh/reference/anvil/)

Use Forno's [Alfajores RPC endpoint](https://docs.celo.org/network/node/forno) and the Celo
derivation path `m/44'/52752'/0'/0` to create a local fork of Celo's Alfajores testnet from block
`23821073` onwards.

```sh
$ anvil \
--fork-url https://alfajores-forno.celo-testnet.org  \
--chain-id 44787 \
--derivation-path "m/44'/52752'/0'/0" \
--fork-block-number 23821073



                             _   _
                            (_) | |
      __ _   _ __   __   __  _  | |
     / _` | | '_ \  \ \ / / | | | |
    | (_| | | | | |  \ V /  | | | |
     \__,_| |_| |_|   \_/   |_| |_|

    0.2.0 (1a2e2e0 2023-11-14T00:27:18.096233000Z)
    https://github.com/foundry-rs/foundry

Available Accounts
==================

(0) "0xf73d7f5A890a131f12E4fB03E50277c49748Cf5E" (10000.000000000000000000 ETH)
(1) "0x2ee6F1cB802695F64D0A81284b36179f2886E7C2" (10000.000000000000000000 ETH)
(2) "0xb5BFF06391c11F3a741b2537843bF2E9Cfa33f5e" (10000.000000000000000000 ETH)
(3) "0x4a6D53025bD680b8d5fF20304d778f35D07A1097" (10000.000000000000000000 ETH)
(4) "0x2944B530F868744695B4F1556d034305b1ACe965" (10000.000000000000000000 ETH)
(5) "0x5a5A7Fc1f97a1BAC1f5D076C1D05713f2047bAC6" (10000.000000000000000000 ETH)
(6) "0x5722238acD9616CB347667260CC50ab5C324313A" (10000.000000000000000000 ETH)
(7) "0x6f66a5A7266bf4369206B4cb429C766091cBb638" (10000.000000000000000000 ETH)
(8) "0xEfEDc43B6B85B5A39c9e169c35C3081ff1E93ABC" (10000.000000000000000000 ETH)
(9) "0x6A6b41338655124d003264Eaafc19F9A1F0c5d15" (10000.000000000000000000 ETH)

Private Keys
==================

(0) 0x3fd3e63b553fd867fe35edd22b1bafc8d558867fc06c076485a30ff1d496de19
(1) 0x8f8cc2047adcd5b893e500fea58ddc4a7b49988ff034dc28f36a42c816d27511
(2) 0xf725a5d3fed9302c297d9d2406d5b478dcc2df800c869566b7870bc482b0cf92
(3) 0xe97627e642b5f615372dcabc1ab44a87e5c90a1a83d0269796a4d71261514089
(4) 0xbbf7a9d64a98c1e0d3a2bc042ea3eb6f7bfa549891573919bf245d36edb17ea9
(5) 0xae81fa8e73f06d9a75c89a37a75e54b2b60dd458436c9ebcf7b3702e24a164cf
(6) 0x2464448c27833b308d525fc347465d5e9d1a2f5d20793120c165af42e41c3a54
(7) 0x3651517c0b89893a22d7ee74a15b71a37259b38d81aa562b1510f904a936f084
(8) 0xfc27445e842236a857262c319e27c60caf0ee94b93da351989ea2eb2b3383aca
(9) 0xe9a06f4db985213b6737ecd0155ca9b6373204d1999a69715c693ad893ff7f9b

Wallet
==================
Mnemonic:          test test test test test test test test test test test junk
Derivation path:   m/44'/52752'/0'/0/


Fork
==================
Endpoint:       https://alfajores-forno.celo-testnet.org
Block number:   23821073
Block hash:     0x260fd28a5163f9b8917ea9dd6cda0fa95a0b7ef343aa294884b8e5ea64d1d181
Chain ID:       44787

Base Fee
==================

5000000000

Gas Limit
==================

30000000

Genesis Timestamp
==================

1714498258

Listening on 127.0.0.1:8545
```

You can now can make transfers and fetch account balances on the local fork:

```sh
$ cast send \
0x2ee6F1cB802695F64D0A81284b36179f2886E7C2 \
--value 1ether \
--private-key 0x3fd3e63b553fd867fe35edd22b1bafc8d558867fc06c076485a30ff1d496de19 \
--rpc-url http://127.0.0.1:8545/

blockHash               0xfe1fc68c97cd2dde21552a3b6f6e978d9b478d050c4efb4c289473ec33728fe4
blockNumber             23820583
contractAddress
cumulativeGasUsed       21000
effectiveGasPrice       5379574965
gasUsed                 21000
logs                    []
logsBloom               0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root
status                  1
transactionHash         0x584f483f0e7756efcc01a3603a55fc97237a04f6e428105f6891470150aa8b9c
transactionIndex        0
type
```

```sh
$ cast balance \
0x2ee6F1cB802695F64D0A81284b36179f2886E7C2 \
--rpc-url http://127.0.0.1:8545/ \
--ether
10001.000000000000000000
```

## `chisel` (REPL)

Source: [`chisel`](https://book.getfoundry.sh/reference/chisel/)

Syntax:

```sh
No solidity versions installed! Installing solidity version 0.8.19...
Welcome to Chisel! Type `!help` to show available commands.
⚒️ Chisel help
=============
General
    !help | !h - Display all commands
    !quit | !q - Quit Chisel
    !exec <command> [args] | !e <command> [args] - Execute a shell command and print the output

Session
    !clear | !c - Clear current session source
    !source | !so - Display the source code of the current session
    !save [id] | !s [id] - Save the current session to cache
    !load <id> | !l <id> - Load a previous session ID from cache
    !list | !ls - List all cached sessions
    !clearcache | !cc - Clear the chisel cache of all stored sessions
    !export | !ex - Export the current session source to a script file
    !fetch <addr> <name> | !fe <addr> <name> - Fetch the interface of a verified contract on Etherscan
    !edit - Open the current session in an editor

Environment
    !fork <url> | !f <url> - Fork an RPC for the current session. Supply 0 arguments to return to a local network
    !traces | !t - Enable / disable traces for the current session
    !calldata [data] | !cd [data] - Set calldata (`msg.data`) for the current session (appended after function selector). Clears it if no argument provided.

Debug
    !memdump | !md - Dump the raw memory of the current state
    !stackdump | !sd - Dump the raw stack of the current state
    !rawstack <var> | !rs <var> - Display the raw value of a variable's stack allocation. For variables that are > 32 bytes in length, this will display their memory pointer.
```


For example:

To translate this precompile address:

```sh
➜ address(0xff - 7)
Type: address
└ Data: 0x00000000000000000000000000000000000000f8
```

Source: [`UsingPrecompiles.sol`](https://github.com/celo-org/celo-monorepo/blob/cd987f334c9422e5e4a817b68a853efa7200aa7f/packages/protocol/contracts/common/UsingPrecompiles.sol#L14-L15)

To run a REPL of a local anvil fork:

```sh
➜ !fork http://127.0.0.1:8546
Set fork URL to http://127.0.0.1:8546

➜ block.number
Type: uint
├ Hex: 0x0000000000000000000000000000000000000000000000000000000000000106
└ Decimal: 262
```