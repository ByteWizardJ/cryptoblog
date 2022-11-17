# 交易平台与聚合器

如果将所有 NFT 交易市场根据性质进行分类的话，可以分为以下几种。

### 1. 交易平台：

指的是可以直接挂单，购买的市场。比如采用中心化订单簿交易模型的 Opensea、X2Y2。以及采用自动做市商的交易模型的 Sudoswap。

### 2. 聚合器

指的是本身不能进行挂单、购买。但是聚合了不同的交易平台的交易信息的交易市场。通过聚合器来可以更方便的交易不同交易平台挂单的 NFT。Gem、Genie 属于这一种。

### 3. 混合型

这种交易市场既有自己的交易平台，可以进行挂单，购买。也聚合了其他平台的交易数据。Ens.vision 和 Blur 属于这种类型。

## 如何区分不同市场的交易

### 交易平台

区分不同的交易平台可以通过交易调用合约的不同来区分。比如，一个交易的 `to` 的值是 `0x00000000006c3852cbEf3e08E8dF289169EdE581`。则表示这个 NFT 的交易是通过 Opensea 的 seaport 合约成交的。但是这个方法有个漏洞。下面讲聚合器的时候会介绍。

### 聚合器

聚合器的情况要分成两类。

#### 一次成交多个平台的交易

这个时候会调用聚合器的合约，这个合约本身不产生交易，会通过聚合器的合约内部调用交易平台的合约从而完成交易。因此这种情况区分不同的聚合器也可以通过交易的 `to` 的值。

#### 一次成交一个平台多个或者一个 NFT

这种情况下的交易成交会直接调用对应市场的合约，而不通过聚合器的合约。这个时候就无法通过交易的 `to` 的值来区分某个交易是来自聚合器还是具体的交易平台了。

为了应对这种情况，一般的处理是在交易的 input 数据结尾加上标识。这个标识目前主流的有两种方式来生成。

##### 1. 将域名转换成16进制

域名被转换为十六进制，并被定界符`1f`包围。

比如 `ens.vision` >> `1f656e732e766973696f6e1f`

##### 2. 使用域名哈希的前 4 个字节

`bytes4(keccak256("mydomain.com")`

比如 `gem.xyz` >> `72db8c0b`

`blur.io` >> `332d1229`

在 reservoir 的 文档中有说明: https://docs.reservoir.tools/docs/calldata-attribution

gem 开发者说明: https://twitter.com/vasa_develop/status/1572331744032067584

blur 开发者说明: https://twitter.com/PacmanBlur/status/1583578608676663296

##### genine 的说明

genine 跟 gem 和 blur 不同，如果一次只成交一个 NFT会有不同情况

1. Opensea

这种情况下，直接调用 seaport 合约进行成交。但是 input 数据中没有后缀。因此无法区分一个交易到底是在 genine 上成交的还是在 Opensea 上成交的。

2. X2Y2

这个时候会调用名为 X2Y2 Aggregator 的合约，这个合约内部调用 X2Y2 的合约来完成交易。

Genie: X2Y2 Aggregator 
https://etherscan.io/address/0xF97E9727d8E7DB7AA8f006D1742d107cF9411412

3. LooksRare

这个时候会调用名为 LooksRare Aggregator 的合约，这个合约内部调用 LooksRare 的合约来完成交易。

Genie: LooksRare Aggregator 
https://etherscan.io/address/0x31837aaF36961274a04B915697FdfCA1Af31a0C7
