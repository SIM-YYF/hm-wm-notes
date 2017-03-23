# 项目架构设计/演变

为什么要强调项目的架构的设计呢?

- 由于项目的庞大,会导致:
    * 代码结构混乱
    * 层次不清
    * 各业务技术方案不统一
    * 冗余代码充斥
    等问题
    
- 在面试时,有时候面试官问道项目的架构设计时, 无从谈起


    

因此, 由必要说一下,项目的架构设计.从而,让大家对实战项目架构设计的理解.

    
## MVC框架设计和实现

首先, 我们一起回顾一下MVC框架设计模式, 之前我们曾经接触过MVC框架. 比如: 智慧北京.
其实,在智慧北京开发中, 业务层其实是一种非标准的 MVC 架构:

由于Activity / Fragment 承担了 View 和 Controller 的职责
![](/assets/mvc非标准的流程图.png)
    
    
但是随着业务的不断迭代,我们慢慢发现业务层这种非标准的 MVC 架构带来了种种影响团队开发效率的问题：

* Activity 和 Fragment 越来越多的同时承担了 Controller 和 View 的职责，导致他们变得及其臃肿且难以维护；
* 由于 Controller 和 View 的揉合，导致单元测试起来很困难；
* 回调嵌套太多，面对负责业务时的代码逻辑不清晰，难以理解且不利于后期维护；
* 各层次模块之间职责不清晰等等


由于以上的架构的设计存在一些缺陷,因此,我们就引入的MVP框架设计
和实现
    
    
## MVP框架的设计和实现
    
由于Google 推出的基于 MVP 架构的案例,我们发现 MVP 架构能解决现MVC框架带来的很多问题.

- 首先我们了解一下,MVC框架的流程图:
![](/assets/mvp标准框架图.png)


* View Layer: 只负责 UI 的绘制呈现，包含 Fragment 和一些自定义的 UI 组件，View 层需要实现 ViewInterface 接口。Activity 在项目中不再负责 View 的职责，仅仅是一个全局的控制者，负责创建 View 和 Presenter 的实例；

* Model Layer: 负责检索、存储、操作数据，包括来自网络、数据库、磁盘文件和 SharedPreferences 的数据；

* Presenter Layer: 作为 View Layer 和 Module Layer 的之间的纽带，它从 Model 层中获取数据，然后调用 View 的接口去控制 View；

* Contract: 参照 Google 的 Demo 加入契约类 Contract 来统一管理 View 和 Presenter 的接口，使得某一功能模块的接口能更加直观的呈现出来，这样做是有利于后期维护的。



大家可以考虑一个问题?

是不是升级到了 MVP 架构就高枕无忧了呢？不是这样！
因为MVP 架构也会带来以下新的问题:

* 由于大量的业务逻辑处理转移到了 Presenter 层，在一些复杂的业务场景中 Presenter 同样会变得臃肿难懂. 

如何解决Presenter 臃肿,不方便维和呢?
大家可以分析以下流程图, 基于MVp框架的扩展:

![](/assets/1.png)

细心的同学可能注意到了前面的架构图中的 Model 层有个 Data Repository 模块.
Data Repository 模块作用是什么?

    * 可以将原本由 Presenter 处理的部分逻辑转移到这里来处理，包括数据的校验、部分单纯只与数据相关的逻辑等等
    * 向 Presenter 屏蔽数据处理细节，比如作为 Presenter 就不必关心 Model 层传递过来的数据到底是来至网络还是来至数据库还是来至本地文件等等


通过以上的MVP框架的扩展,我们就很好的解决presenter的臃肿,不方便维护的问题了.


但是, 随着项目的庞大,有些功能需要重用,比如:网络请求功能,作为一个公共的模块,让其他项目或者模块使用. 为了避免重复造轮子,节省开发维护成本. 这里我们提出一些新的概念.
就是组件化, 模块化, 插件化

## 组件化/模块化

### 组件化

为了避免各个业务团队重复造轮子，团队中也需要有一定的技术沉淀，因此组件化是必须的

- 组件化: 就是基于可重用的目的，将一个大的软件系统拆分成一个个独立组件。


- 组件化的带来的好处不言而喻：

    * 避免重复造轮子，节省开发维护成本；
    * 降低项目复杂性，提升开发效率；
    * 多个团队公用同一个组件，在一定层度上确保了技术方案的统一性。
    
    
- 开发常见的组件

    * appcompat-v7
    * design
    * okhttp3
    * retrofit
    * recyclerview-v7
    * ormlite
    * eventbus
    * rxjava
    * rxandroid
    * Gson
    * picasso
    * glide
    
    
### 模块化

- 模块化: 将一个项目或者系统软件,分为多个模块.


      比如:黑马外卖: 
    
        * 登录module, 
        * 外卖超市module, 
        * 外卖海购module, 
        * 外卖支付module等


    如果不进行模块化分开,他们之间耦合性太严重.
    
    
- 模块化带来地好处

    * 业务模块间解耦
    * 单个业务模块单独编译打包，加快编译速度
    * 多团队间并行开发、测试
    * 解决好App需要单独维护的问题，降低研发成本



- App模块化设计图

为了解决项目按照模块拆分后各业务模块间解耦的问题, 这里我们把一个APP项目分为三层:

![](/assets/组件和模块化.png)
    
    
从以上设计图我们知道,分为三层:

整个项目分为三层，从下往上分别是：



        * Basic Component Layer: 基础组件层
    
        顾名思义就是一些基础组件，包含了各种开源库以及和业务无关的各种自研工具库；
    
    
        *  Business Component Layer: 业务组件层
    
        这一层的所有组件都是业务相关的，例如上图中的
        
            支付组件 tokeoutPay
            数据模拟组件 DataSimulator
            公有模块不足以构成单独组件或者模块的放在CommonBusiness中 
    
    
        * Business Module Layer: 业务 Module 层
    
        在 Android Studio 中每块业务对应一个单独的 Module。
    
        例如: 黑马外卖用户 App 我们就可以拆分成多个module:
    
        外卖Module
        商铺Module
        外卖超市Module
        等.
        
    而且, 每个单独的 Business Module 都必须准遵守前面提到的 MVP 架构。


设计技巧

通过以上分层划分, 我们可以把 Basic Component Layer 和 Business Component Layer 放在一起看做是 takeout SDK，新的业务或者项目只需要依赖 takeout SDK 就好.

这样我们就开发一套属于自己的组件管理平台，业务层可以根据自己的需求选择自己需要的组件，定制业务专属的 takeout SDK


    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    