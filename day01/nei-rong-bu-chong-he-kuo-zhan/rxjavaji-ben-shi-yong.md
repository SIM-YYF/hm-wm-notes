## rxjava认识

### 什么是RxJava？

RxJava 在 GitHub 是这样说的:

 "a library for composing asynchronous and event-based programs using observable sequences for the Java VM"

什么意思呢？ 一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库。

其实， RxJava 的本质可以压缩为"异步" 这一个词。它就是一个实现异步操作的库.

### RxJava介绍和基本使用

由于RxJava 的异步实现，是通过一种扩展的观察者模式来实现的。

#### 扩展的观察者模式概念

先简述一下观察者模式：

比如：
A 对象（观察者）对 B 对象（被观察者）的某种变化高度敏感，需要在 B 变化的一瞬间做出反应

再比如：

警察抓小偷，警察需要在小偷伸手作案的时候实施抓捕。
警察是观察者，小偷是被观察者，警察需要时刻盯着小偷的一举一动，才能保证不会漏过任何瞬间

需要注意:

程序的观察者模式和以上的『观察』略有不同。

**程序的观察者不需要时刻盯着被观察者（例如 A 不需要每过 2ms 就检查一次 B 的状态），而是采用称为订阅(Subscribe)的方式，告诉被观察者：我需要你的某某状态，你要在它变化的时候通知我**

例如：
 Android 开发中一个比较典型的例子是点击监听器 OnClickListener 。对设置 OnClickListener 来说， View 是被观察者， OnClickListener 是观察者，二者通过 setOnClickListener() 方法达成订阅关系。订阅之后用户点击按钮的瞬间，Android Framework 就会将点击事件发送给已经注册的 OnClickListener 。
 
**采取这样被动的观察方式，既省去了反复检索状态的资源消耗，也能够得到最高的反馈速度**
 
 

OnClickListener 的模式大致如下图：

![](/assets/rxjava-01.png)

如图所示:

通过 setOnClickListener() 方法，Button 持有 OnClickListener 的引用（这一过程没有在图上画出）
当用户点击时，Button 自动调用 OnClickListener 的 onClick() 方法。

另外，如果把这张图中的概念抽象出来:

    Button -> 被观察者、
    OnClickListener -> 观察者、
    setOnClickListener() -> 订阅函数，
    onClick() -> 事件


#### RxJava 相关概念


##### RxJava 有四个基本概念：

- Observable (被观察者)
- Observer (观察者)
- subscribe (订阅)事件
- 事件

      onNext(): 普通事件,相当于onClick() / onEvent()
      onCompleted():队列完结事件
        RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。
        RxJava 规定，当不会再有新的 onNext() 发出时，需要触发 onCompleted() 方法作为标志。
       
      onError(): 队列异常事件
        在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出

      注意:
          1. 在一个正确运行的事件序列中, onCompleted() 和 onError() 有且只有一个，并且是事件序列中的最后一个。
          2. onCompleted() 和 onError() 二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。
     
     

#### Rxjava中 Observable /Observer /subscribe三者之间的关系

Observable 和 Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。


#### Rxjava观察者模式原理图

![](/assets/Rxjava观察者模式原理图.png)



#### Rxjava基本使用


##### 创建 Observer(观察者)

Observer 即观察者，它决定事件触发的时候将有怎样的行为。 
RxJava 中的 **Observer 接口**的实现方式：
 
```java

 Observer<String> observer = new Observer<String>() {
            @Override
            public void onNext(String s) {
                Log.i(tag, "Item: " + s);
            }
            @Override
            public void onCompleted() {
                Log.i(tag, "Completed!");
            }
            @Override
            public void onError(Throwable e) {
                Log.i(tag, "Error!");
            }
        };
```


除了 Observer 接口之外，**RxJava 还内置了一个实现了 Observer 的抽象类：Subscriber**。 
Subscriber 对 Observer 接口进行了一些扩展，但他们的基本使用方式是完全一样的：



```java

Subscriber<String>  subscriber = new Subscriber<String>() {
            @Override
            public void onNext(String s) {
                Log.i(tag, "Item: " + s);
            }
            @Override
            public void onCompleted() {
                Log.i(tag, "Completed!");
            }
            @Override
            public void onError(Throwable e) {
                Log.i(tag, "Error!");
            }
        };

```


 
通过两种实现方式，实质上，在 RxJava 的 subscribe 过程中，**Observer 也总是会先被转换成一个 Subscriber 再使用**。
所以如果你只想使用基本功能，选择 Observer 和 Subscriber 是完全一样的

##### 创建 Observable

Observable 即被观察者，**它决定什么时候触发事件以及触发怎样的事件。**


RxJava 使用 create() 方法来创建一个 Observable ，并为它定义事件触发规则：



```java
Observable observable = Observable.create(new Observable.OnSubscribe<String>() {
            @Override
            public void call(Subscriber<? super String> subscriber) {
                subscriber.onNext("Hello");
                subscriber.onNext("Hi");
                subscriber.onNext("Aloha");
                subscriber.onCompleted();
            }
        });
        

```


 
可以看到，这里传入了一个 OnSubscribe 对象作为参数。

**OnSubscribe 会被存储在返回的 Observable 对象中，它的作用相当于一个计划表，当 Observable 被订阅的时候，OnSubscribe 的 call() 方法会自动被调用，事件序列就会依照设定依次触发（对于上面的代码，就是观察者Subscriber 将会被调用三次 onNext() 和一次 onCompleted()）**。

这样，由被观察者调用了观察者的回调方法，就实现了由被观察者向观察者的事件传递，即观察者模式。


create() 方法是 RxJava 最基本的创造事件序列的方法。
基于这个方法， RxJava 还提供了一些方法用来快捷创建事件队列。


* just(T...): 将传入的参数依次发送出来
    
    Observable observable2 = Observable.just("Hello", "Hi", "Aloha");


* from(T[]) / from(Iterable<? extends T>) : 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来
        
    String[] words = {"Hello", "Hi", "Aloha"};
        Observable observable3 = Observable.from(words);




##### Subscribe (订阅)
创建了 Observable 和 Observer 之后，再用 subscribe() 方法将它们联结起来，整条链子就可以工作了。

代码形式很简单：



```java

 //订阅，建立订阅关系
 observable.subscribe(observer);

```

建立订阅关系, 执行的流程图为: 

![](/assets/订阅的流程图.png)


#### Rxjava的使用案例

- 打印简单字符串


```java
String[] names ={"a", "b"};
Observable.from(names) .subscribe(new Action1<String>() {
            @Override
            public void call(String name) {
                Log.d(tag, name);
            }
        });


```




- 由 id 取得图片并显示



```java
final  int drawableRes = R.mipmap.ic_launcher;
final ImageView imageView = new ImageView(this);
Observable.create(new Observable.OnSubscribe<Drawable>() {
    @Override
    public void call(Subscriber<? super Drawable> subscriber) {
        Drawable drawable = MainActivity.this.getResources().getDrawable(drawableRes);
        subscriber.onNext(drawable);
        subscriber.onCompleted();
    }
}).subscribe(new Observer<Drawable>() {
    @Override
    public void onNext(Drawable drawable) {
        imageView.setImageDrawable(drawable);
    }

    @Override
    public void onCompleted() {
    }

    @Override
    public void onError(Throwable e) {
        Toast.makeText(activity, "Error!", Toast.LENGTH_SHORT).show();
    }
});

```


#### Rxjava线程控制

- Scheduler 的 API 

在RxJava 中，Scheduler ——调度器，相当于线程控制器，RxJava 通过它来指定每一段代码应该运行在什么样的线程。
RxJava 已经内置了几个 Scheduler ，它们已经适合大多数的使用场景

        Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
        Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
        Schedulers.io(): I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，区别在于 io() 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 io() 比 newThread() 更有效率。
        另外， Android 还有一个专用的 AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行



- 线程控制

有了这几个 Scheduler ，就可以使用 subscribeOn() 和 observeOn() 两个方法来对线程进行控制了。 

     subscribeOn(): 
     指定 subscribe() 所发生的线程，即 Observable.OnSubscribe 被激活时所处的线程。或者叫做事件产生的线程。 

     observeOn(): 
     指定 Subscriber 所运行在的线程。或者叫做事件消费的线程。

- 线程控制案例说明

**打印数字**

比如以下代码:

```java

Observable.just(1, 2, 3, 4)
        .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
        .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
        .subscribe(new Action1<Integer>() {
            @Override
            public void call(Integer number) {
                Log.d(tag, "number:" + number);
            }
        });

```
解析说明:

上面这段代码中，由于 subscribeOn(Schedulers.io()) 的指定，被创建的事件的内容 1、2、3、4 将会在 IO 线程发出；

而由于 observeOn(AndroidScheculers.mainThread()) 的指定，因此 subscriber 数字的打印将发生在主线程 。
 


**加载图片**

在子线程中解析图片,在主线程中显示图片

代码如下:



```java
Observable.create(new Observable.OnSubscribe<Drawable>() {
    @Override
    public void call(Subscriber<? super Drawable> subscriber) {
        Drawable drawable = MainActivity.this.getResources().getDrawable(drawableRes);
        subscriber.onNext(drawable);
        subscriber.onCompleted();
    }
})
        .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
        .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
        .subscribe(new Observer<Drawable>() {
            @Override
            public void onNext(Drawable drawable) {
                imageView.setImageDrawable(drawable);
            }

            @Override
            public void onCompleted() {
            }

            @Override
            public void onError(Throwable e) {
                Toast.makeText(activity, "Error!", Toast.LENGTH_SHORT).show();
            }
        });


```

















