# web开发回顾

csrf:跨站伪造请求攻击
	
	具体是怎么实现攻击的呢？
	
		首先，用户U去访问一个正常网站A，访问A站登录成功后会生成一个cookie信息。
		然后用户U再去访问一个异常网站B，B网站有段代码是跳转到A网站，这个时候会带上上一步生成的cookie去请求A站。
		结果就可想而知了。
	
	具体要怎么防护？
	其实防护也很简单，客户端去请求的时候传递一个伪随机的值，这个值可以是服务端返回给客户端的值放在cookie里存储（理论上攻击者是不会拿到cookie信息的），然后服务端端进行校验。
	
	flask中配置：
		如果没有做前后端分离，最简单的方法就是直接使用`flask_wtf.csrf`，放在meta中，在有表单提交的时候加一个hidden就行了。如果是Ajax请求做下ajaxSetup，beforeSend中做下设置就就行。
	django中配置：
		django使用中间件`django.middleware.csrf.CsrfViewMiddleware`
		如果是前后端分离，可以使用token，请求的时候把token放在headers里。
		