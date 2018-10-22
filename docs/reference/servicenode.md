# Service节点手册

## 主要功能

	* 与 public node 相连，提供所有的账本的实时同步功能
	* 对外提供区块链相关的api与rpc调用, 具体见[JSON RPC API](# json-rpc-api)

## 命令行参数

	- 参数配置类
		* **-config**, 指定当前配置文件包括生成的帐户文件所在的目录，默认为当前目录
		* **-data_dir**， 指定账本存放目录，默认为当前目录
		* **-database_name**， 指定账本数据库目录名，默认为"jingtum.db"
		* **-endpoint**, 指定连接的public node的地址，默认为测试环境的一个public node的地址，即"ws://ts.jingtum.com:5020"
		* **-thread_count**, 指定同步账本的线程数量，默认为8
	- 帐户管理类
		* **account list**， 列出本地帐户相关情况
		* **account generate [-nickname]**， 生成新的本地帐户，可指定新帐户的nickname
	- 启动服务类
		* **-sync**, 启动账本实时同步功能，默认为true
		* **-api**, 启动JSON REST API服务，默认为false
		* **-apiaddr**, 设定启动的JSON REST API服务的地址，默认为"localhost"
		* **-apiport**, 设定启动的JSON REST API服务的端口，默认为7544
		* **-rpc**, 启动JSON RPC API服务，默认为false
		* **-rpcaddr**, 设定启动的JSON RPC API服务的地址，默认为"localhost"
		* **-rpcport**, 设定启动的JSON RPC API服务的端口，默认为7545

## JSON-RPC API列表

- [JSON RPC API](#json-rpc-api)
	- [JSON-RPC methods](#json-rpc-methods)
		* [jt_syncing](#jt_syncing)
		* [jt_blockNumber](#jt_blocknumber)
		* [jt_getBalance](#jt_getbalance)
		* [jt_getTransactionCount](#jt_gettransactioncount)
		* [jt_getBlockTransactionCountByHash](#jt_getblocktransactioncountbyhash)
		* [jt_getBlockTransactionCountByNumber](#jt_getblocktransactioncountbynumber)
		* [jt_getBlockByHash](#jt_getblockbyhash)
		* [jt_getBlockByNumber](#jt_getblockbynumber)
		* [jt_get
