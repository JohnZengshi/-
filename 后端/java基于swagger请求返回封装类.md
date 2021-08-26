java基于swagger请求返回封装类

### 自定义CommonResult类

![image-20210826114616401](https://tva1.sinaimg.cn/large/008i3skNgy1gtu1nd0oc4j61ar0u0win02.jpg)

#### @Data

> 因为code，data，message都是private私有变量，需要加上@Data，或者手动实现get，set，不然返回的类spring会读取不到数据，前端会显示空。



#### @ApiModelProperty

> @ApiModelProperty注释允许我们控制特定于Swagger的定义，如描述(值)、名称、数据类型、示例值和模型属性的允许值。

#### 泛型T

> 数据（data）的类型

#### ResultCode

> 枚举ResultCode

![image-20210826110008059](https://tva1.sinaimg.cn/large/008i3skNgy1gtu0bclhtzj60uk0tcgnp02.jpg)

### 使用：

![image-20210826114803763](https://tva1.sinaimg.cn/large/008i3skNgy1gtu1p7t6fjj614a0gcwga02.jpg)

#### 效果：

![image-20210826115302812](https://tva1.sinaimg.cn/large/008i3skNgy1gtu1uej1tij60f305ddg802.jpg)

