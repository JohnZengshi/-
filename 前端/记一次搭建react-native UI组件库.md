



# 记一次搭建react-native UI组件库

## 1，创建一个组织，如图：

#### 1，找到Your organizations

#### 2，创建组织

![image-20210802100515884](https://tva1.sinaimg.cn/large/008i3skNgy1gt27uwsodcj317k07ggm6.jpg)

#### 3，填写信息

![image-20210802101024181](https://tva1.sinaimg.cn/large/008i3skNgy1gt2807c46sj30yf0u077h.jpg)

## 2，本地创建：

创建目录@john-ui-kit，参考：https://docs.npmjs.com/organizations/managing-organization-packages

创建react-native的UI库：@john-ui-kit/react-native

```
cd @john-ui-kit && mkdir react-native && cd react-native
```

#### 1，初始化

```
yarn init
```

![image-20210802102929963](https://tva1.sinaimg.cn/large/008i3skNgy1gt28k2t98gj306n049wek.jpg)

```
question name (test): // 包名
question version (1.0.0): //版本号
question description: 测试组件库 // 描述
question entry point (index.js): // 入口文件，
question repository url: // 资源Url
question author: John // 作者
question license (MIT): // 协议
question private: // 是否是私有package
```

#### 2，package.json详细：

> （参考：https://docs.npmjs.com/cli/v7/configuring-npm/package-json）

![image-20210802104704193](https://tva1.sinaimg.cn/large/008i3skNgy1gt292d6divj30u01gsgsc.jpg)



#### 3，目录结构：

> 源码：https://github.com/john-ui-kit/react-native

![image-20210802105856693](https://tva1.sinaimg.cn/large/008i3skNgy1gt29epfkkgj304l0a3aa7.jpg)

#### 4，编写组件

![image-20210802112346238](https://tva1.sinaimg.cn/large/008i3skNgy1gt2a4jeskbj306w03m746.jpg)

在src/components中随意编写tsx组件，最后只要在src根目录导出index即可。如下：（index.ts）

![image-20210802112520527](https://tva1.sinaimg.cn/large/008i3skNgy1gt2a66ah4fj31480ccq3w.jpg)



## 3，本地调试：

主要是在example当中创建，这里使用expo创建app，

![image-20210802113102792](https://tva1.sinaimg.cn/large/008i3skNgy1gt2ac484f2j306n085jrj.jpg)

#### 调试：

> 调用yarn example:start

![image-20210802113130290](https://tva1.sinaimg.cn/large/008i3skNgy1gt2acl24p5j30dc065mxx.jpg)

#### 说明：

在example当中需要引用根目录src当中的包，这时需要在example中的ts config中配置：

```
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "paths": {
      "@john-ui-kit/react-native": ["../src/index"] // 这里指定包的名称
    },
  },
}
```

##### 引用本地组件：

![image-20210802113522825](https://tva1.sinaimg.cn/large/008i3skNgy1gt2agm980gj312s0bct98.jpg)

## 3，构建与发布

#### react-native-builder-bob：

> 构建工具：（参考：https://github.com/callstack/react-native-builder-bob）

package.json中插入配置：

![image-20210802111518679](https://tva1.sinaimg.cn/large/008i3skNgy1gt29vqptg3j30w20rcdi6.jpg)

##### 打包：

> 使用bob build，或者 yarn build

![image-20210802111820539](https://tva1.sinaimg.cn/large/008i3skNgy1gt29yw3bb6j30ea064dgo.jpg)

会将src当中的代码打包进lib当中，遵循commonjs，和es module两种模块系统

#### release-it：

> 发布工具（可同时发布到npm，github，参考：https://npmjs.com/package/release-it）

##### .release-it.json：

```json
{
  "git": {
    "commitMessage": "chore: release ${version}", // 提交信息
    "tagName": "v${version}" // 标签
  },
  "npm": {
    "publish": true
  },
  "github": {
    "release": false
  },
  "plugins": {
    "@release-it/conventional-changelog": {
      "preset": "angular"
    }
  }
}
```

##### 发布：

> 执行release-it ，或者yarn release

![image-20210802112206851](https://tva1.sinaimg.cn/large/008i3skNgy1gt2a2ttjmoj30dz06eaau.jpg)

![image-20210802104704193](https://raw.githubusercontent.com/release-it/release-it/HEAD/docs/assets/release-it.svg?raw=true)

## 4，源码参考：

> https://github.com/john-ui-kit/react-native

