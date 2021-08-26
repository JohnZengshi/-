react-dom源码解析

### react-dom导出：

![image-20210825135921200](https://tva1.sinaimg.cn/large/008i3skNgy1gtszviqbfaj61m10u0td102.jpg)

> ./src/client/ReactDOM参考：https://github.com/facebook/react/blob/main/packages/react-dom/src/client/ReactDOM.js

#### render

> 渲染函数

![image-20210825140603650](https://tva1.sinaimg.cn/large/008i3skNgy1gtt02hqrnej60u00wadig02.jpg)

##### legacyRenderSubtreeIntoContainer

> 将渲染树呈现到容器中



###### legacyCreateRootFromDOMContainer

> 从DOM容器中创建根元素