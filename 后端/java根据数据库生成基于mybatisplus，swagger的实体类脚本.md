java根据数据库生成基于mybatisplus，swagger的实体类脚本

#### 自定义GenerateMysqlEntity类

##### 单例模式：

![image-20210818171217066](https://tva1.sinaimg.cn/large/008i3skNgy1gtl243hy7ij618w0cc3zb02.jpg)

###### 入口：

![image-20210818171540436](https://tva1.sinaimg.cn/large/008i3skNgy1gtl27mh5wcj617u0fcmy402.jpg)

> generate方法下面讲

##### 数据库信息：

![image-20210818171153010](https://tva1.sinaimg.cn/large/008i3skNgy1gtl23ok7m0j616k0bcwf502.jpg)

###### DataBaseUtils

> 参考：https://juejin.cn/post/6994348019315376136

##### 用到的静态数据：

![image-20210818171418344](https://tva1.sinaimg.cn/large/008i3skNgy1gtl267h9y4j61dk0hctb702.jpg)

##### 生成实体类文件：

![image-20210825163133692](https://tva1.sinaimg.cn/large/008i3skNgy1gtt49vt7pij61480u00x502.jpg)

##### 生成class的所有内容：

![image-20210825163240779](https://tva1.sinaimg.cn/large/008i3skNgy1gtt4b1xschj60u00zmtdg02.jpg)

###### StringBuffer类

> 构造一个没有字符的字符串缓冲区，初始容量为16个字符

###### CommonUtils.getTransStr

> 参考：https://juejin.cn/post/6995853448730116110

###### getKeyStringList

> 生成Key列表(数据库表的字段列表)

![image-20210825160431256](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3hr1q3sj61cq0u079202.jpg)

生成效果：

![image-20210825160709000](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3khacutj609806s74g02.jpg)

###### getKeyLabelList

> 生成key对应的注释

![image-20210825160902204](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3mg29hij61d40rcadp02.jpg)

生成效果：

![image-20210825160935391](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3n0lv21j609505iglm02.jpg)

###### processAllAttrs

> 生成所有成员变量

![image-20210825161101716](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3oiyzsxj61ff0u0tec02.jpg)

生成效果：

![image-20210825161119316](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3otmkjmj60bx07c3yy02.jpg)

###### sqlType2JavaType

> 查找sql字段类型所对应的Java类型

![image-20210825161553396](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3tl5e1pj60u023ajyz02.jpg)



##### 总结步骤：

1. 获取表信息。（利用DataBaseUtils）
2. 获取根目录路径。（File类）
3. 返回packageOutPath（com.api.johnzeng.Entity）的绝对路径。
4. 生成packageOutPath（com.api.johnzeng.Entity）的file实例。
5. 生成packageOutPath（com.api.johnzeng.Entity）目录。
6. 遍历tableInfoList。
   1. 生成文件内容。
   2. 利用FileWriter，PrintWriter，将内容写入，输出到Entity（com.api.johnzeng.Entity）目录



#### 实体类的使用：

##### 配置MyBatisPlugsConfig

![image-20210825170021989](https://tva1.sinaimg.cn/large/008i3skNgy1gtt53v0t1yj60qg0ecmxs02.jpg)

##### 创建mapper

![image-20210825165845253](https://tva1.sinaimg.cn/large/008i3skNgy1gtt526pqotj60zw0dct9902.jpg)

> 将mapper放到Mapper目录下，MapperScan才会扫描到

##### service层使用

![image-20210825170345878](https://tva1.sinaimg.cn/large/008i3skNgy1gtt57dxzz0j613o0qcwhe02.jpg)

1. 使用@Autowired注入mapper
2. 初始化单例的QueryWrapper类
3. 使用方法参考maybatis plus文档
