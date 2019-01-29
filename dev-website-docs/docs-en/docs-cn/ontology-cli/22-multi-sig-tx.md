
本体客户端 `Ontology-CLI` 提供了多重签名模块，用于对以十六进制字符串显示的序列化交易进行签名。

- `--wallet`：用于指定钱包路径。
- `--account`：用于指定签名账户（默认使用钱包默认账户）。
- `--pubkey`：用于指定多重签名的公钥列表，公钥之间用 `,` 分隔。
- `-m`：用于指定签名门限（默认为 `1`）。
- `--send`：用于指定将签名后的交易直接发送到网络中。
- `--prepare`：用于启用对签名后交易的预执行。
- `--rpcport`：用于指定 RPC 服务器的监听端口号（默认为 `20336`）。

> **注意**：在多重签名中，需要使用公钥列表中的不同的账户对同一笔交易进行签名，一个账户签名后，返回的带签名交易需要作为下一个账户签名的输入，直到满足签名门限 `m`。

```shell
$ ontology multisigtx --account AM6WtTARSpGsF8ugSnx9QZRu2wnP2Dk8Vo  --pubkey 1419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f36817042801013,02b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a,02929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac78157 -m 2 00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e736665721400000000000000000000
000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b650000
Password:
RawTx after multi signed:
00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e736665721400000000000000000000000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b65000141406b7af12334496c508bbb725726af7f50b17614b8ac6ec3180aeee741fc5a1d2524123ecf7b92e6c05a462fc38d2223476c82ef45483da6ad333ec0cdef48a27d6a522102929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac781572102b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a221419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f3681704280101353ae
```

```shell
$ ontology sendtx 00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e736665721400000000000000000000000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b65000141406b7af12334496c508bbb725726af7f50b17614b8ac6ec3180aeee741fc5a1d2524123ecf7b92e6c05a462fc38d2223476c82ef45483da6ad333ec0cdef48a27d6a522102929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac781572102b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a221419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f3681704280101353ae
{"desc":"","error":45021,"id":"cli","jsonrpc":"2.0","result":"transaction verify signature fail"}
```

```shell
$ontology multisigtx --account 2  --pubkey 1419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f36817042801013,02b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a,02929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac78157 -m 2
00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e736665721400000000000000000000000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b65000141406b7af12334496c508bbb725726af7f50b17614b8ac6ec3180aeee741fc5a1d2524123ecf7b92e6c05a462fc38d2223476c82ef45483da6ad333ec0cdef48a27d6a522102929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac781572102b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a221419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f3681704280101353ae
Password:
RawTx after multi signed:
00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e736665721400000000000000000000000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b65000182406b7af12334496c508bbb725726af7f50b17614b8ac6ec3180aeee741fc5a1d2524123ecf7b92e6c05a462fc38d2223476c82ef45483da6ad333ec0cdef48a27d408488b2fd12407671a59430f23c8af6875c7f7959dd050c42f34320dbc8d1a2fd9ebedf44ff8763054c447a142f0058e43aea07d9e6f2d96689342b31d26362c76a522102929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac781572102b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a221419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f3681704280101353ae
```

```shell
$ ontology sendtx 00d17d3efaa1f401000000000000204e000000000000675e90df552510f7485cbb947472bbfb1ccef3d66e00c66b6a14675e90df552510f7485cbb947472bbfb1ccef3d6c86a143a5f33fe68147164f2fbc523d18c5a065956fed0c86a55c86c51c1087472616e7366657214000000000000000000
00000000000000000000010068164f6e746f6c6f67792e4e61746976652e496e766f6b65000182406b7af12334496c508bbb725726af7f50b17614b8ac6ec3180aeee741fc5a1d2524123ecf7b92e6c05a462fc38d2223476c82ef45483da6ad333ec0cdef48a27d408488b2fd12407671a59430f23c8af6875c7f7959dd050c42f34320dbc8d
1a2fd9ebedf44ff8763054c447a142f0058e43aea07d9e6f2d96689342b31d26362c76a522102929e9d107f318d2cf8d99e4de47731e2d6a8eb8da5164e8d012373f06ac781572102b76e86481df6e9814b203a41a0b040c87807d1bcfd18f8ea69c7d73ebf2b944a221419e9b0d726b0712fb92015b8e41ee5e5bb3d0321485b13322c6f3681
704280101353ae
Send transaction success.
  TxHash:8a153e69a39ce5f2bab1092bf15ae428388af1b57ecb29d75b45cce32fe7addb

Tip:
  Using './ontology info status 8a153e69a39ce5f2bab1092bf15ae428388af1b57ecb29d75b45cce32fe7addb' to query transaction status.
```

```shell
$ ontology info status 8a153e69a39ce5f2bab1092bf15ae428388af1b57ecb29d75b45cce32fe7addb
Transaction states:
{
   "TxHash": "8a153e69a39ce5f2bab1092bf15ae428388af1b57ecb29d75b45cce32fe7addb",
   "State": 1,
   "GasConsumed": 10000000,
   "Notify": [
      {
         "ContractAddress": "0100000000000000000000000000000000000000",
         "States": [
            "transfer",
            "ARCSXp1YYX5KUTogyTVczoypaAKc1h6o3h",
            "AM6WtTARSpGsF8ugSnx9QZRu2wnP2Dk8Vo",
            5
         ]
      },
      {
         "ContractAddress": "0200000000000000000000000000000000000000",
         "States": [
            "transfer",
            "ARCSXp1YYX5KUTogyTVczoypaAKc1h6o3h",
            "AFmseVrdL9f9oyCzZefL9tG6UbviEH9ugK",
            10000000
         ]
      }
   ]
}
```