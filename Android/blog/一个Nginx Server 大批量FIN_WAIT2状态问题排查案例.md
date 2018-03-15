
# 问题描述
后台同事反馈 Nginx Server 主机上出现大量FIN_WAIT2状态连接，希望能够协助优化。
![Nginx Server -FIN_WAIT2](/Android/blog/Nginx%20Server%20-FIN_WAIT2.png "Nginx Server -FIN_WAIT2")
# 问题分析
1. FIN_WAIT2是什么<br>
>参考 [关于FIN_WAIT2](https://huoding.com/2016/09/05/542 "关于FIN_WAIT2") 可知，FIN_WAIT2是在TCP四次挥手中，一方(此例为服务端)已发送FIN指令
>而另一方（客户端）迟迟未回复FIN指令，导致的TCP连接半关闭状态。在此状态下，客户端已无法复用此通道，该通道确实应该关闭。
2. 为什么会产生FIN_WAIT2状态？
>当一个开启 keep alive 的 http请求完成数据交换后，连接并不会立即断开，而是闲置一段时间（tcp_fin_timeout）等待复用，
>参考[HTTP协议头部与Keep-Alive模式详解](https://www.byvoid.com/zhs/blog/http-keep-alive-header "HTTP协议头部与Keep-Alive模式详解")<br>
>当闲置超时后，TCP就会超时关闭，发出FIN指令，而如果此时对方仍未关闭或者等待超时，就会造成一方进入FIN_WAIT2。
>综上，服务端产生FIN_WAIT2的原因可能有二：
> 1. 客户端在某些情况下未及时关闭tcp socket 
> 2. 客户端keep alive 时间长于 服务端，导致服务端超时时，客户端仍在等待。
3. okhttp的实现原理
> 参考[OkHttp3源码分析[复用连接池]](https://www.jianshu.com/p/92a61357164b "OkHttp3源码分析[复用连接池]") 可知：
> okhttp 采用了类似java gc回收的 socket回收方案，在该方案下基本不存在socket未关闭的情况，原因1不成立。
> 考虑原因2，查询后发现，后台（linux系统）tcp_fin_timeout默认值为60s，生产环境实际调整为30s
> android 不存在该参数（http.keepAliveDuration），会读取jdk默认值为5分钟。符合原因2。
> 修改okhttp keepAliveDurationMs 为10s，重现问题，问题消失。
# 解决方案
> okhttp 初始化时定制ConnectionPool,
> 其中maxIdleConnections为线程池最大空闲数，在满足使用的情况下越小，对后台并发压力越小
> keepAliveDurationMs空闲超时关闭时间，建议尽量与后台超时时间一致。过小不利于线程复用，过大时后台已关闭tcp连接，无法复用。
```
this.okHttpClient.setConnectionPool(new ConnectionPool(maxIdleConnections,keepAliveDurationMs));
```

# TBD
1. keep alive 的socket的命中管理机制是如何的？如何判断一个socket是处在复用中还是应该关闭？
2. 
