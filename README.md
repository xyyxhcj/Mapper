#MyBatis通用Mapper3

##极其方便的使用MyBatis单表的增删改查

##支持单表操作，不支持通用的多表联合查询

##优点?

通用Mapper都可以极大的方便开发人员。可以随意的按照自己的需要选择通用方法，还可以很方便的开发自己的通用方法。

##MyBatis工具网站:[http://mybatis.tk](http://www.mybatis.tk)

##项目文档

初学者如果感觉下面的文档太多不方便入手，那么可以下载[通用Mapper简单实用文档.pdf](http://git.oschina.net/free/Mapper/attach_files/download?i=15246&u=http%3A%2F%2Ffiles.git.oschina.net%2Fgroup1%2FM00%2F00%2F99%2FfMqNk1XbGl2AQA8mAAKDipSqnlc703.pdf%3Ftoken%3D14d69a3d1526cb5b4dc3e1d2cc030b2c%26ts%3D1440516629)学习基本的配置和使用！

###在你打算使用通用Mapper前，一定要看看下面的文档，许多人在初次使用时遇到的问题，99%都在文档中有说明！！

1. [Mapper3变化](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/1.Changes.md)

2. [如何集成通用Mapper](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/2.Integration.md)

3. [如何使用通用Mapper](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.Use.md)

4. [高级应用](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/4.Professional.md)

5. [Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)

6. [快速开发自己的通用接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/6.MyMapper.md)

7. [如何使用Mapper专用的MyBatis Generator插件](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/7.UseMBG.md)

8. [在Spring4中使用通用Mapper](http://git.oschina.net/free/Mapper2/blob/master/wiki/mapper/4.Spring4.md)

9. [Mapper3常见问题和用法](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/9.QA.md)

##通用Mapper - 简单用法示例

全部针对单表操作，每个实体类都需要继承通用Mapper接口来获得通用方法。

示例代码：

    CountryMapper mapper = sqlSession.getMapper(CountryMapper.class);
    //查询全部
    List<Country> countryList = mapper.select(new Country());
    //总数
    Assert.assertEquals(183, countryList.size());

    //通用Example查询
    Example example = new Example(Country.class);
    example.createCriteria().andGreaterThan("id", 100);
    countryList = mapper.selectByExample(example);
    Assert.assertEquals(83, countryList.size());

    //MyBatis-Generator生成的Example查询
    CountryExample example2 = new CountryExample();
    example2.createCriteria().andIdGreaterThan(100);
    countryList = mapper.selectByExample(example2);
    Assert.assertEquals(83, countryList.size());

CountryMapper代码如下：

    public interface CountryMapper extends Mapper<Country> {
    }

这里不说更具体的内容，如果您有兴趣，可以查看下面的<b>项目文档</b>

##实体类注解

从上面效果来看也能感觉出这是一种类似hibernate的用法，因此也需要实体和表对应起来，因此使用了JPA注解。更详细的内容可以看下面的<b>项目文档</b>。

Country代码：

    public class Country {
        @Id
        private Integer id;
        @Column
        private String countryname;
        private String countrycode;
        //省略setter和getter方法
    }
    
[使用Mapper专用的MyBatis Generator插件](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/7.UseMBG.md) 可以方便的生成这些（带注解的）实体类。

##通用Mapper支持Mybatis-3.2.4及以上版本 

##使用Maven

###重要提示,3.1.0及以后版本的groupId修改为tk.mybatis，artifactId为mapper

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper</artifactId>
    <version>3.1.3</version>
</dependency>
```

##引入Jar包，下载地址：

https://oss.sonatype.org/content/repositories/releases/tk/mybatis/mapper

http://repo1.maven.org/maven2/tk/mybatis/mapper

由于通用Mapper依赖JPA，所以还需要下载persistence-api-1.0.jar：

http://repo1.maven.org/maven2/javax/persistence/persistence-api/1.0/

##[更新日志](http://git.oschina.net/free/Mapper/blob/master/wiki/Changelog.md)

##Maven坐标以及下载地址

###最新版本3.1.3 - 2015-08-25

* 去掉了3.1.3-SNAPSHOT版本中的`MapperOnceInterceptor`拦截器，下个版本会完善`MapperHelper`的配置方式
* `Example`增加了`example.selectProperties("id", "countryname", ...)`方法，可以指定查询列，注意这里参数写的是属性名，`Example`会自动映射到列名
* `Example`增加`andEqualTo(实体对象)`方法，可以将一个实体放进去，会自动根据属性和值拼出column=value的条件 <b>Bob - 0haizhu0@gmail.com 提供</b>
* MyBatis在处理`<cache/>`和`@CacheNamespace`的时候不统一，只有一个能生效，这导致xml中配置二级缓存对通用Mapper注解形式的方法无效，该问题已解决
* 二级缓存配置方法，如果接口有对应的xml，在xml中配置二级缓存。如果只有接口没有xml，用注解配置二级缓存即可
* 需要注意的是，二级缓存在xml配置时，只对通用Mapper方法有效，自己用`@Select`等注解定义的这种仍然无效，这种情况只能在xml中定义

###3.1.2 - 2015-07-14

* 解决别名时的一种特殊情况，例如`@Column(name="`desc`")`的时候，就不需要自动添加别名
* 反射获取所有列名的时候，不在自动转换为大写形式，对数据库区分大小写的情况有用

###3.1.1 - 2015-07-01

* 解决`ConditionMapper`中`selectByCondition`和`updateByCondition`方法错误

###3.1.0 - 2015-06-10

* 基础包名从`com.github.abel533`改为`tk.mybatis.mapper`
* Maven的groupId改为`tk.mybatis`,artifactId为`mapper`
* 增加和Example功能类似的Condition查询，仅仅名字不同
* 更多详细变化请看[Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)
* 关于3.0.x版本请看[Mapper3.0.x](http://git.oschina.net/free/Mapper/tree/Mapper3.0.x/)

###3.0.0 - 2015-06-04

* 将`EntityMapper`和`SqlMapper`移出，现在是独立项目[EntityMapper](http://git.oschina.net/free/EntityMapper)
* 将`Mapper<T>`全部接口方法拆分为独立接口，方便选择集成
* 增加`MySqlMapper<T>`包含批量插入和单个插入，批量插入可以回写全部id
* 增加`RowBoundsMapper<T>`包含两个分页查询，可以配合[PageHelper](http://git.oschina.net/free/Mybatis_PageHelper)实现物理分页
* 详细变化请看[Mapper3变化](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/1.Changes.md)
* Mapper2资深用户请看[Mapper3高级应用](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/4.Professional.md)
* [Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)
* [快速开发自己的通用接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/6.MyMapper.md)

##作者信息

作者博客：http://blog.csdn.net/isea533

作者邮箱： abel533@gmail.com

Mybatis工具群： <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=29e4cce8ac3c65d14a1dc40c9ba5c8e71304f143f3ad759ac0b05146e0952044"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="Mybatis工具" title="Mybatis工具"></a>

推荐使用Mybatis分页插件:[PageHelper分页插件](https://github.com/pagehelper/Mybatis-PageHelper)