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



## 在进程树中的current树 和 work

在第一次渲染之后，react最终会得到一个用于反映渲染UI应用程序状态的fiber树，这个树通常称为current。当react开始处理更新时，它会构建一个所谓workInProgess树，该树反映后面将要刷新到屏幕的状态。

### workInProgess树

所有的工作都在`workInProgress树`中的`fiber`执行。当react遍历`current树`时，它会为每一个现有的`fiber节点`创建一个**`alternate`（备用节点）**，该节点构成`workInProgress树`，使用`render方法`返回的react元素中的数据创建该节点。一旦更新被处理并且所有相关的工作都完成了，react将会把备用树刷新到屏幕上，一旦这个`workInProgress树`在屏幕上呈现，它也就变成了`current树`。

react的核心原则是一致性。react总是一次性更新DOM，不会只显示部分结果。workInProgress树充当用户不可见的“草稿”。因此react会先处理所有的组件，然后将它们的更新刷新到屏幕上。

在源代码中，你可以看到有许多函数会从current树和workInProgress树中获取fiber节点，比如：

![image-20210830114510524](https://tva1.sinaimg.cn/large/008i3skNgy1gtyo3fy02nj61bw0bcdgj02.jpg)

每一个fiber节点在备用字段中保存对来自另一棵树的对应引用，比如：current树中的节点指向workInProgress树中的节点，反之亦然。

## 副作用（effects）

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

## fiber树的根元素

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

## fiber节点的结构

下面是ClickCounter组件创建的fiber节点的结构：

![image-20210830143101605](https://tva1.sinaimg.cn/large/008i3skNgy1gtysw0csgzj60pi0ncwg102.jpg)

以及span Dom元素：

![image-20210830143136802](https://tva1.sinaimg.cn/large/008i3skNgy1gtyswmg8r5j60pi0nc40402.jpg)



在fiber节点中有相当多的字段，在前面已经讲过`alternate`，`effectTag`，`nextEffect`的字段用途。下面我们来看看其他的字段。

### stateNode

保存对组件的类实例、Dom节点或与fiber节点关联的其他react元素类型的引用。通常，我们可以说此属性用于保存与fiber相关联的本地状态。

### type

定义fiber关联的函数或类。对于类组件，它指向构造函数，对于Dom元素，它指定HTML标记。

### tag

定义[fiber的类型](https://github.com/facebook/react/blob/769b1f270e1251d9dbdce0fcbd9e92e502d059b8/packages/shared/ReactWorkTags.js)。在协调算法中使用它来确定需要完成哪些工作。比如前面提到的，根据react元素的类型，`work`（工作）会有所不同。函数`createFiberFromTypeAndProps`将一个react元素映射到相应的fiber节点类型。在我们的应用程序中，ClickeCounter组件的属性tag为1，表示一个`ClassComponent`，而span元素的属性tag为5，表示一个`HostComponent`。

### updateQueue

state更新、回调和Dom更新的队列。

### memoizedState

用于创建输出的fiber的状态。在处理更新时，它反映屏幕上当前呈现的状态。

### memoizedProps

在上一个渲染期间用于创建输出fiber的props

### pendingProps

根据react元素中的新数据更新的props，需要应用到子组件或Dom元素。

### key

具有一组子项的唯一标识符，可以帮助确定哪些项已更改，哪些项已添加或从列表中删除。它与react描述的“[列表和键](https://reactjs.org/docs/lists-and-keys.html#keys)”功能相关



你可以在[这里](https://github.com/facebook/react/blob/6e4f7c788603dac7fccd227a4852c110b072fe16/packages/react-reconciler/src/ReactFiber.js#L78)找到一个fiber节点的完整结构。上面的解析中省略了一些字段，比如指针的子元素（`child`）、兄弟元素（`sibling`）和返回元素（`return`）。以及一类特定于`Scheduler`的字段，如：`expirationTime`、`childExpirationTime`和`mode`。



## 通用算法

react协调中的两个主要的阶段：**`render`**和**`commit`**。



在第一个render阶段，react对通过`setState`或`react.render`调度的组件应用更新，并找出UI中需要更新的内容。如果是初次渲染，react将`render方法`返回的每一个`react元素`创建一个fiber节点。在之后的更新中，**将重用并更新现有react元素的fiber**。该阶段的结果是一棵标有`side- effect`（副作用）的`fiber节点`树。这个`effect`（副作用）描述了在接下来的提交阶段需要完成的工作。在`commit`（提交阶段）阶段，react获取标记有effect（副作用）的fiber树，并将其应用于实例。**之后遍历`Effects list`（上面有讲到）并执行Dom更新和用户可见的其他更改。**



**第一个render阶段是可以异步执行的**。根据可用的时间，react可以处理一个或多个fiber节点，然后停下完成的工作并触发一些事件。之后从停止的地方继续下去。但有时会抛弃已经完成的工作，从头开始。之所以可以暂停，是因为在<u>此阶段执行的工作不会导致用户可见的更改</u>，比如Dom的更新。相反，下面的**`commit`提交阶段始终时同步的**。<u>这是因为这个阶段执行的工作会导致用户可见的更改，比如Dom的更新</u>。**这也是为什么react需要一次性更新Dom**。



调用生命周期的方法是react执行的工作类型之一。一些方法在render阶段调用，另一些方法在commit阶段调用。

### render阶段会调用的方法：

- [UNSAFE_]componentWillMount (deprecated)    // 组件将挂起（已弃用）
- [UNSAFE_]componentWillReceiveProps (deprecated)    // 组件将接收props（已弃用）
- getDerivedStateFromProps    // 从props获取派生的state
- shouldComponentUpdate  // 是否应更新组件
- [UNSAFE_]componentWillUpdate (deprecated)    // 组件将更新（已弃用）

在渲染阶段执行的某些遗留生命周期方法被标记为对16.3版不安全（[UNSAFE_]），它们将在未来的16.x版本中弃用，而不带不安全前缀的对应版本将在17.0中删除，[更多参考](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)



### commit阶段会调用的方法：

- getSnapshotBeforeUpdate    //更新前获取快照
- componentDidMount    // 组件已经挂载
- componentDidUpdate  // 组件已经更新
- componentWillUnmount   // 组件将卸载

因为这些方法是在同步提交阶段执行的，所以它们可能会包含副作用并影响Dom。



## 渲染阶段（render）

协调算法总是使用[renderRoot](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L1132)函数从最上面的HostRoot fiber阶段开始。但是react会跳过已经处理过的fiber节点。直到找到为完成工作的fiber节点。比如，如果在组件树深处调用setState，react将从顶部开始，但很快跳过父节点，直到达到其setState方法被调用的组件。



### work loop的主要步骤（workLoop函数）

所有的fiber节点都在[workLoop函数](https://github.com/facebook/react/blob/f765f022534958bcf49120bf23bc1aa665e8f651/packages/react-reconciler/src/ReactFiberScheduler.js#L1136)（工作循环）中处理。下面是部分代码的实现：

![image-20210831114444342](https://tva1.sinaimg.cn/large/008i3skNgy1gtztpaxq86j60zy0hc75g02.jpg)

#### nextUnitOfWork

在上面的代码中，`nextUnitOfWork`保存了对`workInProgress树`中有一些工作要做的fiber节点的引用。当react遍历fiber树时，它使用这个变量来知道是否还有其他fiber节点没有完成工作，处理当前fiber节点后，这个变量会包含下一个fiber节点的引用，或者为null，当为null时，react会退出workLoop（工作循环）并准备提交更改（commit）。



#### 四个主要函数

有四个函数主要用于遍历树并启动或完成工作：

- [performUnitOfWork](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L1056)  // 执行工作单元
- [beginWork](https://github.com/facebook/react/blob/cbbc2b6c4d0d8519145560bd8183ecde55168b12/packages/react-reconciler/src/ReactFiberBeginWork.js#L1489)  //开始工作
- [completeUnitOfWork ](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L879)  //完成工作单元
- [completeWork](https://github.com/facebook/react/blob/cbbc2b6c4d0d8519145560bd8183ecde55168b12/packages/react-reconciler/src/ReactFiberCompleteWork.js#L532)   // 完成工作



下面是遍历fiber树的动画。右边是函数的简化实现，每一个函数都需要处理一个fiber节点，当react沿着树下行时，你可以看到当前活动的fiber节点的更改。这个动画可以清楚的看到算法是如何从一个分支转移到另一个分支，它首先完成children的工作，然后才转移到parent。

![tmp2](https://images.indepth.dev/images/2019/08/tmp2.gif)

> 注意：垂直分支表示兄弟节点，水平分支表示子节点。



##### performUnitOfWork和beginWork

![image-20210831120804899](https://tva1.sinaimg.cn/large/008i3skNgy1gtzudl2w49j61100ncmz802.jpg)

函数`performUnitOfWork`从`workInProgress树`接受一个fiber节点，并通过调用`beginWork函数`启动工作。这个函数将启动fiber需要执行的所有活动。函数`beginWork`始终返回一个指针，<u>指向循环中需要处理的下一个子元素或者null。</u>



如果有下一个元素，它将被分配给`workLoop函数`中的`变量nextUnitOfWork`。但是，如果没有子节点，则react知道它已经到达分支的末尾，因此可以完成当前节点。一旦节点完成，它将需要为兄弟节点执行工作，然后回溯到父节点。这是在`completeUnitOfWork函数`中完成的：

![image-20210831135711027](https://tva1.sinaimg.cn/large/008i3skNgy1gtzxj57jyfj60v10u0tc102.jpg)



可以看到，这个函数的要点是一个大while循环。当`workInProgress节点`没有子节点，react进入此函数。当完成当前fiber的工作之后，它会检查是否有同级的fiber。如果找到，react退出该函数并返回指向同级的指针。它将被分配到`nextUnitOfWork变量`，react将从这个兄弟变量开始执行分支的工作。

**重要的是要明白，只有在以子节点开始的所有分支都完成之后，它才会完成父节点和回溯的工作。**

`completeUnitOfWork函数`主要用于迭代目的，主要的活动发生在`beginWork`和`completeWork`函数中。



## 提交阶段（commit）

该阶段从函数[completeRoot](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L2306)开始。这就是React更新DOM并调用突变前和突变后生命周期方法的地方。



当react达到这个阶段时，它有2棵树和1个副作用列表（`effect list`，前面讲到）。<u>第一棵树表示当前呈现在屏幕上的状态。另一棵是在渲染阶段构建的备用树。</u>

在源代码中称为`finishWork`或`workInProgress`，表示需要在屏幕上反映的状态。

备用树通过子指针和同级指针类似地链接当前树。

然后还有一个副作用列表（`effect list`），通过`nextteffect指针`链接`finishedWork树`的节点子集。请记住，副作用列表是运行render阶段的结果。渲染的关键是确定需要插入、更新或删除哪些节点，以及需要调用哪些组件的生命周期方法。**它就是一个在提交阶段迭代的节点集。**

### commitRoot

提交阶段运行的主函数是[commitRoot](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L523)，基本上，它执行以下操作：

1. 在标记有快照（`Snapshot`）的节点上调用`getSnapshotBeforeUpdate`生命周期方法
2. 在标记有删除（`Deletion`）的节点上调用`ComponentWillUnmount`生命周期方法
3. 执行所有DOM插入、更新和删除
4. 将`finishedWork`树设置为当前
5. 在标记有放置（`Placement`）的节点上调用`ComponentDidMount`生命周期方法
6. 在标记有更新（`Update`）的节点上调用`ComponentDidUpdate`生命周期方法



在调用`getSnapshotBeforeUpdate`方法之后，react提交树中的所有副作用。它分为两个阶段：

第一个阶段执行所有Dom的插入、更新、删除和ref卸载。然后react将`finishedWork`分配给`FiberRoot`，将`workInProgress`树标记为当前树，这是在提交阶段的第一次遍历完成的，因此在`componentWillUnmount`期间上一棵树仍然是当前的，在第二个阶段之前，`componentDid Mount/Update`期间的树是当前的。

第二个阶段react调用所有其他的生命周期方法，ref回调。这些方法作为单独的传递执行，因此整个树中的所有放置、更新和删除都已被调用。



下面是运行上述步骤的函数要点：

![image-20210831145453848](https://tva1.sinaimg.cn/large/008i3skNgy1gtzz75l949j60t80gct9v02.jpg)



### 预变异生命周期方法

例如，下面的代码遍历效果树并检查一个节点是否具有 `Snapshot` 副作用（effect）:

![image-20210831145633703](https://tva1.sinaimg.cn/large/008i3skNgy1gtzz8vwowwj612w0ocmz902.jpg)

对于类组件，这个effect意味着调用`getSnapshotBeforeUpdate`生命周期方法。

### 更新 DOM

[commitAllHostEffects](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L376)是react执行Dom更新的函数，该函数基本上定义了一个节点需要执行的操作类型：

![image-20210831145848172](https://tva1.sinaimg.cn/large/008i3skNgy1gtzzb7p7z2j60u00uigo502.jpg)

> react调用`componentWillUnmount`方法其实就是`commitDeletion`函数中删除过程的一部分。

### 变异后生命周期方法

[commitAllLifecycles](https://github.com/facebook/react/blob/95a313ec0b957f71798a69d8e83408f40e76765b/packages/react-reconciler/src/ReactFiberScheduler.js#L465)是一个函数，其中react调用所有剩余生命周期的方法`componentDidUpdate`和`componentDidMount`。



完结撒花(*^o^*)



[文章摘要](https://indepth.dev/posts/1008/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react)

