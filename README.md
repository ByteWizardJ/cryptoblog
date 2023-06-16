# cryptoblog
crypto blog

## hardhat verify

使用hardhat verify合约的时候 由于hardhat-etherscan用的是undici的request， 所有不好加代理。

在hardhat.config.js里 添加如下几句代码可以给undici加上代理， 这样就能使用代理，通过hardhat verify合约了

```
const { setGlobalDispatcher, ProxyAgent } = require('undici')
const proxyAgent = new ProxyAgent('http://127.0.0.1:7890')
setGlobalDispatcher(proxyAgent)
```
