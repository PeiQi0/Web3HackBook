# 20220524 - HackerDao - 价格操纵 ～ 175 $BNB

## 相关地址

攻击者地址：0xcfc591db031b760961fe8943a183741ed7cd1f82

攻击合约：0x24cb6980995aeb7d5a9204e04b17dcd1e99a4694

被攻击合约：0x94e06c77b02ade8341489ab9a23451f68c13ec1c

攻击交易：0x04673c95054247588bb8380dbc7d361f08f8f0baa319366f48ad46e51d08422d

## 攻击分析

![image.png](../../img/1708938684155-6cb5fabe-6b78-4d41-9f76-1eb3cc4e0458.png)

攻击者通过闪电贷借入 WBNB 购买 Token

![img](../../img/1708938772604-b6088bbc-796d-458e-8ab1-49f68404c814.png)

购买获取的 Token 数量为 swap 计算的 88% , 其中 12% 将用于销毁和手续费

![img](../../img/1708939023268-7099a4ba-728f-4f9e-968c-b55d4d132718.png)

![img](../../img/1708938978698-2a1c7065-514b-476e-8c16-1a3d283cc8c7.png)

将 Token 发送至 LP 中，并调用 skim 将 Token 发送到 USDT LP 

![img](../../img/1708939096940-d937c880-261e-454d-8db5-36004863eaaa.png)

在 _transfer 函数中当 recipient == uniswapV2Pair 时，fee 手续费将扣除发送者的

![img](../../img/1708939240836-9b6c7542-82b4-4bd2-8a60-685cf1393f65.png)

![img](../../img/1708939296215-696f9235-5aed-4765-be7a-877631214c81.png)

调用 skim 将 token 从 WBNB LP 发送到 USDT LP 时，符合代码逻辑，fee 手续费将扣除 WBNB LP 中的 Token 余额，所以此时 skim 发送到 USDT LP 时数量不变

![img](../../img/1708939463368-19fd508e-c95b-43f1-94f0-ae4c66df04c5.png)

此时 WBNB LP 中扣除了手续费，使池中的 Token 减少，WBNB 数量不变，相当于拉升了 Token 价格，攻击者想要最大获利如下图

![img](../../img/1708939629872-50a292ee-ccd0-467b-82be-6d26b9b96fd8.png)

计算出的结果为 x < 10371 , 当借款接近该值时，将会通过扣除手续费将 WBNB LP 中的 Token 数量接近 0 ，攻击者通过买入 10315 Token，skim 后 LP 中只剩 61 Token，此时攻击者将 Token 全部转换为 WBNB 获利离场

![img](../../img/1708939807089-884b04f6-7ccb-479c-93c8-0c696766bf18.png)