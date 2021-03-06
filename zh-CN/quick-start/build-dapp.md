# 完成一个DApp

这个将部分带你完成一个DApp，并让它运行在我们的手机钱包Neuron上。

本文档假设你已经有了一些简单的前端知识，包括基本的HTML和JavaScript。

## 示例代码

我们已经为上一节中我们部署的智能合约开发了一个前端示例，通过这个示例你可以了解到如何使用Nervos Web3 SDK完成与AppChain的交互。
完整的代码文件也可以在上一节中的代码包里找到。

### 引入 web3.js

在index.html中引入`bundle.js`,这里面包括了`web3.js`和相关依赖。`index.js`为调用web3的js脚本。
```
<script src="./node_modules/@cita/web3/lib/bundle.js"></script>
<script src="./index.js"></script>
```

### 设置参数

设置AppChain的地址。
```
const server = 'http://47.97.108.229:1337'
```

### 检测web3环境

检测web3实例是否已经存在。一般来说，DApp浏览器环境（如Neuron）会给DApp应用注入web3实例，并提供交易签名等方法。所以我们在这里检测环境中是否已经有了实例，如果没有则自己构建；如果已经存在，则使用DApp浏览器的web3实例。
```
if (typeof web3 !== 'undefined') {  
    web3 = CITAWeb3(web3.currentProvider)
    console.log('Web Injected')
    div.innerHTML = 'Web Injected';
} else {
    web3 = CITAWeb3(server)
    console.log('Web3 Initialized')
    div.innerHTML = 'Web Initialized';
}
```

### 发送交易
下面分别给出发送以太坊交易和发送AppChain交易的两种方法。

#### 以太坊交易
首先构造以太坊交易，然后调用`web3.eth.sendTransaction`。
```
var tx_eth = {
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "gasLimit": "0x76c0", 
  "gasPrice": "0x5AF3107A4", 
  "value": "100000000000000000", 
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8"
}

function sendETHTransaction() {
  web3.eth.sendTransaction(tx_eth, function(err, res){
    console.log(res)
  })
}
```

#### AppChain 交易
首先构造AppChain交易，然后调用`web3.eth.sendTransaction`。
```
var tx_appchain = {
  to: '0x2cc18375F32a98EfC017D1dDEBCEBD6F9Ee75152',
  nonce: 100,
  quota: 100,
  data: '0x2cc18375F32a98EfC017D1dDEBCEBD6F9Ee75152',
  value: "10000000000000000000",
  chainId: 1,
  version: 0
}

function sendCITATransaction() {
  web3.eth.sendTransaction(tx_appchain, function(err, res){
    console.log(res)
  })
  
}
```

#### Neuron 回调
`DAPP` 发起的交易并交由 `Neuron` 钱包的本地私钥签名，并最终由钱包将交易发至链上，发送交易的结果会通过以下三个回调方法通知 `DAPP` 。

```
cancelled()                  // 交易取消
onSignSuccessful(hexHash)    // 交易成功，并返回交易hash
onSignFail(errMessage)       // 交易失败，并返回失败错误信息
```

例如：
```
function cancelled() {
    console.log("transaction cancel");
}

function onSignSuccessful(hash) {
    console.log("transaction hash: " + hash);
}

function onSignFail(err) {
    console.log("transaction err: " + err);
}
```


### 签名交易
直接调用`signTransaction`来实现对某一笔交易的签名。
```
function signTransaction() {
  web3.eth.signTransaction(tx_appchain, function(err, res) {
    if (!err) {
      console.log(res)
    }
  })
}
```


## 测试DApp
我们可以通过使用python在本地启动一个简单的http服务，并使用Neuron来访问这个DApp。Python的安装请参照[Python网站](https://www.python.org/downloads/)。

```
python -m SimpleHTTPServer 3000
```

查看一下自己本机的ip，并打开Neuron，在浏览器页面输入地址，如`http://192.168.2.239:3000`。

在屏幕上点击相应按钮可以唤起本地的交易页面和交易签名页面。


