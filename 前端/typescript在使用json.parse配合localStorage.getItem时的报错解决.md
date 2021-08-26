在使用类似

```typescript
JSON.parse(localStorage.getItem(xxxx))
```

的使用会报错，如图：

![WX20200531-175050@2x](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfbt5usqfgj30w409cjtj.jpg)

原因：JSON.parse的第一个参数必须是string类型

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfbtvmxcx2j317o01i3yz.jpg)

但是localStorage.getItem的返回值可以是string或者null

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfbtw6pay0j30m801smxb.jpg" style="zoom:50%;" />

解决办法：

- 使用typeof判断localStorage.getItem返回的类型是不是string

  ```typescript
  let LOGIN_DATA;
  let _LOGIN_DATA_KEY = localStorage.getItem(xxxx);
  if (typeof _LOGIN_DATA_KEY === "string") {
    LOGIN_DATA = JSON.parse(_LOGIN_DATA_KEY);
  }
  ```

- 在JSON.parse里面加个兼容

  ```typescript
  JSON.parse(localStorage.getItem(xxxx) || '{}');
  ```

- 加类型断言（不推荐）：如果localStorage.getItem(xxxx)确实是一个null的话，运行时就会报错

  ```typescript
  const LOGIN_DATA = JSON.parse( localStorage.getItem(xxxx) as string );
  ```

  