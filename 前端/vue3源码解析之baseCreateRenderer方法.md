vue3源码解析之baseCreateRenderer方法

> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-core/src/renderer.ts#L334

### 定义：

> 主要有两个定义。
>
> 类似函数重载，区别所传的参数。

![image-20210826150406068](https://tva1.sinaimg.cn/large/008i3skNgy1gtu7d6y8tuj616m0mc41d02.jpg)

### 代码块1：

![image-20210826150938186](https://tva1.sinaimg.cn/large/008i3skNgy1gtu7iyb0puj60pa0ecgma02.jpg)

#### initFeatureFlags

![image-20210826152141968](https://tva1.sinaimg.cn/large/008i3skNgy1gtu7vi6ocxj61ap0u0n1502.jpg)

> 这仅在ESM-Bundler版本中调用。在`baseCreateRenderer`中创建渲染器时调用，保证导入运行时核心无副作用。
>
> __ESM_BUNDLER__，__TEST__，__FEATURE_OPTIONS_API__，__FEATURE_PROD_DEVTOOLS__由rollup.config.js中的配置决定，可以参考：
>
> https://github.com/vuejs/vue-next/blob/master/rollup.config.js#L228
>
> https://github.com/vuejs/vue-next/blob/master/rollup.config.js#L224

##### getGlobalThis

> 全局变量（any类型）

![image-20210826152500571](https://tva1.sinaimg.cn/large/008i3skNgy1gtu7yy8a6xj60t00qc40d02.jpg)

###### 关于globalThis，self，window，global

> 参考：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis#%E7%A4%BA%E4%BE%8B

### 代码块2：

![image-20210826154727684](https://tva1.sinaimg.cn/large/008i3skNgy1gtu8mbmz11j60zm0hcab902.jpg)

> target为全局对象globalThis
>
> 如果浏览器装了vuejs-devtools插件的话，全局对象中会有__VUE_DEVTOOLS_GLOBAL_HOOK__
>
> ![image-20210826155429347](https://tva1.sinaimg.cn/large/008i3skNgy1gtu8tm82n9j60oo0aw3zl02.jpg)

#### setDevtoolsHook

![image-20210826155035314](https://tva1.sinaimg.cn/large/008i3skNgy1gtu8pk68c3j60xe0fc75602.jpg)

##### devtools

> vuejs-devtools插件的工具hook，以此来调用插件的功能。

#### `interface`  DevtoolsHook

![image-20210826155619082](https://tva1.sinaimg.cn/large/008i3skNgy1gtu8vinbcaj60w20hcta502.jpg)