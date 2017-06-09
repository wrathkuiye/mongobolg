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


### nginx 上传大文件失败问题的解决

nginx 参数解析

client_body_buffer 请求体缓存区域大小，不配置默认x86机器是8k  x64是16k
client_body_temp_path 设置请求体缓存的临时文件的存放路径  当请求体的大小超过缓存区域的时候  会写到临时文件中 默认路径  当前路径下 client_body_temp （通常看不到 一旦出现问题 直接就销毁了）
client_max_body_size 设置上传文件的最大的大小 默认 1M

根据这三个参数。可以分析得到 请求体必须小于client_max_body_size
当请求体的大小 大于请求体缓存区域的大小的时候，会将请求体缓存到临时文件里面  文件路径是 client_body_temp_path

通常出现问题的情况有两种

1,请求体超过了 client_max_body_size （此时的错误信息 413 entity too large 错误）
2,请求体超过了 client_body_buffer 但是写入缓存文件的时候 写入失败，通常的表现是无权限（此时的错误信息 500 Internal server error）

我遇到的是第二种情况 解决办法  ： 配置nginx的user 为root


### mongo replset配置  基于2.4版本的mongo






