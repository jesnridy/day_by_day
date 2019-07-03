python private method
> 用句话怎么说：“大家都是成年人”，所以python在实现类似c++中private method的时候，可以通过约定的双下划线来实现这种，只有我愿意调用的时候才能被调用，其他人调用不了，但是又可以保证他是可复用的。

```python
class Foo(object):
	def __private_method(self):
		return 1
	
	def invoke(self):
		return self.__private_method()

```

- python通过使用双下划线来定义私用方法。
- 使用这种方法主要是为了防止子类中调用超类出现同名覆盖的情况。
- 通过`__dir__()`可以看到`_Foo__private_method`，想要直接访问需要加上下划线加类名。