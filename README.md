# mongobolg


## 如无特殊标记 本记录所使用的代码均为python代码


### mongo cursor not valid at server  过期的问题解决记录

每次find获取到cursor的时候 mongo会为cursor设置一个ID，并第一次返回100行数据 或者 1M的数据
当这些数据处理完成之后 会再次使用 cursor的ID去获取下次的数据  下次获取的数量是4M的数据
如果在两次之间间隔超过10分钟的时候 cursorID会过期，就会出现 cursor过期的问题 mongo cursor not valid at server

常用的解决方案
1,设置cursor用不过期（但是出现问题的时候 mongo会一直持有这个cursor,占有服务器资源）
  设置方式：db.Collection.find({"_id":"test"},timeout=False)

2,设置每次获取的数量 即是修改游标获取的量 来频繁的获取下一次数据，让cursorID不过期

  设置方式: db.Collection.find({"_id":"test"}).batch_size(10)



### mongo find sort规则

int long double 类型 按照大小进行排序
字符串排序 按照分割成char类型的模式来进行排序

cllection test:

{"_id":"1002","name":"zhangsan"}
{"_id":"999","name":"lisi"}

db.test.find().sort({"_id":-1})

result ：
{"_id":"999","name":"lisi"}
{"_id":"1002","name":"zhangsan"}
