深入Fiber，概述react新的协调算法（reconciliation）

![Inside-Fiber_-in-depth-overview-1](https://images.indepth.dev/images/2020/10/Inside-Fiber_-in-depth-overview-1.png)

> react是一个构建用户页面的javascript库，其核心是跟踪组件状态更改（state）并将更新状态投影到屏幕上的机制。这个过程就叫协调 `reconciliation` 。调用setState方法，框架就会检查state或者props是否发生变化并在UI上重新呈现组件。

## 虚拟DOM

react文档对机制提供了一个全面的概述：`react元素`，`生命周期`，`render方法`，以及应用于组件和子组件的`diff算法`。从render方法返回不可变的 <u>`react元素`的树</u> 通常被称为**“虚拟DOM”**。

## **Fiber**算法

除了<u>react元素树（虚拟DOM）</u>之外，框架还会有一个内部实例树（组件，DOM节点等）来保持状态。从`v16.0.0`开始，react推出了这个内部实例树的实现，**Fiber**算法。



## 一个🌰

![tmp1](https://images.indepth.dev/images/2019/07/tmp1.gif)

这里有个简单的例子，有一个按钮，可以递增呈现屏幕上的数字

实现方法：

![image-20210827224913979](https://tva1.sinaimg.cn/large/008i3skNgy1gtvqfi9onwj61890u0ad002.jpg)

这是一个简单的组件，render方法返回两个元素，button和span。

程序在第一次渲染和状态更新之后会执行一些操作：

- 更新state中的count
- 检索并比较ClickCounter以及它的props
- 更新span元素的props（这里span的内容实际是span的children，也属于props）

在这个过程中（协调）还会执行其他操作，比如调用[lifecycle methods](https://reactjs.org/docs/react-component.html#updating)（生命周期方法）或更新[refs](https://reactjs.org/docs/refs-and-the-dom.html)，所有的这些活动在fiber架构中统称为“**work**”，这个work类型通常取决于`react元素`的类型，这个类型由`createElement`函数的第一个参数定义，这个函数通常用在`render`方法中创建元素。

> react中有许多种元素，类组件，功能组件，host组件（DOM节点，比如：div，span等）

## 从react元素到fiber节点

react中的每一个组件都有一个UI表示，我们可以调用render方法返回视图或模版，下面是ClickCounter组件的模版：

![image-20210827231841825](https://tva1.sinaimg.cn/large/008i3skNgy1gtvra4r7ykj612k0cct9k02.jpg)

### react元素

一旦模版通过jsx编译器，就会编译成react元素，实际上render返回之前就会编译成react元素，ClickCounter也就变成这样了：

![image-20210827232122665](https://tva1.sinaimg.cn/large/008i3skNgy1gtvrcwtxqgj60ts0wc40102.jpg)

在render方法中，react.createElement的调用将创建如下两个数据结构：

![image-20210827232302005](https://tva1.sinaimg.cn/large/008i3skNgy1gtvren3ovjj60sg0tcgn402.jpg)

> React 将属性 $$typeof 添加到这些对象中，以唯一地将它们标识为 React 元素。

### Fiber节点

在协调（**reconciliation**）过程中，`render`返回的每一个`react元素`都会合并到`Fiber节点树`中，每一个`react元素`都有一个对应的`Fiber节点`，与`react元素`不同，`Filber节点`不是每次渲染都会重新创建，`Filber节点`用来保存组件状态和Dom的可变数据结构。



前面讲过，协调过程会根据`react元素`的类型执行不同的操作活动，在上面的例子中，对于类组件ClickCounter，它调用`生命周期方法`和`render`方法，而对于span等host组件，则会执行DOM的突变。

<u>每一个react元素都会被转换成相对应的Fiber节点</u>，**该节点会描述需要完成的工作。**

你可以将Fiber看成一种数据结构，它表示一些要做的工作，或者是一个工作单元。Fiber架构也提供了一个方便的方式跟踪，计划，暂停，终止工作。



当一个`react元素`第一次被转换成一个`fiber节点`时，react使用该元素的`数据`通过`createFiberFromTypeAndProps`函数创建一个`fiber`，在后面的更新中，<u>react会重用fiber节点，</u><u>并使用相应的react元素中的数据更新必要的属性</u>。如果相应的react元素不再从render函数返回，那么react可能还需要基于key，prop移动层次结构中的节点，或者删除它。



因为react为每一个`react元素`创建一个`fiber`，因此我们有这些`react元素树`，也就会有一个`fiber树`。在实例程序中，它看起来是这样的：

![image-20210830110912277](https://tva1.sinaimg.cn/large/008i3skNgy1gtyn21yz6xj60g107tmxc02.jpg)

所有的fiber节点通过链表链接，`fiber节点`有以下属性：<u>子节点，兄弟节点以及返回节点</u>。



### 在进程树中的current树 和 work

在第一次渲染之后，react最终会得到一个用于反映渲染UI应用程序状态的fiber树，这个树通常称为current。当react开始处理更新时，它会构建一个所谓workInProgess树，该树反映后面将要刷新到屏幕的状态。

#### workInProgess树

所有的工作都在`workInProgress树`中的`fiber`执行。当react遍历`current树`时，它会为每一个现有的`fiber节点`创建一个**`alternate`（备用节点）**，该节点构成`workInProgress树`，使用`render方法`返回的react元素中的数据创建该节点。一旦更新被处理并且所有相关的工作都完成了，react将会把备用树刷新到屏幕上，一旦这个`workInProgress树`在屏幕上呈现，它也就变成了`current树`。

react的核心原则是一致性。react总是一次性更新DOM，不会只显示部分结果。workInProgress树充当用户不可见的“草稿”。因此react会先处理所有的组件，然后将它们的更新刷新到屏幕上。

在源代码中，你可以看到有许多函数会从current树和workInProgress树中获取fiber节点，比如：

![image-20210830114510524](https://tva1.sinaimg.cn/large/008i3skNgy1gtyo3fy02nj61bw0bcdgj02.jpg)

每一个fiber节点在备用字段中保存对来自另一棵树的对应引用，比如：current树中的节点指向workInProgress树中的节点，反之亦然。

### 副作用（effects）

我们可以将react中的组件看作是一个使用state和props来计算UI展示的函数。每一个活动，比如Dom的突变或者调用生命周期的方法，都应该被视为一种副作用（`effects`）。文档也有提到：

> You’ve likely performed data fetching, subscriptions, or manually **changing the DOM** from React components before. We call these operations “side effects” (or “effects” for short) because they can affect other components and can’t be done during rendering. 
>
> 您以前可能已经从react组件执行过数据获取、订阅或手动更改DOM。我们称这些操作为“副作用”(或简称“效果”)，因为它们会影响其他组件，并且不能在渲染过程中完成。

大多数state和props的更新都会导致副作用（`effects`），除了更新功能之外，`fiber节点`也具有跟踪副作用（`effects`）的方便机制，每一个`fiber节点`都具有与其相关联的副作用（`effects`）。它们被编码在`effectTag`字段中。



因此fiber中的effects基本上定义了在处理更新之后实例需要完成的工作。对于`Host组件`（div，span，p等），[工作](https://github.com/facebook/react/blob/b87aabdfe1b7461e7331abb3601d9e6bb27544bc/packages/shared/ReactSideEffectTags.js)就包括了<u>添加，更新和删除元素</u>。对于`类组件`，<u>react可能需要更新refs并调用componentDidMount和componentDidUpdate生命周期方法</u>，还有其他类型fiber相应的副作用（effects）

### Effects list

react更新过程非常快，为了达到这水平的性能，它采取了一些技术。**其中之一是构建一个线性的fiber节点列表，以便快速迭代。**迭代的线性列表比树要快得多，而且没有必要在没有副作用（effects）的节点上花费时间。

此列表的目标是**标记具有与其相关联的Dom更新或其他效果节点**。此列表是`finishedWork树`的子集，并且使用`nextEffect`属性（而不是Current树和workInProgress树中使用的子属性）进行链接。



可以把Effect list想像成一棵圣诞树，用“圣诞灯”把所有具有副作用（`effects`）的节点捆绑起来，为了可视化这一点，让我们假设下面的fiber节点树中突出显示的节点有一些工作要做。比如，更新将导致c2插入到Dom、d2和c1中更改属性，并导致b2启动生命周期方法。Effects list将它们链接到一起，因此react可以跳过其他节点：

![image-20210830141323710](https://tva1.sinaimg.cn/large/008i3skNgy1gtysdpk5rfj60e20addg602.jpg)

可以看到，带有副作用（`effects`）的节点是如何链接在一起的。在遍历节点时，react使用`firstEffect`指针指出列表从哪里开始。因此上面的列表可以表示为一个线性列表，如下：

![image-20210830141606171](https://tva1.sinaimg.cn/large/008i3skNgy1gtysgh7khqj60ki03fq3202.jpg)

### fiber树的根元素

每一个react应用程序都有一个或多个作为容器的Dom元素，在我们实例中，它是包含Id容器的div元素。

![image-20210830142031437](https://tva1.sinaimg.cn/large/008i3skNgy1gtysl2zu1oj613i0ccjse02.jpg)

react会为每一个容器创建一个fiber对象，你可以使用Dom的引用访问它：

![image-20210830142150887](https://tva1.sinaimg.cn/large/008i3skNgy1gtysmgltp8j616w0bcq3j02.jpg)



这个fiberRoot是react保存fiber树的参考点，它存储在fiberRoot的current属性中：

![image-20210830142338831](https://tva1.sinaimg.cn/large/008i3skNgy1gtysoc1dy6j60t40bcwet02.jpg)



fiber树以一种特殊类型的fiber节点开始，即HostRoot。它是在内部创建的，充当最顶层组件的父组件，又一个通过setNode属性从HostRoot fiber节点返回到FiberRoot的链接。

![image-20210830142748788](https://tva1.sinaimg.cn/large/008i3skNgy1gtyssqpyqmj60wm0ccjrv02.jpg)



你可以通过fiber根访问最上面的HostRoot fiber 节点来探索fiber树，或者从一个组件实例中获得一个单独的fiber节点：

![image-20210830142953814](https://tva1.sinaimg.cn/large/008i3skNgy1gtysuv39e7j60wm0bcaae02.jpg)

### fiber节点的结构

下面是ClickCounter组件创建的fiber节点的结构：

![image-20210830143101605](https://tva1.sinaimg.cn/large/008i3skNgy1gtysw0csgzj60pi0ncwg102.jpg)

以及span Dom元素：

![image-20210830143136802](https://tva1.sinaimg.cn/large/008i3skNgy1gtyswmg8r5j60pi0nc40402.jpg)



在fiber节点中有相当多的字段，在前面已经讲过`alternate`，`effectTag`，`nextEffect`的字段用途。下面我们来看看其他的字段。

#### stateNode

保存对组件的类实例、Dom节点或与fiber节点关联的其他react元素类型的引用。通常，我们可以说此属性用于保存与fiber相关联的本地状态。

#### type

定义fiber关联的函数或类。对于类组件，它指向构造函数，对于Dom元素，它指定HTML标记。

#### tag

定义[fiber的类型](https://github.com/facebook/react/blob/769b1f270e1251d9dbdce0fcbd9e92e502d059b8/packages/shared/ReactWorkTags.js)。在协调算法中使用它来确定需要完成哪些工作。比如前面提到的，根据react元素的类型，`work`（工作）会有所不同。函数`createFiberFromTypeAndProps`将一个react元素映射到相应的fiber节点类型。在我们的应用程序中，ClickeCounter组件的属性tag为1，表示一个`ClassComponent`，而span元素的属性tag为5，表示一个`HostComponent`。

#### updateQueue

state更新、回调和Dom更新的队列。

#### memoizedState

用于创建输出的fiber的状态。在处理更新时，它反映屏幕上当前呈现的状态。

#### memoizedProps

在上一个渲染期间用于创建输出fiber的props

#### pendingProps

根据react元素中的新数据更新的props，需要应用到子组件或Dom元素。

#### key

具有一组子项的唯一标识符，可以帮助确定哪些项已更改，哪些项已添加或从列表中删除。它与react描述的“[列表和键](https://reactjs.org/docs/lists-and-keys.html#keys)”功能相关



你可以在[这里](https://github.com/facebook/react/blob/6e4f7c788603dac7fccd227a4852c110b072fe16/packages/react-reconciler/src/ReactFiber.js#L78)找到一个fiber节点的完整结构。上面的解析中省略了一些字段，比如指针的子元素（`child`）、兄弟元素（`sibling`）和返回元素（`return`）。以及一类特定于`Scheduler`的字段，如：`expirationTime`、`childExpirationTime`和`mode`。



## 通用算法

react协调中的两个主要的阶段：**`render`**和**`commit`**。















[文章摘要](https://indepth.dev/posts/1008/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react)

