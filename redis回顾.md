# redis数据结构回顾

## 五种数据结构 string, list, hash, set, sorted set

### string：

	在redis中string是作为可变字节数组，可以覆盖修改。采用预分配冗余空间来减少内存频繁申请。
	预分配的内存会大于字符串的长度，小于1M每次申请加倍内存空间，超过1M一次只扩容1M大小，最
	大支持512M。
	字符串的内容如果是整形的话，可以当成计数器来用`incrby xx 1` `decrby xx 1`范围处于
	Long.MIN-Long.Max	
	字符串删除可以指定expire过期时间来进行失效删除。
	
		
### list:

	redis的列表使用双向链表结构，可以在首位进行追加，删除。
	redis在list插入的时候可以指定before after但是后面跟的是具体元素，不是下标（分布式系统中元素变化很快的情况需要特别留意）。
	通常把list作为异步队列来使用。
	
	列表中还有类特殊的叫做定长列表
	
	 应用场景：消息队列
### hash 
	
	类似Python的字典，支持扩容和缩容，扩容需要申请两倍的大小原数组大小（hash结构是个二维结构，第一维是数组存的是链表头指针，第二维是链表结构，存的是key value）。
	然后重新分配新的数组下标对应到链表中。为了避免单线程redis压力，会保持新旧两份结构，后续在定时任务中慢慢扩掉。
	
	
	
### set

	结构类似HashMap，每个value都指向同一个对象。

### sorted set
	
	增加了权重设置，可以按照权重进行排序
	
## 订阅推送
> redis中可以使用订阅推送来选择处理自己感兴趣的数据


----

# Redis为什么快？	

redis支持10w+ qps不必单进程多线程的memcache差太多。

1. 首先redis完全基于内存。数据操作都是在内存中进行。
2. 数据结构简单，对数据操作也简单。
3. 采用单线程，这样做就避免了上下文切换消耗，你不需要考虑锁机制。
4. 采用IO多路复用，BIO。

**注意点：**由于redis是单线程，当并发请求过来如果出现一个耗时操作，那么会导致并发下降，所以一般采用master-slave的方式，将耗时操作放到slave上跑。

# Redis锁机制
1. 原子加，在redis里生成一个key，然后每次去对这个key+1，作为乐观锁使用。
2. 使用SETNX设置一个key，如果不存在才能设置成功。

# Redis 缓存命中率
1. redis中有个info命令可以看到里面这两个字段`keyspace_hits` `keyspace_misses` 可以计算出缓存命中率。
2. 提高缓存命中率的一个场景就是在**读多写少**，否则使用缓存意义不大。
3. 再一个业务逻辑设计中缓存对象的粒度越小，命中率约高，比如你把所有用户信息扔到一个集合中，只要当其中一个用户发生变化都要更新或移除缓存。只有当你把业务逻辑设计成单个用户，那对缓存命中率就很好。
4. 缓存容量的限制，当单机缓存容量到最大时候也会进行落盘处理，影响缓存命中率，可以采用分布式部署对容量进行动态扩展，缓存的淘汰算法目前用的最多的就是[LRU](https://www.cnblogs.com/-OYK/archive/2012/12/05/2803317.html)算法(新数据访问过来插入链表头，每当缓存命中则将数据移到表头，当链表满的时候，将链表尾部数据丢掉)。

```
送一个Python实现的LRU

class LRUCache(object):
	def __init__(self, capacity):
		self.capacity = capacity
		self.cache = {}
		self.lru = {}
		self.tm = 0
	
	def get(self, key):
		if key in self.cache.keys():
			self.lru[key] = self.tm
			self.tm += 1
			return self.cache[key]
		return -1
	
	def set(self, key, value):
		if len(self.cache) >= self.capacity:
			old_key = min(self.lru.keys, keys=lambda k: self.lru[k])
			self.cache.pop(old_key)
			self.lru.pop(old_key)
		self.cache[key] = value
		self.lru[key] = self.tm
		self.tm += 1
		

```