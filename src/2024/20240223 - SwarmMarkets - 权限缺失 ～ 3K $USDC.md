# 20240223 - SwarmMarkets - 权限缺失 ～ 3K $USDC

## 相关地址

攻击者地址：0x38f68f119243adbca187e1ef64344ed475a8c69c

被攻击合约：0x2b9dc65253c035eb21778cb3898eab5a0ada0cce

攻击交易：0xc0be8c3792a5b1ba7d653dc681ff611a5b79a75fe51c359cf1aac633e9441574

## 攻击分析

![image.png](../../img/1708828935975-536d24e9-06e7-4acb-b7ba-441761d2365f.png)

项目方地址创建了3个合约，其中两个为测试 xToken, Caller 为 代币对注册

![image.png](../../img/1708829037794-a85a5d56-9501-40e0-b725-1e0c9563567a.png)

![img](../../img/1708829107276-b5b804b3-99f8-4141-9dba-aa6c5a9a37d8.png)

![img](../../img/1708829134827-9f7b20d4-816e-4838-a1d7-c18144819674.png)

0x958E2C 地址通过 upgrade 函数设置 Caller 代理

![img](../../img/1708829292017-39ff50d8-72dc-4caf-b6b5-4ce3cc27c583.png)

通过代理地址调用 XTokenWrapper 合约的 RegisterToken 时, msg.sender 通过权限判断，函数会注册对应代币对，在后续 unwrap 可以完成交换逻辑

![img](../../img/1708829486517-54f4e8d3-b924-4a7b-89f6-ae567da08774.png)

![img](../../img/1708829467952-784216b6-21df-45e5-8774-ffce702380a5.png)

函数会通过传入的 _xToken 找到对应代币对执行销毁 _xToken ，safeTransfer 发送代币给调用者

![img](../../img/1708829585409-2fc92dfd-d03b-43f6-815c-184480eddd29.png)

![img](../../img/1708829599462-ce25d5f2-dc71-4564-8369-89d426fafbae.png)

例如上图项目方在注册过程中将 XToken 与 WETH 相关联，而 XToken 为测试代币，可以无限 mint

![img](../../img/1708829658070-6b1ec61b-8ced-4a14-a1e6-fe8f1ab65ed7.png)

所以此时可以 mint 对应 XToken 代币换出对应 register 的代币

![img](../../img/1708830079740-4fc9cc19-f137-48eb-9208-dd2444b7c897.png)