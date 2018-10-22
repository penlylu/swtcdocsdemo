**使用手册 For Jingtum Blockchain Service Node**

- Jingtum Blockchain三种节点类型
  	* validation node, 共识节点，参与共识，不对外提供信息
  	* public node, 公开节点，对外提供服务， 与validation node互联
 	* service node, 服务节点，服务于区块链应用，对外提供服务， 与public node 相连
  
- service node当前主要功能
	* 与 public node 相连，提供所有的帐本的实时同步功能
	* 对外提供区块链相关的api与rpc调用, 具体见[JSON RPC API](#json-rpc-api)
- service node 命令行参数
	- 参数配置类
		* **-config**, 指定当前配置文件包括生成的帐户文件所在的目录，默认为当前目录
		* **-data_dir**， 指定帐本存放目录，默认为当前目录
		* **-database_name**， 指定帐本数据库目录名，默认为"jingtum.db"
		* **-endpoint**, 指定连接的public node的地址，默认为测试环境的一个public node的地址，即"ws://ts.jingtum.com:5020"
		* **-thread_count**, 指定同步帐本的线程数量，默认为8
	- 帐户管理类
		* **account list**， 列出本地帐户相关情况
		* **account generate [-nickname]**， 生成新的本地帐户，可指定新帐户的nickname
	- 启动服务类
		* **-sync**, 启动帐本实时同步功能，默认为true
		* **-api**, 启动JSON REST API服务，默认为false
		* **-apiaddr**, 设定启动的JSON REST API服务的地址，默认为"localhost"
		* **-apiport**, 设定启动的JSON REST API服务的端口，默认为7544
		* **-rpc**, 启动JSON RPC API服务，默认为false
		* **-rpcaddr**, 设定启动的JSON RPC API服务的地址，默认为"localhost"
		* **-rpcport**, 设定启动的JSON RPC API服务的端口，默认为7545
- [JSON RPC API](#json-rpc-api)
	- [JSON-RPC methods](#json-rpc-methods)
		* [jt_syncing](#jt_syncing-%E5%90%8C%E6%AD%A5)
		* [jt_blockNumber](##jt_blocknumber-%E5%8C%BA%E5%9D%97%E7%BC%96%E5%8F%B7)
		* [jt_getBalance](#jt_getbalance-%E8%8E%B7%E5%8F%96%E4%BD%99%E9%A2%9D)
		* [jt_getTransactionCount](#jt_gettransactioncount-%E8%8E%B7%E5%8F%96%E4%BA%A4%E6%98%93%E6%95%B0)
		* [jt_getBlockTransactionCountByHash](#jt_getblocktransactioncountbyhash-%E6%A0%B9%E6%8D%AE%E5%8C%BA%E5%9D%97%E5%93%88%E5%B8%8C%E8%8E%B7%E5%8F%96%E4%BA%A4%E6%98%93%E6%95%B0)
		* [jt_getBlockTransactionCountByNumber](#jt_getblocktransactioncountbynumber-%E6%A0%B9%E6%8D%AE%E5%8C%BA%E5%9D%97%E7%BC%96%E5%8F%B7%E8%8E%B7%E5%8F%96%E4%BA%A4%E6%98%93%E6%95%B0)
		* [jt_getBlockByHash](#jt_getblockbyhash-%E6%A0%B9%E6%8D%AE%E5%8C%BA%E5%9D%97%E5%93%88%E5%B8%8C%E8%8E%B7%E5%8F%96%E5%8C%BA%E5%9D%97%E4%BF%A1%E6%81%AF)
		* [jt_getBlockByNumber](#jt_getblockbynumber)
		* [jt_getTransactionByHash](#jt_gettransactionbyhash)
		* [jt_getTransactionByBlockHashAndIndex](#jt_gettransactionbyblockhashandindex)
		* [jt_getTransactionByBlockNumberAndIndex](#jt_gettransactionbyblocknumberandindex)
		* [jt_getTransactionReceipt](#jt_gettransactionreceipt)
		* [jt_accounts](#jt_accounts)
		* [jt_sign](#jt_sign)
		* [jt_sendTransaction](#jt_sendtransaction)
		* [jt_signTransaction](#jt_signtransaction)
		* [jt_sendRawTransaction](#jt_sendrawtransaction)
		* [jt_account](#jt_account)
		* [jt_issueToken](#jt_issueToken)
		* [jt_createToken](#jt_createToken)
		* [jt_transferToken](#jt_transferToken)
		* [jt_ownerOf](#jt_ownerOf)
		* [jt_tokensOf](#jt_tokensOf)
		* [jt_getToken](#jt_getToken)
		* [jt_getTokenInfo](#jt_getTokenInfo)
  	- [JSON RPC API Reference](#json-rpc-api-reference) 

## JSON RPC API Reference
***

#### jt_syncing 同步

返回同步状态数据对象或`false`


##### Parameters 参数
无

##### Returns 返回值

`Object|Boolean`, 一个带有同步状态数据的对象。当未同步时，返回结果为`FALSE`:  
  - `startingBlock`: `QUANTITY` - import指令开始的区块（仅在同步信号到达其顶部时会被重置）  
  - `currentBlock`: `QUANTITY` - 当前区块, 同 jt_blockNumber  
  - `highestBlock`: `QUANTITY` - 预估最高区块

##### Example 举例
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_syncing","params":[],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id":1,
  "jsonrpc":"2.0",
  "result":
    {
      "currentBlock":4599,
      "highestBlock":850088,
      "startingBlock":20
    },
  "status":"success"
}

// Or when not syncing 未同步时的结果
{
  "id":1,
  "jsonrpc": "2.0",
  "result": false,
  "status": "success"
}
```

***

#### jt_blockNumber 区块编号

返回最新区块的编号

##### Parameters 参数
无

##### Returns 返回值

`QUANTITY` - 客户端运行的当前区块编号.

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_blockNumber","params":[],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id":1,
  "jsonrpc": "2.0",
  "result": 112948,
  "status": "success"
}
```

***

#### jt_getBalance 获取余额

返回给定地址的帐户余额

##### Parameters 参数

1. `DATA` - 所要查询余额的地址.
2. `QUANTITY|TAG` - 区块编号, 区块哈希, 或者是字符串 `"validated"`, `"current"`, `"closed"`中的一个, 详见 [default block parameter](#the-default-block-parameter)

```js
params: [
   "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
   "validated"
]
```

##### Returns 返回值

`OBJECT` - 当前余额和货币列表


##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getBalance","params":["j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E", "validated"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id":1,
  "jsonrpc":"2.0",
  "result":
{
	"account":"j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
	"balance":"82002267",
	"coins":[
		{
			"account":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or",
			"balance":"0.02",
			"currency":"CNY",
			"limit":"10000000000",
			"limit_peer":"0",
			"no_skywell":true,
			"quality_in":0,
			"quality_out":0
		}
	],
  "ledger_hash":"6ACD74B8C7E0E9E9DE001BD98DCCA45F26CC47BE455C681CB32440BD1BF1991D",
  "ledger_index":10449036,
  "validated":true
  },
  "status":"success"
}
```

***

#### jt_getTransactionCount 获取交易数

返回某个地址*sent*（发起）的交易数


##### Parameters 参数

1. `DATA` - 地址.
2. `QUANTITY|TAG` - 区块编号, 区块哈希, 或者是字符串 `"validated"`, `"current"`, `"closed"`中的一个, 详见 [default block parameter](#the-default-block-parameter)

```js
params: [
   'j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E',
   '113199' // the block number 区块编号
]
```

##### Returns 返回值

`QUANTITY` - 该地址发起的交易数.


##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getTransactionCount","params":["j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E", "113199"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
	"id":1,
	"jsonrpc":"2.0","
	result":1,
	"status":"success"
}
```

***

#### jt_getBlockTransactionCountByHash 根据区块哈希获取交易数

返回给定哈希值所对应的区块的交易数


##### Parameters 参数

1. `DATA` - 区块哈希

```js
params: [
   '4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E'
]
```

##### Returns 返回值

`QUANTITY` - 这个区块的交易数.


##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getBlockTransactionCountByHash","params":["4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
	"id":1,
	"jsonrpc":"2.0",
	"result":2,
	"status":"success"
}
```

***

#### jt_getBlockTransactionCountByNumber 根据区块编号获取交易数
> > 
返回给定编号所对应的区块的交易数


##### Parameters 参数

1. `QUANTITY|TAG` - 区块编号

```js
params: [
   '0xe8', // 232
]
```

##### Returns 返回值

`QUANTITY` - 这个区块的交易数.

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getBlockTransactionCountByNumber","params":["113199"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id":1,
  "jsonrpc": "2.0",
  "result":0,
  "status":"success"
}
```

***

#### jt_getBlockByHash 根据区块哈希获取区块信息
返回哈希值所对应的区块信息


##### Parameters 参数

1. `DATA`, 32 Bytes - 区块哈希
2. `Boolean` - 如果参数为true`，则返回整个交易对象，如果参数为`false`则只返回交易哈希。

```js
params: [
   "4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E",
   true
]
```

##### Returns 返回值

`Object` - 一个区块对象，当区块不存在时则返回`null`:


##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getBlockByHash","params":["4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E", false],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "ledger": {
      "accepted": true,
      "account_hash": "63A773D95DA86938313CEB400ADC90A6AED4C087985755E1F3596041A5226258",
      "close_time": 587650500,
      "close_time_human": "2018-Aug-15 12:15:00",
      "close_time_resolution": 10,
      "closed": true,
      "hash": "4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E",
      "ledger_hash": "4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E",
      "ledger_index": "113199",
      "parent_hash": "144C61BA96372BA427DE2C1244BD8D8F6E18810E403999599F0AC9E111E8D220",
      "seqNum": "113199",
      "totalCoins": "600000000000000000",
      "total_coins": "600000000000000000",
      "transaction_hash": "0000000000000000000000000000000000000000000000000000000000000000",
      "transactions": [
		  {
		    "Account": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
		    "Amount": "1",
		    "Destination": "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
		    "Fee": "12",
		    "Flags": 2147483648,
		    "Sequence": 362,
		    "SigningPubKey": "0330E7FC9D56BB25D6893BA3F317AE5BCF33B3291BD63DB32654A313222F7FD020",
		    "TransactionType": "Payment",
		    "TxnSignature": "3045022100EAAE8B2E451D784CEE5406BBFE4FDA286152AC3395F672AF09689D2F85C4510D02202EF038B5DF3088A2AC097EF1617103BDDF2513FFA11D8E5E868C6465BDDADBCC",
		    "date": 587199710,
		    "hash": "0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55",
		    "inLedger": 68120,
		    "ledger_hash": "3B98F9DB9BA4A91A140C5ACC8E6FAA01746408938E89F7D1963AEB257228DBBA",
		    "ledger_index": 68120,
		    "validated": true
		  }
      ]
    },
    "ledger_hash": "4F8040D0EF9A2360DFE5E715D3991B75340DA937F5ABA00C7686B76B1F84E26E",
    "ledger_index": 113199,
    "validated": true
  },
  "status": "success"
}
```

***

#### jt_getBlockByNumber 根据区块编号获取区块信息

返回区块编号所对应的区块信息

##### Parameters 参数

1. `QUANTITY|TAG` - 区块编号，或者是字符串`"earliest"`, `"latest"`, `"pending"`中的一个, 正如在 [default block parameter](#the-default-block-parameter)里的一样.
2. `Boolean` - 如果参数为true`，则返回整个交易对象，如果参数为`false`则只返回交易哈希。

```js
params: [
	"113199",
   true
]
```

##### Returns 返回值

详见 [jt_getBlockByHash](#jt_getblockbyhash)

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getBlockByNumber","params":["113199", false],"id":1}' http://localhost:7545/v1/jsonrpc
```

结果详见 [jt_getBlockByHash](#jt_getblockbyhash)

***

#### jt_getTransactionByHash 根据区块哈希获取交易信息

返回交易哈希请求的交易信息


##### Parameters 参数

1. `DATA`, 32 Bytes - 交易哈希

```js
params: [
   "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"
]
```

##### Returns 返回值

`Object` - 返回一个交易对象，当交易不存在时返回`null`

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getTransactionByHash","params":["0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "Account": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
    "Amount": "1",
    "Destination": "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
    "Fee": "12",
    "Flags": 2147483648,
    "Sequence": 362,
    "SigningPubKey": "0330E7FC9D56BB25D6893BA3F317AE5BCF33B3291BD63DB32654A313222F7FD020",
    "TransactionType": "Payment",
    "TxnSignature": "3045022100EAAE8B2E451D784CEE5406BBFE4FDA286152AC3395F672AF09689D2F85C4510D02202EF038B5DF3088A2AC097EF1617103BDDF2513FFA11D8E5E868C6465BDDADBCC",
    "date": 587199710,
    "hash": "0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55",
    "inLedger": 68120,
    "ledger_hash": "3B98F9DB9BA4A91A140C5ACC8E6FAA01746408938E89F7D1963AEB257228DBBA",
    "ledger_index": 68120,
    "validated": true
  },
  "status": "success"
}
```

***

#### jt_getTransactionByBlockHashAndIndex 根据区块哈希和索引获取交易信息

返回区块哈希和交易索引所对应的交易信息


##### Parameters 参数

1. `DATA`, 32 Bytes - 区块哈希.
2. `QUANTITY` - 交易索引.

```js
params: [
   "3B98F9DB9BA4A91A140C5ACC8E6FAA01746408938E89F7D1963AEB257228DBBA",
   "0"
]
```

##### Returns 返回值

详见 [jt_getTransactionByHash](#jt_gettransactionbyhash)

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getTransactionByBlockHashAndIndex","params":["3B98F9DB9BA4A91A140C5ACC8E6FAA01746408938E89F7D1963AEB257228DBBA", "0"],"id":1}' http://localhost:7545/v1/jsonrpc
```

结果详见 [jt_getTransactionByHash](#jt_gettransactionbyhash)

***

#### jt_getTransactionByBlockNumberAndIndex 根据区块编号和索引获取交易信息

返回区块编号和交易索引所对应的交易信息.


##### Parameters 参数

1. `QUANTITY|TAG` - 区块编号，或者是字符串`"earliest"`, `"latest"`, `"pending"`中的一个, 正如在 [default block parameter](#the-default-block-parameter)里的一样.
2. `QUANTITY` - 交易索引.

```js
params: [
   "68120",
   "0"
]
```

##### Returns 返回值

详见 [jt_getTransactionByHash](#jt_gettransactionbyhash)

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getTransactionByBlockNumberAndIndex","params":["68120", "0"],"id":1}' http://localhost:7545/v1/jsonrpc
```

结果详见 [jt_getTransactionByHash](#jt_gettransactionbyhash)

***

#### jt_getTransactionReceipt 获取交易收据

返回交易哈希所对应的交易收据.

**注意** 无法获得正在进行中的交易的收据.


##### Parameters 参数

1. `DATA`, 32 Bytes - 交易哈希

```js
params: [
   "0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55"
]
```

##### Returns 返回值

`Object` - 返回一个交易收据对象，当收据不存在时返回`null`


##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_getTransactionReceipt","params":["0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "AffectedNodes": [
      {
        "ModifiedNode": {
          "FinalFields": {
            "Account": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
            "Balance": "244499999099995309",
            "Flags": 0,
            "OwnerCount": 0,
            "Sequence": 363
          },
          "LedgerEntryType": "AccountRoot",
          "LedgerIndex": "2B6AC232AA4C4BE41BF49D2459FA4A0347E1B543A4C92FCEE0821C0201E2E9A8",
          "PreviousFields": {
            "Balance": "244499999099995322",
            "Sequence": 362
          },
          "PreviousTxnID": "79EA2CCE7B64AADCC02CB252AE3E2E375BBEEB11A61A16B2634C4CDE54EA74CE",
          "PreviousTxnLgrSeq": 68120
        }
      },
      {
        "ModifiedNode": {
          "FinalFields": {
            "Account": "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
            "Balance": "200000346",
            "Flags": 0,
            "OwnerCount": 0,
            "Sequence": 1
          },
          "LedgerEntryType": "AccountRoot",
          "LedgerIndex": "BA84D071F8AB10AA7FBA28E04EF1E75A2D36362B85BFC494FCF257C5960C8A09",
          "PreviousFields": {
            "Balance": "200000345"
          },
          "PreviousTxnID": "79EA2CCE7B64AADCC02CB252AE3E2E375BBEEB11A61A16B2634C4CDE54EA74CE",
          "PreviousTxnLgrSeq": 68120
        }
      },
      {
        "ModifiedNode": {
          "FinalFields": {
            "Account": "jBbRz4Erf7j4oBboXcChKWVgX5HhymygLT",
            "Balance": "4332",
            "Flags": 0,
            "OwnerCount": 0,
            "Sequence": 362
          },
          "LedgerEntryType": "AccountRoot",
          "LedgerIndex": "F88E3CB9279ED427BB4CF37D13840A0728489CC273AE92A5C05D5390B1FF5F2A",
          "PreviousFields": {
            "Balance": "4320",
            "Sequence": 361
          },
          "PreviousTxnID": "79EA2CCE7B64AADCC02CB252AE3E2E375BBEEB11A61A16B2634C4CDE54EA74CE",
          "PreviousTxnLgrSeq": 68120
        }
      }
    ],
    "TransactionIndex": 249,
    "TransactionResult": "tesSUCCESS",
    "hash": "0019A03D4D5CAC27A2B25F3E2BEBBE2B0FE68C758128481E7D50FFD2D12C6F55"
  },
  "status": "success"
}
```

***

#### jt_accounts 账户列表

返回客户端所拥有的地址列表.


##### Parameters 参数
无

##### Returns 返回值

`Array of DATA`, 20 Bytes - 客户端所拥有的所有地址

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_accounts","params":[],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "jPtkTgG9o4Jfq6w5G6FaNGgEnVRsSNoUuB",
    "jfRiTK5t9uBcmfYp5B95q5k3oggdwGLcSv",
    "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E"
  ],
  "status": "success"
}
```

***

#### jt_sign 签名

通过给消息添加前缀，使得计算出的签名可以被识别成Skywell特定签名.

##### Parameters 参数
账户，信息

1. `DATA` - 地址
2. `DATA`, N Bytes - 要签名的消息

##### Returns 返回值

`DATA`: 签名

##### Example 例子

```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_sign","params":["j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E","0xdeadbeaf"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x30450221008D119BA6ABB97D5A541D0BD5649AA0DC35E00C19E5C381A15611CD38E933B18902201E76D713F7966A1F1BAC6EFB87D0953F8697EB6DFE485F434F34D959ACD74FED",
  "status": "success"
}
```

***

#### jt_sendTransaction 发起交易

创建一个新交易，签名，并提交交易.

##### Parameters 参数

1. `Object` - 交易对象
  - `from`: `DATA` - 交易发起一方的地址
  - `to`: `DATA` - (当创建新交易时可选) 交易所指向的地址。
  - `fee`: `QUANTITY`  - (可选，默认12) 执行交易所需提供的交易费用. 
  - `value`: `QUANTITY`  - (可选) 整数交易金额.

```js
params: [
	{
		"from":"jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
		"to":"j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
		"value":"1"
	}
]
```

##### Returns 返回值

`DATA`, 32 Bytes - 返回交易哈希，当交易不存在时返回0哈希.

使用 [jt_getTransactionReceipt](#jt_gettransactionreceipt) 来获取交易收据

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_sendTransaction","params":[{"from":"jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh","to":"j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E","value":"1"}],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "637FE35E7ECEDA862A1C51596945F69444C169796197CC174F449C176116395D"
  ],
  "status": "success"
}
```

***

#### jt_signTransaction 签名交易

创建并签署一个交易

##### Parameters 参数

1. `Object` - 交易对象
  - `from`: `DATA` - 交易发起一方的地址
  - `to`: `DATA` - (当创建新交易时可选) 交易所指向的地址。
  - `fee`: `QUANTITY`  - (可选，默认12) 执行交易所需提供的交易费用. 
  - `value`: `QUANTITY`  - (可选) 整数交易金额.

```js
params: [
	{
		"from":"jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
		"to":"j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
		"value":"1"
	}
]
```

##### Returns 返回值

`DATA` - 十六进制的签名交易.

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_signTransaction","params":[{"from":"jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh","to":"j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E","value":"1"}],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "120000228000000024000003FB61400000000000000168400000000000000C73210330E7FC9D56BB25D6893BA3F317AE5BCF33B3291BD63DB32654A313222F7FD0207446304402207268AD2FC68CD6AB8157F5BD0B0884F37DFF18D01E5414D6A277E33D4328D2BD02205502FDFFF00B7F1F9D7EB75329F65B50E619B6E1F9D24BE0D65F862419398A178114B5F762798A53D543A014CAF8B297CFF8F2F937E883145D2221763934C9380F43168B760199AC8C4DC074"
  ],
  "status": "success"
}
```

***

#### jt_sendRawTransaction 发起原始交易

提交十六进制格式的原始交易

##### Parameters 参数

1. `DATA`, 签署后的交易数据.

```js
params: ["120000228000000024000003FB61400000000000000168400000000000000C73210330E7FC9D56BB25D6893BA3F317AE5BCF33B3291BD63DB32654A313222F7FD0207446304402207268AD2FC68CD6AB8157F5BD0B0884F37DFF18D01E5414D6A277E33D4328D2BD02205502FDFFF00B7F1F9D7EB75329F65B50E619B6E1F9D24BE0D65F862419398A178114B5F762798A53D543A014CAF8B297CFF8F2F937E883145D2221763934C9380F43168B760199AC8C4DC074"]
```

##### Returns 返回值

`DATA` - 返回交易哈希，当交易不存在时返回0哈希.

在交易生效后，使用 [jt_getTransactionReceipt](#jt_gettransactionreceipt) 来获取交易收据

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_sendRawTransaction","params":["120000228000000024000003FB61400000000000000168400000000000000C73210330E7FC9D56BB25D6893BA3F317AE5BCF33B3291BD63DB32654A313222F7FD0207446304402207268AD2FC68CD6AB8157F5BD0B0884F37DFF18D01E5414D6A277E33D4328D2BD02205502FDFFF00B7F1F9D7EB75329F65B50E619B6E1F9D24BE0D65F862419398A178114B5F762798A53D543A014CAF8B297CFF8F2F937E883145D2221763934C9380F43168B760199AC8C4DC074"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "15392C8703C7141F3604C6DF251E5FB361FA3DF7B835843EF007EA51508FF77D"
  ],
  "status": "success"
}
```

***

#### jt_account 查询帐户历史交易

查询帐户历史交易

##### Parameters 参数

1. `STRING` 	- 所要查询余额的地址.
2. `INT` 		- 起始页号，可省略，默认为0
3. `INT` 		- 每页交易数, 可省略，默认为所有
4. `BOOL` 	- 是否最新的交易在前边，可省略，默认为true
5. `STRING`  - 交易方向，可省略，默认为'both',有'in', 'out'可选


```js
params: ["j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E", 0, 2, true, "in"]
```

##### Returns 返回值

`LIST` - 交易列表

##### Example 例子
```js
// Request 请求
curl -X POST --data '{"jsonrpc":"2.0","method":"jt_account","params":["j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E", 0, 2, true, "in"],"id":1}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": [
        {
            "amount": "1",
            "date": "2018-09-27T12:49:00+08:00",
            "from_address": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
            "hash": "2EBDD99F0030C42127A7EB151F4852CC45F0CBADE7AB3A9B0321845C7EDCA826",
            "to_address": "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
            "type": "Payment"
        },
        {
            "amount": "1000000000000",
            "date": "2018-09-27T12:30:40+08:00",
            "from_address": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
            "hash": "6E7751B22E630888C89AFF087D8A0197BA84704F19E7865611A2124C1CE8DE01",
            "to_address": "j9VSrHSiZPiJBPUS6iwYiT8yfy8iFbeR4E",
            "type": "Payment"
        }
    ],
    "status": "success"
}
```

***

#### jt_issueToken 发行类erc721的Token

发行类似erc721的token

##### Parameters 参数

- `from`:`ADDRESS`: 发行token的帐户地址
- `to`:`ADDRESS`:创建token的帐户地址
- `token_info`:`OBJECT`: token的定义信息
	- `name`:`STRING`: 类erc721的token的名称   
	- `symbol`:`STRING`: 类erc721的token的简称
	- `total_supply`:`INT`: 该token的总供应量  
	- `items`:`LIST`：定义该token的属性
  		- `name`:`STRING`: 该属性的名称  
	  	- `type`:`STRING`: 该属性的类型，现支持三种类型，string, number, binary  
  		- `desc`:`STRING`: 该属性描述, 可以为空

1. `DATA`, 所发行Token的哈希字符串

```js
"params":[
	{
		"from":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
		"to":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR", 
		"token_info":{
			"name":"TEST", 
			"symbol":"test", 
			"total_supply":100,
			"items":[
				{
					"name":"code",
					"type":"string",
					"desc":"the code of the token"
				},
				{
					"name":"quality",
					"type":"string",
					"desc":"the quality of the token"
				},
				{
					"name":"weight",
					"type":"number",
					"desc":"the weight of the token"
				},
				{
				   "name":"image",
				   "type":"binary",
				   "desc":"the image of the token"
				}
			]
		}
	}
]
```

##### Returns 返回值

- `hash`:`HASH` - 返回区块链上的交易哈希，当交易不存在时返回0哈希
- `info`:`HASH` - 返回该token定义的hash, 后续jt_createToken的时候要用到

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_issueToken",
	"params":[
		{
			"from":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
			"to":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR", 
			"token_info":{
				"name":"TEST", 
				"symbol":"test", 
				"total_supply":100,
				"items":[
					{
						"name":"code",
						"type":"string",
						"desc":"the code of the token"
					},
					{
						"name":"quality",
						"type":"string",
						"desc":"the quality of the token"
					},
					{
						"name":"weight",
						"type":"string",
						"desc":"the weight of the token"
					}
				]
			}
		}
	],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": [
        {
            "hash": "1684C9321EA993D0C6AA558C5E0DD855F66A67C944AD1C4A7BD2CB3A59B24C69",
            "info": "1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A"
        }
    ],
    "status": "success"
}
```

***

#### jt_createToken 根据发行的Token种类，创建具体每一个唯一的Token

创建类似erc721的token

##### Parameters 参数

- `from`:`ADDRESS`: 创建token的帐户地址[建议为jt_issueToken的to]
- `to`:`ADDRESS`:接收token的帐户地址
- `token`:`OBJECT`: token的定义信息
	- `info`:`HASH`: 类erc721的定义token的hash, 见jt_issueToken返回值
	- `uri`:`STRING`: 类erc721的token的uri, erc721标准属性
	- `items`:`LIST`：该token的属性
  		- `name`:`STRING`: 该属性的名称  
	  	- `value`:`STRING`: 该属性的值，要符合jt_issueToken中的定义

```js
params: [
  {
		"from":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
		"to":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR",
		"token":{
			"info":"1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A", 
			"uri":"http://www.jingtum.com", 
			"items":[
				{
					"name":"code",
					"value":"002"
				},
				{
					"name":"quality",
					"value":"99.99999%"
				},
				{
					"name":"weight",
					"value":"10"
				},
				{
					"name":"image",
					"value":"ffd8ffe000104a46494600010100004800480000ffe100584578696600004d4d002a000000080002011200030000000100010000876900040000000100000026000000000003a00100030000000100010000a00200040000000100000078a0030004000000010000007800000000ffed003850686f746f73686f7020332e30003842494d04040000000000003842494d0425000000000010d41d8cd98f00b204e9800998ecf8427effc00011080078007803011100021101031101ffc4001f0000010501010101010100000000000000000102030405060708090a0bffc400b5100002010303020403050504040000017d01020300041105122131410613516107227114328191a1082342b1c11552d1f02433627282090a161718191a25262728292a3435363738393a434445464748494a535455565758595a636465666768696a737475767778797a838485868788898a92939495969798999aa2a3a4a5a6a7a8a9aab2b3b4b5b6b7b8b9bac2c3c4c5c6c7c8c9cad2d3d4d5d6d7d8d9dae1e2e3e4e5e6e7e8e9eaf1f2f3f4f5f6f7f8f9faffc4001f0100030101010101010101010000000000000102030405060708090a0bffc400b51100020102040403040705040400010277000102031104052131061241510761711322328108144291a1b1c109233352f0156272d10a162434e125f11718191a262728292a35363738393a434445464748494a535455565758595a636465666768696a737475767778797a82838485868788898a92939495969798999aa2a3a4a5a6a7a8a9aab2b3b4b5b6b7b8b9bac2c3c4c5c6c7c8c9cad2d3d4d5d6d7d8d9dae2e3e4e5e6e7e8e9eaf2f3f4f5f6f7f8f9faffdb00430001010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101ffdb00430101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101ffdd0004000fffda000c03010002110311003f00f05f4e0fb7b7d47a7a0fc0f7aff623fafebeeeff0079fe435afd7aeafbfe1f2bae5d6fa696899ebc1ffbe49fa67d7bff0023d051fd7f5b7f4b4beac35d35febcb6f4d7a2eb76c5ebff00ea23afbfd3f519ed402fbffaff0030e3f33e8debe9dba75f5e7bd030e39ebc7d79eff8fd074e9de8013d3dbfce719e3d79e7b633c51fd7f5dfe41fd7f5f30ec7dfebe9dbbb7e58c74ce0d1fd7f5dbe602f7cfb7e5df9e703f0dd9ebd06680febfaee27f439faf3e99e7ea76f3cf7cd01fd7f5d85f5f7f7e9ce3afafb01c1eb8c8a004c74f63e9c7e5d8e3d475e99c9a3fafebbfc8031d7dff3e9df9e476e377a7bd1fd7f5dbe6018e9c741fd3b7381cf1cedfc866800c7047bf1c1fd7b9fae31dce38a005c73f863dff039e98e78e7be3271401fffd0f05fa371819fd79f4e7bfebc62bfd883fc865fafe4f4feb4b7e325e7d467e9dbff00adfd680fbdf97ebbafc5adbae9cc7e381eb8e9fd3d473fce805f37e7dff1fc95bcfa073ebf518ebee3bfb71fce81fc9fe1a7fe4df9397a09e9f37e381cfb7a1e7f1f4ef407cbf2d7f1fcf97f51c88cee1132f239544551b9999b8555519259891800649c019e286d24db764b56dec9777b6debf702bb764aedb564badde896f77e49796ba1ef9e08fd94ff00697f89114373e06f809f173c49612e3cbd574ff007894e8a49f947fc4ee6d361d254e413f3de0e8cc4615997e5333e3be0bc9a4e19a715f0fe0aac77a15b35c17d657fdcb46acabbf943e6af63eab2de06e32ce2319e59c2d9fe3294adcb5e96558dfabbff00b989528d05f3a9f75bdef61ff876d7edcbe41b8ff866ff001e6c2a5f6e741f3f6e01c7d9bfb60dc86038d9e4b3e4f427017e77fe232f861cfc9feb96557bdaf6c5f276fe27d5bd9dbcf9bcf9ba9f41ff00107bc4ce5e6ff53f34b6f6be179b6bfc1f58536ffab2b729e3fe37fd94ff00697f86f1c975e39f809f177c356106449aaea1e00f128d15486c1035a874d9b496c703e4bd6c0607a3035f459671df05e73250caf8af87f1b565b50a39ae0beb2ffee5a55635d7ce1f376b1f3f99703719e4f194f33e16cff074a3bd7ab956356197fdccc694a86fda7e7adfddf016464668e4051d5caba38dac18310caca70430230548c82b8e306bead34d269dd3d535b35dd6fbfafde7cabba7669a69b4d3ba69ded6b68f7df4d3cae181fd474f9bbfafaf3fc3d3d39a03fab69f7efd7f4dafa06073fcf8e31cff005cf4fae38a05db5f9e9aff005bede97d431d3dbb71f4cf5e700febd33c501fd74f774f5ffe4beed4303a76f5e3e98f6e38e9f99a07fd5b4d7f1f97d9f3e8c31ffd71c719f5e79ce31fc5d38ee6817cfe7a77dbf4dbee67ffd1f05ebfc3f4efce7a7f507fc2bfd883fc865d7aeaff00e1ba7e2fe6adeeaf18fbb81e9c75fe5cf4ff00f58a035ef6fbbeefd76975db4e543c76193fe47a9ed9fc31c120d00bfa5a69f9b7f7ad1fc3d4500b10aabb89215428dcc58e30028fbc4e72029c9e830686edabd12d5b7d0767a2bb6efe577e5f0fe4a2fcf73f60bf637ff8242fc5df8ff69a5f8f7e2eddde7c19f8617ab15dd8c175a6adc78f7c5362ca8f1c9a668b772c09a069f751b8f2356d711a674fdf5ae8f756ee93bff3cf88bf482e1fe13a95f2ae1fa74f88b3ba4e54eace9d6e5cab0355369c6be269c64f17569b4f9a8619a8a7eecebd39a940fe80f0f7c02cff008aa14734cfea54e1ec92af2d4a50a9454b34c7527ade861a7282c2d2a89fbb88c52727f153c2d483523fa2ff00811fb097ecb7fb3b5ada7fc2bef851e1f9b5eb745dfe33f15dbc1e2bf185c4a31bee1359d612e1b4e790a82d1e8b06976a01da96cb18d8dfc7dc53e2971cf17cea7f6be7d8b8e166db59760272c065f18f483c3e1e5055924eca58975ea757393bf2ff005d70c7861c11c234e0b29c8b092c5412be638f8471f984a5d66b11888c9d16def1c32a34fa28a4944faed42a2aaa2aaaa00a8abb5554018daa060018e3002f4ce3a257e7edb6db6db6f76f56fcdeff009fde7df256d16896892e82fa8c7d4647a75ef8f5e437af39f95008caaea55d15d5c15656da5594e0152a77020f391f8739dead369a69b4d6cd68d79adbf3fb81abe8f54f469f53e45f8edfb09fecb5fb44daddff00c2c2f851e1f875db8560be32f0a5bc1e14f184131cecb87d6b478eddb517889ca45ad43a9dab0fddbdbba0615fa070b78a5c73c21387f6467d8b96160d5f2ec7ce58fcbe51d2f0587c4ca6a8a6b472c33a153aa9c5db9be0789fc30e08e2ea7359b645848e2a69db31c042380cc232e93788c3c62eb34f68e2556a7d1c5a6e27f3a1fb63ff00c121be2efc00b4d53c7bf086f2f7e32fc30b3596f2feded34e10f8ff00c296288f2492ea5a2da3cc9e21d3ed11099b56d0d12655265bad1ed208de7afec1f0ebe907c3fc59528655c414e9f0e67751aa74a752b73e538eaaec9468e26a283c2559b7eed0c53716f48626a4da81fc8be217805c41c2b4ebe699054a9c4392d3e6a95614e8a8e6b81a4b56eb61a0e6b174a093e6af86e4925ef4f0f4e09ce5f8fac1958ab02aca482a410411c10d9c60823046323be38afe864efaad53d535d4fe7fd55efa35a493e8faf44efebeb65a89f9fe5cfd31e9db27ebde817f5ebeba77edcbf8d83d7fcfe5ea71d87d3bd01f3ff80f6eddfbf36ba7907a75fcbafd7d3d7f4a07fd5bf5fbbcfcb53fffd2f051f5278fcc64fa7391d3ff00af5fec47f5fd7aee7f90dd3b6fff000faffc37cac2ff00c0b3efc71fd38eb40755fd7cff004d3bea182c5554162c70aa012c49e06d032c4e7000039dd8e78a1bb6af44b56df405e5aeba777fadfa77d0fe9b3fe0981ff04c0d2fc37a57877f68dfda33c3b06a5e2bd4a1b4d73e1c7c37d6ed61b8b0f0d585c44b71a7789fc51a75c24915d7886ea3962bad2749bc894682a22bbbb8bfb5fcb8f4dfe29f1bbc6eaf8daf8ce0fe0fc64a8e028caa61b39ce30d3942ae36ac65c95b0581ad069c309092953af5e9caf8b7cd4e9c961f9a588fecef057c15a183a184e2ee2ec246b63ab469e2727c9f134e33a583a538a9d2c6e368cf994b1734d54a187a917f55f76a548fd61c6143fa020300018c0e001d319e3d080476036fcbd9722bf934feab399f1a6bb2785fc1fe2bf13410c7773f877c33aeebb0dacb2148ae26d234bbad423b795d177ac733db8491d7e7556dc0070a1bb72dc2c71d98e030539ba71c66370b8594e293942388af0a4e714f46e2a7749e8dad4e3cc7152c165f8ec6462a72c260f158a8c24da8ce542854aaa2dad5293824dad527a1f217ec07fb58ebdfb62fc18d4fe28f88bc27a47836fac3c6dab785534ad16fef2fed24834eb0d2ef12eda7be8d261348da83ab4614a0f2d5812e48afd07c57e02c27877c494323c1e6188cca955cb30f8f75f134a951a8a55aad7a6e9a8d2725cb154534deaefd7551f80f0af8ef15e20f0ed6cef1780c3e5d5296655f02a861aad4ad070a34a854551caac62d4a4eab4d2d1597acbee2e3d7b7a8f4e9c0e31d38cf5e33960bf989fa59f0efedf9fb58ebdfb1d7c18d2fe28f877c27a478cafaff00c6da4f855f4ad6afef2c2d238352b1d52edeed67b08e498cb11d3d6358c858cac8f96c05ddfa77851c0584f113892be478ccc31196d2a5966231eabe1a953ad5252a35685354dc6ab51e56ab5dbbdd5b4b9f9a78a9c778af0fb8768e7784c061f31a95732a1817431356a5184635a957a8ea2952529392746c9356777b6f1faf7c17aecbe28f077853c4d3c31dacfe22f0d685aecd6b1485e2b79757d2ed6fe4b78ddd43b470bdc18d19fe664405f1906bf3eccb0b1c0e638fc1426ea47078dc561633924a538e1ebce929c92d139285da5a26f43eff002ec54b1b97e071928a84b1783c2e2a508b6e30957a14eab8a6f56a2e6d26f5696a74b8ce01e41e39c9041ec411f371d7ee83df80a2b88ec3f9feff00829f7fc13074bf13697e23fda33f674f0ec1a6f8af4d86eb5af891f0df44b586dec3c4b616f0b5c6a3e28f0c69f6eb0c56be22b68a292ef56d26d233fdbe0cb756b10d60489a97f58f823e3757c157c1f07f1863255f015a54f0b93671899ca75705567250a381c7569b729e0e726a9e1ebd46de11f2d3a9278671950fe55f1abc15a18da18be2ee10c2468e3e8c6a62737c9f0d4e30a58da508f355c6e0a941a50c5c22a53af429c2d8a57a94e1f584e35ff99421949560559490c0820820e0820e086078e46473d39afed74efaad53d535d4fe317d77f3deebf5ff0083f313fc8ff3dc67f4a03fafebcec1fe4fff005bd2817f5f8fe5fa1fffd3f05e7dbe9e873d7e87afaf1ea6bfd88febfaf4d8ff0021ff00e0ff005fd7cba0bcff00fabf4e7f9d02eabfad7fe1b5f9799fb07ff0486fd8ded3f680f8bd79f177c79a5adefc30f8357ba75dc1617912bd978a7c7d3acb75a269924722ba5dd86811c09adead07c8b24cfa35acde65bde5c257f3cfd20fc45a9c27c3f4f87f2aaee9677c454eb425569cad57039542d0c4d64d59d3ab8b72786a12bdd4562271e5942123fa07c02f0f21c579fd4cff0035a3ed325e1ea94aa469548de963b359a73c35069dd4e96154162b111b59ca587a72bc6a4cfebaf0070060018007403038ec0718c061edc00057f9fa7f7c1c5fc44f889e0af84fe0cd7be20fc44f10d87857c1de19b36bed6b5bd49dd6ded21f3638a3548e2592e6e6e6e2e248aded2d2d229ae6eee658a0b786499d236f4b28c9f32cfb32c2e539460eae3b31c6d4f6586c2d149cea4ace4db726a108423194ea54a8e34e9c22e5394629c8f3b36cdb2dc8b2ec566d9b62e96072fc153f6b89c4d66f929c6fca92514e739ce4d429d3a6a53a93928423293513f18be2c7fc16d7f65ed4fc35e37f07786bc15f18f5f7d6fc37e21f0fd86b69a2785f4dd2259b55d2eef4f82ed9351f15c1ab476cb25c248e24d2d67f281c45e6616bfa3f21fa3471c50c6e5998e3732e1dc22c3633078bab8678ac756c428d0af4eb4e9a747013a0e6d45c55ab4a37fb4aedc7f9db3dfa48f04d6c1e659760f2de21c53c4e0f1785a589586c0d0a12957a152942a355b1cabc609caef9a8c656da37691f037fc13f3fe0a77f0e3f63cf82baa7c2ff00167c36f1bf8b352bff001beafe2a4d4fc3b7ba15bd8a5b6a361a55a476ac9a95dc170678df4f777611f9656450a4104d7eade2d78259c7889c4b433cc06739665f46965987c03a38ca78a9d573a35b1151cd3a3070516ab2495dbbc5deda1f95f853e34e4fe1f70dd6c971f93e658fab5732c463956c255c3429a856a5420a16ad352725ec5b7656d746ddd1f74ffc3fabe08ffd10ef8adff835f08fe7ff00211fc78c7e1c86fcbffe255b89bfe8a7c8bff09f1fff00cacfd3bfe268f86bfe89acf3ff000a301ffcb343e16ff8281ffc14efe1c7ed87f0574bf85fe13f86fe37f096a5a7f8df48f153ea7e22bdd06e2c64b5d3ac355b492d5534dba9ee04f236a08e8cd1f96023e719515fa8784be09673e1df12d7cf31f9ce599851ab9662300a860e962a1554eb56c3d4551bad050714a8b4d5ef792b5f53f32f15bc69c9fc41e1ba39260327ccb015a9e6787c73ad8bab869d370a34ebd3704a8cdcf99bac9dedcb68eb6ba3ef9f84ff00f05b5fd97b4cf0d7823c1de25f057c63d01f44f0e787bc3f7fadbe89e18d4b488a5d2b4bb4d3e7bb55d3bc573eacf6ccf6ed2208f4b698c646620ff257e539f7d1a38e2be3733cc7059970ee2d627198cc5d2c32c563a8e21c6bd7a95a14dbad80850534a4a2ef5a31bfda764e5fa9645f491e09a183cb32fc665dc4385786c1e13095714f0d81ad878ce851a746534a963e55e5072836b968b9dadeedfdd3f673e1dfc44f057c58f06681f107e1df886c3c55e0ef13598bed1b5ad35e46b6bc87cc78a5568e658e7b6b9b7b88e4b6bbb4bb8e0bab3b88a582e604951917f9c337c9f32c8732c56539be0eae0731c154f6589c2d6494e9caca49a716e1384e328ce9d4a6e54ea424a5094a2d48fe8aca737cb73dcbb0b9b6518ba58ecbf1b4fdae1b1345be4a91bb8c93524a709c271942a539a8ce138b84e3192713b420104119078e77608fee9e09c1ee36e3b1249cd79a7a27f22bff00057afd8ded3e007c5db2f8bde03d296cfe187c65bdd46eee2c2d2144b2f0a78fe111ddeb5a6c71c6a8969a7ebe93beb9a4c0032a4ebad5ac3e5c1670c75fe817d1f3c45a9c59c3f5787f35aeeae77c394e8c2156a4af571d94caf4f0d59b7773ab8471586c44af771786a8ef2a933f81fc7df0f21c2b9fd3cff2ba2a964bc4352b4e54a9c52a581cd636a989a2a31b2852c5293c4d08daca4b134e368422a3f8f9ebc7f2e7dbf2e39afe863f9fbfafc7f2fd03d38fe5f2ff009e9c503febfaf3b1ffd4f05f4ebd7faf4ee38e47bf1d320d7fb12ffcbf2fd773fc87feaffa7e63d559d8222177760a8aa3733b370155464966c84000c9278ce6936926dbb25ab6f64bbbdb6f5fb8126ec92d5bb256bbd76492dddffc91fdd87ec27f026d7f676fd96be14fc3d36896daecde1fb7f15f8cdd57135c78bfc570c7aceb2970fb54cada69b887458998605ae97046a4246bbffcb8f14b8a67c5fc739f66fed1cf0b1c5cf0196abfbb1cbf012961b0ce0aed4556509626497fcbcaf26eee4dcbfd3af0c38629f08f0464594f22862a584863f317f6a59863a2b1188537a393a2e71c345b5a53a304ac923ebcf5f5e73e8071d3d0f439f9bd48ce16bf3e3ef8fc40ff0082ea6b1a95bfecf5f08bc3b6d7735b699e24f8cd6ababc51315fb647a678675d9ad2198749228aeae05d2a382a2782090e5906dfe9afa2ee1e8cf8b7883193a719d7c170e547879495fd9cabe3b0b1a928ea9a94a10f66da69b84a71babdcfe6bfa4e622b4384b21c242728d1c6710c3eb118bb7b48d1c162a508c96ce319cd54b37a4a106949a5cbfa69e3787e157ecbdfb3f78b358d0b42f08781fc29f0cfc05abdde8f0369f6169a7c775a4e8f70748b69048b136a17fa85fc76f0ed7964bed4ef27da5e6b89b2df8ae592cf78e38b72fc3e2b1598e678fcef35c3d3c4cd56ad52b4a15f110fac4d5aea952a549ce574a34a85385f48451fb36651c8f8278531f88c2e1b2fcb30192e57889e1a0e952a7454e861e5f5783ba87b5ab5aac611d5ba95aa4ed77395cf9f7fe09bbf1abe22fed35fb39a7c59f8c1a2f85d358d53c69e25d2b439742f0dc3a3585de83a2b5a5889d2dcb5c09993564d52d64b85976992d648880f138afadf19386f27e09e3079070f6271ef0f432dc157c52c5636789ab0c5627da54e573518f2a741d0a8a0d6919a926d491f29e0ff11e71c67c22b3de20c3e09622b6658ca1869617071c352a985c33a74b99439a7ccd5755a9b9a767284a3f64fbf3fb2348ff00a05587fe01c1ff00c679fc47e3dabf28face23fe7fd6ff00c1b50fd53d850ff9f34bff0005c4f817fe0a43f1a7e22feccbfb394df167e0fe8be177d674bf1a786b4bd6e5d77c3916b36169a0eb4f7762d7125b06b71133eaafa5da473b4bb44b751c4007996bf57f06f86f27e35e30590710e271cb0f5f2dc6d7c2c70b8d9e1ab54c5e1953aaa119b8cb9ad4157a92872b6e34dcb451933f2cf18388f38e0ce10967dc3f87c13c450ccb0543132c560d626953c2e25d4a4e6e1cd0e56ebba14d4dbb27512fb48f7ff0345f0abf6a2fd9fbc25ac6bba0f843c71e13f89be01d22ef59b71a7d85de9f2ddeada3c0757b68d51656d3afb4fbf96e61da92477da5de41b43c5730064f92cd259ef03f1666186c2e2b31cb31f9266b88a7879bad5a9d550a1889fd5e6dbb2ab4aad25095ed2a55e9cefac267d565ab23e36e14c06231586c066580ceb2ba15311054a94e939d7c3c3eb105653746ad1aae71d1aab46a46d7538dcfccbff8215eb1a8dc7ecf7f177c3d737735ce9be1bf8cd74ba3c52bee1651ea5e17d0a6bc8a218d91c735cdb9b928a31f689ee25e5a42abfb5fd28b0f461c5bc3d8c8538c2be338721f589455bda3a18ec5469ca5ab6e5184f92edb7c9182bbb5cfc67e8c788ad3e12cfb093a929d1c1f10cfeaf193bfb38d6c161655231daca538f3b497c7293d2e7ee07afcbf51dcfb8ee467df1e9924d7f321fd287c87fb767c09b5fda27f65af8adf0f4da25cebd1787ae7c57e0c90806587c5fe148a5d63454b77033149a8bdbcda2ca541cdaea73c6d98d9ebf41f0b78a67c21c7390e6fcee185962e180cc55fdd965f8f6b0d89735a292a2a71c4c537f1d083567147c0f89fc314f8bb8233dca7914f151c24f1f973fb51cc3031788c3a83d5c5d670961a4d2d69d69a774d9fc2732ba33a3a947425591be56899721959480559482083c83c6060d7fa8e9a6934ee9ea9ad9aeeb7dfd7ef3fcc56ad74d34d3b34ef74efb3d9e8fcbee1bcf1d3e991f37bf4e78ff77f218a00ffd5f06f4efeff008fb7af07d3839e0d7fb107f90ff8eda7ebfd76d3a9efbfb2a78221f891fb4c7c04f035d47e7587897e2ef8034fd562c67768c7c4ba74dad600c64ae9515e49d474f98aa826be538ef33964dc17c579a41f2d5c170fe6b5a83ffa89fa9558e1b7b6f5e54d7f9ec7d4f0365b1ce38cb8572c9c79e96333fcaa9575df0ff5ba52c4bd3fea1e353b7aa5767f7e015500440115142a800055555002803a05007b018f45aff28db6db6dddb776df56f767fa9e95b45a25a24ba1f9a1fb507fc13d3c47fb467c57bff89ba67ed53f16be135a5f68fa2e92be0ef080befec6b67d22d7eccf7d1fd9fc57a447e7df31f36e36d9ab6fc02eec4b2fed1c11e2e60f83f21a59256e05c833e9d2c4626bbcc731f65f599ac44f9d527cf80c43e4a4bdd87ef5e9d237b1f8d71af84f8ce2ecf6ae75478e33ec8a9d5c3e1a87f67e5eaafd5a0f0f0e475572e3b0eb9eafc53fddad7772d19f9a7ff0544f815a8fecf5fb1f7ece5f0a751f897e27f8a97567f1cbc53a83f8d7c5a26fedcb95d6743d4eea2b690cfa9ead3795a6aced05b66f597628c2478db5fb4781fc51478b7c43e31cfa8e4b81c8a153863034965b97f2fd561f56c4d0a729c792861e3cd59c14e7fbbddeefed7e37e3670cd6e13f0fb84323ad9d6373b9d3e26c75679963f9beb33fac61ab4e3097356c44b968a97243f79b6c95bdefa92dbfe08afe19d7352d3e4f8b1fb547c6df89de1fb3b98ee5fc3d7ecb66b7451b2d19bfd4f58f133db2cabb91e5b5b586e02bb18e7898efaf869fd24b1b85a155643c0dc339262ea41c162e9275392eb47eca8e1706a6e2f58c6752506d6b16958fb787d1cf078aad49e7bc6fc499d6129cd4de12ada9a9b4eed3ab5b158c7052574dc29c27ae9522d5cfd88f007807c23f0b7c17e1ef87be02d16d3c37e0ff0009e9b0e91a168d6424f22c6ca1dcdb7cc99e59ee2e2695e5b8bbbbb89a6bbbcbb9a6bab99a5b89a476fe78cdb35cc33cccb199b66b8aa98ccc31f5a55f1589abcbcd56a4b4bf2c546108c629429d384630a708c6108c6118c4fe82cab2ac06499760f29caf0d4f0797e028c70f85c353bb8d2a71d6dcd26e5394a4e53a9526e53a952529ce529c9c8ec79c8e7b70307f33ff00d7fe75e71e81c778ff00c03e11f8a3e0bf11fc3ef1ee8b69e24f07f8b34e9b49d7b46be127917d67310db4490b473c13c32a457169756b2c37367750c3756d3453c28ebe86539ae6191e6383cdb2ac554c1e6180ad1c4617134b979a9548df5e59294270945b854a738ca1529ca509c65094a279f9ae5780cef2ec66539a61a9e332fc7d1961f1586ab7e4ab4e5676bc5a94251928ce9d4838ce9ce319c2519c548fc77b9ff822bf86743d4f507f84ff00b547c6df863e1fbcb992e53c3f60eb78b6a5db2b18bfd3758f0d1b958976a249776b3dcb2a299667602bfa221f492c6e2a85259f70370ce778ba705078baa9d373496afd956c2e3141c9ddb50a918768a5a1fcfb3fa39e0b0b5aabc878df89325c2d49b9ac2d2b5450bbd17b5a38ac1b9f2ab2529c253d359b7ef4be5bff00825c7c0bbffda0bf63efda47e1569df127c4ff000aeef50f8dfe16bc4f1a784fcffeddb65d1744d32f1e08bc8d4f4a97cbd4162305cedbe405246cac84ec6fb9f1c78a297097885c199ed6c9b059e53a5c358fa6f2dc7f2fd566f1389ad4d4e5cd47111e6a2e5cf0fdd3d63a38eacf88f04f866af16787fc5f9251ce31b91d4abc4980aab32c0737d661f56c351a8e11e5ad87972d651e49fef568f552d11fa57fb307fc13d3c47fb39fc58b0f89ba97ed57f16be2c5ad8e8dad694de0ff00178bff00ec5b87d5ed45b25ecbf68f15ead189ec08f36df364cde674911896afc5f8dfc5cc1f186435724a3c0b906433ab88c357598e5decbeb305879f3ba4b9301877c9557bb3fdead3a4ad63f64e0af09f19c239ed2ceab71c67d9ec2961f1341e5f982abf569bc44391547cf8fc42e7a5f143f77bf55f67f4bd955d4a380c8ca5590f2195861811ce4107040182091cae6bf174da69a7669dd35d1ad99fb2b57d1ea9e8d3ea7f021fb5678221f86ffb4c7c7cf035ac461b0f0d7c5cf1f69fa547823fe24c3c4ba8cda2e41ce37695359b753c1e0b260d7fab9c0999cb39e0be14cd26f9aae3787f2aad5dff00d44fd4a94713b5f6af1a8bfcb63fcb1e39cb6393f19714e5908f2d2c1e7f9ad2a11ed86faed5961fefa12a6fadbbbdcf01fcfebcfcbfe3ebf37e3dabeacf953fffd6f05f4e31cff5ebebfd3e6e78cd7fb12ffcbf2fd363fc87fc3fadbf43ee0ff826df93ff000dcbfb37fda0a797ff0009eb6ddf8004ff00d85acfd9bae46e173e484e321c03c922bf31f1979ffe218719725eff00d94af6fe4fad61fda7cb939afe573f49f07b97fe226707f3356fed5d9dbe2faa627952f3e6e56baf6e87f71e7a9e33edc7a75f5fcff00720aff9887fa5e27ff5f9fef74e07afd06df407ab5007e14ffc176ffe48f7c01e3fe6b0ddfcbe9ff14c5eff00fafaf4e98c57f51fd16ffe4a1e2cff00b27a9ffea7533f98be93dff24ff0ae97ff0085fa9a7fdc9543f75fd3e41efc0fd39fc327eb8ed5fcb87f4e87afc83dba7ebffd61f9e6800ff800c77e179fd78ffc7bd3de800e7fb8339f41c0fcf93ff7cfa7bd001ff0018edf77fcfe9f962803f09ffe0849ff00247be3ff007ff8bc369cf391ff0014bd9703a93cf1c7d383935fd47f4a4ff928784ffec9ea9ffa9d50fe62fa30ff00c93dc53ff63fa5ff00a8348fdd8e79e067d3b1f7f4049f7cf1db15fcb87f4e87a7e879cfd0f1923bf3b477e831401fc387fc1497c8ff0086e5fda47ece5447ff0009e2efd9d167fec0d1bed58c606e173e76fefbf7139eb5fe9df835cfff0010c38379ef7feca76bff0027d6b11ecfe5c9cb6f2b1fe68f8c3cbff11338c396cd7f6a2dbf9bead86e7f9a9defe773e1fe78e7e839e7ebe9e9cfd7a9afd34fcd7fabff005dbf53ffd7f05f4e73edc7afe07a7e236f7c115fec47f5fd7aee7f90ff00d7f5f89eff00fb2a78de2f86ff00b4c7c03f1cdcc861b1f0d7c5df006a1aacb9618d1478974e875a00af23769335e2f75cb7208c8af94e3bcb259cf05f15e5705cd571bc3f9ad1a0bfea27ea556586defb578d37fe5b9f53c0d994727e32e15cce6f96960f3fcaaad7976c37d7294712fba6e84aa2ebbea9dda3fbef0cae03ab028c032ba9c86565c82a7a6082082339ce47560bfe51b4d369ab34ecd3e8d6e8ff0053d3beab54f54d753f343f6a0ff8289ea1fb377c58d43e185bfecc9f16be28c563a368bab0f15f841663a2dc36b16bf68367114d03505f3ec48f26e3fd2586e38db1815fb470478414b8cb21a59dcf8d720c8dd5c4e270ff0050cc1afacc3eaf3e4f692be2a97bb52fcd0f736eacfc6b8d7c5cabc1f9ed5c923c199f676a961f0d5febf97f37d5a7f58873fb356c2d55cf4fe19fbfbf4dcfcd3ff82a27c78b8fda27f63dfd9cbe2ccff0efc51f0ce4bdf8e5e29d3dbc21e2dde35cb7fec6d1353b549e50f63a7b797a82c065b622d57f76cbcb654d7ed1e07f0b43843c42e31c8239c6073a54b863035566197ffbacfeb389a351c62d55aab9a8f3f2cfdfdefa2b33f1bf1b38a25c5de1f708e7b2ca31d933abc4d8ea2f2fc7dfeb30786c356a6a4ef4a8be5adcbcf0f712e57bb5694bea3b5ff82d3f85f44d474e4f8aff00b2dfc6ef863e1ebdb94b77f10df2477b1daef3832fd8752d17c34d74b180cef1da5ccd72554f9504cc315f0d3fa36e3b1346b3c878e78673ac5d2839ac2526e9b9db68fb5a38ac62a6e4eca32a908c2ef56ad73ede1f48cc161ab52fedde08e24c9709567183c5d5b5450beefd9d6c2e0dcd4526dc69ce536969076e597ec4f807c7be0ff8a3e0cf0f7c41f016bd6be23f07f8b34d8757d0b5ab1de20bfb29b72e7cb9a38ae2de7825496daeed2e618ae6ceea19ed6e628a786444fe77cdb2acc323cc71994e6b85a983cc3015a587c561aaf2f352a91b69cd17284e128b53a75212942a539467094a128c8fe82cab34c06779760f36caf134f1997e3e8c71185c4d2bf255a72babda494a128c94a1529cd4674e71942718ce2e2761c647cc7a7fb381fd33db8af3cf40e3fc7de3df07fc2ff06788fe20f8f75db5f0e783fc27a6cfabebdacdf6e30595940557798e18e5b8b89e695e2b7b4b4b78a5babcba960b5b68a69e68e27f4329cab30cf331c1e5395616a63330c7d68e1f0b86a5cbcd56a4afa7349c61084629cea549ca30a74e329ce51846523cfcd734c06499763336cd3134f0797e028cb118ac4d5bf252a71b2bda29ca72949c614e9c14a752728c21194e4a27e3bdd7fc16a3c2fadea3a8afc28fd96fe37fc4dd02cae1ed93c436291d94773b0e04a2cb4dd17c48f6ab2295748eeae21b80180921473b6bfa221f46dc761a8d179f71cf0ce4b8bab053784aadd470bef1f6b5b1583551c5dd4a54e1285d68ddee7f3ecfe91982c4d6aab22e09e24ceb094a7282c5d2b5353b3d25ece8e1718e0a4acd46a4e3349ae6826f963f2dff00c12e3e3bcffb3cfec7dfb48fc5783e1e789fe25cda7fc6ff000b59af837c261ceb97435bd134cb333c612cef98c76026f3ae7fd188f2d1b25546eafb9f1c785a3c5de21706e432cdf0592c6af0ce3ea7f68e3edf5587d57135aa72caf5696b5797961efeef67a1f11e09713cb84bc3fe2fcf2394e3739952e24c052fecfc05feb33face1a8d3738da955f76973734fdcd93d51fa57fb307fc144efff00691f8b161f0c2e3f664f8b7f0ba2bed1f5bd58f8b7c5eb30d1ad9b47b51722ca42fa0e9ea27beff536ff00e9232fd9f815f8bf1bf8414b83721ab9dc38d720cf1d2c4e1b0ff50cbdafaccfeb13e4f691b62aafbb4edcd3f736ea8fd9382bc5babc619ed2c967c199f648aae1f135febf985fead0fabc39fd9bbe1697bf53e187bfbf467e97b32a02ce42aaa9672d80a1472ce49200c01cb31e02e4e0e057e2e936d249b6dd925bb7d96fabf4fbcfd95bb6af44b56df43f810fdaafc6f17c48fda63e3e78e6da4f3ec7c4bf173c7da8693292496d18f897518745dcc793b74a86cc7451f2f00676affab9c09964b26e0be14cae6b96ae0b87f2aa35d7fd44fd4a94b13b5b7af2a8ff00cf73fcb0e39cca39c719714e67097352c667f9ad5a12ef87faed58e197fe088d38f4f24b44780ffc07ebc7f2e78c75e3775c0ee6beacf96feadfd77fd0ffd0f06fa8effd7afa73c1f5e4e3906bfd88febfaf4d8ff21ffafebfaee39599195d1d91d1832329219194e5595861810c01041e31c6314349a69aba7a34f66bb3df7f4fbc13b34eed34ee9abee974ebe6add57a9fdd87ec27f1dad7f68afd96be147c42174975af45e1fb6f0a78ca30dfbc83c5fe14863d1f597b842498df526b78b5a8938dd6ba95bc883cb65aff002e3c52e169f0871ce7d94723861658b9e3f2e76f76597e3dbc4e1941e8a4a8a9cb0d2697c74269d9c59fe9d7861c4f4f8bb82322cdb9d4f151c2430198afb51cc3031587c439ad5c5d6708e2629bd69d683574d1f5dff2f5ee78e87d011cf3c719fbb5f9f1f7c7e20ffc175747d4ae3f67bf845e22b6b49ae34df0dfc65b56d625850b8b28f52f0c6b90da4d29fba91cb736e2d95d8a8373710479cbad7f4d7d17711461c5bc41839d48c2be37872a2c3c64eded25431d859548c746dca309fb46926d4233959dac7f35fd2730f5a7c279062e1094a8e0f88a1f5894637f671ad82c5469ca5aa494a70f669bd1ce7157573f4d3c6f37c2bfda8ff67ef1668fa16b9e0ef1c784fe26f80b57b5d1ee1b51d3aeec24bad5b47b81a45c48599db4ebfd3efe4b79f73c71df6997906e2b0dc406bf15cb239ef03f16e5f88c561731cb31f9266b87a98982a35a9d68c286221f5882b5955a556929c6c9ca957a73b6b0923f66cca591f1b70a63f0f85c4e5f996033acab114f0f3f6b4aa5273c461e7f579b7793a556955942576955a35217b73c6d1f9fff00e09bbf05be23feccbfb39a7c26f8bfad7851f58d2fc69e25d53438b43f12c1acd85a683ad35a5f2c11dc14b71133eacfaa5dc96eb1ed596e649496699abeb7c64e24c9f8d78c1e7fc3d86c7ac3d7cb7054314f15829e1aacf158655297338734f992a0a85353beaa0a3b451f29e0ff000de71c19c22b21e20c4609e2286638caf858e1b191c4d2861712e9d45153b46cdd775ea4a16d2536f5e66e5f7dff006be97ff414d378edf6eb6e7df3e671f4c7e75f947d5b11ff003e2b7fe0aa87ea9ede87fcfea5ff0083227c0bff000521f82df11bf69afd9ca5f84df0835af0aa6b3aa78d3c35aa6b716b9e25b7d1ac2ef41d15eeefdade4b8d9722529aac7a5ddc76ed190d2db24a183c486bf57f06f89327e0ae3059ff0010e1b1cf0f432dc6d0c2cb0b829e26b53c5e2553a4a718394796f41d7a729f326a351c7552923f2cf18387338e33e109645c3f88c12c457ccb055f131c5632387a5530b86752a3839f2cf99aaea85450b59ca9a7ba47bff81a5f859fb2e7ecfde12d1b5dd77c1de07f09fc32f0169169ac5c0d4b4fb4d3e3bad2b48806af728cbe5bea37da85fc77136e48defb53bb9f7049ae67cd7c96691cf78e38b330c4e170b98e678fcef35c454c3c1d1ad3aae15f113fabc1deea8d2a549c2166e34a8538db48459f5596bc8f8278530187c56272fcb70192e5787a7889fb5a50a4a7430f1fac4d5b95d6ab56aa9cb48caad6a92bd9ce5697e657fc10ab47d46dff0067af8bde22b8b49adb4cf127c66ba6d2669936fdb63d37c2fa143772c273b648a2b9b836e640cca2782e23e7cb6ddfb5fd28b11467c5bc3d838548cebe0f8721f588c5dfd9baf8ec54a9c65a26a528439ecd27c9283b2bd8fc67e8c787ad0e13cfb173a72851c6710cfeaf292b7b454705858d4947a3519cdc1b5a73c64ba1fb81dcf2338e0f6c7bfbff0017031eb90457f321fd287c87fb76fc76b5fd9dbf65af8adf108ddc76dae4be1fb9f0a7835090269bc61e2b8a5d1f466b74ce647d35ae26d6a55f98adae9b70eff2a3357e83e1770b4f8bf8e721ca391cf0b1c5c31f98bb7bb1cbf0128e2712a6ecf9556508e1a2dad6a5682d1b47c0f89fc4f4f8478233dcdb9d43152c24f01972bfbd2cc31f1787c3b82d1c9d1739626493d29d09b76499fc26b333b3bbbb3bb92d2331dcd2331cb12c72c58b1c124e49cf524d7fa8e924924ac96892d92ecb6dbd3ee3fcc56dbbb7ab6eedb7bebbb7aeaf7df7ee27a73f43f2f1ec7d73d38f4fad007fffd1f05fa1f4e3d39f6f43c1fa60f5aff620ff0021fbfe7fd76fd7d40f6e3ffafc1eb9f6e39ef407e5dbb1fb09ff000485fdb22d3e007c5ebcf843e3dd516cbe187c65bed3ad20bebb9112c7c2be3e8564b5d175292492444b5d3fc4093c7a1eaf31f91264d1eea5f2eded2792bf9e7e905e1d54e2ce1fa7c4195507573be1ca75a72a54e37ab8ecaa4e353134525773ab8471789c3c6d771788a71bcaa40fe80f00bc42870ae7f5320cd6baa792f10d4a34e356a492a581cd637861abc9c9a54e962d4d61abcb652586a92b421271febac72320839c1c8c6d03d4727a9f9b39e833c9c0aff3f4fef838bf889f0ebc15f163c19af7c3df887e1eb0f14f83bc4d646c359d0f524736f790ef496271243245716d736d3c71dd5a5ddacb05d5a5cc715c5b4d1cc8ae9e96519c66590e6585cdb28c655c0e6382a9ed70d8aa2d29d3959c5a6a49c2709c652854a7514a9d4849c671945b89e766d9465b9ee5d8aca737c252c765f8ca7ecb1386ac9b8548dd4934e2e3384e128c674ea5394674e718ce0d4a299f8c3f167fe0895fb2f69be1af1c78c7c33e36f8c5a049a27873c45e20b1d0d35cf0bea7a4432e97a55d6a305a17d47c2536af25bb496e91397d51a6f2c9fdf17c3aff0047e41f497e38ad8dcb32ec6e59c3b8b589c6613095712f0b8ea188946bd7a546535ec71eb0ea6949c95b0f18df4699fceb9efd1bb82a8e0f33cc30599710e15e1b078bc552c37d67055e846542854ab18375702ebb837049deb4a56fb57499f02ffc13effe0989f0dff6c3f82baa7c50f167c4af1bf84b53b0f1c6b1e168f4cf0ed968171632db69d63a55dc774efa95a4f3fda247d41d5d55c205442b862f5fab78b5e36e73e1df12d0c8f01936599850ab9661f1eeb632ae261554eb56c4539412a3351e44a8a69b49de4d6a7e5be14f82f93f883c395b3bc7e7199602b52ccb1181547094b0d3a4e146950a8a6dd6839733759a693b5a29e973eeaff870a7c10e7fe2f9fc55f73fd99e10e0e7a7fc8373d7d4631d30335f97ff00c4d4f137fd131917fe1463ff00f961fa6ffc4aef0d7fd14b9e7fe13e03ff00959f0b7fc140ff00e0987f0e3f63cf82ba57c50f09fc49f1bf8b752bff001be91e167d33c4565a0dbd8c76ba8d86ab76f76afa65a5bcff00688db4f4445f33cb2aefb9785dbfa8f84be36e73e22712d7c8f1f9365997d1a5966231eab60eae26755ce8d6c3d3506ab4dc795aacdb693778a5a1f9978ade0be4fe1f70e51cef019c6658fad5732a181747174b0d0a4a15a957a8e69d1829392749249b4b57b9f7cfc26ff8224fecbba9786bc0fe31f1378dbe317881f5bf0e787bc417fa23eb9e16d3348966d574ab4d427b30fa7784e1d592d95ee1a252baa24fe5aae6567cbafe519ffd25f8e28e3733cbb059670ee1161b198bc252c4ac2e3abe2231a15ead18cdfb6c7bc3b9b515277c3ca37d1247ea5917d1bb82ab60f2ccc31b99710e29e270784c555c37d67054284a55e853ab28274b02aba8273695ab4656fb576d9fb3bf0efe1df82be13f83341f87bf0efc3ba7f857c1de19b3fb0e8ba1e9aac2ded213234d2bb492b49737373737124b3ddde5c4f3dd5e5ccb2dc5c4d24eeeedfce19be719967d9962b36cdf19571d98e36a7b5c4e2ab34e75256514928a50842118c614e9d351a74e1151846314a27f456539465b9165d85ca728c252c0e5f82a7ecb0d86a29f2538ddca4db93739ce7394a752a4dca739c9ce72949b91da138049c6072492b8207a9c63d793b4f71d18579a7a27f22bff00057afdb22d3e3ffc5db4f843e02d516f3e18fc19bdd46d27bfb4951ac7c55e3e9c25aeb5a947244f225d69fe1f8e09344d267f951e77d66ea1df6d7703b7fa05f47cf0eaa709f0fd4e20cd683a79df11d3a352146a26aa60b298b7530d45a693855c5ca4b135e3ba8ac3d3769c26a3fc0fe3f78854f8ab3fa7906575bda64bc3b56ad39d5a72bd2c766b2b53c4d68b8dd4e961145e1a84af6727889af727172fc7ae78e47e9c71f974e783c74390457f437f57febbfe87f3f87383c8fd3fc3b703e6f5c13c034fe5fd5b7fd43b6ffd77ff00827fffd2f06fd7a7e3fd39181f89cf02bfd883fc87f9fcbb7fc3ff005b087ebf53c71c76cffdf5f873fc340bfabf7febeed7d0012acacb90cac0a9070ca4721b2304329f981cf0587439a1abe8f54f469f50bdb55756d7d3d3a69f77e27f4d9ff04c0ff829fe95e25d2bc3dfb39fed19e21874df1569b0dae89f0dfe23eb7750dbd8789ac6de14b7d3bc2fe28d46e1e28adbc436d1451d9e95ab5dcac35f062b4bb986b0229352fe29f1bbc11af82af8ce30e0fc1cebe02bcea62739c9f0d094eae0aace5cf5b1d81a304e53c24e52752bd0a69bc23e6a94e2f0ce51a1fd9de0af8d543194309c21c5d8b8d1c7518d3c364f9c626a4614b194a1150a582c6d69f2a8e2e09469d0c4547fed578d3a92588519623fa02041e4739192c31861e8b8cf5e00c9edc64d7f269fd56737e34d0a5f14f83fc57e1982e12d66f11786b5ed0a0ba9559e3b79757d2aeb4f8e79a35c3bc70bdc095d50e591481824576e5b8a8e0731c063670752383c6e17152845a529c70f5e155c22de89c942c9bd137a9c798e1658dcbf1d838c94258bc1e2b0b19c937184abd0a94949a5ab51734da5ab4b43e42fd80ff64ed7ff0063af831aa7c2ef1178bb47f195f5ff008db57f150d5745b0bcd3ad628352b2d2ed12d4c37cef2b4d135833b383b48954001812dfa0f8afc7b85f113892867983cbf119752a59661f00e8626ad3ad373a356bd47514a9251e592ac9256bae5d6e7c07857c098af0fb876b6498bc7e1f31ab5732af8e55f0d4aa5182856a5429aa6e355ca4e51749b6d3b3bf4da3f71f39f7c71ee3dfb8207af193c77afcc4fd2cf873f6fcfd93b5ff00db17e0c697f0bfc3be2ed23c1b7b61e36d23c54daaeb5637ba85ac90e9d63aa5a3d9ac160e9289646d411924cb2288dc153b857e9de1471ee17c3be24af9e6332fc46634aae5988c02a186ab4e8d453ad56854551caaa947962a8b4d6ef9b4bef1fcd3c54e04c57883c3b4724c263f0f9755a59950c73af89a552b41c28d2af4dd351a528c94a4eaa69bd159fa4bebdf066853785bc1fe14f0ccf711ddcfe1df0ce85a14f7312b243732691a55ae9f25c448f96449dedcc88921dca87073835f9f6658a8e3b31c7e361074e38cc6e2b151849a72847115e755424d68dc54ecdad1b5a1f7f97616582cbf03839494e584c1e170b29c53519ca850a749c927aa52706d27aa4f53a42400493c004e49ea00eff875c6de78272035711d87f3fdff00053fff00829fe97e19d2fc45fb3a7ece9e228752f166a50dd689f11fe23e89770dcd87866c2e6136fa8f863c31a8dbf9b15d788aea2964b5d5755b5907fc23fb66b5b498eb25df4ffeb1f047c11af8daf83e30e30c1ca8e028ca9e2726c9f130942ae36ac25cf471b8ea3349c3070946352850a91be2df2d4a915864a388fe55f1abc6aa183a18be11e11c646b63ab46786ce338c34e33a582a538f2d5c160aac14e353175232953af5e9ced845cd4e9c9e25ca543f99224b33336e66639625892cc492589e5892dce73ce49193babfb5d24ac968969a2dbd3e5fe5d0fe316dbd5bbb6eedbebff0007cfe627a71f8e7f4f4ebe9db91f2e6800edd0fe7d38effa839e3a7f09147f5fd7cc3fafebb1ffd3f05fd3a73d7d7d71d393f91e718aff00620ff21fe5ff0007fadbfa407b76f41f877ffd07f1f71407f4d7f5dff4f313f1fe21f89ff0edff0001f5a05d37f9edf3eabf0fbc504a9dcae5594a90c0e082324152390c3eff00cbd0f4a1abe8f54f469f51ecd6b6feb7efa793fbf43f60ff00637ff82bdfc5efd9fed34af01fc5db4bdf8cbf0c2c963b3b09ef35210f8fbc2b648891a47a6eb7751ca9afd85a468041a4eb8c26446f26d759b3b748e1afe79f117e8fbc3fc5952be6bc3f529f0ee7755caa55853a3cd9563aab6db957c353945e12ad46df357c32716fde9d0a937299fd01e1ef8fb9ff000ad3a195e7f0a9c4392d3e5a74a552b28e6981a4bdd51a389a8a6b174a09251c3e25c64be08626108c632fe8c3e04feddbfb2d7ed156b6bff0afbe2bf87e2d7ae50197c19e2bb98fc29e2f865c2efb74d175896ddb52923276b4da2cfa95ab0f9e3b864c1afe3ee29f0b78e7842753fb5f21c5cb0b06d2cc7010963f2f947a4de230f19aa29a5751c4aa153a3845df9bfaeb863c4fe08e2ea70794e7b848e2a695f2ec7ce380cc232eb0587c44a2eb34f79619d6a7d549a6a47d76acaeaae8c1d586e4756ca9538c1dc3390dc631c11dce4357e7ed34da69a6b74f46bc9edf97dc7df277d56a9ea9aea2faf5f71ce739ea3d0679e0fb718a402332a297765455192ec7081402496270381924b141df2314d26da4936dec96adf92dff002fbc1bb6af44b56df43e44f8edfb76fecb3fb3b5a5dffc2c2f8afe1f975cb64629e0cf0a5d47e2bf18cd300765bb68da3cb70fa6b48410b2eb53e9b683efc97289cafe81c2de16f1cf17ce1fd9190e2e3859b57cc71f096032f8c74bcd62313182ac92d5c70cabd4e8a1276e5f81e27f13f82384a9cde6d9ee1258a8276cbb0338e3f3094ba41e1f0ee4e8b93d14b13ec69f5724939c7f9d0fdb23fe0af5f177e3fda6a9e02f843697bf067e18deacd697f716ba8f9fe3ef15593ab44f1ea5ad5ac7047e1fd3ee91cf9fa5688ef3347886eb59bcb691e0afec1f0ebe8f9c3fc2752866bc41529f11e774daa94a1528f265581aaacd4a8e1aa4a6f17560d7bb5f1518c53d618784d299fc8be2178fd9ff001553af95e414eaf0f64b539a9d59d3aca59ae3a935671ad89a6a0b094aa26d4a8619ca4d7bb3c44a0e48fc7b2ccc773312ccc492c589662dc924e4939ce73d73824fdeafe86b5b4564969656b2f4e9f774f99fcfedddddeadddddff57d7fe1c4f5e4febf97e5c70724fa30a7dbfafbff00e087cbafebbfea1e9c9fd7d3e9ce47aede4647208a41fd5ffaedfa8763f37f3f4fa71d88c6e1d4f42453febf0fcff50eda7fc0feb6d0ffd4f06ffeb71d3bf1d7d781cfa64726bfd883fc87effd5bfadffa427f9cfaf07a63db27d33d39cd02eda7fc0feb6f9fa87e9cfe5c0e3d324607fc08e3bd01df4ff83a796be5dfb073cf1f87f8fa8278fa0a07fd7f5d3fad3a873c74e9d7d78ede993cfd16817f9f9f7fbff4f90e56746574628c84323292ad13ae0ab0618c32919041c8ea31834349a69aba7a34f66bb3df7f4fbca4da69a6d34ee9add3eeb6d57afdc7be7823f6abfda67e1b4315af81be3e7c5cf0d5843f734ad3fc7de261a370d907fb166d4e7d28e3247cf66c3e6233b58d7ca667c09c179cc9cf34e14e1fc6d596f5eb65582facbffb998d28d75f29fc9dae7d4e5bc73c6593c630cb38a73fc1d28fc342966b8d5865ff0072d2ab2a0fe70f2d6feefb0ffc3c97f6e6f23ecfff000d23e3bf2f6ec0fb341f3c0fbbff001f5fd8ff00692d8fe312abe792d9e6be77fe20d7861cfcff00ea6e557bded7c5f277fe1fd67d9dbcb97cb97a1f41ff001187c4ce5e5ff5c334b6d7b6179bff0003fabf3dfcff001ea78f78dff6abfda63e24c525b78e7e3e7c5cf12d84d932e93a878fbc4c74662c72c7fb161d4a1d297240e964bc281c6d55afa2cb381382f26929e57c29c3f82ab1dabd1cab05f595ff007332a52aefe73f92bdcf9fccb8e78cb388ca199f14e7f8ca52f8a855cd71af0cff00ee5a3563417ca1e5a5bdef026677667772f23b16776259a466c9624f2c589258b6793cf249afab492492564b4496c9765b6de9f71f2cdb6db6db6dddb7bb7ddefabf5fbc4e73f7474f41ff00eae9f2f23dc641340bbebff03fadf5139e3e5efe9ffd7c8c8e3f88718fba7347f5fd770feadfd76fd45e79e07e43fc7073d7f84f183d8d1fd7f5d83e7d7f5dbf40e78e3f4ff27afcdc8e7d98501fd5bfaeff00a09ce3ee8fcba7ebdbfd9f5c819c8a3fafebe61db5ff0083fd6fa1ffd5f05fc31ee38cfafafd47bb638241aff620ff0021ff00afebfafcc0f51ebfcbdfdf9e3e99f4a05f3ff83fd6fa09e9d4fcc31efeff00873d3dbb501d37ff0081a7dfe7dff00fef75f73f874fc07af73f4a07fd7f5d3fad3a87a707a7bf1c73efe83ebef40bfcfcfbfdff00a7c838e78ec79fef7038ff00f50fa630681871c71dcf6fb9c8f7fc73f8e36f140071cf1f87f7be6ebfe47d7208a0038e3f0e71d3afcbd78f4fe2f43ce0d001c63ee9fa63a7cbd7af3ff8efa7dee6800e32793d3dbf31ff00a1f4fa63068feadfd77fd03fafebb071c75ebe83d7ebdbae7a7cd9036e453efaff00c1feb7d4038e7afe9c7b7a71f77a743ce411476d7fe07f5be81fd7f5f20e38ebf4c0ff001e33f739ddd3079c1a5fd5bfaedfa809c63b9e7d073faf39f6dbcae3ef734fe7fd5b6fd03fafebb9ffd9"
				}
			]
		}
	}
]
```

##### Returns 返回值

- `hash`:`HASH` - 返回在区块链上的交易哈希，当交易不存在时返回0哈希
- `info`:`HASH` - 返回该token定义的哈希
- `token`:`HASH` - 返回该token的唯一的哈希

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_createToken",
	"params":[
		{
			"from":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
			"to":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR",
			"token":{
				"info":"1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A", 
				"uri":"http://www.jingtum.com", 
				"items":[
					{
						"name":"code",
						"value":"002"
					},
					{
						"name":"quality",
						"value":"99.99999%"
					},
					{
						"name":"weight",
						"value":"10"
					},
					{
						"name":"image",
						"value":"ffd8ffe000104a46494600010100004800480000ffe100584578696600004d4d002a000000080002011200030000000100010000876900040000000100000026000000000003a00100030000000100010000a00200040000000100000078a0030004000000010000007800000000ffed003850686f746f73686f7020332e30003842494d04040000000000003842494d0425000000000010d41d8cd98f00b204e9800998ecf8427effc00011080078007803011100021101031101ffc4001f0000010501010101010100000000000000000102030405060708090a0bffc400b5100002010303020403050504040000017d01020300041105122131410613516107227114328191a1082342b1c11552d1f02433627282090a161718191a25262728292a3435363738393a434445464748494a535455565758595a636465666768696a737475767778797a838485868788898a92939495969798999aa2a3a4a5a6a7a8a9aab2b3b4b5b6b7b8b9bac2c3c4c5c6c7c8c9cad2d3d4d5d6d7d8d9dae1e2e3e4e5e6e7e8e9eaf1f2f3f4f5f6f7f8f9faffc4001f0100030101010101010101010000000000000102030405060708090a0bffc400b51100020102040403040705040400010277000102031104052131061241510761711322328108144291a1b1c109233352f0156272d10a162434e125f11718191a262728292a35363738393a434445464748494a535455565758595a636465666768696a737475767778797a82838485868788898a92939495969798999aa2a3a4a5a6a7a8a9aab2b3b4b5b6b7b8b9bac2c3c4c5c6c7c8c9cad2d3d4d5d6d7d8d9dae2e3e4e5e6e7e8e9eaf2f3f4f5f6f7f8f9faffdb00430001010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101ffdb00430101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101010101ffdd0004000fffda000c03010002110311003f00f05f4e0fb7b7d47a7a0fc0f7aff623fafebeeeff0079fe435afd7aeafbfe1f2bae5d6fa696899ebc1ffbe49fa67d7bff0023d051fd7f5b7f4b4beac35d35febcb6f4d7a2eb76c5ebff00ea23afbfd3f519ed402fbffaff0030e3f33e8debe9dba75f5e7bd030e39ebc7d79eff8fd074e9de8013d3dbfce719e3d79e7b633c51fd7f5dfe41fd7f5f30ec7dfebe9dbbb7e58c74ce0d1fd7f5dbe602f7cfb7e5df9e703f0dd9ebd06680febfaee27f439faf3e99e7ea76f3cf7cd01fd7f5d85f5f7f7e9ce3afafb01c1eb8c8a004c74f63e9c7e5d8e3d475e99c9a3fafebbfc8031d7dff3e9df9e476e377a7bd1fd7f5dbe6018e9c741fd3b7381cf1cedfc866800c7047bf1c1fd7b9fae31dce38a005c73f863dff039e98e78e7be3271401fffd0f05fa371819fd79f4e7bfebc62bfd883fc865fafe4f4feb4b7e325e7d467e9dbff00adfd680fbdf97ebbafc5adbae9cc7e381eb8e9fd3d473fce805f37e7dff1fc95bcfa073ebf518ebee3bfb71fce81fc9fe1a7fe4df9397a09e9f37e381cfb7a1e7f1f4ef407cbf2d7f1fcf97f51c88cee1132f239544551b9999b8555519259891800649c019e286d24db764b56dec9777b6debf702bb764aedb564badde896f77e49796ba1ef9e08fd94ff00697f89114373e06f809f173c49612e3cbd574ff007894e8a49f947fc4ee6d361d254e413f3de0e8cc4615997e5333e3be0bc9a4e19a715f0fe0aac77a15b35c17d657fdcb46acabbf943e6af63eab2de06e32ce2319e59c2d9fe3294adcb5e96558dfabbff00b989528d05f3a9f75bdef61ff876d7edcbe41b8ff866ff001e6c2a5f6e741f3f6e01c7d9bfb60dc86038d9e4b3e4f427017e77fe232f861cfc9feb96557bdaf6c5f276fe27d5bd9dbcf9bcf9ba9f41ff00107bc4ce5e6ff53f34b6f6be179b6bfc1f58536ffab2b729e3fe37fd94ff00697f86f1c975e39f809f177c356106449aaea1e00f128d15486c1035a874d9b496c703e4bd6c0607a3035f459671df05e73250caf8af87f1b565b50a39ae0beb2ffee5a55635d7ce1f376b1f3f99703719e4f194f33e16cff074a3bd7ab956356197fdccc694a86fda7e7adfddf016464668e4051d5caba38dac18310caca70430230548c82b8e306bead34d269dd3d535b35dd6fbfafde7cabba7669a69b4d3ba69ded6b68f7df4d3cae181fd474f9bbfafaf3fc3d3d39a03fab69f7efd7f4dafa06073fcf8e31cff005cf4fae38a05db5f9e9aff005bede97d431d3dbb71f4cf5e700febd33c501fd74f774f5ffe4beed4303a76f5e3e98f6e38e9f99a07fd5b4d7f1f97d9f3e8c31ffd71c719f5e79ce31fc5d38ee6817cfe7a77dbf4dbee67ffd1f05ebfc3f4efce7a7f507fc2bfd883fc865d7aeaff00e1ba7e2fe6adeeaf18fbb81e9c75fe5cf4ff00f58a035ef6fbbeefd76975db4e543c76193fe47a9ed9fc31c120d00bfa5a69f9b7f7ad1fc3d4500b10aabb89215428dcc58e30028fbc4e72029c9e830686edabd12d5b7d0767a2bb6efe577e5f0fe4a2fcf73f60bf637ff8242fc5df8ff69a5f8f7e2eddde7c19f8617ab15dd8c175a6adc78f7c5362ca8f1c9a668b772c09a069f751b8f2356d711a674fdf5ae8f756ee93bff3cf88bf482e1fe13a95f2ae1fa74f88b3ba4e54eace9d6e5cab0355369c6be269c64f17569b4f9a8619a8a7eecebd39a940fe80f0f7c02cff008aa14734cfea54e1ec92af2d4a50a9454b34c7527ade861a7282c2d2a89fbb88c52727f153c2d483523fa2ff00811fb097ecb7fb3b5ada7fc2bef851e1f9b5eb745dfe33f15dbc1e2bf185c4a31bee1359d612e1b4e790a82d1e8b06976a01da96cb18d8dfc7dc53e2971cf17cea7f6be7d8b8e166db59760272c065f18f483c3e1e5055924eca58975ea757393bf2ff005d70c7861c11c234e0b29c8b092c5412be638f8471f984a5d66b11888c9d16def1c32a34fa28a4944faed42a2aaa2aaaa00a8abb5554018daa060018e3002f4ce3a257e7edb6db6db6f76f56fcdeff009fde7df256d16896892e82fa8c7d4647a75ef8f5e437af39f95008caaea55d15d5c15656da5594e0152a77020f391f8739dead369a69b4d6cd68d79adbf3fb81abe8f54f469f53e45f8edfb09fecb5fb44daddff00c2c2f851e1f875db8560be32f0a5bc1e14f184131cecb87d6b478eddb517889ca45ad43a9dab0fddbdbba0615fa070b78a5c73c21387f6467d8b96160d5f2ec7ce58fcbe51d2f0587c4ca6a8a6b472c33a153aa9c5db9be0789fc30e08e2ea7359b645848e2a69db31c042380cc232e93788c3c62eb34f68e2556a7d1c5a6e27f3a1fb63ff00c121be2efc00b4d53c7bf086f2f7e32fc30b3596f2feded34e10f8ff00c296288f2492ea5a2da3cc9e21d3ed11099b56d0d12655265bad1ed208de7afec1f0ebe907c3fc59528655c414e9f0e67751aa74a752b73e538eaaec9468e26a283c2559b7eed0c53716f48626a4da81fc8be217805c41c2b4ebe699054a9c4392d3e6a95614e8a8e6b81a4b56eb61a0e6b174a093e6af86e4925ef4f0f4e09ce5f8fac1958ab02aca482a410411c10d9c60823046323be38afe864efaad53d535d4fe7fd55efa35a493e8faf44efebeb65a89f9fe5cfd31e9db27ebde817f5ebeba77edcbf8d83d7fcfe5ea71d87d3bd01f3ff80f6eddfbf36ba7907a75fcbafd7d3d7f4a07fd5bf5fbbcfcb53fffd2f051f5278fcc64fa7391d3ff00af5fec47f5fd7aee7f90dd3b6fff000faffc37cac2ff00c0b3efc71fd38eb40755fd7cff004d3bea182c5554162c70aa012c49e06d032c4e7000039dd8e78a1bb6af44b56df405e5aeba777fadfa77d0fe9b3fe0981ff04c0d2fc37a57877f68dfda33c3b06a5e2bd4a1b4d73e1c7c37d6ed61b8b0f0d585c44b71a7789fc51a75c24915d7886ea3962bad2749bc894682a22bbbb8bfb5fcb8f4dfe29f1bbc6eaf8daf8ce0fe0fc64a8e028caa61b39ce30d3942ae36ac65c95b0581ad069c309092953af5e9caf8b7cd4e9c961f9a588fecef057c15a183a184e2ee2ec246b63ab469e2727c9f134e33a583a538a9d2c6e368cf994b1734d54a187a917f55f76a548fd61c6143fa020300018c0e001d319e3d080476036fcbd9722bf934feab399f1a6bb2785fc1fe2bf13410c7773f877c33aeebb0dacb2148ae26d234bbad423b795d177ac733db8491d7e7556dc0070a1bb72dc2c71d98e030539ba71c66370b8594e293942388af0a4e714f46e2a7749e8dad4e3cc7152c165f8ec6462a72c260f158a8c24da8ce542854aaa2dad5293824dad527a1f217ec07fb58ebdfb62fc18d4fe28f88bc27a47836fac3c6dab785534ad16fef2fed24834eb0d2ef12eda7be8d261348da83ab4614a0f2d5812e48afd07c57e02c27877c494323c1e6188cca955cb30f8f75f134a951a8a55aad7a6e9a8d2725cb154534deaefd7551f80f0af8ef15e20f0ed6cef1780c3e5d5296655f02a861aad4ad070a34a854551caac62d4a4eab4d2d1597acbee2e3d7b7a8f4e9c0e31d38cf5e33960bf989fa59f0efedf9fb58ebdfb1d7c18d2fe28f877c27a478cafaff00c6da4f855f4ad6afef2c2d238352b1d52edeed67b08e498cb11d3d6358c858cac8f96c05ddfa77851c0584f113892be478ccc31196d2a5966231eabe1a953ad5252a35685354dc6ab51e56ab5dbbdd5b4b9f9a78a9c778af0fb8768e7784c061f31a95732a1817431356a5184635a957a8ea2952529392746c9356777b6f1faf7c17aecbe28f077853c4d3c31dacfe22f0d685aecd6b1485e2b79757d2ed6fe4b78ddd43b470bdc18d19fe664405f1906bf3eccb0b1c0e638fc1426ea47078dc561633924a538e1ebce929c92d139285da5a26f43eff002ec54b1b97e071928a84b1783c2e2a508b6e30957a14eab8a6f56a2e6d26f5696a74b8ce01e41e39c9041ec411f371d7ee83df80a2b88ec3f9feff00829f7fc13074bf13697e23fda33f674f0ec1a6f8af4d86eb5af891f0df44b586dec3c4b616f0b5c6a3e28f0c69f6eb0c56be22b68a292ef56d26d233fdbe0cb756b10d60489a97f58f823e3757c157c1f07f1863255f015a54f0b93671899ca75705567250a381c7569b729e0e726a9e1ebd46de11f2d3a9278671950fe55f1abc15a18da18be2ee10c2468e3e8c6a62737c9f0d4e30a58da508f355c6e0a941a50c5c22a53af429c2d8a57a94e1f584e35ff99421949560559490c0820820e0820e086078e46473d39afed74efaad53d535d4fe317d77f3deebf5ff0083f313fc8ff3dc67f4a03fafebcec1fe4fff005bd2817f5f8fe5fa1fffd3f05e7dbe9e873d7e87afaf1ea6bfd88febfaf4d8ff0021ff00e0ff005fd7cba0bcff00fabf4e7f9d02eabfad7fe1b5f9799fb07ff0486fd8ded3f680f8bd79f177c79a5adefc30f8357ba75dc1617912bd978a7c7d3acb75a269924722ba5dd86811c09adead07c8b24cfa35acde65bde5c257f3cfd20fc45a9c27c3f4f87f2aaee9677c454eb425569cad57039542d0c4d64d59d3ab8b72786a12bdd4562271e5942123fa07c02f0f21c579fd4cff0035a3ed325e1ea94aa469548de963b359a73c35069dd4e96154162b111b59ca587a72bc6a4cfebaf0070060018007403038ec0718c061edc00057f9fa7f7c1c5fc44f889e0af84fe0cd7be20fc44f10d87857c1de19b36bed6b5bd49dd6ded21f3638a3548e2592e6e6e6e2e248aded2d2d229ae6eee658a0b786499d236f4b28c9f32cfb32c2e539460eae3b31c6d4f6586c2d149cea4ace4db726a108423194ea54a8e34e9c22e5394629c8f3b36cdb2dc8b2ec566d9b62e96072fc153f6b89c4d66f929c6fca92514e739ce4d429d3a6a53a93928423293513f18be2c7fc16d7f65ed4fc35e37f07786bc15f18f5f7d6fc37e21f0fd86b69a2785f4dd2259b55d2eef4f82ed9351f15c1ab476cb25c248e24d2d67f281c45e6616bfa3f21fa3471c50c6e5998e3732e1dc22c3633078bab8678ac756c428d0af4eb4e9a747013a0e6d45c55ab4a37fb4aedc7f9db3dfa48f04d6c1e659760f2de21c53c4e0f1785a589586c0d0a12957a152942a355b1cabc609caef9a8c656da37691f037fc13f3fe0a77f0e3f63cf82baa7c2ff00167c36f1bf8b352bff001beafe2a4d4fc3b7ba15bd8a5b6a361a55a476ac9a95dc170678df4f777611f9656450a4104d7eade2d78259c7889c4b433cc06739665f46965987c03a38ca78a9d573a35b1151cd3a3070516ab2495dbbc5deda1f95f853e34e4fe1f70dd6c971f93e658fab5732c463956c255c3429a856a5420a16ad352725ec5b7656d746ddd1f74ffc3fabe08ffd10ef8adff835f08fe7ff00211fc78c7e1c86fcbffe255b89bfe8a7c8bff09f1fff00cacfd3bfe268f86bfe89acf3ff000a301ffcb343e16ff8281ffc14efe1c7ed87f0574bf85fe13f86fe37f096a5a7f8df48f153ea7e22bdd06e2c64b5d3ac355b492d5534dba9ee04f236a08e8cd1f96023e719515fa8784be09673e1df12d7cf31f9ce599851ab9662300a860e962a1554eb56c3d4551bad050714a8b4d5ef792b5f53f32f15bc69c9fc41e1ba39260327ccb015a9e6787c73ad8bab869d370a34ebd3704a8cdcf99bac9dedcb68eb6ba3ef9f84ff00f05b5fd97b4cf0d7823c1de25f057c63d01f44f0e787bc3f7fadbe89e18d4b488a5d2b4bb4d3e7bb55d3bc573eacf6ccf6ed2208f4b698c646620ff257e539f7d1a38e2be3733cc7059970ee2d627198cc5d2c32c563a8e21c6bd7a95a14dbad80850534a4a2ef5a31bfda764e5fa9645f491e09a183cb32fc665dc4385786c1e13095714f0d81ad878ce851a746534a963e55e5072836b968b9dadeedfdd3f673e1dfc44f057c58f06681f107e1df886c3c55e0ef13598bed1b5ad35e46b6bc87cc78a5568e658e7b6b9b7b88e4b6bbb4bb8e0bab3b88a582e604951917f9c337c9f32c8732c56539be0eae0731c154f6589c2d6494e9caca49a716e1384e328ce9d4a6e54ea424a5094a2d48fe8aca737cb73dcbb0b9b6518ba58ecbf1b4fdae1b1345be4a91bb8c93524a709c271942a539a8ce138b84e3192713b420104119078e77608fee9e09c1ee36e3b1249cd79a7a27f22bff00057afd8ded3e007c5db2f8bde03d296cfe187c65bdd46eee2c2d2144b2f0a78fe111ddeb5a6c71c6a8969a7ebe93beb9a4c0032a4ebad5ac3e5c1670c75fe817d1f3c45a9c59c3f5787f35aeeae77c394e8c2156a4af571d94caf4f0d59b7773ab8471586c44af771786a8ef2a933f81fc7df0f21c2b9fd3cff2ba2a964bc4352b4e54a9c52a581cd636a989a2a31b2852c5293c4d08daca4b134e368422a3f8f9ebc7f2e7dbf2e39afe863f9fbfafc7f2fd03d38fe5f2ff009e9c503febfaf3b1ffd4f05f4ebd7faf4ee38e47bf1d320d7fb12ffcbf2fd773fc87feaffa7e63d559d8222177760a8aa3733b370155464966c84000c9278ce6936926dbb25ab6f64bbbdb6f5fb8126ec92d5bb256bbd76492dddffc91fdd87ec27f026d7f676fd96be14fc3d36896daecde1fb7f15f8cdd57135c78bfc570c7aceb2970fb54cada69b887458998605ae97046a4246bbffcb8f14b8a67c5fc739f66fed1cf0b1c5cf0196abfbb1cbf012961b0ce0aed4556509626497fcbcaf26eee4dcbfd3af0c38629f08f0464594f22862a584863f317f6a59863a2b1188537a393a2e71c345b5a53a304ac923ebcf5f5e73e8071d3d0f439f9bd48ce16bf3e3ef8fc40ff0082ea6b1a95bfecf5f08bc3b6d7735b699e24f8cd6ababc51315fb647a678675d9ad2198749228aeae05d2a382a2782090e5906dfe9afa2ee1e8cf8b7883193a719d7c170e547879495fd9cabe3b0b1a928ea9a94a10f66da69b84a71babdcfe6bfa4e622b4384b21c242728d1c6710c3eb118bb7b48d1c162a508c96ce319cd54b37a4a106949a5cbfa69e3787e157ecbdfb3f78b358d0b42f08781fc29f0cfc05abdde8f0369f6169a7c775a4e8f70748b69048b136a17fa85fc76f0ed7964bed4ef27da5e6b89b2df8ae592cf78e38b72fc3e2b1598e678fcef35c3d3c4cd56ad52b4a15f110fac4d5aea952a549ce574a34a85385f48451fb36651c8f8278531f88c2e1b2fcb30192e57889e1a0e952a7454e861e5f5783ba87b5ab5aac611d5ba95aa4ed77395cf9f7fe09bbf1abe22fed35fb39a7c59f8c1a2f85d358d53c69e25d2b439742f0dc3a3585de83a2b5a5889d2dcb5c09993564d52d64b85976992d648880f138afadf19386f27e09e3079070f6271ef0f432dc157c52c5636789ab0c5627da54e573518f2a741d0a8a0d6919a926d491f29e0ff11e71c67c22b3de20c3e09622b6658ca1869617071c352a985c33a74b99439a7ccd5755a9b9a767284a3f64fbf3fb2348ff00a05587fe01c1ff00c679fc47e3dabf28face23fe7fd6ff00c1b50fd53d850ff9f34bff0005c4f817fe0a43f1a7e22feccbfb394df167e0fe8be177d674bf1a786b4bd6e5d77c3916b36169a0eb4f7762d7125b06b71133eaafa5da473b4bb44b751c4007996bf57f06f86f27e35e30590710e271cb0f5f2dc6d7c2c70b8d9e1ab54c5e1953aaa119b8cb9ad4157a92872b6e34dcb451933f2cf18388f38e0ce10967dc3f87c13c450ccb0543132c560d626953c2e25d4a4e6e1cd0e56ebba14d4dbb27512fb48f7ff0345f0abf6a2fd9fbc25ac6bba0f843c71e13f89be01d22ef59b71a7d85de9f2ddeada3c0757b68d51656d3afb4fbf96e61da92477da5de41b43c5730064f92cd259ef03f1666186c2e2b31cb31f9266b88a7879bad5a9d550a1889fd5e6dbb2ab4aad25095ed2a55e9cefac267d565ab23e36e14c06231586c066580ceb2ba15311054a94e939d7c3c3eb105653746ad1aae71d1aab46a46d7538dcfccbff8215eb1a8dc7ecf7f177c3d737735ce9be1bf8cd74ba3c52bee1651ea5e17d0a6bc8a218d91c735cdb9b928a31f689ee25e5a42abfb5fd28b0f461c5bc3d8c8538c2be338721f589455bda3a18ec5469ca5ab6e5184f92edb7c9182bbb5cfc67e8c788ad3e12cfb093a929d1c1f10cfeaf193bfb38d6c161655231daca538f3b497c7293d2e7ee07afcbf51dcfb8ee467df1e9924d7f321fd287c87fb767c09b5fda27f65af8adf0f4da25cebd1787ae7c57e0c90806587c5fe148a5d63454b77033149a8bdbcda2ca541cdaea73c6d98d9ebf41f0b78a67c21c7390e6fcee185962e180cc55fdd965f8f6b0d89735a292a2a71c4c537f1d083567147c0f89fc314f8bb8233dca7914f151c24f1f973fb51cc3031788c3a83d5c5d670961a4d2d69d69a774d9fc2732ba33a3a947425591be56899721959480559482083c83c6060d7fa8e9a6934ee9ea9ad9aeeb7dfd7ef3fcc56ad74d34d3b34ef74efb3d9e8fcbee1bcf1d3e991f37bf4e78ff77f218a00ffd5f06f4efeff008fb7af07d3839e0d7fb107f90ff8eda7ebfd76d3a9efbfb2a78221f891fb4c7c04f035d47e7587897e2ef8034fd562c67768c7c4ba74dad600c64ae9515e49d474f98aa826be538ef33964dc17c579a41f2d5c170fe6b5a83ffa89fa9558e1b7b6f5e54d7f9ec7d4f0365b1ce38cb8572c9c79e96333fcaa9575df0ff5ba52c4bd3fea1e353b7aa5767f7e015500440115142a800055555002803a05007b018f45aff28db6db6dddb776df56f767fa9e95b45a25a24ba1f9a1fb507fc13d3c47fb467c57bff89ba67ed53f16be135a5f68fa2e92be0ef080befec6b67d22d7eccf7d1fd9fc57a447e7df31f36e36d9ab6fc02eec4b2fed1c11e2e60f83f21a59256e05c833e9d2c4626bbcc731f65f599ac44f9d527cf80c43e4a4bdd87ef5e9d237b1f8d71af84f8ce2ecf6ae75478e33ec8a9d5c3e1a87f67e5eaafd5a0f0f0e475572e3b0eb9eafc53fddad7772d19f9a7ff0544f815a8fecf5fb1f7ece5f0a751f897e27f8a97567f1cbc53a83f8d7c5a26fedcb95d6743d4eea2b690cfa9ead3795a6aced05b66f597628c2478db5fb4781fc51478b7c43e31cfa8e4b81c8a153863034965b97f2fd561f56c4d0a729c792861e3cd59c14e7fbbddeefed7e37e3670cd6e13f0fb84323ad9d6373b9d3e26c75679963f9beb33fac61ab4e3097356c44b968a97243f79b6c95bdefa92dbfe08afe19d7352d3e4f8b1fb547c6df89de1fb3b98ee5fc3d7ecb66b7451b2d19bfd4f58f133db2cabb91e5b5b586e02bb18e7898efaf869fd24b1b85a155643c0dc339262ea41c162e9275392eb47eca8e1706a6e2f58c6752506d6b16958fb787d1cf078aad49e7bc6fc499d6129cd4de12ada9a9b4eed3ab5b158c7052574dc29c27ae9522d5cfd88f007807c23f0b7c17e1ef87be02d16d3c37e0ff0009e9b0e91a168d6424f22c6ca1dcdb7cc99e59ee2e2695e5b8bbbbb89a6bbbcbb9a6bab99a5b89a476fe78cdb35cc33cccb199b66b8aa98ccc31f5a55f1589abcbcd56a4b4bf2c546108c629429d384630a708c6108c6118c4fe82cab2ac06499760f29caf0d4f0797e028c70f85c353bb8d2a71d6dcd26e5394a4e53a9526e53a952529ce529c9c8ec79c8e7b70307f33ff00d7fe75e71e81c778ff00c03e11f8a3e0bf11fc3ef1ee8b69e24f07f8b34e9b49d7b46be127917d67310db4490b473c13c32a457169756b2c37367750c3756d3453c28ebe86539ae6191e6383cdb2ac554c1e6180ad1c4617134b979a9548df5e59294270945b854a738ca1529ca509c65094a279f9ae5780cef2ec66539a61a9e332fc7d1961f1586ab7e4ab4e5676bc5a94251928ce9d4838ce9ce319c2519c548fc77b9ff822bf86743d4f507f84ff00b547c6df863e1fbcb992e53c3f60eb78b6a5db2b18bfd3758f0d1b958976a249776b3dcb2a299667602bfa221f492c6e2a85259f70370ce778ba705078baa9d373496afd956c2e3141c9ddb50a918768a5a1fcfb3fa39e0b0b5aabc878df89325c2d49b9ac2d2b5450bbd17b5a38ac1b9f2ab2529c253d359b7ef4be5bff00825c7c0bbffda0bf63efda47e1569df127c4ff000aeef50f8dfe16bc4f1a784fcffeddb65d1744d32f1e08bc8d4f4a97cbd4162305cedbe405246cac84ec6fb9f1c78a297097885c199ed6c9b059e53a5c358fa6f2dc7f2fd566f1389ad4d4e5cd47111e6a2e5cf0fdd3d63a38eacf88f04f866af16787fc5f9251ce31b91d4abc4980aab32c0737d661f56c351a8e11e5ad87972d651e49fef568f552d11fa57fb307fc13d3c47fb39fc58b0f89ba97ed57f16be2c5ad8e8dad694de0ff00178bff00ec5b87d5ed45b25ecbf68f15ead189ec08f36df364cde674911896afc5f8dfc5cc1f186435724a3c0b906433ab88c357598e5decbeb305879f3ba4b9301877c9557bb3fdead3a4ad63f64e0af09f19c239ed2ceab71c67d9ec2961f1341e5f982abf569bc44391547cf8fc42e7a5f143f77bf55f67f4bd955d4a380c8ca5590f2195861811ce4107040182091cae6bf174da69a7669dd35d1ad99fb2b57d1ea9e8d3ea7f021fb5678221f86ffb4c7c7cf035ac461b0f0d7c5cf1f69fa547823fe24c3c4ba8cda2e41ce37695359b753c1e0b260d7fab9c0999cb39e0be14cd26f9aae3787f2aad5dff00d44fd4a94713b5f6af1a8bfcb63fcb1e39cb6393f19714e5908f2d2c1e7f9ad2a11ed86faed5961fefa12a6fadbbbdcf01fcfebcfcbfe3ebf37e3dabeacf953fffd6f05f4e31cff5ebebfd3e6e78cd7fb12ffcbf2fd363fc87fc3fadbf43ee0ff826df93ff000dcbfb37fda0a797ff0009eb6ddf8004ff00d85acfd9bae46e173e484e321c03c922bf31f1979ffe218719725eff00d94af6fe4fad61fda7cb939afe573f49f07b97fe226707f3356fed5d9dbe2faa627952f3e6e56baf6e87f71e7a9e33edc7a75f5fcff00720aff9887fa5e27ff5f9fef74e07afd06df407ab5007e14ffc176ffe48f7c01e3fe6b0ddfcbe9ff14c5eff00fafaf4e98c57f51fd16ffe4a1e2cff00b27a9ffea7533f98be93dff24ff0ae97ff0085fa9a7fdc9543f75fd3e41efc0fd39fc327eb8ed5fcb87f4e87afc83dba7ebffd61f9e6800ff800c77e179fd78ffc7bd3de800e7fb8339f41c0fcf93ff7cfa7bd001ff0018edf77fcfe9f962803f09ffe0849ff00247be3ff007ff8bc369cf391ff0014bd9703a93cf1c7d383935fd47f4a4ff928784ffec9ea9ffa9d50fe62fa30ff00c93dc53ff63fa5ff00a8348fdd8e79e067d3b1f7f4049f7cf1db15fcb87f4e87a7e879cfd0f1923bf3b477e831401fc387fc1497c8ff0086e5fda47ece5447ff0009e2efd9d167fec0d1bed58c606e173e76fefbf7139eb5fe9df835cfff0010c38379ef7feca76bff0027d6b11ecfe5c9cb6f2b1fe68f8c3cbff11338c396cd7f6a2dbf9bead86e7f9a9defe773e1fe78e7e839e7ebe9e9cfd7a9afd34fcd7fabff005dbf53ffd7f05f4e73edc7afe07a7e236f7c115fec47f5fd7aee7f90ff00d7f5f89eff00fb2a78de2f86ff00b4c7c03f1cdcc861b1f0d7c5df006a1aacb9618d1478974e875a00af23769335e2f75cb7208c8af94e3bcb259cf05f15e5705cd571bc3f9ad1a0bfea27ea556586defb578d37fe5b9f53c0d994727e32e15cce6f96960f3fcaaad7976c37d7294712fba6e84aa2ebbea9dda3fbef0cae03ab028c032ba9c86565c82a7a6082082339ce47560bfe51b4d369ab34ecd3e8d6e8ff0053d3beab54f54d753f343f6a0ff8289ea1fb377c58d43e185bfecc9f16be28c563a368bab0f15f841663a2dc36b16bf68367114d03505f3ec48f26e3fd2586e38db1815fb470478414b8cb21a59dcf8d720c8dd5c4e270ff0050cc1afacc3eaf3e4f692be2a97bb52fcd0f736eacfc6b8d7c5cabc1f9ed5c923c199f676a961f0d5febf97f37d5a7f58873fb356c2d55cf4fe19fbfbf4dcfcd3ff82a27c78b8fda27f63dfd9cbe2ccff0efc51f0ce4bdf8e5e29d3dbc21e2dde35cb7fec6d1353b549e50f63a7b797a82c065b622d57f76cbcb654d7ed1e07f0b43843c42e31c8239c6073a54b863035566197ffbacfeb389a351c62d55aab9a8f3f2cfdfdefa2b33f1bf1b38a25c5de1f708e7b2ca31d933abc4d8ea2f2fc7dfeb30786c356a6a4ef4a8be5adcbcf0f712e57bb5694bea3b5ff82d3f85f44d474e4f8aff00b2dfc6ef863e1ebdb94b77f10df2477b1daef3832fd8752d17c34d74b180cef1da5ccd72554f9504cc315f0d3fa36e3b1346b3c878e78673ac5d2839ac2526e9b9db68fb5a38ac62a6e4eca32a908c2ef56ad73ede1f48cc161ab52fedde08e24c9709567183c5d5b5450beefd9d6c2e0dcd4526dc69ce536969076e597ec4f807c7be0ff8a3e0cf0f7c41f016bd6be23f07f8b34d8757d0b5ab1de20bfb29b72e7cb9a38ae2de7825496daeed2e618ae6ceea19ed6e628a786444fe77cdb2acc323cc71994e6b85a983cc3015a587c561aaf2f352a91b69cd17284e128b53a75212942a539467094a128c8fe82cab34c06779760f36caf134f1997e3e8c71185c4d2bf255a72babda494a128c94a1529cd4674e71942718ce2e2761c647cc7a7fb381fd33db8af3cf40e3fc7de3df07fc2ff06788fe20f8f75db5f0e783fc27a6cfabebdacdf6e30595940557798e18e5b8b89e695e2b7b4b4b78a5babcba960b5b68a69e68e27f4329cab30cf331c1e5395616a63330c7d68e1f0b86a5cbcd56a4afa7349c61084629cea549ca30a74e329ce51846523cfcd734c06499763336cd3134f0797e028cb118ac4d5bf252a71b2bda29ca72949c614e9c14a752728c21194e4a27e3bdd7fc16a3c2fadea3a8afc28fd96fe37fc4dd02cae1ed93c436291d94773b0e04a2cb4dd17c48f6ab2295748eeae21b80180921473b6bfa221f46dc761a8d179f71cf0ce4b8bab053784aadd470bef1f6b5b1583551c5dd4a54e1285d68ddee7f3ecfe91982c4d6aab22e09e24ceb094a7282c5d2b5353b3d25ece8e1718e0a4acd46a4e3349ae6826f963f2dff00c12e3e3bcffb3cfec7dfb48fc5783e1e789fe25cda7fc6ff000b59af837c261ceb97435bd134cb333c612cef98c76026f3ae7fd188f2d1b25546eafb9f1c785a3c5de21706e432cdf0592c6af0ce3ea7f68e3edf5587d57135aa72caf5696b5797961efeef67a1f11e09713cb84bc3fe2fcf2394e3739952e24c052fecfc05feb33face1a8d3738da955f76973734fdcd93d51fa57fb307fc144efff00691f8b161f0c2e3f664f8b7f0ba2bed1f5bd58f8b7c5eb30d1ad9b47b51722ca42fa0e9ea27beff536ff00e9232fd9f815f8bf1bf8414b83721ab9dc38d720cf1d2c4e1b0ff50cbdafaccfeb13e4f691b62aafbb4edcd3f736ea8fd9382bc5babc619ed2c967c199f648aae1f135febf985fead0fabc39fd9bbe1697bf53e187bfbf467e97b32a02ce42aaa9672d80a1472ce49200c01cb31e02e4e0e057e2e936d249b6dd925bb7d96fabf4fbcfd95bb6af44b56df43f810fdaafc6f17c48fda63e3e78e6da4f3ec7c4bf173c7da8693292496d18f897518745dcc793b74a86cc7451f2f00676affab9c09964b26e0be14cae6b96ae0b87f2aa35d7fd44fd4a94b13b5b7af2a8ff00cf73fcb0e39cca39c719714e67097352c667f9ad5a12ef87faed58e197fe088d38f4f24b44780ffc07ebc7f2e78c75e3775c0ee6beacf96feadfd77fd0ffd0f06fa8effd7afa73c1f5e4e3906bfd88febfaf4d8ff21ffafebfaee39599195d1d91d1832329219194e5595861810c01041e31c6314349a69aba7a34f66bb3df7f4fbc13b34eed34ee9abee974ebe6add57a9fdd87ec27f1dad7f68afd96be147c42174975af45e1fb6f0a78ca30dfbc83c5fe14863d1f597b842498df526b78b5a8938dd6ba95bc883cb65aff002e3c52e169f0871ce7d94723861658b9e3f2e76f76597e3dbc4e1941e8a4a8a9cb0d2697c74269d9c59fe9d7861c4f4f8bb82322cdb9d4f151c2430198afb51cc3031587c439ad5c5d6708e2629bd69d683574d1f5dff2f5ee78e87d011cf3c719fbb5f9f1f7c7e20ffc175747d4ae3f67bf845e22b6b49ae34df0dfc65b56d625850b8b28f52f0c6b90da4d29fba91cb736e2d95d8a8373710479cbad7f4d7d17711461c5bc41839d48c2be37872a2c3c64eded25431d859548c746dca309fb46926d4233959dac7f35fd2730f5a7c279062e1094a8e0f88a1f5894637f671ad82c5469ca5aa494a70f669bd1ce7157573f4d3c6f37c2bfda8ff67ef1668fa16b9e0ef1c784fe26f80b57b5d1ee1b51d3aeec24bad5b47b81a45c48599db4ebfd3efe4b79f73c71df6997906e2b0dc406bf15cb239ef03f16e5f88c561731cb31f9266b87a98982a35a9d68c286221f5882b5955a556929c6c9ca957a73b6b0923f66cca591f1b70a63f0f85c4e5f996033acab114f0f3f6b4aa5273c461e7f579b7793a556955942576955a35217b73c6d1f9fff00e09bbf05be23feccbfb39a7c26f8bfad7851f58d2fc69e25d53438b43f12c1acd85a683ad35a5f2c11dc14b71133eacfaa5dc96eb1ed596e649496699abeb7c64e24c9f8d78c1e7fc3d86c7ac3d7cb7054314f15829e1aacf158655297338734f992a0a85353beaa0a3b451f29e0ff000de71c19c22b21e20c4609e2286638caf858e1b191c4d2861712e9d45153b46cdd775ea4a16d2536f5e66e5f7dff006be97ff414d378edf6eb6e7df3e671f4c7e75f947d5b11ff003e2b7fe0aa87ea9ede87fcfea5ff0083227c0bff000521f82df11bf69afd9ca5f84df0835af0aa6b3aa78d3c35aa6b716b9e25b7d1ac2ef41d15eeefdade4b8d9722529aac7a5ddc76ed190d2db24a183c486bf57f06f89327e0ae3059ff0010e1b1cf0f432dc6d0c2cb0b829e26b53c5e2553a4a718394796f41d7a729f326a351c7552923f2cf18387338e33e109645c3f88c12c457ccb055f131c5632387a5530b86752a3839f2cf99aaea85450b59ca9a7ba47bff81a5f859fb2e7ecfde12d1b5dd77c1de07f09fc32f0169169ac5c0d4b4fb4d3e3bad2b48806af728cbe5bea37da85fc77136e48defb53bb9f7049ae67cd7c96691cf78e38b330c4e170b98e678fcef35c454c3c1d1ad3aae15f113fabc1deea8d2a549c2166e34a8538db48459f5596bc8f8278530187c56272fcb70192e5787a7889fb5a50a4a7430f1fac4d5b95d6ab56aa9cb48caad6a92bd9ce5697e657fc10ab47d46dff0067af8bde22b8b49adb4cf127c66ba6d2669936fdb63d37c2fa143772c273b648a2b9b836e640cca2782e23e7cb6ddfb5fd28b11467c5bc3d838548cebe0f8721f588c5dfd9baf8ec54a9c65a26a528439ecd27c9283b2bd8fc67e8c787ad0e13cfb173a72851c6710cfeaf292b7b454705858d4947a3519cdc1b5a73c64ba1fb81dcf2338e0f6c7bfbff0017031eb90457f321fd287c87fb76fc76b5fd9dbf65af8adf108ddc76dae4be1fb9f0a7835090269bc61e2b8a5d1f466b74ce647d35ae26d6a55f98adae9b70eff2a3357e83e1770b4f8bf8e721ca391cf0b1c5c31f98bb7bb1cbf0128e2712a6ecf9556508e1a2dad6a5682d1b47c0f89fc4f4f8478233dcdb9d43152c24f01972bfbd2cc31f1787c3b82d1c9d1739626493d29d09b76499fc26b333b3bbbb3bb92d2331dcd2331cb12c72c58b1c124e49cf524d7fa8e924924ac96892d92ecb6dbd3ee3fcc56dbbb7ab6eedb7bebbb7aeaf7df7ee27a73f43f2f1ec7d73d38f4fad007fffd1f05fa1f4e3d39f6f43c1fa60f5aff620ff0021fbfe7fd76fd7d40f6e3ffafc1eb9f6e39ef407e5dbb1fb09ff000485fdb22d3e007c5ebcf843e3dd516cbe187c65bed3ad20bebb9112c7c2be3e8564b5d175292492444b5d3fc4093c7a1eaf31f91264d1eea5f2eded2792bf9e7e905e1d54e2ce1fa7c4195507573be1ca75a72a54e37ab8ecaa4e353134525773ab8471789c3c6d771788a71bcaa40fe80f00bc42870ae7f5320cd6baa792f10d4a34e356a492a581cd637861abc9c9a54e962d4d61abcb652586a92b421271febac72320839c1c8c6d03d4727a9f9b39e833c9c0aff3f4fef838bf889f0ebc15f163c19af7c3df887e1eb0f14f83bc4d646c359d0f524736f790ef496271243245716d736d3c71dd5a5ddacb05d5a5cc715c5b4d1cc8ae9e96519c66590e6585cdb28c655c0e6382a9ed70d8aa2d29d3959c5a6a49c2709c652854a7514a9d4849c671945b89e766d9465b9ee5d8aca737c252c765f8ca7ecb1386ac9b8548dd4934e2e3384e128c674ea5394674e718ce0d4a299f8c3f167fe0895fb2f69be1af1c78c7c33e36f8c5a049a27873c45e20b1d0d35cf0bea7a4432e97a55d6a305a17d47c2536af25bb496e91397d51a6f2c9fdf17c3aff0047e41f497e38ad8dcb32ec6e59c3b8b589c6613095712f0b8ea188946bd7a546535ec71eb0ea6949c95b0f18df4699fceb9efd1bb82a8e0f33cc30599710e15e1b078bc552c37d67055e846542854ab18375702ebb837049deb4a56fb57499f02ffc13effe0989f0dff6c3f82baa7c50f167c4af1bf84b53b0f1c6b1e168f4cf0ed968171632db69d63a55dc774efa95a4f3fda247d41d5d55c205442b862f5fab78b5e36e73e1df12d0c8f01936599850ab9661f1eeb632ae261554eb56c4539412a3351e44a8a69b49de4d6a7e5be14f82f93f883c395b3bc7e7199602b52ccb1181547094b0d3a4e146950a8a6dd6839733759a693b5a29e973eeaff870a7c10e7fe2f9fc55f73fd99e10e0e7a7fc8373d7d4631d30335f97ff00c4d4f137fd131917fe1463ff00f961fa6ffc4aef0d7fd14b9e7fe13e03ff00959f0b7fc140ff00e0987f0e3f63cf82ba57c50f09fc49f1bf8b752bff001be91e167d33c4565a0dbd8c76ba8d86ab76f76afa65a5bcff00688db4f4445f33cb2aefb9785dbfa8f84be36e73e22712d7c8f1f9365997d1a5966231eab60eae26755ce8d6c3d3506ab4dc795aacdb693778a5a1f9978ade0be4fe1f70e51cef019c6658fad5732a181747174b0d0a4a15a957a8e69d1829392749249b4b57b9f7cfc26ff8224fecbba9786bc0fe31f1378dbe317881f5bf0e787bc417fa23eb9e16d3348966d574ab4d427b30fa7784e1d592d95ee1a252baa24fe5aae6567cbafe519ffd25f8e28e3733cbb059670ee1161b198bc252c4ac2e3abe2231a15ead18cdfb6c7bc3b9b515277c3ca37d1247ea5917d1bb82ab60f2ccc31b99710e29e270784c555c37d67054284a55e853ab28274b02aba8273695ab4656fb576d9fb3bf0efe1df82be13f83341f87bf0efc3ba7f857c1de19b3fb0e8ba1e9aac2ded213234d2bb492b49737373737124b3ddde5c4f3dd5e5ccb2dc5c4d24eeeedfce19be719967d9962b36cdf19571d98e36a7b5c4e2ab34e75256514928a50842118c614e9d351a74e1151846314a27f456539465b9165d85ca728c252c0e5f82a7ecb0d86a29f2538ddca4db93739ce7394a752a4dca739c9ce72949b91da138049c6072492b8207a9c63d793b4f71d18579a7a27f22bff00057afdb22d3e3ffc5db4f843e02d516f3e18fc19bdd46d27bfb4951ac7c55e3e9c25aeb5a947244f225d69fe1f8e09344d267f951e77d66ea1df6d7703b7fa05f47cf0eaa709f0fd4e20cd683a79df11d3a352146a26aa60b298b7530d45a693855c5ca4b135e3ba8ac3d3769c26a3fc0fe3f78854f8ab3fa7906575bda64bc3b56ad39d5a72bd2c766b2b53c4d68b8dd4e961145e1a84af6727889af727172fc7ae78e47e9c71f974e783c74390457f437f57febbfe87f3f87383c8fd3fc3b703e6f5c13c034fe5fd5b7fd43b6ffd77ff00827fffd2f06fd7a7e3fd39181f89cf02bfd883fc87f9fcbb7fc3ff005b087ebf53c71c76cffdf5f873fc340bfabf7febeed7d0012acacb90cac0a9070ca4721b2304329f981cf0587439a1abe8f54f469f50bdb55756d7d3d3a69f77e27f4d9ff04c0ff829fe95e25d2bc3dfb39fed19e21874df1569b0dae89f0dfe23eb7750dbd8789ac6de14b7d3bc2fe28d46e1e28adbc436d1451d9e95ab5dcac35f062b4bb986b0229352fe29f1bbc11af82af8ce30e0fc1cebe02bcea62739c9f0d094eae0aace5cf5b1d81a304e53c24e52752bd0a69bc23e6a94e2f0ce51a1fd9de0af8d543194309c21c5d8b8d1c7518d3c364f9c626a4614b194a1150a582c6d69f2a8e2e09469d0c4547fed578d3a92588519623fa02041e4739192c31861e8b8cf5e00c9edc64d7f269fd56737e34d0a5f14f83fc57e1982e12d66f11786b5ed0a0ba9559e3b79757d2aeb4f8e79a35c3bc70bdc095d50e591481824576e5b8a8e0731c063670752383c6e17152845a529c70f5e155c22de89c942c9bd137a9c798e1658dcbf1d838c94258bc1e2b0b19c937184abd0a94949a5ab51734da5ab4b43e42fd80ff64ed7ff0063af831aa7c2ef1178bb47f195f5ff008db57f150d5745b0bcd3ad628352b2d2ed12d4c37cef2b4d135833b383b48954001812dfa0f8afc7b85f113892867983cbf119752a59661f00e8626ad3ad373a356bd47514a9251e592ac9256bae5d6e7c07857c098af0fb876b6498bc7e1f31ab5732af8e55f0d4aa5182856a5429aa6e355ca4e51749b6d3b3bf4da3f71f39f7c71ee3dfb8207af193c77afcc4fd2cf873f6fcfd93b5ff00db17e0c697f0bfc3be2ed23c1b7b61e36d23c54daaeb5637ba85ac90e9d63aa5a3d9ac160e9289646d411924cb2288dc153b857e9de1471ee17c3be24af9e6332fc46634aae5988c02a186ab4e8d453ad56854551caaa947962a8b4d6ef9b4bef1fcd3c54e04c57883c3b4724c263f0f9755a59950c73af89a552b41c28d2af4dd351a528c94a4eaa69bd159fa4bebdf066853785bc1fe14f0ccf711ddcfe1df0ce85a14f7312b243732691a55ae9f25c448f96449dedcc88921dca87073835f9f6658a8e3b31c7e361074e38cc6e2b151849a72847115e755424d68dc54ecdad1b5a1f7f97616582cbf03839494e584c1e170b29c53519ca850a749c927aa52706d27aa4f53a42400493c004e49ea00eff875c6de78272035711d87f3fdff00053fff00829fe97e19d2fc45fb3a7ece9e228752f166a50dd689f11fe23e89770dcd87866c2e6136fa8f863c31a8dbf9b15d788aea2964b5d5755b5907fc23fb66b5b498eb25df4ffeb1f047c11af8daf83e30e30c1ca8e028ca9e2726c9f130942ae36ac25cf471b8ea3349c3070946352850a91be2df2d4a915864a388fe55f1abc6aa183a18be11e11c646b63ab46786ce338c34e33a582a538f2d5c160aac14e353175232953af5e9ced845cd4e9c9e25ca543f99224b33336e66639625892cc492589e5892dce73ce49193babfb5d24ac968969a2dbd3e5fe5d0fe316dbd5bbb6eedbebff0007cfe627a71f8e7f4f4ebe9db91f2e6800edd0fe7d38effa839e3a7f09147f5fd7cc3fafebb1ffd3f05fd3a73d7d7d71d393f91e718aff00620ff21fe5ff0007fadbfa407b76f41f877ffd07f1f71407f4d7f5dff4f313f1fe21f89ff0edff0001f5a05d37f9edf3eabf0fbc504a9dcae5594a90c0e082324152390c3eff00cbd0f4a1abe8f54f469f51ecd6b6feb7efa793fbf43f60ff00637ff82bdfc5efd9fed34af01fc5db4bdf8cbf0c2c963b3b09ef35210f8fbc2b648891a47a6eb7751ca9afd85a468041a4eb8c26446f26d759b3b748e1afe79f117e8fbc3fc5952be6bc3f529f0ee7755caa55853a3cd9563aab6db957c353945e12ad46df357c32716fde9d0a937299fd01e1ef8fb9ff000ad3a195e7f0a9c4392d3e5a74a552b28e6981a4bdd51a389a8a6b174a09251c3e25c64be08626108c632fe8c3e04feddbfb2d7ed156b6bff0afbe2bf87e2d7ae50197c19e2bb98fc29e2f865c2efb74d175896ddb52923276b4da2cfa95ab0f9e3b864c1afe3ee29f0b78e7842753fb5f21c5cb0b06d2cc7010963f2f947a4de230f19aa29a5751c4aa153a3845df9bfaeb863c4fe08e2ea70794e7b848e2a695f2ec7ce380cc232eb0587c44a2eb34f79619d6a7d549a6a47d76acaeaae8c1d586e4756ca9538c1dc3390dc631c11dce4357e7ed34da69a6b74f46bc9edf97dc7df277d56a9ea9aea2faf5f71ce739ea3d0679e0fb718a402332a297765455192ec7081402496270381924b141df2314d26da4936dec96adf92dff002fbc1bb6af44b56df43e44f8edfb76fecb3fb3b5a5dffc2c2f8afe1f975cb64629e0cf0a5d47e2bf18cd300765bb68da3cb70fa6b48410b2eb53e9b683efc97289cafe81c2de16f1cf17ce1fd9190e2e3859b57cc71f096032f8c74bcd62313182ac92d5c70cabd4e8a1276e5f81e27f13f82384a9cde6d9ee1258a8276cbb0338e3f3094ba41e1f0ee4e8b93d14b13ec69f5724939c7f9d0fdb23fe0af5f177e3fda6a9e02f843697bf067e18deacd697f716ba8f9fe3ef15593ab44f1ea5ad5ac7047e1fd3ee91cf9fa5688ef3347886eb59bcb691e0afec1f0ebe8f9c3fc2752866bc41529f11e774daa94a1528f265581aaacd4a8e1aa4a6f17560d7bb5f1518c53d618784d299fc8be2178fd9ff001553af95e414eaf0f64b539a9d59d3aca59ae3a935671ad89a6a0b094aa26d4a8619ca4d7bb3c44a0e48fc7b2ccc773312ccc492c589662dc924e4939ce73d73824fdeafe86b5b4564969656b2f4e9f774f99fcfedddddeadddddff57d7fe1c4f5e4febf97e5c70724fa30a7dbfafbff00e087cbafebbfea1e9c9fd7d3e9ce47aede4647208a41fd5ffaedfa8763f37f3f4fa71d88c6e1d4f42453febf0fcff50eda7fc0feb6d0ffd4f06ffeb71d3bf1d7d781cfa64726bfd883fc87effd5bfadffa427f9cfaf07a63db27d33d39cd02eda7fc0feb6f9fa87e9cfe5c0e3d324607fc08e3bd01df4ff83a796be5dfb073cf1f87f8fa8278fa0a07fd7f5d3fad3a873c74e9d7d78ede993cfd16817f9f9f7fbff4f90e56746574628c84323292ad13ae0ab0618c32919041c8ea31834349a69aba7a34f66bb3df7f4fbca4da69a6d34ee9add3eeb6d57afdc7be7823f6abfda67e1b4315af81be3e7c5cf0d5843f734ad3fc7de261a370d907fb166d4e7d28e3247cf66c3e6233b58d7ca667c09c179cc9cf34e14e1fc6d596f5eb65582facbffb998d28d75f29fc9dae7d4e5bc73c6593c630cb38a73fc1d28fc342966b8d5865ff0072d2ab2a0fe70f2d6feefb0ffc3c97f6e6f23ecfff000d23e3bf2f6ec0fb341f3c0fbbff001f5fd8ff00692d8fe312abe792d9e6be77fe20d7861cfcff00ea6e557bded7c5f277fe1fd67d9dbcb97cb97a1f41ff001187c4ce5e5ff5c334b6d7b6179bff0003fabf3dfcff001ea78f78dff6abfda63e24c525b78e7e3e7c5cf12d84d932e93a878fbc4c74662c72c7fb161d4a1d297240e964bc281c6d55afa2cb381382f26929e57c29c3f82ab1dabd1cab05f595ff007332a52aefe73f92bdcf9fccb8e78cb388ca199f14e7f8ca52f8a855cd71af0cff00ee5a3563417ca1e5a5bdef026677667772f23b16776259a466c9624f2c589258b6793cf249afab492492564b4496c9765b6de9f71f2cdb6db6db6dddb7bb7ddefabf5fbc4e73f7474f41ff00eae9f2f23dc641340bbebff03fadf5139e3e5efe9ffd7c8c8e3f88718fba7347f5fd770feadfd76fd45e79e07e43fc7073d7f84f183d8d1fd7f5d83e7d7f5dbf40e78e3f4ff27afcdc8e7d98501fd5bfaeff00a09ce3ee8fcba7ebdbfd9f5c819c8a3fafebe61db5ff0083fd6fa1ffd5f05fc31ee38cfafafd47bb638241aff620ff0021ff00afebfafcc0f51ebfcbdfdf9e3e99f4a05f3ff83fd6fa09e9d4fcc31efeff00873d3dbb501d37ff0081a7dfe7dff00fef75f73f874fc07af73f4a07fd7f5d3fad3a87a707a7bf1c73efe83ebef40bfcfcfbfdff00a7c838e78ec79fef7038ff00f50fa630681871c71dcf6fb9c8f7fc73f8e36f140071cf1f87f7be6ebfe47d7208a0038e3f0e71d3afcbd78f4fe2f43ce0d001c63ee9fa63a7cbd7af3ff8efa7dee6800e32793d3dbf31ff00a1f4fa63068feadfd77fd03fafebb071c75ebe83d7ebdbae7a7cd9036e453efaff00c1feb7d4038e7afe9c7b7a71f77a743ce411476d7fe07f5be81fd7f5f20e38ebf4c0ff001e33f739ddd3079c1a5fd5bfaedfa809c63b9e7d073faf39f6dbcae3ef734fe7fd5b6fd03fafebb9ffd9"
				]
			}
		}
	],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": [
        {
            "hash": "2424716B3F000B2DC6698D8FC17DDC569E78500B27DF839C88576735DE1CCC3F",
            "info": "6197B141C9F7DB46ECD721F90F4E9506641DF6CFED1CF9B5CE8F63FF061EDD49",
            "token": "8C6B700771D452DD89158FF498E2114763055B1F7C0FBCE01D10BB24FF86FC68"
        }
    ],
    "status": "success"
}
```

***

#### jt_transferToken 转移token

转移类似erc721的token

##### Parameters 参数

- `from`:`ADDRESS`: 发行token的帐户地址
- `to`:`ADDRESS`:创建token的帐户地址
- `token`:`OBJECT`: token的定义信息

```js
params: [
  {
		"from":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR", 
		"to":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
		"token":"8C6B700771D452DD89158FF498E2114763055B1F7C0FBCE01D10BB24FF86FC68"
	}
]
```

##### Returns 返回值

同 `jt_createToken` 返回值定义

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_transferToken",
	"params":[
		{
			"from":"jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR", 
			"to":"jw27f8oUXpJtM4YeATNE9ozSrzFRRxG6R5",
			"token":"8C6B700771D452DD89158FF498E2114763055B1F7C0FBCE01D10BB24FF86FC68"
		}
	],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": [
        {
            "hash": "F1EA1514085AECDA49F592E9A85C21A624A9B6C902D4A8189793370BA65A53E5",
            "info": "BD8854945C839920EEC9DAB86B8D496086B0E997B5EA5BAE333C9AE3348BE0D4",
            "token": "8C6B700771D452DD89158FF498E2114763055B1F7C0FBCE01D10BB24FF86FC68"
        }
    ],
    "status": "success"
}
```

***

#### jt_ownerOf 查询token的拥有者

查询token的拥有者

##### Parameters 参数

1. `HASH`, 代表token的唯一哈希，同jt_createToken或者jt_transferToken返回的`hash`值

```js
params: ["C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D"]
```

##### Returns 返回值

`ADDRESS` - 该token的拥有者的地址

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_ownerOf",
	"params":["C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D"],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": "jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR",
    "status": "success"
}
```

***

#### jt_tokensOf 查询某个地址拥有哪些token

查询某个地址拥有哪些token

##### Parameters 参数

1. `ADDRESS`, 要查询的帐户的地址

```js
params: ["jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR"]
```

##### Returns 返回值

`LIST` - 返回token的概要信息列表
- `date`: `STRING` token的创建的日期
- `info`: `HASH` token的定义的哈希
- `invoice`:  `HASH` token的指纹信息
- `symbol`: `STRING` token所属的token类型的简称
- `token`: `HASH` token的唯一的哈希, 可在jt_getToken中用来查询token详细信息
- `tx`: `HASH` token生成所有的区块链的交易的哈希

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_tokensOf",
	"params":["jQfvETFtVeQGXQZbMTxUm5VHgSA145ucPR"],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": [
        {
            "date": "2018-09-30T22:54:20+08:00",
            "info": "1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A",
            "invoice": "F8F0B74598FD1E512C846697680EEA12A33EDAB618296B87D5C87CBDEBACBED9",
            "symbol": "test",
            "token": "C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D",
            "tx": "032276039CD21A826D9728537B44CDBFC05719C7BB909136A670366625190C77"
        }
    ],
    "status": "success"
}
```
***

#### jt_getToken 查询token的具体信息

查询token的具体信息

##### Parameters 参数

1. `HASH`, token的唯一哈希，见jt_createToken, jt_transferToken或者jt_tokensOf中返回的token值

```js
params: ["C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D"]
```

##### Returns 返回值

`OBJECT` - token的详细信息

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_getToken",
	"params":["C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D"],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": {
        "Hash": "C0CA0215D1530B2A138FB069DB4B9593F2A6D8F98087EA12959A51DF05ADD03D",
        "Info": "1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A",
        "Items": [
            {
                "Name": "code",
                "Value": "001"
            },
            {
                "Name": "quality",
                "Value": "99.99999%"
            },
            {
                "Name": "weight",
                "Value": "10"
            }
        ],
        "TokenURI": "http://www.jingtum.com"
    },
    "status": "success"
}
```
***

#### jt_getTokenInfo 查询token定义的具体信息

查询token定义的具体信息

##### Parameters 参数

1. `HASH`, token定义的info信息，见jt_issueToken或者jt_tokensOf中返回的info值

```js
params: ["1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A"]
```

##### Returns 返回值

`OBJECT` - token的具体定义信息

##### Example 例子
```js
// Request 请求
curl -X POST --data '{
	"jsonrpc":"2.0",
	"method":"jt_getTokenInfo",
	"params":["1414C09094153EAE9C1D3FB255EEDC7090167DD461F413BCA1DD5C9706283A5A"],
	"id":1
}' http://localhost:7545/v1/jsonrpc

// Result 结果
{
    "id": 1,
    "jsonrpc": "2.0",
    "result": {
        "Name": "TEST",
        "Symbol": "test",
        "TotalSupply": 100,
        "Items": [
            {
                "Name": "code",
                "Type": "string",
                "Desc": "the code of the token"
            },
            {
                "Name": "quality",
                "Type": "string",
                "Desc": "the quality of the token"
            },
            {
                "Name": "weight",
                "Type": "number",
                "Desc": "the weight of the token"
            }
        ]
    },
    "status": "success"
}
```

## JSON REST API Reference JSON格式的API引用

POST or GET, /v1/jsonrpc/[JSON-RPC methods](#json-rpc-methods)?params=xxxx

比如
```js
curl -g http://localhost:7544/v1/jsonrpc/jt_getBalance?params=[%22jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh%22,%22validated%22]

或者

curl -X POST --data '{"params":["jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh","validated"]}' http://localhost:7544/v1/jsonrpc/jt_getBalance

{
    "id": 1,
    "jsonrpc": "2.0",
    "result": {
        "account": "jHb9CJAWyB4jr91VRWn96DkukG4bwdtyTh",
        "balance": "244499999099986768",
        "coins": [],
        "ledger_hash": "443AADF864838F4F1C62C93F9BCA5721F987851CE1D4DB19F9CF28CB226AFE33",
        "ledger_index": 113996,
        "validated": true
    },
    "status": "success"
}
```

## The default block parameter 默认区块参数

以下方法有额外的默认区块参数

- [jt_getBalance](#jt_getbalance)
- [jt_getTransactionCount](#jt_gettransactioncount)

当请求以太的状态时，最后一个默认区块参数决定区块的高度.

以下可能是默认区块参数:

- `Block Number String` - 区块编号，整数
- `Block Hash String` - 区块哈希，字符串
- `String "current"` - 表示当前区块
- `String "validated"` - 最新生效的区块
- `String "closed"` - 最近关闭的区块
