#REST接口设计规范
> Representational State Transfer 表现层状态转化

##为什么要用REST
1. 简单轻量：完全基于HTTP协议，通过GET，POST，PUT，DELET，PATCH来进行操作资源。
2. 面向资源，一目了然，自带解释属性。请求响应过程不仅包括了客户需要的数据，还可以包括与请求相关的信息，在不查看文档的情况下就可以知道下一步要做什么。
3. 数据描述简单，一般通过json，xml作为数据交换。
4. 服务端状态可以通过HTTP code来反馈请求结果。
5. 为了适配各种客户端平台（web，IOS，Android等），大家可以共用一套RESTful api 就好了。


##涉及到的概念
1. 资源：即就是URI表示的东西。
2. 表现层：是指可以把资源转换成什么东西（一个txt可以转换成HTML，json等 ）。在Http中使用Accept和Content-Type来指定。
3. 状态转化，状态转化是客户端通过某种方式来通知到服务端更改数据状态的变化。

##资源原型
1. 文档：作为资源单一表现形式，可以是个对象，数据库中的一条记录。一般是返回文档对应的数据，或者返回指定到另个资源的链接。
2. 集合：作为资源的一个容器，可以通过post向里面添加资源。eg: `/posts/1/comments`
3. 仓库：作为客户端管理的一个集合，可以向这个集合中新增或者删除资源。eg：`POST /posts/1/comments/`
4. 控制器：把动作转换为一个CRUD。eg: `POST /posts/1/publish`


##命名注意点：
1. 资源：使用单数名词
2. 集合：使用复数名词
3. 仓库：使用单数名词
4. 控制器：使用动词命名


##最佳实践
1. 接口命名可以放在域名里也可以放在path中。
2. 接口版本控制可以放在path中，也可以放在head里（github就是放在head里）
3. 确保HEAD和GET不会对数据造成污染。
4. 如果返回的数据过大，可以通过分页和限制来进行操作，HTTP中支持在head中定义link来完成分页。
5. 返回接口要尽量清晰，避免数字含义。
6. 关于安全的话，可以使用https，平台的话可以上OAuth2
7. 如果操作的资源就是一个动词，eg:比较分支差异，那就可以采用github_v3中通过[...](https://developer.github.com/v3/repos/commits/#compare-two-commits)来操作。(`curl https://api.github.com/repos/:owner/:repo/compare/hubot:branchname...octocat:branchname`)


**参考阅读**
[stackoverflow](https://stackoverflow.com/questions/671118/what-exactly-is-restful-programming/3950863#3950863)