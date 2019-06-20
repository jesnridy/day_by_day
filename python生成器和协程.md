# python生成器和协程
> 两个都用到了yield关键字，主要区别在于生成器是数据的生产者，协程相当于数据的消费者。

- 生成器例子
	
	```python
	def fib():
		pre, curr = 0, 1
		while True:
			yield curr
			pre, curr = curr, curr + pre
	
	import itertools
	f = fib()
	list(itertools.islice(f, 0 10))
	```

- 协程例子

	```python
	def coroutine(pattern):
		while True:
			line = (yield)
			if pattern in line:
				print(line)
	
	crt = coroutine('demo')
	next(crt)
	crt.send("test test")
	crt.send("demo test")
	crt.close()
	
	```