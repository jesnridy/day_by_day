# nginx工作原理

## 从架构上理解
- nginx分为master进程和worker进程。
- master进程主要是接收信号（start，stop，reload），读取配置信息，分配任务给worker进程执行，监听worker状态，如果异常会进行重启一个新worker。
- worker进程主要就是负责处理网络请求事件，各个worker是独立运行，同时竞争客户端的请求。
- 所以整体上看，nginx的架构就是相当于于一个分布式系统的架构，可以通过动态扩充worker的方式来进行分散压力（当然，官方建议是worker最好不超过cpu核心数）

## 高并发的原理

- nginx采用多worker的方式运行，每个worker里只有一个主线程，通过采用异步非阻塞的方式避免类似apache多线程上下文切换造成cpu开销过大的情况。
	1. 一个请求事件到系统层面可以看作是一次读写事件，如果采用阻塞调用，当读写事件还没准备好，就只能等着了，等待的过程会导致内核等待，CPU空闲下来给其他事件使用，所以会导致CPU利用率上不去。对于采用单线程的worker就尴尬了。
	2. 换成非阻塞的模型，当读写事件没有准备好，马上返回给你，告诉你还没准备好，等会再来，所以，你会过一会在来检查下时间，等到时间准备好为止。在这期间可以去做其他事情。虽然你可以做其他事情，但是这种做法带来的开销也不小，要定时查看。
	3. 所以才会使用到异步非阻塞事件处理，就是通过系统调用（select，poll，epoll，kqueue），这种方式提供了一种机制，让你可以同时监听多个事件，调用他们是阻塞的，但是可以设置超时，在超时前事件准备好就返回。eg：当请求过来，事件没有准备好，会把事件放到epool中，事件准备好了，就去读写，当读写还没准备好时，将它再次放到epool中，只要用事件准备好就去读它。这样就可以实现并发处理大量请求了，这里的并发值得就是epool中未处理的时间，因为单线程每个时间片也只处理一个请求，处理的过程就是循环的处理准备好的事件，所以这个过程就不存在特别大的系统开销。
	4. 官方推荐worker数量为CPU核数，就是为了避免过多的worker导致进程去竞争CPU资源，从而导致不必要的上下文切换，nginx提供了cpu亲缘性绑定选项，可以将某个进程绑定到某个核上，避免进程切换导致的cache实现。

## 基本概念理解

### connection

- worker_connections: 这个参数用来表示每个进程支持的最大连接数，如果这个值大于`ulimit -n (一个进程可以打开的最大文件句柄数)`那么最大连接数就是`ulimit -n`。对于一个静态服务器来说支持的最大并发数就是`worker_connections * worker_processes`，对于反向代理服务的话，支持最大并发数就是`worker_connections * worker_process/2`，因为反向代理会创建于客户端的连接和与后端服务器的连接。

### keepalive

- http从1.0开始支持长连接，http请求是基于tcp协议的，所以发起请求前会先进行三次握手，如果客户端与服务端网络比较差，三次握手也会耗时比较久。而http是请求应答式的，如果知道了每个请求头与响应体长度那么就可以在一个连接上进行多次请求，就是长链接。
- 长链接建立条件：需要客户端在请求中设置connection为keep-alive表示打开长链接，http1.0默认是close的，1.1是默认keep-alive的。在nginx响应完后，会设置当前连接为keepalive属性，等待下一次请求过来。当然nginx不会一直等待下去，通过设置keepalive_timeout来设置最大等待时间，超时则关闭，如果keepalive_timeout参数设置为0表示关闭长连接。

## nginx不同模块解析

### log
#### 日志等级

- 第一级别日志"stderr"，"emerg"，"alert"，"crit"，"error"，"warn"，"notice"，"info"，"debug"。

- 第二级别日志"debug_core"，"debug_alloc"，"debug_mutex"，"debug_event"，"debug_http"，"debug_mail"，"debug_mysql"

- 第一级别日志在同一个block之间是互斥的，如果配置了warn再配置了info，nginx会报`duplicate log level `

- 在不同的block中，如果内层block重新定义了日志，但是没有指定相应的日志等级，那么就会屏蔽外层的debug日志。

- 第二级别日志是多选的，只有第一级别日志是debug的时候才可以配置第二级别日志，否则会报`invalid log level`。

#### 日志格式

- 日志格式使用`log_format log_name "xxx"`
	
		log_format  customLog "$remote_addr^A$remote_user^A$time_local^A$request_method^A$uri^A$args^A$server_protocol"
        "^A$status^A$body_bytes_sent^A$http_referer"
        "^A$http_user_agent";
		access_log /path/logs/access.log customLog;`
		
### 其他点

- 关闭日志记录：`error_log /dev/null crit;`