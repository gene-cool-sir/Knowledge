# mongo

mongo知识图谱:

<https://www.processon.com/view/link/61bef8c70791294d047ada3f>

1.  MongoDB快速实战与基本原理

<https://note.youdao.com/ynoteshare/index.html?id=c1fdfae65ce29ffac56d7315bbfc7079&type=note&_time=1697074365495>

1.  MongoDB聚合操作

<https://note.youdao.com/ynoteshare/index.html?id=dad1b8090503bafd9cc48019bda7570f&type=note&_time=1697074116009>

1.  MongoDB索引详解

<https://note.youdao.com/ynoteshare/index.html?id=0d777b87c34ec809163471a66bb5033c&type=note&_time=1697074180322>

4\. MongoDB复制（副本）集实战及其原理

<https://note.youdao.com/ynoteshare/index.html?id=768f4ec76d7d637bae9abe826958a78f&type=note&_time=1697074473782>

5.MongoDB分片集群架构实战

<https://note.youdao.com/ynoteshare/index.html?id=16c445f1d16c79e23d1205a79110d3c5&type=note&_time=1697074533675>

6.MongoDB多文档事务详解

<https://note.youdao.com/ynoteshare/index.html?id=4c81223e709ec11e17d06ef08082cfc0&type=note&_time=1697074568775>

7.MongoDB建模调优\&change stream实战

<https://note.youdao.com/ynoteshare/index.html?id=06f45164f11e98d0ec81f86129a4ceac&type=note&_time=1697074640418>

基本命令

```java
// 选择或者创建数据库
use test;
// 创建集合
db.createCollection('person')
// 查看命令帮助
db.person.help()
// 批量执行,支持操作：insertOne、updateOne、updateMany、deleteOne、deleteMany、replaceOne
db.person.bulkWrite([
  { insertOne: { document: { name: 'xzb', age: 20, language: 'zh' } } },
  { insertOne: { document: { name: 'zhangsan', age: 22, language: 'us' } } }
])
// 根据条件统计find()集合
db.person.count() // 查看文档数量, 此方法被弃用
db.person.find({age:20}).count()
// 查看文档数量(可通过match匹配) db.collection.countDocuments(query, options)
db.person.countDocuments({age:20})
db.person.countDocuments()
// 删除filter匹配的多个文档
db.person.deleteMany({
 age: {$gt: 20}
})
// 删除单个文档
db.person.deleteOne({
  age:20
})
// distinct 按照指定的值去重后的文档 db.collection.distinct(field, query, options)
db.person.distinct('age',{age:20})
db.person.distinct('age')
// 统计集合所有文档
db.person.estimatedDocumentCount()
// 查找find: db.collection.find(query, projection)
db.person.find()
db.person.find({ age: { $gt: 20 } })
db.person.find({}, { name: 1, age: 1, _id: 0 })
// 更新
db.person.findAndModify({
   query: { name: "John" },
   update: { $set: { age: 30 } },
   new: true
})
// update updateOne updateMany
// 创建索引
// 聚合操作
db.person.aggregate()
```
