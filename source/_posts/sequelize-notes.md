title: sequelize 笔记
date: 2015-11-09 22:00:10
tags: node-orm
---
![](/images/sequelize-logo.png)
# 介绍
Sequelize是一个promise风格的 node orm。支持常见的关系型数据库，比如 MariaDB, SQLite and MSSQL。
（持续更新中）
<!--more-->

事实上，有很多特性只是为了 postgresql 而存在，这也说明了 postgresql 越来越得到业界的认可。

# 关于promise
有一些程序员可能并不喜欢promise风格，这里'不顾羞耻'的推荐一下promise。 冰冻三尺非一日之寒，事实上使用promise的程序员可能也存在一些困惑，到底到底为啥要 promise 化呢。之后我会写一篇文章来‘同化’这些同学。

# 基本配置
说道orm，当然离不开数据库的配置，下面是一个简单的例子：
```
var Sequelize = require('sequelize');
var sequelize = new Sequelize('database', 'username', 'password');
```
Sequelize官方推荐用小写的 ***sequelize*** 来代表 orm 实例，个人觉得会引起一些不必要的歧义，可以尝试用其他命名。比如，就用 orm 。下面的说明都会使用 orm 这个名称。

创建 instance 的时候，可以指定一些配置项，比如最大连接数，空闲时间，等等。
```
var orm = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'|'mariadb'|'sqlite'|'postgres'|'mssql',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: 'path/to/database.sqlite'
});
```
然后导出，定义model只需要它就好了
```
module.exports = orm;
```

# 数据模型

可以使用 instance.define创建model
```
var student = orm.define('student',{
  id:{
   type:Sequelize.INTEGER,
   primaryKey:true
  },
  name:{
   type:Sequelize.STRING
  }
 },{
 tableName:'student'
 }
);
```
这样就定义了一个model，它有很多方便的方法。
* find
* findAll
* findOne
* destory
* update
可以到参考[官网说明](http://sequelize.readthedocs.org/en/latest/api/model/)

因为官方文档的语焉不详，下面是一些补充
## include
include用于连接查询，比如有一个班级（grade）model
```
var student = orm.define('student',{
  id:{
   type:Sequelize.INTEGER,
   primaryKey:true
  },
  name:{
   type:Sequelize.STRING
  },
  gradeId:{
   type:Sequelize.INTEGER
  }
 },{
 tableName:'student'
 }
);

var grade = orm.define('grade',{
  id:{
   type:Sequelize.INTEGER,
   primaryKey:true
  },
  name:{
   type:Sequelize:STRING
  }
 },{
  tableName:'grade'
 }
);
```
我们想查询学生和其所属的班级名称，如果是写sql，就是一个连接查询。
如果用sequelize做类似的查询，我们需要先指定这两个model的关系[associations](http://sequelize.readthedocs.org/en/latest/docs/associations/)。
```
student.belongsTo(grade,{
 foreignKey:'gradeId'
});
```
如果不指定foreignKey,Sequelize会自己去判断哪个是外键，这样可能会和实际情况并不相符。我推荐手动指定外键。当然，如果你喜欢用Sequelize生成表结果，那这个可以不用担心。这里介绍的是，数据库已经存在的情况下。
然后，我们可以使用incldue来查询信息。
```
student.findOne({
 where:{
  id:91
 },
 include:[
  {
   model:grade
  }
 ]
}).then(function(result){

}).catch(function(err){

});
```
result就是返回的值，Sequelize官方叫做 instance，可以认为是 model 的一个实例。它也有很多常用的[方法](http://sequelize.readthedocs.org/en/latest/api/instance/)，用于获取数据。

## getter setter 方法
有些时候，我们需要在对model进行存取之前，进行数据转换，比如，学生的爱好是一个数组，但是我们使用的数据库并不支持数组。这个时候，可以通过setter方法，在数据存储之前自动转型成字符串；通过getter，在查询成功之后自动又转回数组类型。

```
var student = orm.define('student',{
  hobbies:{
   type:Sequelize.STRING,
   get: function() {
      return this.getDataValue('hobbies').split(',');
   },
   set: function(hobbies){
      this.setDataValue('hobbies',hobbies.toString());
   }
  }
 },{
 tableName:'student'
 }
);
```
这样其实相当于变相的提供了数组类型存储的支持。对于调用 student model的程序来说，hobbies就是一个数组类型的字段。

这里为了叙述方便，省略了其他的字段。

## raw query
orm并不能解决所有的问题，很少的情况下，我们需要手写sql表达式。
orm.query('select * from student where id = ?',{
 replacements:[91],
 type: orm.QueryTypes.SELECT
});
当然上面的sql没有必要用 raw query，这只是一个例子。
这里的sql也可以是非查询语句，但是支持不大好，慎用。

## 还在更新
Sequelize还在更新，我的项目也在继续，未来肯定有更多的发现和心得，会持续的分享到这里。谢谢。

# 参考文献
* [sequelize docs](http://sequelize.readthedocs.org/en/latest/)
