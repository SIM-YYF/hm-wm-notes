#MVP结构的扩展

## 查看google-MVP案例

主页

[google-MVP架构案例](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)

通过案例,我们一起看一下MVP框架的设计和实现

## MVP框架的设计和实现

    
由于Google 推出的基于 MVP 架构的案例,我们发现 MVP 架构能解决现MVC框架带来的很多问题.

- 首先我们了解一下,MVC框架的流程图:
![](/assets/mvp标准框架图.png)

- mvp分层结构:

        * View Layer: 只负责 UI 的绘制呈现，包含 Fragment 和一些自定义的 UI 组件，View 层需要实现 ViewInterface 接口。Activity 在项目中不再负责 View 的职责，仅仅是一个全局的控制者，负责创建 View 和 Presenter 的实例；
        
        * Model Layer: 负责检索、存储、操作数据，包括来自网络、数据库、磁盘文件和 SharedPreferences 的数据；
        
        * Presenter Layer: 作为 View Layer 和 Module Layer 的之间的纽带，它从 Model 层中获取数据，然后调用 View 的接口去控制 View；
        
        * Contract: 参照 Google 的 Demo 加入契约类 Contract 来统一管理 View 和 Presenter 的接口，使得某一功能模块的接口能更加直观的呈现出来，这样做是有利于后期维护的。
        


