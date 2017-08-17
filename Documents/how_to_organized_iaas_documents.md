# 如何组织基础设施云平台的文档相关

知识图谱从来就是艰难的，只有最有经验的人才会有条有理的分享出来。越是抽象的事物，越是难以使用文字准确的表达，即使表达的很清楚，理解也会有差异，更不用提多个事物累积起来会产生多深的误解了。

## 什么是文档？

文档就是利用这个世界上的某一种完善的文字来描述一样事物，按照人类可以理解的方式，介绍其分类、命名、操作步骤、原理叙述、历史溯源、使用场景、案例等等。

以上是我自己为文档下的一个定义，不知道是否完备，但是这就是我所理解的文档。

```
I've signed thousands of documents, but never read them.  
                                       --- Terry McAuliffe
```

### 何又为云计算的文档

这个世界上很多事物都使用文档，即使连日常生活中最为简单的牙刷也是有文档的，它有使用说明，适应条件、禁忌条件、安全说明等等。那么，复杂如云计算的业务、产品的文档该如何组织？该如何细分和归类？

面对错综复杂的云平台产品，彼此依赖、缠绵还松耦合，怎么搞？还好，人们早就发明了相互引用，这也是当初WWW最为原始的发明动力。于是，从整个文档体系来讲，每一个细节都是你中有我，我中有你的相互引注。

那么，我们不妨罗列为以下部分的文档：

* 架构之路：以设计的思路，概览的高度，多方视野来看待云计算平台，如何满足业务需求，降低成本，发挥自己的聪慧之处，找出最完美的技术设计。
* 定价： 所有人都是希望获得性价比最高的产品，那么必须以合理的价格体系为原则。不欺诈，公平买卖。
* 适用场景：或者叫做解决实际的问题，云计算平台并不能解决所有问题，这个世界总是存在迷人的问题，那么云计算平台解决的是什么问题？
* 案例：绝大多数人都不是敢于第一个，或者前几个吃螃蟹的人，更何况是放在某个集体中来做决策，相对要保守。那么必然就观望，观望都有谁采用了。效果如何？案例的精彩之处也是新技术的卓越营销之道。
* 白皮书： 以言简意赅的方式将产品的特色、原则、适用体系、风格讲明白了，就是一本绝佳的白皮书。
* 开发者文档： IT 开发人员是一群特殊的群体，有着各自不同的爱好和风格，也经常站各种阵营：Java、.NET、Python，编程语言，然后是各种IDE：Eclipse、Emacs、Vim，不过这些你都可以忽略，不能忽略的是API和SDK以及命令行的提供，文档一定要容易查，符合各个阵营的风格。请记住：**如果你没有准备好了一份高质量的文档，那么就不要提供。**
* 向导指南：详细的步骤，该如何操作才能达到某个目的的详尽描述。
* 管理员手册： 这是一份交钥匙的说明，管理一切能管理的，好的管理员手册要吊足胃口，由浅入深，有很多管理员是会晋升到开发者的。
* 文章与教程： 这就像是填补管理员手册、用户场景、案例之间的缝隙一样，以多样性出现，将某些功能发挥至极致。
* 入门或新手指南： 这一部分是为小白准备的，这个世界不是所有人都是云计算的专家和从业者，也有一些是售卖糖果的销售，最好是有视频录制，以比喻的方式告诉大家什么是云计算？能用来干什么？解决了什么问题？会花多少钱？如何找到青云的相关专家进行咨询？
* 备忘录/memo: 这是对自己的架构师看的，比如一个虚拟主机最多支持多少块硬盘？

> 笔者注： 关于售卖软件产品，比如OpenStack的厂家、Linux商业版的厂家等，所提供的文档要稍有不同。有机会的话，笔者会撰文另行介绍。

## 读者是怎么思考的？

绝大多数人是不看文档的，是的，很不幸这是一句实话。因为大多数人想的是：反正有人看，到时候我问就可以了；我用到的时候再看就是了。这就是为什么Mac的产品，默认是之有很少文档的，但是遇到问题之后，再去查才会发现有大量翔实的在线文档。

言归正传，和基础设施云计算平台相关的读者，按照职位角色来区分的话，大约有如下几种：

* 企业IT高管：架构、案例、场景、定价。
* 架构师：需要架构设计、技术边界、各种白皮书。
* 系统管理员：详尽的管理手册，能够满足日常任务的指南
* 开发人员： SDK、API、命令行等关键文档，最好还应该有开发环境的配置，比如配上流行的很酷的Atom。
* 财务人员： 当然最关心的是价格了。不过这个要和IT主管一起来看。

他们是如何思考的？其实说实话，我不知道他们是怎么思考的。或者说很难以一种职业角色来探究一个人的思考习惯，这非常难以捕获所有的共性，依据每个人的成长环境、文化氛围、习俗、个人习惯、政治环境等等。换句话说，基本上无法穷尽这些的想法的。那么只能在摸索着前进，尽可能的按照职位的职责是什么来划分文档的边界。

```
Change your thoughts and you change your world.
                       --- Norman Vincent Peale

```

## 云计算平台有多少种产品？假如它们以满足业务架构的话，能玩出多少种花样来？

<table>
  <tbody>
    <tr>
      <th>产品家族</th>
      <td align="center">产品分类</td>
      <td align="right">具体产品</td>
    </tr>
    <tr>
      <th rowspan="10">QingCloud 云平台</th>
      <td>计算</td>
      <td align="center">
        <ul>
         <li>虚拟主机</li>
         <li>容器主机</li>
         <li>物理主机</li>
         <li>GPU主机</li>
         <li>专属宿主机</li>
         <li>映像</li>            
        </ul>
      </td>
    </tr>
    <tr>
      <th>网络</th>
      <td align="center">
      <ul>
       <li>基础网络</li>
       <li>私有网络VPC</li>
       <li>SDN网络直通</li>
       <li>网络流量镜像</li>
       <li>SDN监控和诊断</li>
       <li>内网域名服务</li>
       <li>弹性公网IP</li>
       <li>Region VPC</li>
       <li>负载均衡</li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>存储</td>
      <td align="center">
      <ul>
       <li>块存储</li>
       <li>Virtual SAN</li>
       <li>Virtual NAS</li>
       <li>备份</li>       
      </ul>
      </td>
    </tr>
    <tr>
      <td>数据库</td>
      <td align="center">
      <ul>
       <li>MySQL</li>
       <li>MySQL Plus</li>
       <li>PostgreSQL</li>
       <li>MongoDB</li>
       <li>RadonDB</li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>Caching</td>
      <td align="center">
      <ul>
      <li>Redis Cluster</li>
      <li>Memcached</li>
       <li>Redis</li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>大数据处理</td>
      <td align="center">
      <ul>
       <li>Zookeeper</li>
       <li>Spark</li>
       <li>Storm</li>
       <li>Hadoop</li>
       <li>HBase</li>
       <li>ElasticSearch</li>
       <li>RabbitMQ</li>
       <li>Kafka</li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>容器平台</td>
      <td align="center">
      <ul>
       <li>Kubernetes</li>
       <li>Harbor</li>
       <li>etcd</li>
       <li>公有镜像仓库</li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>消息总线服务</td>
      <td align="center">
      <ul>
       <li>Serverless</li>
       <li>通知服务</li>
       <li>队列服务</li>
       <li></li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>安全</td>
      <td align="center">
      <ul>
       <li>SSH密钥</li>
       <li>防火墙</li>
       <li>WAF</li>
       <li>账户安全</li>
       <li>资源协作</li>
       <li>子账户管理（IAM）</li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>监控与运维</td>
      <td align="center">
      <ul>
       <li>自动伸缩</li>
       <li>资源编排</li>
       <li>定时器</li>
       <li>操作日志</li>
       <li>标签</li>
       <li>回收栈</li>
       <li>服务健康状态监控</li>
       <li>监控告警</li>
      </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="4">App center</th>
      <td>IoT</td>
      <td align="center">
        <ul>
         <li>EMQ</li>
         <li>wrtnode</li>
         <li>edgeon</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>Security</td>
      <td align="center">
      <ul>
       <li>360 安全</li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>AI</td>
      <td align="center">
      <ul>
       <li>第四科技</li>
       <li>桃树科技</li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>其它</td>
      <td align="center">
      <ul>
       <li></li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="3">QingStor 存储超融合</th>
      <td>对象存储</td>
      <td align="center">
        <ul>
         <li>对象存储</li>
         <li>NAS 网关</li>
         <li>企业网盘</li>
         <li>冷存储</li>
         <li>音视频服务</li>
         <li>CDN服务</li>
         <li>DNS服务 </li>
         <li>DDoS防护 </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>ServerSAN存储</td>
      <td align="center">
      <ul>
       <li>NeonSAN</li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <td>分布式NAS</td>
      <td align="center">
      <ul>
       <li>分布式NAS</li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="2">QingCloud 基础设施及骨干网服务</th>
      <td>托管云</td>
      <td align="left">
        <ul>
         <li>托管机柜</li>
         <li>数据中心组网</li>
         <li>出口带宽IP</li>
         <li>防D服务</li>
         <li>物理机服务</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>混合云接入</td>
      <td align="left">
      <ul>
       <li>市内光纤连接</li>
       <li>市内大容量专线</li>
       <li>长途 MPLS/VPN 专线</li>
       <li>长途 SDH/MSTP 窄带专线</li>
       <li>SD-WAN </li>
       <li></li>
       <li></li>
      </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="1">QingCloud云管平台</th>
      <td>QingCloud云管平台</td>
      <td align="center">
        <ul>
         <li>混合云</li>
         <li>VMware</li>
         <li>OpenStack</li>
        </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="1">QingCloud Express</th>
      <td>QingCloud 易捷版</td>
      <td align="center">
        <ul>
         <li>x86 平台</li>
         <li>ARM 平台</li>
         <li></li>
        </ul>
      </td>
    </tr>
    <tr>
      <th rowspan="1">移动App控制台</th>
      <td>移动App控制台</td>
      <td align="center">
        <ul>
         <li>iOS</li>
         <li>Android</li>
         <li>Windows phone</li>
        </ul>
      </td>
    </tr>

  </tbody>
</table>

在青云QingCloud几乎能够找到你任何所需要的产品和服务，只需要稍加组合，就可以满足你的业务需求。比如说你的业务是web站点、大数据处理、文件存储与协作、传统办公应用迁移等等。

而且随着岁月的推移，产品和服务会逐年增多：

![](https://raw.githubusercontent.com/lijiangsheng1/yunify/master/media/aws_new_product.png)

每款产品应该包含的文档有：

* 用户手册
* API 文档（这是云计算，永远记住这一点）
* 命令行参考
* 迁移手册（如果需要的话）

提供的格式应该包含：

* HTML/HTML singel
* PDF
* Kindle/help （可选）

## 阅读路径

看完上述的阐述，你一定会发现，无法在一本册子或一个页面将所有的这些内容囊括进来。唯一的可能就是专心的描述其中一个，然后以超链接的形式引用其它，相互穿插！直到满足实现某个功能为止。

我这里举一个架构师的例子，试图来说明，如何设计和描绘“阅读路径”，类似读书清单一样。

作为一名架构师，要对自己的业务场景所需要的基础设施和服务具备相当的理解，可能需要读到的QingCloud的内容有：

QingCloud最佳实践-->如何在QingCloud上部署-->如何在QingCloud中进行扩展-->构建容错的应用-->如何做灾备-->如何做安全-->主机/容器规格-->镜像指南-->去除单点故障-->如何利用预留资源省钱。

云计算是区别与传统的架构设计的，比如云计算有如下几条原则：

* 扩展性第一
* 用完即丢的资源，而不是固定的服务器
* 自动化
* 松耦合
* 提供服务，而不是服务器。
* 使用缓冲服务
* 安全要牢记在心

基于这些原则，寻找最佳的阅读路径。

若是将驾驭云计算平台的知识比喻为征服一座移动迷宫的话，身在迷宫深处的你选择阅读路径上要谨慎小心，从无数隐形的开源知识，到基础设施就是代码，再到快速发展的创新业务，某种程度上说，阅读路径决定了你能够多久爬到驾驭的顶峰，而不是在迷宫中陷入迷惘和惆怅。

## 结语

文档要随着技术的进化而进化，也就是说文档具有时效性，不得不面临一直持续不断的更新，直到某个产品的生命周期终止。

从更高的层次上讲，文档是知识传播的载体，有多少人愿意写？又有多少人愿意读？写的好不好？效果怎么样？还得不断的完善和补充，这是一条持久更新、调整的路线。

云计算，文档才是永远的痛。
