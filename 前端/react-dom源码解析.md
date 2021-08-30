react-dom源码解析

### react-dom导出：

![image-20210825135921200](https://tva1.sinaimg.cn/large/008i3skNgy1gtszviqbfaj61m10u0td102.jpg)

> ./src/client/ReactDOM参考：https://github.com/facebook/react/blob/main/packages/react-dom/src/client/ReactDOM.js

#### render/hydrate

> 渲染函数
>
> 1. render()，hydrate()`方法本质是返回了函数`legacyRenderSubtreeIntoContainer()
> 2. `hydrate()`和`render()`的唯一区别是传入`legacyRenderSubtreeIntoContainer()`的第四个参数不一样：
>    1. `forceHydrate`为`true`，表示在服务端尽可能复用节点，提高性能; 
>    2. `forceHydrate`为`false`，表示在浏览器端不会去复用节点，而是全部替换掉。

![image-20210826180944254](https://tva1.sinaimg.cn/large/008i3skNgy1gtucqcjsjbj60xh0u0jti02.jpg)

##### legacyRenderSubtreeIntoContainer

> 将渲染树呈现到容器中



###### legacyCreateRootFromDOMContainer

> 从DOM容器中创建根元素



###### getPublicRootInstance

> 获取公共根实例（root实例）

###### updateContainer

> 作用是更新`container`

