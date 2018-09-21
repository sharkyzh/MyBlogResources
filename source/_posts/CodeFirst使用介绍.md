---
title: EF CodeFirst使用介绍
category:
  - .net
  - mvc
tags:
  - .net
  - mvc
abbrlink: c566f6d0
date: 2018-06-28 11:51:49
---
#### EF简单DataAnnotations实体配置

1. 数据库创建表T_Persons，有Id（主键，自动增长）、Name、CreateTime字段
2. 创建Person类


```
[Table("T_Persons")]//因为类名和表名不一样，所以要使用Table标注
public class Person
{
    public long Id{get;set;}
    public string Name{get;set;}
    public DateTime CreteTime{get;set;}
}
```


因为EF约定主键名是Id，所以不用再特殊指定Id是主键，如果非要指定就使用[key]。
因为字段名字和属性名字一致，所以不用再特殊指定属性和字段名的对应关系，如果需要特殊指定，则要用[Column("Name")]。
（*）必填字段标注[Required]、字段长度[MaxLength(5)]、可空字段用int?、如果字段在数据库有默认值，则要在属性上标注[DatabaseGenerated]。
注意实体类都要写成public，负责后面可能会有麻烦。

3. 创建DbContext类（模型类、实体类）


```
public class MyDbContext:DbContext
{
    public MyDbContext():base("name=conn1")
    {
      
    }

    public DbSet<Person> Persons{get;set;}
}
```

未完待续

