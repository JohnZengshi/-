使用typescript实现对后端接口规范，参数自动匹配，以及返回值的定义。

#### 先看效果：^_^

![Kapture 2021-08-16 at 16.36.56](https://tva1.sinaimg.cn/large/008i3skNgy1gtipvcorylg60ic0dy4qv02.gif)

#### API

> 将后端接口地址转成枚举

![image-20210816165407095](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqckucuwj60na0fcwf202.jpg)

#### ModelApi

> 使用declare module定义模块

![image-20210816170207381](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqkx0nn0j60vx0u076e02.jpg)

##### RequestParams：

> 定义请求的参数

![image-20210816170928919](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqsk9gk4j60m80jc74z02.jpg)

###### BaseParams：

> 包装请求的参数，这里使用的是axios，所以参数为**`url`**，和**`data`**

![image-20210816170250637](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqlnmyx8j60ps0ec3yw02.jpg)

###### BasePage：

> 定义列表的统一传参

![image-20210816171223120](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqvlb84wj60iw0gcq3b02.jpg)

###### 使用：

> 每一个参数定义都用｜隔开！

![image-20210816171139885](https://tva1.sinaimg.cn/large/008i3skNgy1gtiquu9pv6j60qu0mcdh602.jpg)

##### RequestRes：

> 接口返回定义

![image-20210816170525015](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqoc0rjrj60og0kcmyc02.jpg)

###### BaseListRes：

> 定义列表的统一返回，（具体看接口怎么定义）

![image-20210816171435763](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqxvuoh0j60m00gcwf102.jpg)

###### 使用：

![image-20210816171550098](https://tva1.sinaimg.cn/large/008i3skNgy1gtiqz6kt16j60ve0fcjrz02.jpg)



#### 在post方法中使用（get同理）：

![image-20210816164403524](https://tva1.sinaimg.cn/large/008i3skNgy1gtiq23zxc5j61iy0u077x02.jpg)

##### axios

> 使用axios请求

#### 总结：

精髓在于使用了type关键字以及或操作符**`“｜”`**，这样能在匹配到`url`这个参数的时候可以推导出`data`中的对应字段！(*^o^*)