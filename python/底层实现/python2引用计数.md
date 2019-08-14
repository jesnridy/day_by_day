# python2引用计数
- 可以看下cpython源代码
```c
#define _Py_NewReference(op) ((op)->ob_refcnt = 1)


#define _Py_Dealloc(op) (*(op)->ob_type->tp_dealloc)((PyObject *)(op)))

#define Py_INCREF(op) ((op)->ob_refcnt++)

#define Py_DECREF(op)                                   \
        if (--(op)->ob_refcnt != 0)                     \
        	;
        else                                            \
            _Py_Dealloc((PyObject *)(op))
            
#define Py_CLEAR(op)                            \
        do {                                    \
                if (op) {                       \
                        PyObject *tmp = (PyObject *)(op);       \
                        (op) = NULL;            \
                        Py_DECREF(tmp);         \
                }                               \
        } while (0)

/* Macros to use in case the object pointer may be NULL: */
#define Py_XINCREF(op) if ((op) == NULL) ; else Py_INCREF(op)
#define Py_XDECREF(op) if ((op) == NULL) ; else Py_DECREF(op)

```
- `_Py_NewReference`宏定义了对象初始化时候设置引用计数为1
- `Py_INCREF`宏定义了增加引用对象
- `Py_DECREF`宏定义了减少引用对象
- 当对象的`ob_refcnt`减少为0时会去调用对象的析构方法，调用析构方法时不一定会立即释放内存，主要是为了避免频繁申请释放内存造成的系统负载。
