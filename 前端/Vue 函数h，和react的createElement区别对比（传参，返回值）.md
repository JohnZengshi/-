vue.h，和react.createElement区别对比（传参，返回值）

#### `vue.h`

![image-20210823153923402](https://tva1.sinaimg.cn/large/008i3skNgy1gtqrj0x5paj60um0cc74v02.jpg)

##### 返回值：

![image-20210823153954478](https://tva1.sinaimg.cn/large/008i3skNgy1gtqrjitfd1j60gr0c4wfe02.jpg)

#### `React.createElement`

![image-20210823154055181](https://tva1.sinaimg.cn/large/008i3skNgy1gtqrkkr08jj60yy0ccgmc02.jpg)

##### 返回值：

![image-20210823154117198](https://tva1.sinaimg.cn/large/008i3skNgy1gtqrkybisbj60i105iwf202.jpg)

#### 对比：

##### 传参：

创建函数传参都是一样的，第一个参数为标签类型（包括script），第二个参数为标签的属性（style，class，onClick，等等），第三个参数为children子元素（可以是一个数组）。注：vue的h传参更灵活，不一定是三个参数（参考：https://vue3js.cn/docs/zh/guide/render-function.html#h-%E5%8F%82%E6%95%B0）。

##### 返回值：

###### vue.h:

(待补充)

###### react.createElement:

可读取的一般就4个：`key`，`props`，`ref`，`type`，具体的含义很好理解，key：元素的全局标志，props：元素属性，ref：（待补充），type：标签类型



#### 说明：

- vue.h一般使用在jsx当中，jsx在编译之后会转变成h("xxx",{xxx},xxx)，react.createElement也是一样。
- react.createElement返回值中的$$typeof是一个Symbol类型，对没有这个标志的对象，react不会创建元素，会抛出错误，其目的是为了防止xss攻击（服务端渲染的时候，由jsx直接返回元素对象时，参考：https://overreacted.io/zh-hans/why-do-react-elements-have-typeof-property/），原理很简单，因为json（后端返回的数据）不支持Symbol类型。但是vue.h返回值没有这样类似的处理😓。
- React.createElement对script标签有处理：
  - 通过React.createElement("script", {}, "alert('this is script')")创建的元素不会执行script代码
  - 通过Vue.h("script", {}, "alert('this is script')")创建的元素会执行script代码

