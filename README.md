Bytom
======

比原链的安装方式有多种。本书从源码分析的角度带领读者了解架构，所以使用源码编译的方式来介绍安装过程。

## 1. 源码编译部署

1）下载源码：

$ git clone https://github.com/Bytom/bytom.git $GOPATH/src/github.com/bytom

2）切换至1.0.5版本：

$ cd $GOPATH/src/github.com/bytom

$ git fetch origin v1.0.5
$ git checkout v1.0.5
3）编译源码：
$ make bytomd
$ make bytomcli
4）初始化：
$ cd ./cmd/bytomd
$ ./bytomd init --chain_id mainnet
目前比原链支持三种网络，使用chain_id进行区分，如下所示：

mainnet：主网。
testnet：也称wisdom，测试网。
solonet：单机模式。
5）启动bytomd进程：
$ ./bytomd node

$ ps -ef|grep bytomd
501 52318 449 0 2:00PM ttys000 0:00.85 ./bytomd node

$ ./bytomcli net-info
{
"current_block": 36714,
"highest_block": 36714,
"listening": true,
"mining": false,
"network_id": "wisdom",
"peer_count": 10,
"syncing": false,
"version": "1.0.5+2bc2396a"
}
当我们执行ps -ef命令看到bytomd进程时，说明进程已经处于运行状态。使用bytomcli获取节点状态信息，可以看到我们已经成功地运行了bytomd进程。
bytomd进程第一次启动后，默认不会开启挖矿功能。此时会从P2P网络种子节点中获取与之相邻的peer节点，建立握手连接并同步区块。我们将在第10章深入分析P2P网络底层工作原理。

## 2. 源码目录结构

比原链的源码目录如下所示：
$ tree -L 1
.
├── accesstoken Token管理
├── account 账户管理
├── api API Server接口管理
├── asset 资产管理
├── blockchain 交易打包、签名、查询等
├── cmd main入口文件
├── common 公共库
├── config 节点配置文件
├── consensus 共识相关模块
├── crypto 加密库
├── dashboard dashboard页面管理
├── database 数据库管理
├── docs 文档
├── encoding 协议相关的编解码库
├── env 环境变量管理
├── equity 智能合约语言编译器
├── errors 错误及异常管理
├── math 数学计算相关库
├── metrics metrics指标库，用于采集API Server请求相关指标
├── mining 挖矿模块
├── net API Server使用的HTTP基础库
├── netsync 网络同步管理
├── node 当前节点管理模块，环境初始化等
├── p2p 分布式网络管理模块
├── protocol 核心数据结构，包含块、交易、bvm虚拟机等
├── test 单元测试
├── testutil 单元测试工具包
├── util 工具包
├── vendor 第三方库
├── version 版本
└── wallet 钱包管理

## 3.开启挖矿模式

开启挖矿模式的命令如下：
$ ./bytomcli set-mining true
在默认情况下比原链的挖矿模式是关闭状态。开启挖矿模式有两种方式，第一种方式，使用bytomcli命令行交互，将mining参数设置为true，此时bytomcli会通过RPC协议与bytomd进程交互并启用挖矿模式。关闭挖矿模式则指定set-mining参数为false。第二种方式，使用dashboard页面启用挖矿参数，在这里请读者自行学习dashboard。

## 4.其他语言SDK简介

比原链技术社区提供了不同语言的SDK，如下所示：
PHP SDK：https://github.com/lxlxw/bytom-php-sdk
Java SDK：https://github.com/chainworld/java-bytom
Java SDK：https://github.com/successli/Bytom-Java-SDK
Python SDK：https://github.com/Bytom-Community/python-bytom
Node SDK：https://github.com/Bytom/node-sdk


[![Build Status](https://travis-ci.org/Bytom/bytom.svg)](https://travis-ci.org/Bytom/bytom) [![AGPL v3](https://img.shields.io/badge/license-AGPL%20v3-brightgreen.svg)](./LICENSE)

**Official golang implementation of the Bytom protocol.**

Automated builds are available for stable releases and the unstable master branch. Binary archives are published at https://github.com/Bytom/bytom/releases.

## What is Bytom?

Bytom is software designed to operate and connect to highly scalable blockchain networks confirming to the Bytom Blockchain Protocol, which allows partipicants to define, issue and transfer digitial assets on a multi-asset shared ledger. Please refer to the [White Paper](https://github.com/Bytom/wiki/blob/master/White-Paper/%E6%AF%94%E5%8E%9F%E9%93%BE%E6%8A%80%E6%9C%AF%E7%99%BD%E7%9A%AE%E4%B9%A6-%E8%8B%B1%E6%96%87%E7%89%88.md) for more details.

In the current state `bytom` is able to:

- Manage key, account as well as asset
- Send transactions, i.e., issue, spend and retire asset

## Installing with Homebrew

```
brew tap bytom/bytom && brew install bytom
```

## Building from source

### Requirements

- [Go](https://golang.org/doc/install) version 1.8 or higher, with `$GOPATH` set to your preferred directory

### Installation

Ensure Go with the supported version is installed properly:

```bash
$ go version
$ go env GOROOT GOPATH
```

- Get the source code

``` bash
$ git clone https://github.com/Bytom/bytom.git $GOPATH/src/github.com/bytom/bytom
```

- Build source code

``` bash
$ cd $GOPATH/src/github.com/bytom/bytom
$ make bytomd    # build bytomd
$ make bytomcli  # build bytomcli
```

When successfully building the project, the `bytomd` and `bytomcli` binary should be present in `cmd/bytomd` and `cmd/bytomcli` directory, respectively.

### Executables

The Bytom project comes with several executables found in the `cmd` directory.

| Command      | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| **bytomd**   | bytomd command can help to initialize and launch bytom domain by custom parameters. `bytomd --help` for command line options. |
| **bytomcli** | Our main Bytom CLI client. It is the entry point into the Bytom network (main-, test- or private net), capable of running as a full node archive node (retaining all historical state). It can be used by other processes as a gateway into the Bytom network via JSON RPC endpoints exposed on top of HTTP, WebSocket and/or IPC transports. `bytomcli --help` and the [bytomcli Wiki page](https://github.com/Bytom/bytom/wiki/Command-Line-Options) for command line options. |

## Running bytom

Currently, bytom is still in active development and a ton of work needs to be done, but we also provide the following content for these eager to do something with `bytom`. This section won't cover all the commands of `bytomd` and `bytomcli` at length, for more information, please the help of every command, e.g., `bytomcli help`.

### Initialize

First of all, initialize the node:

```bash
$ cd ./cmd/bytomd
$ ./bytomd init --chain_id mainnet
```

There are three options for the flag `--chain_id`:

- `mainnet`: connect to the mainnet.
- `testnet`: connect to the testnet wisdom.
- `solonet`: standalone mode.

After that, you'll see `config.toml` generated, then launch the node.

### launch

``` bash
$ ./bytomd node
```

available flags for `bytomd node`:

```
Flags:
      --auth.disable                     Disable rpc access authenticate
      --chain_id string                  Select network type
  -h, --help                             help for node
      --log_file string                  Log output file (default "log")
      --log_level string                 Select log level(debug, info, warn, error or fatal)
      --mining                           Enable mining
      --p2p.dial_timeout int             Set dial timeout (default 3)
      --p2p.handshake_timeout int        Set handshake timeout (default 30)
      --p2p.keep_dial string             Peers addresses try keeping connecting to, separated by ',' (for example "1.1.1.1:46657;2.2.2.2:46658")
      --p2p.laddr string                 Node listen address. (0.0.0.0:0 means any interface, any port) (default "tcp://0.0.0.0:46656")
      --p2p.lan_discoverable             Whether the node can be discovered by nodes in the LAN (default true)
      --p2p.max_num_peers int            Set max num peers (default 50)
      --p2p.node_key string              Node key for p2p communication
      --p2p.proxy_address string         Connect via SOCKS5 proxy (eg. 127.0.0.1:1086)
      --p2p.proxy_password string        Password for proxy server
      --p2p.proxy_username string        Username for proxy server
      --p2p.seeds string                 Comma delimited host:port seed nodes
      --p2p.skip_upnp                    Skip UPNP configuration
      --prof_laddr string                Use http to profile bytomd programs
      --simd.enable                      Enable SIMD mechan for tensority
      --vault_mode                       Run in the offline enviroment
      --wallet.disable                   Disable wallet
      --wallet.rescan                    Rescan wallet
      --wallet.txindex                   Save global tx index
      --web.closed                       Lanch web browser or not
      --ws.max_num_concurrent_reqs int   Max number of concurrent websocket requests that may be processed concurrently (default 20)
      --ws.max_num_websockets int        Max number of websocket connections (default 25)

Global Flags:
      --home string   root directory for config and data
  -r, --root string   DEPRECATED. Use --home (default "/Users/zcc/Library/Application Support/Bytom")
      --trace         print out full stack trace on errors
```

Given the `bytomd` node is running, the general workflow is as follows:

- create key, then you can create account and asset.
- send transaction, i.e., build, sign and submit transaction.
- query all kinds of information, let's say, avaliable key, account, key, balances, transactions, etc.

__simd feature:__

You could enable the _simd_ feature to speed up the _PoW_ verification (e.g., during mining and block verification) by simply:
```
bytomd node --simd.enable
```

To enable this feature you will need to compile from the source code by yourself, and `make bytomd-simd`. 

What is more,

+ if you are using _Mac_, please make sure _llvm_ is installed by `brew install llvm`.
+ if you are using _Windows_, please make sure _mingw-w64_ is installed and set up the _PATH_ environment variable accordingly.

For more details about using `bytomcli` command please refer to [API Reference](https://github.com/Bytom/bytom/wiki/API-Reference)

### Dashboard

Access the dashboard:

```
$ open http://localhost:9888/
```

### In Docker

Ensure your [Docker](https://www.docker.com/) version is 17.05 or higher.

```bash
$ docker build -t bytom .
```

For the usage please refer to [running-in-docker-wiki](https://github.com/Bytom/bytom/wiki/Running-in-Docker).

## Contributing

Thank you for considering helping out with the source code! Any contributions are highly appreciated, and we are grateful for even the smallest of fixes!

If you run into an issue, feel free to [bytom issues](https://github.com/Bytom/bytom/issues/) in this repository. We are glad to help!

## License

[AGPL v3](./LICENSE)
