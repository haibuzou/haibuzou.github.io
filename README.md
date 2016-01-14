# haibuzou.github.io
还不走A 博客

Rxjava学习之路

Rxjava从去年开始就已经宣传的满天飞了，但是Rxjava并不是很好理解，网上的例子并不是很多，而且很多都是国外的资源，导致学习的过程比较崎岖。不过Rxjava一旦开始可以明白他的理念，你会发现它实在是太好用了。从这里开始是我自己的一点学习心得，顺带做点笔记了。希望能给大家点帮助。

###**如何开始学习**
 我最开始接触到Rxjava是从Android Weekly的周刊上，当时只有官方的资料，以及国外网站的一些资源，当时看的云里雾里。所以我这里不推荐直接去找官方的资料，而是做点相关的知识学习。
说道Rxjava其实他的设计理念与java设计模式中的观察者模式很相似，如果对于观察者模式不了解，推荐看看翔哥的博客 [传送门](http://blog.csdn.net/lmj623565791/article/details/24179699)
关于观察者和订阅者翔哥的博客有了很形象的解释，现在我们对于观察这模式已经有了清晰的理解，下面来看看简单哥的关于Rxjava的前文介绍[传送门](http://blog.csdn.net/bboyfeiyu/article/details/45557453)
有了以上的2个基础，会对后面的学习过程有很大的帮助作用。

###**什么是Rxjava**
Rxjava 即 Reactive Java 一种 java的 Functional Reactive Programming的实现，通俗的说就是：响应式函数编程。类似回调（callback）的感觉，当A类中发生变化会通过callback 通知B类中的callback实现接口，我们函数不再是死的，而是编程能够相应的函数。与回调的单对单不同，它可以单对多。当然他的功能不止是能够响应那么简单。


###**Hello World**
万事从hello world开始，Rxjava主要的成员2人：Observables(被观察者，事件源)，Subscribers（订阅者），了解了上面提到观察这模式的，对于这两个很好理解。
从Observables事件源开始，Observable相当于被观察者，他会发出事件给订阅者Subscribers。
相关的uml图：

![这里写图片描述](http://img.blog.csdn.net/20160105161948373)


现在我们来创建一个Observable 

```java
 Observable<String> hello = Observable.create(new Observable.OnSubscribe<String>() {
            @Override
            public void call(Subscriber<? super String> sub) {
                sub.onNext("hello word");
                sub.onNext("hello word");
                sub.onCompleted();
            }
        });
```
hello 定义了返回类型String，使用create()方法创建了一个事件也就是Observable(create()方法其实是创建了一个Observable)，create()方法的成员变量OnSubscribe定义了事件应该如何发出，这里需要特别注意一点，onNext()方法是可以执行多次的，但是最后必须要在最后调用onCompleted()方法，因为Rxjava定义了onCompleted() 为不会再有onNext()的标志。最后当hello这个Observable被订阅的时候 call()方法就会被执行，那么如何被订阅呢？ 别急，我们还有另外一个主角Subscriber。

创建一个Subscriber
```java
        Subscriber<String> sub = new Subscriber<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Toast.makeText(MainActivity.this,s,Toast.LENGTH_LONG).show();
            }
        };
```
相比较起来Subscriber理解起来就简单很多，除开前面已经亮相的onNext()和onCompleted()，还有一个onError()方法，从字面上很好理解就是回调出错的时候会进入的方法。我在onNext()接收到 hello 这个Observable的消息的时候就会去Toast一个消息出来。回到上面的问题，如何完成订阅呢？
```java
  hello.subscribe(sub);
```
我们的第一个hello world 就这样完成了。看起来与观察者模式很像，他的功能也不止上面写的那么简单，Rxjava内部提供了丰富的api，让我们来进行各样的操作。 拿上面的helloworld来举例，做点简单的修改。

```java
   Observable<String> hello = Observable.just("hello world","hehe");
```

just(T) 这个方法就可以将"hello world"和"hehe" 这2个信息依次发送出去，到订阅的Subscriber中。传单个变量当然不能满足需求，发送数组和集合就要用到另外一个方法

```java
String[] words ={ "hello","world"};
List<String> hello= Arrays.asList(words);
Observable<String> hello = Observable.from(hehe);
//Observable<String> hello = Observable.from(hello);
```
from(T[]) 就是专门用来发送集合的方法。类似还有很多方法，在以后还会继续介绍

###**自定义Action**
订阅方法subscribe() 除了 subscribe(Subscriber) 以外，还可以自定义自己的Action。

```java
 Action1<String> nextAction = new Action1() {
            @Override
            public void call(Object o) {
                Toast.makeText(MainActivity.this,o+"",Toast.LENGTH_LONG).show();
            }
        };

hello.subscribe(nextAction);
```
不用复写Subscriber中的所有方法，只需自定义一个Action就可以接收消息。感觉越来越好用了。这里可能有人好奇Action1的1代表什么意思，其实就是代表参数个数，比如 Action2<T1,T2> ， Action3<T1,T2,T3>。至于有多少个Action有兴趣可以去看看源码。

###**总结**
现在我们初步了解了Rxjava，但是他还有很多很强大的功能，以及Rxjava好在哪里，都还要好好研究一下。
