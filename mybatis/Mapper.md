# 映射器
映射是mybatis最强大的功能，映射器主要是对这一过程的描述。

## 基本概念
### 获得一个映射器
通过SqlSession.getMapper(xxxMapper.class)方法，可以获得一个映射器对象mapper。  
其中：
* xxxMapper通常为程序员编写的接口，该接口定义了一个映射器；  
  在其中可以定义许多抽象方法，用以描述其具备的映射功能
* `mapper instanceof xxxMapper`的结果为`true`

### 映射器的具体实现

由于xxxMapper只是一个接口，mybatis还需要知道这个接口中的抽象方法是如何实现的（即如何完成映射的过程）。

可以通过两种方式去告知mybatis关于接口的实现：
* Mapper XML 配置文件
* 在抽象方法上进行注解

通常我们使用配置文件。  
因为注解相比于X配置文件，就像水枪对比加特林。

## 配置文件
SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：
* cache – 该命名空间的缓存配置。
* cache-ref – 引用其它命名空间的缓存配置。
* resultMap – 描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素。
* sql – 可被其它语句引用的可重用语句块。
* insert – 映射插入语句。
* update – 映射更新语句。
* delete – 映射删除语句。
* select – 映射查询语句。
