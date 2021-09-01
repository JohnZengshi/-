深入了解react hook是怎么实现的。



hook从根本上来讲是封装状态和代码逻辑的一种简单方法。最初是在react中引入，并且已经被他框架接受，比如vue，svelte，甚至被封装为一个[纯js库](https://github.com/getify/TNG-Hooks)。hook的js设计模式需要对javascript中的闭包有很好的理解。



本文将简单实现react hook。

### 闭包

react中使用hook的目的是完全避免类和高阶组件的复杂性。hook会涉及到两个需要理解的问题，一个是执行上下文，一个是闭包（不再需要使用“this”）。

闭包简单来说就是一个函数能访问这个函数之外的变量。

> *Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.*

### useState

下面有个很好说明的例子：

![image-20210901151633670](https://tva1.sinaimg.cn/large/008i3skNgy1gu15g1cm4nj611k0rc77q02.jpg)

上面的代码中，我们创建了一个react的简易版useState，函数中有两个内部函数，state和setState。state返回上面定义的局部变量_val，setState设置局部变量。

但是这里实现的state是一个getter函数，这并不理想，后面会稍做修改。重要的是，通过foo和setFoo，我们可以访问和操作内部变量_val，这个引用称为闭包。



#### 在函数组件中的用法

下面是一个计数器组件：

![image-20210901153016168](https://tva1.sinaimg.cn/large/008i3skNgy1gu15u9mpflj611u0mctap02.jpg)

这里使用console.log输出我们的state，而不是渲染Dom，使用render模拟react的渲染过程。

上面的例子中，我们导出是state是一个函数而不是变量，但是如果我们直接导出_val，会有一个bug：

![image-20210901153440819](https://tva1.sinaimg.cn/large/008i3skNgy1gu15yuz9kij60sw0ncdi302.jpg)

这是state闭包的一个问题，当我们从useState的输出中解构foo时，它引用了初始useState调用的_val，并且不会再更改。



#### 模块模式（The Module Pattern）

利用[模块模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)中的闭包可以解决上面的问题。

简单的来说就是把闭包转移到模块范围内的闭包中：

![image-20210901154846983](https://tva1.sinaimg.cn/large/008i3skNgy1gu16dj6eq7j60xm0sc41102.jpg)

这样就可以达到跟踪到组件状态的目的，并且useState对外导出的是一个变量，当组件render时，每一次都能正确的使用闭包分配内部_val值：

![image-20210901154717672](https://tva1.sinaimg.cn/large/008i3skNgy1gu16bze1ftj60xm0mctar02.jpg)

> render方法只是模拟了react的渲染过程，每次更新了state，都要render一次（react的一个特性是一次性更新Dom），真正使用的时候react会帮我们做这一步。

### useEffect

与useState不同，useEffect是异步执行的。不过实现也类似：

![image-20210901160222013](https://tva1.sinaimg.cn/large/008i3skNgy1gu16rodgj6j61gk0u0djq02.jpg)

usag：

![image-20210901160039699](https://tva1.sinaimg.cn/large/008i3skNgy1gu16pvui7lj60u00z3tc802.jpg)

当组件render的时候，useEffect会重新运行，根据_deps判断需不需要执行callback。



### Not Magic, just Arrays

上面的useState和useEffect都是大概的实现，当时如果要使用多个相同的hook时，_val或者_deps就会被覆盖（只有一个）。

如 《[React hooks: not magic, just arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)》所写，我们可以使用数组，来解决 Hooks 的复用问题。

利用数组将_val和_deps添加到hook队列中，这样就不会出现覆盖问题了。

![image-20210901162027167](https://tva1.sinaimg.cn/large/008i3skNgy1gu17ahm9joj61840u0td702.jpg)

> [hooks[currentHook++], setState]，这段代码的意思是导出之后currentHook才会+1，比如：currentHook是0，导出的就是[hook[0],setState]，之后currrentHook才会+1。

因此，基本的原理就是一个hook数组和索引。

### 使用hook的规则

- **不要在循环、条件或嵌套函数中调用 Hooks**。
- **只在react函数中调用hook。**

#### 堆栈图描述react hook执行过程

![image-20210901165508522](https://tva1.sinaimg.cn/large/008i3skNgy1gu18akvvjcj60u00v6dji02.jpg)

用堆栈图描述上面的过程：

![react hook 机制堆栈图](https://tva1.sinaimg.cn/large/008i3skNgy1gu1a6tlq78j60mn1qcwj402.jpg)

可以很清晰的看到，由于hook是一个数组，所有当在if判断语句里面使用hook，一旦条件不成立，第二次重新渲染的时候索引将会出现错乱。比如：

![image-20210901180750497](https://tva1.sinaimg.cn/large/008i3skNgy1gu1ae832z3j613f0u0diu02.jpg)

### 真正的 React 实现

（待补充）

