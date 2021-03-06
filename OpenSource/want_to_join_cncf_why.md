# 加入？还是不加入？CNCF是什么鬼

## 一份来自企业的思考脑图

> Redmonk 公司的一份调查显示，财富100强企业中有71%的企业正在使用Kubernetes。其中一个最大的原因就是 Kubernetes 这项技术承诺人们从某个基础设施迁移到其它平台是非常容易的，假如你是在Google的云平台下运行的，如果想迁出的话随时，不管是AWS、Azure还是自建。
>              -- 摘自[财富杂志对AWS加入CNCF的报道](http://fortune.com/2017/08/09/amazon-google-cloud-foundation/)

## 什么是CNCF？

CNCF 是 Cloud Native Computing Foundation的首字母缩写，翻译成汉语为：**云原生计算基金会**。

在2015年6月所创立了 OCI 之后不久，一些云计算市场上关键的角色开始意识到需要在 OCI 所提供的运行时环境之上建立一些标准。尽管在单个容器的核心管理上的重要性已经达成一致，但是在这之上更高层次的容器管理也非常的有必要。于是，在2015年8月，在 Linux 基金会合作项目的羽翼之下，云原生计算基金会（CNCF）成立了。

CNCF 对于自身的定义非常的清晰：**基于CNCF的[技术](https://www.cncf.io/projects)，应用程序可以在不同的云平台上移植，无论这些云平台是私有的还是公有的，没有任何厂商可以锁定用户，用户可以随心所欲的将自己的整个web-scale应用迁移。**

### 目前所托管的关键开源项目

* Kubernetes : 编排
* Prometheus ：监视
* OpenTracing：追踪
* Fluentd： 日志
* Linkerd： 服务mesh
* GRPC： 远程过程调用
* CoreDNS： 服务发现
* Contrained： 容器运行时
* rkt： 容器运行时
* CNI ： 网络

> 在后GitHub时代，仅仅提供软件仓库、邮件列表和站点已经不再适用于软件基金会了：需要提供更多的、更好的服务，才能吸引更多的企业采用、开发人才。

### 目前中外都有那些重量级的厂商加入？分别是什么级别？

Gartner 基础设施公有云平台的魔力象限，作为领导者的前三名都是CNCF 的铂金会员：AWS、Google、Azure。我这里就不穷举所有厂商了，有心的读者请移步：[CNCF 会员单位](https://www.cncf.io/about/members/)。我这里仅列出国内的厂商：

铂金： 华为

黄金： 阿里云、腾讯云

银牌：才云、超算云、DaoCloud、Easytack、谐云科技、时速云

## 何为基金会治理模式？

Nathen Harvey 在 Information Week 中的文章中指出了三个问题：“项目应该由商业的赞助商驱动还是外围的贡献者驱动？商业利益是否应该凌驾于社区的意愿之上？该如何以及在哪里为商业实体和开源社区之间划上一个明确的界线？”

这三个问题确实是异常的尖锐，回答起来就显得非常的关键，但通过基金会的模式，开放的治理可以解决大多数的问题。

* Apache 软件基金会
* Linux软件基金会
* Opentack 基金会
......

限于篇幅和时间关系，我们无法做到涵盖所有的开源基金会，以及他们所支持的开放管理和相关的流程，以及围绕和商业的合作等等。但是，仍然有一些事值得我们为之写上他们的名字的，正式因为他们，才能够让整个的开源更加的健康。他们有：

* 自由软件基金会（FSF）
* Creative Commons
* Eclipse 基金会
* Internet Systems Consortium (ISC)
* Mozilla 基金会
* 开源促进会（OSI）
* 软件自由法律中心（SFLC）

还有我们无法在这里一一列出的，他们都在相应的开源项目和促进中扮演着各自的角色，尤其是在商业和独立兴趣之间的平衡。

## 关于会员之权益和每年的会费

### 权益

#### 业内领先

* 共同打造云原生市场
* 和其它CNCF的会员建立联系
* 参与CNCF线下会议和演讲机会
* 加入特别兴趣小组、CNCF 技术委员会和最终用户委员会

#### 市场机会

* Benefit from and participate in CNCF press relations, analyst relations and marketing efforts
* nEngage with your peers on the marketing committee
* Feature your products and applications based on CNCF technology at key industry events, website and marketing collateral
* Receive a 3% sponsorship discount on any CNCF-produced events (5% when committing to multiple events)
* Participate in our hosted projects and attend our events, meetups, and roadshows

#### 技术领先

* Contribute to and influence the direction of CNCF technologies
* Drive leadership for future enhancements and extensions that matter for your business
* Ability to guide feature sets in requirements documents and future technology roadmaps

#### 学习和鼓励

* Engage and collaborate with the membership community to enhance your cloud roadmap
* Work together across company lines and industries
* Design your applications and services to work with the cloud native platform of orchestrated containers as part of a microservices architecture
* Create products & apps secure in the knowledge that they will be interoperable in the future
* Ability to serve as a CNCF Ambassador


![](https://www.cncf.io/wp-content/uploads/sites/2/2016/09/cncfscope.png)

### 会员级别的差异

#### 铂金会员

* 指定1名成员代表参与CNCF 的治理委员会。
* 指定1名成员代表，任何子委员会的选票或者是治理委员的积极人士。
* 在会员展示中享受最显眼的位置，包括在网站上
* 访问linux基金会邀请制的开源领导力峰会
* 正在与CNCF执行董事和工作人员进行个人接触

#### 黄金会员

* 每5家黄金会员可指定1名成员代表参与CNCF 的治理委员会。
* 加强CNCF执行董事和工作人员的参与

#### 银牌会员

* 指定3名成员代表参与CNCF 的治理委员会。


更多细节，请参考：[会员详细列表](https://www.cncf.io/about/charter/)

### 会费


会员级别  | 年费 |备注 |
 ------------ | :-----------: | -----------: |
  铂金     |         $370,000       |
  黄金     | $120,000   |          
  银牌和最终用户会员            |   $50,000: 5,000 employees and above |
   | $45,000: 3,000 – 4,999 |
   |$35,000: 1,000 – 2,999  |
   |$25,000: 500 – 999 |
   |$15,000: 100 – 499 |
   |$10,000: 50 – 99  |
   | $7,000: Less than 50 employees |  
研究机构和非盈利机构    |$500 Non-profit or $1,000 Academic |      

## 那到底加还是不加？

正如 CNCF 官方所说，你根本就不需要成为所谓的会员。CNCF 非常欢迎参与项目建设、在线讨论、邮件来往，以及线下的各种活动。

参考资料：

1. [CNCF常见问答](https://www.cncf.io/about/faq/)
2. [精心布局的开源](http://www.ocselected.org/posts/opensource/open_by_design/)
3. [CNCF会员细则](https://www.cncf.io/about/charter/)
4. [CNCF演讲介绍](https://docs.google.com/presentation/d/1BoxFeENJcINgHbKfygXpXROchiRO2LBT-pzdaOFr4Zg/edit#slide=id.p4)
