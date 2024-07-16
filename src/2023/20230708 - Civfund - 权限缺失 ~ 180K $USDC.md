# 20230708 - Civfund - 权限缺失 ~ 180K $USDC

## 相关地址

攻击者地址: 0xc0ccff0b981b419e6e47560c3659c5f0b00e4985

被攻击合约地址: 0x7caec5e4a3906d0919895d113f7ed9b3a0cbf826

攻击交易: 0xc42fc0e22a0f60cc299be80eb0c0ddce83c21c14a3dddd8430628011c3e20d6b?line=2

攻击合约地址: 0xf466f9f431aea853040ef837626b1c59cc963ce2

## 攻击分析

![image.png](../../img/1707719095292-deede7ea-b21a-4f8b-8aa0-a8b067d5ec56.png)

攻击者调用合约的 uniswapV3MintCallback 和 0x5ffe72b7 将受害者的代币转出

![img](../../img/1707719166584-a140aecb-8b9a-49d4-9469-815c0f370c5c.png)

反编译合约后在函数 0x5ffe72b7 中，当 varg3 可控时会调用 varg3 地址的 mint 函数, 并且赋值     _uniswapV3MintCallback

![img](../../img/1707719476796-7a6d70d8-9cac-4731-a955-4555ccd65d98.png)

![img](../../img/1707719500884-d8faf1fe-572d-4b9d-b084-f8096afb0ab6.png)

此时在攻击合约中使用 mint 函数调用 uniswapV3MintCallback 函数就可以通过地址检测![img](../../img/1707719631614-eb537305-fba4-403a-8a82-a32d305a651d.png)

![img](../../img/1707719674091-2c4badfc-a57e-4714-b5df-b55be7c187d5.png)

最后控制 v2 和 v8 将对合约授权的用户代币通过 transferFrom 取出

![img](../../img/1707719775997-0d7e561c-6695-4fad-841b-1f07377f5e86.png)