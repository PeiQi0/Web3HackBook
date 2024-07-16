#  20211222 - Visor Finance - 合约逻辑漏洞 ～ 195KK $vVISR

## 相关地址

攻击者地址: 0x8efab89b497b887cdaa2fb08ff71e4b3827774b2

被攻击合约地址: 0xC9f27A50f82571C1C8423A42970613b8dBDA14ef

攻击交易: 0x69272d8c84d67d1da2f6425b339192fa472898dce936f24818fda415c1c1ff3f

攻击合约地址: 0x10c509aa9ab291c76c45414e7cdbd375e1d5ace8

## 攻击分析

攻击者通过攻击合约调用 RewardsHypervisor 合约中的 deposit 函数

![img](../../img/1707554360143-f8513017-72b0-4b77-ad04-abc54fdf97f6.png)

在函数中 from 地址是可控的，if(isContract(from)) 逻辑里调用的是 from 地址的 owner函数 和 delegatedTransferERC20 函数

![img](../../img/1707554388331-b3b19120-d2c8-4ca0-bd40-25c7a3e9b5ef.png)

最后根据合约逻辑调用攻击合约 owner 和 delegatedTransferERC20 函数后，通过 vVISR.mint 获利

![img](../../img/1707556333991-56335594-7f54-4ba7-9c46-b52fc4e3f239.png)