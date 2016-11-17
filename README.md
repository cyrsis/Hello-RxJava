# Learning RxJava(RxJava详解)  

RxJava的精妙之处就是异步操作极其方便，也就是Schedulers的使用，本项目可以让大家彻底掌握Schedulers的使用，还有常用的操作符、RxAndroid...的使用详解。

What is Exjava,

The most simply explain one in my mind is 

1 "When something happen "   THEN

2 " Here is what will happen next"

**Why:** Whenever we deal with http request or anything that cannt be done rigth the way. We need something to manage "When" will it be done






## Screenshots
 <img src="screenshots/just.gif" width="22%" />
 <img src="screenshots/just6.gif" width="22%" />
<img src="screenshots/polling.gif" width="22%" />
<img src="screenshots/rxjavaretrofit.gif" width="22%" />



## 详解 (Details Explain)
我们可以把Schedulers看作线程控制符，一共五种线程控制符，可以通过这些线程控制符切换不同的线程。以下是五种线程控制符的区别：

To define the "time" of something happen, we need a "Scheduler",

To define the "things happen" of something happen, we need a "Thread"

To define the "things happen right now" is Main Threads and other Thread treat as background Threads

1. `Schedulers.immediate()` 在当前线程运行，相当于不切换线程。这是默认的 Scheduler。

    **Explain:** Read as "Do it Right now"

2. `Schedulers.newThread()`总是启用新线程，并在新线程执行操作。

    **Explain:** Read as "I dont care u are busy or not,Do this too "

3. `Schedulers.io()` I/O 操作（读写文件、数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，区别在于 io() 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 io() 比 newThread() 更有效率。不要把计算工作放在 io() 中，可以避免创建不必要的线程。

    **Explain:** Read as "Give me a minutes, I am writing the database "

4. `Schedulers.computation()` 计算所使用的 Scheduler。这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。

    **Explain:** Read as "Give me a minutes, my brain is busy "

5. `AndroidSchedulers.mainThread()` 切换到主线程，指定的操作将在Android 主线程运行。
    
    **Explain:** Read as "Dont do anything else, Just do this one first "

` 其实我们常用的就2种：Schedulers.io()和AndroidSchedulers.mainThread()`

    **Note:** Schedulers.io() and AndroidSchedulers.mainThread() are the most common one

> 以下几个例子都是使用Observable.just(1,2,3)创建被观察对象，观察者输出1，2，3. 

> This example show how to use make a few threads that we can watch their actions    


#### 1. 基本使用(Simply way to monitor the Threads)
<img src="screenshots/just.png" width="61%" />
 <img src="screenshots/just.gif" width="34%" />
 <img src="screenshots/justp.png" width="96%" />

#### 2. 使用 subscribeOn(Schedulers.io())设置被观察者的线程 -Put it down to other Threads but Not Main Thread

<img src="screenshots/just1.png" width="61%" />
 <img src="screenshots/just1.gif" width="34%" />
 <img src="screenshots/just1p.png" width="96%" /> 

  > 以下几个例子中看不出被观察者发生在什么线程，使用Observeble.create()创建被观察者可以看出发生在什么线程，可参看源码中的其它Demo。
  
#### 3. 使用 subscribeOn(Schedulers.io()) 和 observeOn() 设置被观察者和观察者的线程

<img src="screenshots/just2.png" width="61%" />
 <img src="screenshots/just2.gif" width="34%" />
 <img src="screenshots/just2p.png" width="96%" />  

#### 4. 使用Schedulers.io()指定被观察者产生事件的线程,然后使用Map对数据转换，这里只是在每个数据后面加‘a’。

<img src="screenshots/just3.png" width="61%" />
 <img src="screenshots/just3.gif" width="34%" />
 <img src="screenshots/just3p.png" width="96%" />  

 
#### 5. 使用Schedulers.io()指定被观察者产生事件的线程,使用Map对数据转换，在每个数据后面加‘a’，使用AndroidSchedulers.mainThread()切换到主线程，然后使用Map变换，每个数据后加‘b’，输出结果。

<img src="screenshots/just4.png" width="61%" />
 <img src="screenshots/just4.gif" width="34%" />
 <img src="screenshots/just4p.png" width="96%" />  

#### 6. 使用Schedulers.io()指定被观察者产生事件的线程,使用Map对数据转换，在每个数据后面加‘a’，使用AndroidSchedulers.mainThread()切换到主线程，然后使用Map变换，每个数据后加‘b’，再用Schedulers.io()切换线程，用Map对数据加‘c’，输出结果。

<img src="screenshots/just5.png" width="61%" />
 <img src="screenshots/just5.gif" width="34%" />
 <img src="screenshots/just5p.png" width="96%" />  
 
####7.这个例子不同的是使用了两次subscribeOn()来指定被观察者的线程，最终最上面的subscribeOn()起作用，下面的subscribeOn()不起作用。然后使用Map对数据转换，这里只是在每个数据后面加‘a’，使用Schedulers.io()切换线程，然后使用Map变换，每个数据后加‘b’，再用AndroidSchedulers.mainThread()切换主线程，用Map对数据加‘c’，最后再切换到非主线程，输出结果。

<img src="screenshots/just6.png" width="61%" />
 <img src="screenshots/just6.gif" width="34%" />
 <img src="screenshots/just6p.png" width="96%" />
 
>最终发现设置被观察者的线程只有最上面的起作用，下面的不起作用。

`更多的例子请看源码`
 
##About me

An android developer in Beijing.Welcome to offer me an [Interview invitation](mailto:maat.xing@gmail.com). If you have any new idea about this project, feel free to [contact me](mailto:maat.xing@gmail.com). :smiley:






License
=======

    Copyright 2016 Maat


    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

