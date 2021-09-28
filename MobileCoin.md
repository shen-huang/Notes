# MobileCoin 调查报告

## 项目介绍

MobileCoin 是一个隐私支付协议，专注于为移动通讯应用程序提供安全、隐私、极简的加密货币钱包服务，有望接入月活跃用户超过 4 千万的 Signal。

### 基本信息

- 项目官网：[https://www.mobilecoin.com](https://www.mobilecoin.com/)

- 主网启动：2020 年 12 月 7 日

- 代币总量：250,000,000 MOB（2.5 亿 MOB）

- 共识机制：MobileCoin 共识协议（恒星共识协议修改版）

- 程序语言：Rust

- 最小单位：1 picoMOB = 0.000000000001 MOB （精度小数点后 12 位）

- 私募预售：37,500,000 MOB

- 白皮书：[MobileCoin 白皮书（2017）](https://mobilecoin.foundation/pdf/MobileCoin_White_Paper.pdf)

- 黄皮书：[MobileCoin 技术黄皮书（2021）](https://raw.githubusercontent.com/UkoeHB/Mechanics-of-MobileCoin/master/Mechanics-of-MobileCoin-v0-0-39-preview-10-11.pdf)

### 公链特点

- 高度隐私

  完全隐私不透明的分布式账本，每个交易都受到密码保护并且向前保密，任何信息服务商或其他第三方都无法获取用户余额、交易历史、转账金额、转账发送方、转账接收方等隐私信息。

- 快速转账

  大多数交易将在 1 秒内完成。

- 用户友好

  易于大规模安全使用，不会消耗过多的能源，而且能够自由地在集成了 MobileCoin Procotol 的 App 之间相互转账，例如在各自集成 MobileCoin Procotol 后，Signal 的用户就能够方便的给 WhatsApp 的用户转账。

- 易于恢复

  如果用户丢失了手机，无需信任私钥提供商就能安全地恢复钱包。

### 核心技术

- MobileCoin 共识协议（恒星共识协议修改版）

  MobileCoin 共识协议基于恒星共识协议（Stellar Consensus Protocol）修改而来。MobileCoin 没有中央授权，参与节点可以是个人、企业、组织，他们之间必须有真实的信任关系，用户可以选择自己信任的一组节点提供服务。例如 Blockdaemon 加入了 MobileCoin 基金会，那么 MobileCoin 可以选择信任 Blockdaemon 的节点，这样两个节点之间就能建立连接，而 Blockdaemon 和 MobileCoin 同时可以选择信任更多节点，更多节点又能去信任更多节点，以此类推可建立一个庞大但性能又非常高的分片网络。

  ![Stellar Consensus Protocol](https://pbs.twimg.com/media/Db0JXrwXkAATr-E?format=jpg)

  与 PoW 相比，MobileCoin 共识协议具备更低的能耗和更高的效率。

  与 PoS 相比，PoS 越富越有控制权，MobileCoin 共识协议获得用户信任越多越有控制权。

- 分布式账本

  MobileCoin 区块链是一种零知识的数据结构，由 UTXO、密钥镜像（key images）和用于确保一致性和结构的 Merkle 证明的元数据块组成。

  每个区块都包含可供未来花费的 UTXO，每笔交易都包含了未花费的 UTXO 输出和未曾被消费过的证明。MobileCoin 是基于隐私保护而设计的加密账本，使用一次性地址来掩盖所有 UTXO 所有者的身份，发送方和接收方通过环签名（RingCT）来隐藏 UTXO 中实际消费的金额。

  MobileCoin 的环签名（RingCT）是通过防弹证明（Bulletproofs）来实现的，防弹证明（Bulletproofs）是一种非交互式零知识证明协议，可以提高性能。只有交易的接受者才能真正知晓转账的金额，并获得新的可用于未来消费的 UTXO。

  交易中每一个 UTXO 都有一个包含了 MobileCoin 账本中的 Merkle 树证明，交易被添加到区块之前输入环都已被删除，数字签名被添加到分类账中以代替完整的交易记录，为审计提供基础。

- SGX 可信执行环境

  所有的 MobileCoin 交易都在安全的在 SGX 可信环境中验证。SGX 可信执行环境对于外界来说是一个不透明的盒子，即时拥有 SGX 硬件也无法查看里面的信息，从而可以实现将交易记录追加到区块链同时丢弃诸如环签名等识别信息。

  SGX 还支持一种成为“远程认证”的特性，可远程识别出节点服务器 SGX 可信执行环境中正在运行的软件及其哈希值，所有节点在建立加密连接之前先进行远程认证，具体而言可以要求节点在 SGX 环境中签名回执消息来证明。远程认证使得整个 MobileCoin 账本和代码都在可信的环境中执行，不受恶意代码攻击。

- 密钥管理

  在 SGX 可信执行环境中运行的 MobileCoin 节点能够为用户安全地管理密钥。客户端先通过远程认证安全的连接到 MobileCoin 节点，再将私钥和简短 6 位数字 PIN 码加密传输到节点，节点收到后在可信执行环境中加密保存，至此用户 + 6 位数字 PIN 码完成了对私钥的映射关系。通过这种方式用户的私钥被安全的存储在节点中，在应用被重新安装时得以恢复，无需信任节点，更不必记住没有规律无法脑记忆的恢复助记词。

- 隐私保护

  MobileCoin 整体设计与 Signal 一脉相承，通过密码学和技术保障数据公开不可逆又最大限度的保障了用户的隐私，真正将隐私的决定权交给用户，用户可以永远不公开交易记录，也可以自主向特定政府机构或组织提交自己的交易记录用于审计等用途。

  MobileCoin 通过 CryptoNote 隐藏收件人，通过环签名（Ring Signature）隐藏发件人，通过环机密交易（Ring Confidential Transactions）隐藏交易金额，并且交易上链时还删除了输入数据（只保留输出和证明）。

  与 Signal 消息向前保密（Forward Secrecy）一样，MobileCoin 的历史交易也无法被破解，就算极端情况例如可信执行环境的 Bug 导致被攻破也无法解密之前的历史交易。

### 节点网络

#### 验证节点（Validator Node）

所有验证节点都必须运行在开启了 SGX 可信执行环境中，否则无法与其他节点建立连接。用户在客户端构造好交易后会把交易提交到自己信任的验证节点，验证节点验证通过后会通过 MobileCoin 网络与其他节点共享该交易。每个验证节点都有一个状态机的副本，状态机使用拜占庭共识算法以确定交易顺序并添加到账本。

#### 观察节点（Watcher Node）

观察节点在 MobileCoin 网络中扮演重要角色，除了维护区块链完整本地副本，校验每个区块的签名，监控分布式网络账本的完整性，还为钱包和交易所提供可供数据查询的 API 。

### 地址

- 一次性地址（One-time addresses）

  永远不会用用户的真实地址收钱，取而代之的是每一笔交易都生成一个唯一的一次性地址。这意味这就算知道所以用户的地址也无法观察和识别出他们之间有任何交易。

- 子地址（Subaddresses）

  用户可以根据自己的地址派生出多个子地址，子地址能够很方便的给资金分组，就像你有多张银行卡或者信用卡一样可以分开使用。

- 多签地址（Multisignature addresses）

  MobileCoin 目前不支持任何形式的多重签名，尽管理论上可以由第三方实现。

### 项目团队

#### 开发团队

MobileCoin 拥有一支以技术为核心的团队，根据 Linkedin 资料显示目前团队有 21 名成员。

- Joshua Goldbard
  CEO
  Joshua 是电信行业专家，曾为 AT＆T 等公司开发、管理、实施非常复杂的通信网络。他同时还是 Secret Party 的创始人、专注于加密货币的对冲基金 Crypto Lotus 的普通合伙人。
- Moxie Marlinspike
  技术顾问
  Moxie Marlinspike 是美国顶级的密码学专家、 Signal 创始人、前 Twitter 安全部的首席负责人。目前担任 Signal Messenger LLC 的 CEO，同时负责帮助 Signal Messenger、WhatsApp、Facebook Messenger、Skype 等集成 Signal Procotol。
- Shane Glynn
  法律总顾问
  Shane Glynn 是法学博士，在 Google 从事法律相关的工作长达 6 年，目前还是加州大学黑斯廷斯法学院兼职客座教授，喜欢了解与法律相关的新奇问题，对现有对加密货币法规有浓厚的兴趣。

#### MobileCoin 基金会

MobileCoin 基金会致力于监督、推动 MobileCoin 协议的开发和生态建设。

- Sarah Novotny
  首名董事会成员
  Sarah Novotny 是开源文化的领导者，现任 Linux 基金会董事，她曾在谷歌工作并领导 Kubernetes 项目的治理设计，主导 Node.js 基金会和 JS 基金会合并建立 OpenJS 基金会，曾任 O’Reilly 开源大会主席，近期加入微软云服务商 Azure。
- Renée DiResta
  董事会成员
  Renée DiResta 是斯坦福互联网观察实验室的技术研发经理，她的研究领域包括谣言、虚假健康信息、阴谋论等，受到国家资助。Renée DiResta 是 2019 年杜鲁门国家安全项目的安全研究员，2019 年 Mozilla媒体、错误信息与信任研究员，外交关系委员会任期成员。
- Alex Feerst
  董事会成员
  Alex Feerst 目前是 Neuralink 的总法律顾问，Neuralink 是一家开发超高带宽脑机接口的初创公司。Alex 曾在 Medium 担任法律主管和信任与安全主管长达 4 年，还担任过责任数据基金会的董事会成员，布鲁金斯学会跨大西洋错误信息工作组成员以及信托与安全专业协会的顾问。
- David Bray
  董事会成员
  David Bray 博士是 GeoTech 委员会首任主任和大西洋新技术理事会的地缘政治影响委员会主任。David Bray 博士在动荡的环境中担任过各种领导要职，包括 2000 年至 2005 年的生物恐怖主义准备和应对，2009 年在阿富汗担任两党全国委员会的无党派执行主任。《商业内幕》（Business Insider）将他评为 40 岁以下“改变世界的 24 名美国人”之一，并在 2016 年至 2021 年的世界经济论坛上被任命为全球青年领袖。
- Sara Drakeley Hall
  技术顾问委员会主席
  Sara 毕业于麻省理工学院，专门研究动画和金融行业的数学算法和软件工程。Sara 曾在迪士尼动画工作室担任动画电影技术总监，她领导过《冰雪奇缘》、《无敌破坏王》、《超能陆战队》、《疯狂动物城》、《海洋奇缘》。Sara 移居曼哈顿后，以核心软件工程师的身份，在算法和金融工程方面协助 Engineers Gate 对冲基金。
- Konstantin Richter
  技术咨询委员会成员
  Konstantin 是 Blockdaemon 的创始人兼首席执行官，Blockdaemon 是领先的区块链基础设施提供商。在过去的十年中，Konstantin 创建多家 B2B、SaaS 公司，其重点是复杂的数据模型和云原生架构。
- Michael Rodriguez
  技术咨询委员会成员
  Michael 是 DreamHost 的创始人兼首席执行官，DreamHost 是一家领先的 WordPress 托管和云服务提供商。MobileCoin 为 DreamHost 全球业务客户提供一种廉价、易于使用且安全的全球支付解决方案，帮客户减少国际支付摩擦，并提高 MobileCoin 在小型企业中的知名度。
- Tony Lai
  政策咨询委员会主席
  Tony Lai 是新加坡国立大学技术、机器人、人工智能和法律中心的高级研究员，2010 年以来，他作为斯坦福大学法律信息学中心 CodeX 的成员，并担任区块链小组的联席主席。他还是《斯坦福大学区块链法律与政策杂志》、《MIT 计算法报告》的创始编辑。
- Faisal Saeed Al Mutar
  政策咨询委员会成员
  Faisal 是伊拉克出生、屡获殊荣的反极端主义者、讽刺作家、演说家。2015 年，Faisal 因对教育的特殊贡献而获得了奥巴马总统颁发的“总统志愿服务奖”。Faisal 建立了多个在线平台，这些平台总共拥有 40 万订户和数百万的访问者，他曾担任中东和北非地区的项目经理，以协助全球封闭社会中的持不同政见者。
- Tiffiniy Cheng
  政策咨询委员会成员
  Tiffiniy Cheng 是“为未来而奋斗”（FFTF）组织的联合创始人兼董事会成员，该组织以改变互联网历史的大规模运动而闻名。

### 合规

MobileCoin 一直以“合规”为核心，行事谨慎低调，Eric Meltzer 曾在 MobileCoin 社群中表示：“MobileCoin 是他遇到对合规最为谨慎的项目之一”。

- 与比特币不同，MobileCoin 用户拥有两组公钥/私钥，与门罗币类似。一组称为 view key，可以查看地址未花费的 UXTO，另一组称为 spend key，可以使用未花费的 UXTO。view key 可用于报税和会计审计等，资产不会因为 view key 被转移。
- Moxie 从 CTO 转为技术顾问对 MobileCoin 的合规和发展起到积极作用。
- MobileCoin 创始人团队成员包括一名担任过 Google 产品顾问的律师 Shane Glynn，目前他在团队中负责确保 MobileCoin 遵守一切加密法规。
- MobileCoin 基金会有专门政策咨询委员会，帮助 MobileCoin 更好的合规。
- MobileCoin 团队对待处理与 Signal 的关系上非常谨慎。甚至 MobileCoin 团队在一些媒体于今年年初错误地报道了 MobileCoin 与 Signal 关系时，要求媒体更正：虽然 Mobilcoin 将与 Signal 合作，但正在独立于 Signal 开发。
- 2020 年 4 月 2 日在美国国会的区块链小组为立法者做有关 MobileCoin 的简报。
- 2020 年 6 月 MobileCoin 向美国专利商标局提交专利申请，该专利可用于提供安全交易网络的系统和方法，支持进行网络通信的多个节点环境，每个网络节点都具有安全处理区域。同时，该专利还包括在客户端设备配置的可执行代码的智能钱包，至少与网络一个节点建立安全数据通信。

### MOB 代币

MOB 总量 2.5 亿枚，不增发，50% 归公司创始人、团队和顾问； 15% 以 0.8 美元的价格出售给了以 Binance Labs 牵头的机构和投资人；剩下的 35% 归 MobileCoin 基金会、合作方和空投等。

受限于美国的法律、法规，律师建议暂时不要公布具体流通和分配，介于团队和机构的代币都已锁定，目前市场流通主要是一些参与私募的大户流出，预估市场实际流通应该会低于 2000 万 MOB，以下是支持 MOB 交易的交易所：

- [4swap](https://app.4swap.org/#/pair-info?base=c6d0c728-2624-429b-8e0d-d9d19b6592fa&quote=eea900a8-b327-488c-8d8d-1428702fe240) ，支持 BTC - MOB、USDT- MOB、pUSD - MOB、XIN - MOB、ETH - MOB 交易对，MOB 相关流动性超过 1800 万美元
- [BigONE](https://big.one/trade/MOB-USDT)，支持 MOB - USDT 交易对
- [FTX](https://ftx.com/trade/MOB/USD)，支持 MOB - USDT 交易对

### 体验钱包

![MobileCoin Desktop Wallet](https://pic3.zhimg.com/80/v2-1341345d690ad873b1fd2fa4e86fd8ea_1440w.jpg?x-oss-process=image/resize,w_768)

MobileCoin 现在还只有面向 macOS 和 Linux 的桌面端钱包，[可在 GitHub 下载](https://github.com/mobilecoinofficial/desktop-wallet/releases) ，也可以[使用 Mixin Messenger 手机版](https://mixin-www.zeromesh.net/messenger) 来存储交易 MOB 资产。

### MobileCoin 社区

- Twitter：https://twitter.com/mobilecoin
- Reddit：https://www.reddit.com/r/mobilecoin
- Medium：https://medium.com/mobilecoin
- Github: https://github.com/mobilecoinfoundation
- MobileCoin 中文群：https://mobilecoin.mixinbots.com （超过 2000 人）

### 常见问题

- MobileCoin 支持挖矿吗？

  不支持，运行节点没有挖矿奖励。

- 验证节点可以运行在没有开启 Intel SGX 服务器上吗？

  可以运行，但是无法与其他节点建立连接，远程认证通不过。

- 观察节点可以运行在没有开启 Intel SGX 服务器上吗？

  可以，运行观察节点校验块签名不需要 SGX 可信执行环境。

- MobileCoin 什么时候会被集成到 Signal？

  不确定，短则几个月长则一两年，主要看合规方面的进展。MobileCoin 有非常强大的法律合规团队，并且从一开始就是往合规的方向在做。

- MOB 现在流通量是多少？

  不知道，在完全合规以前律师不建议公布具体的代币分配和流通量，估计约有 2000 万。

- MobileCoin 什么时候上币安？

  MobileCoin 集成到 Signal 是很关键的一环，集成到 Signal 之后就可以借力 Signal 的 4000 万月活用户发展，那个时候，MobileCoin 作为币安领投的项目，肯定会上币安的。

- MobileCoin 这个项目看起来和 Mixin 很像，有什么区别吗？

  MobileCoin 是一个隐私支付协议，目标是和 Signal Protocol 一样集成到各大通讯软件，例如 WhatsApp、Facebook Messenger 等，Signal Messenger 将会是第一个被集成的通讯软件；
  Mixin Network 是一个 Layer 2 专注跨链资产的网络，Mixin Messenger 是 Mixin Network 第一个集成了 Mixin 钱包的 DApp。

  这两个项目的区别很大，只是凑巧两个典型范例很近似，都是端对端加密通讯 + 加密钱包。

## 项目分析

### 这世界真的需要这东西吗？

MobileCoin 希望能够实现安全、私密、简单、便宜的资金储存和转移。这世界永远需要对资金进行储存和转移，如果一种东西可以在某一方面比其他的同类竞品有优势，它就会有一定的需求。

### 它解决了什么原本没有被解决的问题？

在以往的类似项目中，Bitcoin 已经实现了“安全”，Monero、Dash、Zcash 实现了“私密”、“便宜”，但它们都不够“简单”，无论是钱包还是地址，对不大了解技术的普通人来说，都相对繁难。MobileCoin 希望能实现像发送邮件一样简单的使用体验，且其计划与现有的广泛使用的即时通讯应用结合，这一愿景一旦实现，它就能在加密数字货币世界站稳脚跟。

### 去中心化在这件事上真的必要吗？

没有“去中心化”，就无法实现“安全”、“私密”。在以往的类似项目中，PoW 和 PoS 是两种历经检验的去中心化共识方案，虽然它们都有可能因为去中心化程度不足被攻击，但只要整个网络大到一定程度，攻击能获得的利益和攻击发生的可能性都会大大减小。MobileCoin 共识协议属于 FBA 协议的去中心化共识方案，这一方案在保证安全的情况下，通过控制节点数目和可信性，提升了交易速度，降低了交易成本。

### 它在多大程度上更接近一个 DAC (Decentralized autonomous corporation，去中心化自治公司)？

MobileCoin 的主网已经正常运行，它只要能接入 Signal 和其他即时通讯应用，让一部分人认可 MobileCoin 网络代币的价值，就会开始依靠社群自然运转。另外，MobileCoin 对合规的重视，使得其在发展的每个环节都与现实的政治系统达成了一致，这令其有可能规避 Monero、Dash、Zcash 等先行者落入的困境。

### 如果我们决定投资，那么我们应该用我们资金的多大比例去投资？

MobileCoin 拥有超豪华的技术团队和法律团队，也有顶级的交易所和投资机构投资。它眼下的问题是什么时候能够全面接入 Signal，实现“落地”。MobileCoin 公开资料不多，其中大部分都是代码，节点的惩罚机制不明确，经济模型不明朗，流通量不透明，合规要求暂时不便透露，这都是明确的风险。整体来讲，由于 MobileCoin 已经开发了很久，主网成功运行了，与 Signal 的整合也在部分地区实现了，故而其技术层面上不大容易失败，至于经济层面，还要看用户的接纳程度。

可以考虑用整体资金的 5% 投资 MobileCoin，逢低买入、中长期持有，MobileCoin 上线主流交易所后很可能会有一波非理智的拉升，出现这一情况时可以考虑少量卖出，再在（发生下跌）之后买回，降低持仓成本。