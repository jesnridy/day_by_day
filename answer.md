1： D
2：A，B
3：A，C
4：A
5: A，C，D

填空题：
1: 18 21 23 3 *
2: sh cmd.sh > /dev/null 2>&1
3: lsof -i:80
4: tar -xcvf openssh-5.4p1-1.tar.bz2
5: rsync -danfl /data /data1
6: 匿名函数
7: 用来捕获异常信息
8: replace
9: (windos, location), (open, close)

简答题：
1.  1）select count(*) from player;  2) select name from player order by id desc limit 10;
2. 方法一： re.search(pattern, string) 方法2: re.match(pattern, string)

3: 	
	```
	def fib():
		pre, curr = 0, 1
		while True:
			yield curr
			pre, curr = curr, curr + pre
		
	```
4:  
	``` 
	from pymysql import connect
	import warning
	host = "10.1.20.100"
	port = 3306
	username = "john"
	password = "megajonhy"
	db = "qqdb"
	table = "qq_list"
	if __name__ == "__main__":
		result = None
		try:
			conn = connect(host=host, port=port, username=username, password=password, db=db)
			result = conn.execute("select * from qq_list")
		exception Exception as e:
			warning.error("操作数据库异常！")
		print(result)
	
	```
	
5: 
	```
	
	```

解决方案题
1： 
	```
	# len(A) = len(B) 
	status = False
	for k,v in enumerate(A):
		if v == B[k+1] and A[k+2:] == B[k+2:]:
			status = True
			break
	return status		
			
	```
2: 

	比如一个验证码识别的机器学习，首先需要一堆训练数据。
	再去采用类似MapReduce的过程去不断的应用算法去识别数据，最终通过不断提高识别率来达到最终效果。
	
3: 
	1) 
	可以通过现在流行的监控系统（zabbix,openfalcon）系统自带的端口监控功能，以及去匹配进行名的方式对进程去做监控。
	还需要考虑进程假死的情况，如果业务是全天都有流量的情况，可以通过监控gamesvr服务日志是否在一段时间没有再新增做监控。
	再就是需要通过自定义脚本去监控进程消耗的CPU，内存，带宽这些指标。

	2) 
	对于gamesvr服务器持续CPU load过高，导致服务不可用的情况，有两种方案去解决。
	第一种方案前提是服务整体架构上是做了高可用，关闭gamesvr不会导致玩家直接掉线，
	这种情况可以写脚本去判断服务load过高，并且错误日志中在一段时间出现超过阀值的
	情况，可以进行重启gamesvr去干预。
	如果架构上保证不了高可用，就需要通过动态扩容的方式去横向增加gamesvr服务器分
	担部分负载来达到缓解单台load过高的情况。
4：
	
	```
	import requests
	import warning
	url = "http://10.12.4.24/api/level"
	def api(method, **parameters):
		assert method in ["get", "post"], "method请求方法不被允许！"
		result = {}
		try:
			if method == "get":
				r = requests.get(url=url, parameters=parameters)
			else:
				r = requests.post(url=url, data=parameters)				
			assert r.code == 200, "请求接口网络有问题【code={code}】".format(r.code())
			assert r.json().get("stat") is True, "请求接口返回stat非true！"			result = r.json()
		except Exception as e
			warning.error("请求API异常！")
		return result
		
	if __name__ == "__main__":
		get_result = api("get", {'getlevel': "123456"})
		if get_result:
				level = int(get_result.get("level"))
				post_result = api("post", {"123456": level+1})
				if post_reuslt:
					print(api("get", {"getLevel": "123456"}))
	```
	
5: 
	```
	from configparser import ConfigParser
	import xx
	
	if __name__ == "__main__":
		arg1 = xx.get()
		arg2 = xx.get()
		result = None
		try:	
			config = ConfigParser('cfg.ini')
			result = config.get(arg1, arg2)
		except Exception as e:
			warning.error("解析配置异常")
		print result
	```

6: 

	```
	import os, warning, xx
	
	
	def parse_file(file_name, qq_num, is_online=False, query_list=[]):
		result = []
		try:
			with open(file_name, 'rb') as f:
				lines = f.readlins()
				for line in lines:
					if line[0] == qq_num:
						if is_online is False:
							result = line[1:]
						elif line[2] in query_list:
							result.append(line[1])
		except Exception as e:
			warning.error("解析文件异常！")
		return result
	
	if __name__ == "__main__":
		qq_num = xx.get()
		parse_qq_level = parse_file("qq_level.txt", qq_num)
		qq_level = parse_qq_level[1] if parse_qq_level else None
		parse_qq_attr = parse_file("qq_attr.txt", qq_num)
		if parse_qq_attr:
			sex, age = parse_qq_attr[1],parse_qq_attr[2]
		else:
			sex, age = None, None
		parse_qq_online = parse_file("qq_online.txt", qq_num, True, ["20160616", "20160617"])
		if parse_qq_online:
			online = sum(parse_qq_online)
		print("{qq_num}, {qq_level}, 时间20160616+20160617的在线时长的求和值{online}, {sex}, {age}".format(qq_num=qq_num, qq_level=qq_level, sex=sex, age=age))			
		
	```
	
5: （股票题）
	思路：给定数组，选出一个元素则对后面的数字进行hash记录key的位置（就是题目中的天数），再进行排序，获取最大的值，然后从hash表中拿到这个值的位置，则计算出天数
	
