## Spring boot：将application.properties的值在静态代码块中使用

### 定义application.properties文件

![image-20210826141242110](https://tva1.sinaimg.cn/large/008i3skNgy1gtu5vpgvh2j607f01qdfp02.jpg)

> 其中application.properties定义的是当前的环境：
>
> 比如：spring.profiles.active=dev。
>
> 打包的时候会根据环境变量选择application-dev.properties还是application-pord.properties。
>
> application-dev.properties和application-pord.properties设置的是不同环境的一些配置。（比如数据库）

### PropertiesExtractorUtils

> 自定义PropertiesExtractorUtils类，获取application.properties当中的键值

![image-20210826141902303](https://tva1.sinaimg.cn/large/008i3skNgy1gtu62b0aiwj60u012878c02.jpg)

#### Properties

> 表示一组持久的属性（由键值对组成）

##### properties.load

> 从输入字节流读取属性列表(键值对)

##### properties.getProperty

> 在属性列表中搜索具有指定键的属性

#### FileInputStream

> 通过打开与实际文件的连接来创建FileInputStream，一般配合properties.load使用



### application-dev.properties/application-prod.properties

![image-20210805164933520](https://tva1.sinaimg.cn/large/008i3skNgy1gt60eg9fpbj30zi0dcdgv.jpg)

### 使用：

![image-20210805165055698](https://tva1.sinaimg.cn/large/008i3skNgy1gt60fvcwh4j31cm0tc42u.jpg)

