# Event Loop in Nginx & Node.js

Note: 这篇文章会关注event-based架构的优势与劣势

## introduce

首先，我们先简单介绍一下Nginx和Node.js

### Nginx

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504355338/Nginx_logo.svg_mtyebo.png)

`Nginx`（发音同engine x）是一个 Web服务器，也可以用作反向代理，负载平衡器和 HTTP缓存。该软件由 Igor Sysoev 创建，并于2004年首次公开发布。同名公司成立于2011年，以提供支持。

Nginx 使用**异步事件驱动**的方法来处理请求。 Nginx的模块化事件驱动架构可以在高负载下提供更可预测的性能。

### Node.js

![](https://res.cloudinary.com/digf90pwi/image/upload/c_scale,w_170/v1504269378/7155aa40d0d8bd4619cd63e30dd68dcd_w7urk7.jpg)

`Node.js`是一个基于Chrome V8 引擎的 JavaScript 运行时。Node.js使用高效、轻量级的**事件驱动、非阻塞 I/O 模型**。Node.js 之生态系统是目前最大的开源包管理系统。

---

Nginx与Node，两者的主要职责分别是Web服务器与应用服务器，但是两者都选择了**事件驱动event-driven**编程模型，这个模型有什么好处呢？

在开始之前我们先来看看Nginx和Node的架构

## archtecture

### nginx

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504355805/nginx-architecture_ovvjgo.png)

众所周知，Nginx采用master + worker的模式来实现不停机graceful的配置更新，master用来启动监控重启worker，而workers则负责处理具体的请求。

worker采用event-driven架构，单线程，worker进程数量一般与cpu逻辑内核数一致。与apache不同，每个worker进程处理的连接数远不止一个，这是因为耗时的I/O抛给了OS去处理，worker只需要处理简单的http等逻辑即可(以及lua插件)。

nginx的event driven是依赖于linux内核中的epoll，在windows下由于使用select，性能会有所下降。

### nodejs

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504356451/QRePV_stanvl.jpg)

与nginx类似，nodejs将耗时的io操作交给了libuv，并将相应的callback句柄传过去(epoll的封装)。

nodejs提供的api，其中的callback函数都会交由event loop来执行

*例如如下的代码：*

```javascript
var http = require("http")

var port = 8080

http
  .createServer((req, res) => { // http handler
    res.end("Hello Node JS !")
  })
  .listen(port, () => console.log(`server started at ${port}`))
```

其中http handler这个callback就是交由event loop来执行，底层的epoll在IO完成之后，event loop就发现该function所需的资源pending了，这段代码就执行了。

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504356963/loop_iteration_gnybay.png)

与nginx不同的是，libuv的event loop的callback是按类型有先后顺序的。

## 分析

nginx与nodejs(libuv)的逻辑都是发起并监听相关IO事件，单个线程即可处理各种不同事件，只要还有工作(request, reqsponse, parse, check ... )，就会一直处于工作状态，最大程度的保证了CPU的使用率，也不会因为频繁切换线程上下文而导致性能损失。

---

*apache2 archtecture*

![](https://res.cloudinary.com/digf90pwi/image/upload/v1504358223/2012110823315630_yucefr.jpg)

与之相比，根据配置不同，apache2可以支持基于线程和基于进程的MPM(多路处理模块)，其中每个线程/进程处理一个具体的连接。一旦并发量增加，server的cpu和内存都会承担很大的压力。

这也是apache2并发量无法提升，逐渐被淘汰的原因。

需要补充的是apache毕竟是95年的项目，而大概03/04年左右，linux才支持epoll。

---

说完了高并发，那么这种模型有什么缺点吗？

有的，比较典型的缺点就是，如果在应用层次上发生阻塞，那么整个线程都会阻塞掉

同样以nodejs举例

```javascript
var http = require("http")

var port = 8080

http
  .createServer((req, res) => { // http handler
    for(var i=1;i<10000;i++) {
      // whatever
    }
    res.end("Hello Node JS !")
  })
  .listen(port, () => console.log(`server started at ${port}`))
```

在http handler中我们加了一个耗时操作，它可能是一个knn算法，可能是在parse xlsx文件，whatever，它阻塞了程序一段时间。那么在这一段时间内，这个node server完全挂掉了，并发能力降为1。(当然，由于网络允许排队，所以请求并不会断掉)

原理并不难理解，js本身是阻塞的，当event loop在resource就绪之后，call http handler，整个线程的控制权交给了handler(还记得吗，nodejs只有单线程的work thread)，那么直到它完成，其它所有操作都被阻塞掉。

---

与之类似，nginx也要避免阻塞操作，但其实大部分情况下，我们只需要使用它的模块即可，这些模块都是严格异步的。

如果编写一个lua模块，采用阻塞操作，无疑，nginx的worker也会整个阻塞掉，所以编写模块的时候一定要小心。

---

so，那么加入多线程可以吗？

简单的加入多线程是不可行的，Developer会直接开始多线程编程。

混合模型理论是可行的，即底层开启多个线程，每个线程跑event loop，相关的handler依旧交给某一个线程来处理，这样可以避免某一个event loop阻塞过久导致整个进程阻塞，但是每个handler依旧需要考虑同步和一致性的问题。

加入多线程，handler和event loop都会变得复杂，没有太大意义。

## references

1. [Nginx Vs Apache: Nginx Basic Architecture and Scalability](http://www.thegeekstuff.com/2013/11/nginx-vs-apache/?utm_source=tuicool)
1. [Which is correct Node.js architecture?](https://stackoverflow.com/questions/36766696/which-is-correct-node-js-architecture)
1. [web服务器apache架构与原理](http://www.cnblogs.com/fnng/archive/2012/11/08/2761713.html)