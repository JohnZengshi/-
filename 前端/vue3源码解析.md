vue3源码解析

## createApp

> 创建app，用法：createApp(App).mount("#app");
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/index.ts#L59

![image-20210826145123539](https://tva1.sinaimg.cn/large/008i3skNgy1gtu6zys45gj60u010hjw202.jpg)

### ensureRenderer

> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/index.ts#L35

![image-20210826144503658](https://tva1.sinaimg.cn/large/008i3skNgy1gtu6tds1g9j61800gcwfk02.jpg)

### renderer

> 由createRenderer创建。
>
> 懒惰创建渲染器-这使得核心渲染器逻辑树可以摇晃，以防用户仅从Vue导入反应性实用程序。
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/index.ts#L31



### createRenderer

> createRenender函数接受两个泛型参数：HostNode和HostElement，对应于宿主环境中的Node和Element类型。例如，对于Runtime-dom，HostNode将是DOM`Node`接口，而HostElement将是DOM`Element`接口。
>
> 返回baseCreateRenderer(options)
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-core/src/renderer.ts#L305

![image-20210826145429812](https://tva1.sinaimg.cn/large/008i3skNgy1gtu7379q4jj610s0hcabn02.jpg)

#### ‼️baseCreateRenderer

> 重要方法，很长很臭，主要返回三个东西。
>
> - render
> - hydrate
> - createApp（vue导出使用的createApp）
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-core/src/renderer.ts#L334

### rendererOptions

![image-20210826144613014](https://tva1.sinaimg.cn/large/008i3skNgy1gtu6ukw8adj60y20bc3yz02.jpg)

#### extend

> 根据源码，其实就是 Object.assign，合并对象

#### patchProp

> （解析待补充）
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/patchProp.ts#L13

#### nodeOps

> （解析待补充）
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/nodeOps.ts#L9

## injectNativeTagCheck

> 在开发环境下，给app.config插入isNativeTag属性，该属性是一个函数，用来判断标签是否是原始标签

![image-20210826160833212](https://tva1.sinaimg.cn/large/008i3skNgy1gtu9890ddkj61160icmyy02.jpg)

## injectCompilerOptionsCheck

> 开发环境下，插入编译器选项检查
>
> 源码参考：https://github.com/vuejs/vue-next/blob/master/packages/runtime-dom/src/index.ts#L136

## normalizeContainer

![image-20210826162236559](https://tva1.sinaimg.cn/large/008i3skNgy1gtu9mvy8ndj612i0u0tc302.jpg)

### window.ShadowRoot

> 表示window的根节点，参考：https://developer.mozilla.org/zh-CN/docs/Web/API/ShadowRoot。
>
> mode的值为closed标签节点不可访问。



## compatUtils

### warnDeprecation

## DeprecationTypes

### GLOBAL_MOUNT_CONTAINER

