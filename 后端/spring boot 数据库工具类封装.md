### DataBaseUtils类

#### 1，用到的static值

![image-20210809163909325](https://tva1.sinaimg.cn/large/008i3skNgy1gtamkwalwaj31s40gcn1b.jpg)

##### PropertiesExtractor类

> （获取application.propreties的值）参考：https://juejin.cn/post/6992867961992118308

#### 2，获取数据库链接

![image-20210805172715746](https://tva1.sinaimg.cn/large/008i3skNgy1gt61hoggcrj31700mc40o.jpg)

##### DriverManager.getConnection

> 用于建立数据库连接

#### 3，关闭数据库链接

![image-20210809164447789](https://tva1.sinaimg.cn/large/008i3skNgy1gtamqrh6m3j310o0pcabr.jpg)

#### 4，获取数据库下的所有表名

![image-20210805181359045](https://tva1.sinaimg.cn/large/008i3skNgy1gt62uav0h5j31ba0u0wie.jpg)

##### DatabaseMetaData类

> 数据库的元数据，全部信息

##### ResultSet类

> 表示数据库结果集的数据表，迭代对象，配合while使用

###### resultSet.getString

> 获取表名：固定参数3

```
resultSet.getString(3)
```

#### 5，获取某个表的元数据

![image-20210809162811209](https://tva1.sinaimg.cn/large/008i3skNgy1gtam9ha4azj31850u00x6.jpg)

##### PreparedStatement类

> 由connection.prepareStatement获取，向数据库发送参数化SQL语句

##### ResultSetMetaData类

> 由preparedStatement.getMetaData获得，可用于获取有关ResultSet对象中列的类型和属性的信息的对象（获取某一张表的元数据）

#### 6，获取表中所有字段名称

![image-20210809163340658](https://tva1.sinaimg.cn/large/008i3skNgy1gtamf79zjjj31qa0pc0wg.jpg)

#### 7，获取表中所有字段类型

![image-20210809163440369](https://tva1.sinaimg.cn/large/008i3skNgy1gtamg89owxj31qa0pc780.jpg)

#### 8，获取表中所有字段注释

![image-20210809163527146](https://tva1.sinaimg.cn/large/008i3skNgy1gtamh16uy1j31pw0scaeg.jpg)



#### 9，统一获取数据库表信息（结合上面的方法）

![image-20210818170213150](https://tva1.sinaimg.cn/large/008i3skNgy1gtl1tms79ij60za0u0afq02.jpg)

##### resultSetMetaData.isAutoIncrement

> 判断字段是否是自增，**注意的是这里的索引要i+1**

##### TableInfo类，ColumnInfo类

> 自定义类，用于存储表的信息

![image-20210818165935359](https://tva1.sinaimg.cn/large/008i3skNgy1gtl1qx7mf7j60t617cwhd02.jpg)

##### 测试：

![image-20210809164606530](https://tva1.sinaimg.cn/large/008i3skNgy1gtams4ladcj311c047abe.jpg)

