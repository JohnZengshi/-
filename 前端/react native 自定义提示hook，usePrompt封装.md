#### 使用到的依赖：

##### react-native-root-siblings

> react-native中插入元素，参考：https://github.com/magicismight/react-native-root-siblings

##### react-spring

> react动画框架，参考：https://react-spring.io/



#### prompt组件：

##### PromptComponentProps

> 组件props

![image-20210812155226968](https://tva1.sinaimg.cn/large/008i3skNgy1gte23cx8qcj60u01ia0y302.jpg)

##### PromptComponentRef

> 组件ref，对外使用

![image-20210812155357999](https://tva1.sinaimg.cn/large/008i3skNgy1gte24sk2s7j610o0hcjsb02.jpg)

##### 显示标志

![image-20210812161624962](https://tva1.sinaimg.cn/large/008i3skNgy1gte2s5ilnkj60tu0ccq3e02.jpg)

##### useTransition

> 动画样式使用useTransition

![image-20210812161426802](https://tva1.sinaimg.cn/large/008i3skNgy1gte2q41k0pj60u00vajui02.jpg)

##### 定时关闭

![image-20210812161533016](https://tva1.sinaimg.cn/large/008i3skNgy1gte2r95l84j60oq0ncwg002.jpg)

##### 对外导出方法

![image-20210812161708660](https://tva1.sinaimg.cn/large/008i3skNgy1gte2sx3nqzj60hs0kc0t902.jpg)

##### 显示内容的组件

> 内容组件，由transitions（即上边useTransition包装的动画样式）包装

![image-20210812161851102](https://tva1.sinaimg.cn/large/008i3skNgy1gte2up1h5ej60u01ib43r02.jpg)

##### 最终导出

> 搭配Mask组件

![image-20210812162116978](https://tva1.sinaimg.cn/large/008i3skNgy1gte2x8968cj61380pcabr02.jpg)

#### mask组件：

##### useTransition

> 动画样式使用useTransition

![image-20210812160207467](https://tva1.sinaimg.cn/large/008i3skNgy1gte2da9clej60ts0icgmn02.jpg)

##### maskTransitions

> 组件使用maskTransitions包装

![image-20210812160146696](https://tva1.sinaimg.cn/large/008i3skNgy1gte2cx6mo3j61040u0tbd02.jpg)

##### 源码：

https://github.com/john-ui-kit/react-native/blob/main/src/components/common/Mask.tsx



#### 组合成usePrompt

##### RootSiblings

> 需要插入的组件ref

![image-20210812162332893](https://tva1.sinaimg.cn/large/008i3skNgy1gte2zkyd72j61380ec74v02.jpg)

##### PromptComponentRef

> Prompt组件的ref

![image-20210812162427603](https://tva1.sinaimg.cn/large/008i3skNgy1gte30j06uyj61060ecq3m02.jpg)

##### show和hide方法

![image-20210812162535714](https://tva1.sinaimg.cn/large/008i3skNgy1gte31pnjbmj60u013gq6302.jpg)



#### 使用：

![image-20210812162756741](https://tva1.sinaimg.cn/large/008i3skNgy1gte345np0gj60u00wxaci02.jpg)

#### 效果：

![Kapture 2021-08-12 at 16.35.05](https://tva1.sinaimg.cn/large/008i3skNgy1gte3bvt92ug608c0hltts02.gif)

#### 完整源码：

https://github.com/john-ui-kit/react-native/blob/main/src/hook/usePrompt.tsx