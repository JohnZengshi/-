spring捕捉系统异常以及自定义抛出异常



## 自定义ExceptionHandle类

![image-20210830171534777](https://tva1.sinaimg.cn/large/008i3skNgy1gtyxn8emwfj61350u078602.jpg)

### @ControllerAdvice/@ExceptionHandler

> 参考：https://blog.csdn.net/andy_zhang2007/article/details/100041219



### InterceptorException

> 自定义异常类

![image-20210830171558213](https://tva1.sinaimg.cn/large/008i3skNgy1gtyxnmr9v4j611i0kcq4c02.jpg)



#### RuntimeException

> 运行时异常类



### 使用：

```
throw new InterceptorException("用户不存在！");
```

