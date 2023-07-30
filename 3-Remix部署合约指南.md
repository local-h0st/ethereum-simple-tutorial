# 3-Remix部署合约指南

先新建一个workspace，不要模板，直接选择空白，自带的文件统统不管我暂时也不懂那些是什么。

新建一个`DonutVendingMachine.sol`文件，贴上以下代码然后编译：

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DonutVendingMachine{
    address public owner;
    mapping(address => uint) public donutBalances;

    constructor(){
        owner = msg.sender;
        donutBalances[address(this)] = 100;
    }

    function getBalance() public view returns (uint) {
        return donutBalances[address(this)];
    }

    function restock(uint amount) public {
        require(msg.sender == owner, "Only owner can restock the donuts!");
        donutBalances[address(this)]+= amount;
    }

    function purchase(uint amount) public payable{
        require(msg.value >= amount * 0.5 ether, "You must pay a minimum of 1 ether for 2 donuts");
        require(donutBalances[address(this)] >= amount, "OOPS! Not enough donuts");
        donutBalances[address(this)] -= amount;
        donutBalances[address(msg.sender)] += amount;
    }
}
```

编译完成后在侧边栏切换到`部署 & 发交易`栏目，环境下拉框有很多环境可选，Remix VM开头的都是临时的浏览器内的本地环境，不上链，可以很方便地测试，由于是本地环境因此可用的地址、代币都很充足，括号的内容指出这个本地环境模拟的是哪个版本的硬分叉，其他选项暂时用不到我也不知道；Injected Provider - xxx（常用MetaMask所以是Injected Provider - MetaMask）会连接钱包直接部署到测试网络上，例如合约直接发布到sepolia测试网，这种情况下合约上链，在sepolia区块浏览器上直接可查，因为上链了所以代币就按照钱包里面的来。

当合约上了sepolia测试网后，下次开Remix如果想要重新测试上回部署的合约，可以先在MetaMask上链接sepolia测试网，然后Remix中环境选择Injected Provider - MetaMask，然后从sepolia区块浏览器上找到上次部署的合约地址，复制到`At Address`按钮右边的文本框中，最后点击`At Address`按钮，合约就出现在下方了。

测试合约的时候，如果需要额外ETH，需要在合约测试区上方的以太币数量中填入，否则无法成功交易。