# JS中异步方法实现
> js是运行在浏览器中，运行的时候是单线程的，开一个浏览器窗口就开一个线程。

## 浏览器本身运行不是单线程的

- javascript引擎线程
- 界面渲染线程
- 浏览器事件出发线程
- HTTP请求线程
 
 浏览器在处理异步任务的时候，会将任务放到一个事件队列里，浏览器内部有个Event Loop，通过去轮训这个事件队列去处理事件。
 
## ajax异步请求过程
- js中处理到ajax时候，发生请求时浏览器会新开一个HTTP请求线程，事件回掉时候放到Event Loop里等待着轮训执行。

## setTimeout(func, 0)的用法
- 按照上面理解js是单线程运行的，所以这里setTimeout也不是实现对线程用的，他的作用是在0秒后把func放到事件队列里，等待当前代码执行完后，再去执行他，逻辑上改变了代码的执行流程。实现异步的一种方式。

----
## 实现异步的几种方式

- 回调方法

```javascript

	function f1(callback){
		setTimeout(function(){
			// execute f1 logic
			callback();
		},1000);
	}

	f1(f2);
```

- 事件监听

```javascript

	f1.on('done', f2);
		
	function f1(callback){
	setTimeout(function(){
		// execute f1 logic
		f1.trigger('done');
	},1000);
	}
	
```

- 发布/订阅

```javascript

	jQuery.subscribe("done", f2);
	
	function f1(){
　		setTimeout(function () {
　　　	// execute f1 logic
　　　	jQuery.publish("done");　　
　　　	}, 1000);
	}


```

- promises对象，这种方式好处是可以实现链式调用，读起来方便理解。

```javascript

	f1().then(f2);
	
	function f1(){
　		var dfd = $.Deferred();
　		setTimeout(function () {
　　　		// execute f1 logic
　　　		dfd.resolve();
　		}, 500);

  	return dfd.promise;
}
	


```