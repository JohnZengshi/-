react源码解析话术：

Render（渲染器） Reconciler（协调器） Scheduler（调度器）







### React的导出：

![image-20210825123911391](https://tva1.sinaimg.cn/large/008i3skNgy1gtsxk4z4lnj60u0136afa02.jpg)

### createElement

> react对外导出的方法

![image-20210825101424716](https://tva1.sinaimg.cn/large/008i3skNgy1gtstdi15hjj61a40ccwfb02.jpg)

#### createElementWithValidation

> 使用验证创建元素

![image-20210825121635503](https://tva1.sinaimg.cn/large/008i3skNgy1gtswwliwfkj60u01ydajn02.jpg)

##### isValidElementType

> 判断是否是有效的元素类型，源码：https://github.com/facebook/react/blob/main/packages/shared/isValidElementType.js

##### createElement

> 创建并返回给定类型的新ReactElement。参考：https://reactjs.org/docs/react-api.html#createelement
>
> 这里使用createElementWithValidation的this和arguments替换createElement的this和arguments。
>
> 源码：https://github.com/facebook/react/blob/main/packages/react/src/ReactElement.js#L349

![image-20210825112322039](https://tva1.sinaimg.cn/large/008i3skNgy1gtsvd82b0ej60u02c3k1f02.jpg)

###### hasValidRef

> 判断config是否具有有效的ref，参考：https://github.com/facebook/react/blob/main/packages/react/src/ReactElement.js#L30

###### warnIfStringRefCannotBeAutoConverted

> 如果字符串引用无法自动转换，则发出警告（只在开发环境下使用），参考：https://github.com/facebook/react/blob/main/packages/react/src/ReactElement.js#L98

###### hasValidKey

> 判断config是否有有效key，参考：https://github.com/facebook/react/blob/main/packages/react/src/ReactElement.js#L42

###### RESERVED_PROPS

> 预留props，默认props

![image-20210825122136580](https://tva1.sinaimg.cn/large/008i3skNgy1gtsx1tba0xj60jm0hcjs002.jpg)

###### ‼️ReactElement

> 创建新的react元素。

![image-20210825123224178](https://tva1.sinaimg.cn/large/008i3skNgy1gtsxd1yy4nj60u01iigtp02.jpg)

###### 总结步骤（createElement）：

1. 创建props，判断config，config存在就就给props赋值，ref（校验），key（校验），self，source，等。
2. 把children跟后面可能会传的值统一组成赋值给props.children。
3. 如果type有默认props（type.defaultProps），则将type.defaultProps赋值给props。
4. 返回由ReactElement返回的对象

##### validateChildKeys

> 第三个参数之后参数验证（children，以及后面可能有的多个参数），参考：https://github.com/facebook/react/blob/main/packages/react/src/ReactElementValidator.js#L168

###### validateExplicitKey

> 参考：https://github.com/facebook/react/blob/main/packages/react/src/ReactElementValidator.js#L120

##### 总结步骤（createElementWithValidation）：

1. 判断是否是有效的元素类型（type），不是有效的元素类型（type），将会抛出一些错误信息
2. 使用createElement创建react对象（由$$typeof，type，key，ref，props，等一些属性组成的对象）
3. 验证children，以及后面可能传过来的参数。（抛出一些错误）
4. 验证props。（抛出一些错误）
5. 返回刚刚创建的react对象。





// 待补充