# 20240216 - Particle - 参数可控 ～ 50 $ETH

## 相关地址

攻击者地址：0x2c903f97ea69b393ea03e7fab8d64d722b3f5559

攻击合约：0xe55607b2967ddbe5fa9a6a921991545b8277ef8f

被攻击合约：0xe4764f9cd8ecc9659d3abf35259638b20ac536e4

攻击交易：0xd9b3e229acc755881890394cc76fde0d7b83b1abd4d046b0f69c1fd9fd495ff6



## 攻击分析

![image.png](../../img/1707928093957-dee55996-94cb-4a80-a068-ee897cb5005f-20240715170156856.png)

攻击者首先调用合约的 offerBid 函数获取 lienId

![img](../../img/1708071076029-5bc503da-824f-4c11-8686-e39874405259.png)

![img](../../img/1708071390029-d239150e-23f4-4abd-a03c-6caab8f23eba.png)

跟据参数会生成对应的 lienId，由于此时 (贷款额度) accountBalance[account] 为 0，所以传入参数为 合约地址,0,0,0 ，这里生成的 liens[lienId] 会在后续函数功能中使用，找到提出贷款功能 withdrawAccountBalance() 可以发现提款数量由 accountBalanace[msg.sender] 参数决定，而函数 _acceptBidSellNftToMarketCheck() 函数可以控制该参数

![img](../../img/1708072176099-c7c54c1f-9b38-4766-9ecd-43578795248c.png)

accountBalance 的值主要由 lien.tolenId 来决定 ，像上跟踪代码

![img](../../img/1708072443159-c93546ff-4006-4825-a654-6beb21c7c133.png)

![img](../../img/1708072431826-24a926bc-560f-4b73-8b4f-2a9279f94879.png)

函数的调用为 onERC721Received --> _pushBasedNftSupply --> _acceptBidSellNftToMarketCheck,  此时可控的参数有 from tokenid data, 其中 data 参数为 lien 结构体和部分参数组成，所以此时 lien.tokenId 我们可以通过 data 参数控制

![img](../../img/1708072709674-12b3c849-6772-4e87-a19e-3d4d38bca062.png)

_acceptBidSellNftToMarketLienUpdate 函数功能为更新 liens 参数

![img](../../img/1708135475726-61d44148-02d1-4d94-af58-c9d96418b7c0.png)

_execSellNftToMarketPull 函数中的 safeTransferFrom， ownerOf 的调用结果攻击合约可控，来源为可控参数 lien.collection

![img](../../img/1708135691701-f62a6c8a-871e-410e-84f0-9f089cd22ecd.png)

但其中代码逻辑中存在一些参数的检测，这里检测了 liens[lienId] 是不是 offerBid 对应生成的 lien 参数，所以此时 lien.tolenId 只能为 0 导致 accountBalance 是不可控的

![img](../../img/1708136245729-2dcdef5d-3a42-4ce8-989e-fc264d9ae8f0.png)

但 _acceptBidSellNftToMarketLienUpdate 函数中会通过可控传参 tokenId 来更新 liens

![img](../../img/1708136621183-dfe4215a-e2ea-4a0e-92be-77b814415986.png)

在第一次传入参数时控制 _acceptBidSellNftToMarketLienUpdate 函数更新的 liens，这样如果在第二次调用时 lien.tokenId 已经被覆盖，也就可以控制 accountBalance 的大小

![img](../../img/1708136972398-b4fd5cb8-2364-4ba9-aa2e-7fb514417359.png)

攻击者通过 onERC721Received 函数第一次调用覆盖 lien.tokenId , 然后再一次调用控制 accountBalance 参数，最后在通过函数 withdrawAccountBalance 将对应  accountBalance 大小的 $ETH 提出完成攻击

![img](../../img/1708137229247-5f78f55b-c3c6-4cee-8151-9449308c9538.png)

![img](../../img/1708137203511-bcbe9230-7a83-4ae5-809b-0b9f5d3aca2b.png)

在后续攻击中攻击者还通过类似方法转移出了合约中的 NFT

![img](../../img/1708144911027-8101700d-ac6a-4a4c-83b8-0a58090b7538.png)

在转移前攻击者同样调用两次 onERC721Received 将 accountBalance 值控制，使 _balanceAccount 方法可以通过

![img](../../img/1708144987373-3d2adb32-fc01-429f-a7d2-61a3a9552307.png)

其中需要的参数可以在历史交易中找到对应的 tokenId principal reteBips 的值

![img](../../img/1708145160892-4dea3a7b-e92d-46d9-8d59-5fc04cfee5b1.png)

在 swapWithEth 中 lien.collection 是攻击者可控的，此时攻击者可以将合约中的 NFT 转移到攻击合约中

![img](../../img/1708145062644-1b8ee1e9-5d87-4e6e-b988-b38f5691056d.png)

![img](../../img/1708145213514-c40870d7-d61e-4f99-8d0a-e777c2b9ffaa.png)