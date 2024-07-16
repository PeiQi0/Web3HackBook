# 20230117 - UpSwingFinance - 逻辑错误 ~ 22 $ETH

## 相关地址

攻击者地址：0x8a2d94ea342cbdd6d57db614b24f20cae286cac6

攻击合约：0x22898dc59145eae79471dd1f06b7e542e0982d30

被攻击合约：0xffee5ecde135a7b10a0ac0e6e617798e6ad3d0d6

攻击交易：0xd099a41830b964e93415e9a8607cd92567e40d3eeb491d52f3b66eee6b0357eb

## 攻击分析

![image.png](../../img/1707451789960-e8c70aef-6b4c-4998-b988-2a1871522aac.png)

攻击者通过闪电贷获取初始资金

![img](../../img/1707451849544-f312f7f7-da3b-411c-a050-81f3727498ff.png)

额外创建两个合约不断做买入卖出动作

![img](../../img/1707451904595-4708ab7b-3242-4de4-bd42-e5a5976f12b1.png)

这么做的原因为 releasePressure 函数中存在 burn 代币销毁功能，且对应的参数是可控的

![img](../../img/1707452013214-19675ffd-4807-409e-99cd-540c81d0485b.png)

销毁量为每次卖出量通过内部函数计算得到的，销毁量和 sellPressure 参数计算

![img](../../img/1707464445980-7af952f1-a17c-437f-97e0-1de8af69db39.png)

```solidity
burnamounbt =  sellPressure[sender] *  balanceof(univ2) * 0.9 / totalsupply
```

由于 txCount 计数机会随着交易变多而变大，使销毁量逐渐变低，所以攻击者用三个不同的地址去重复三次买卖功能

![img](../../img/1707464619281-04144f28-f144-4be0-8067-2233bb376d1a.png)

销毁结束时地址中需要含有部分 $UPS 将池中的 $ETH 换出来，攻击者在其中一次 buy 中多购买一部分 Token

![img](../../img/1707464968089-f874538e-2860-43a3-80bd-e7b0b093aab2.png)

控制 amount 和 recipient 参数将池中的 Token 进行销毁，最后卖出 Token 归还闪电贷获利离场

![img](../../img/1707472171259-d80a5ca0-f8d8-42c7-bbac-97fd6f13a8ac.png)

![img](../../img/1707472276232-70cb3479-0028-4064-b790-431242f2ea41.png)