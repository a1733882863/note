# redis

#### 1.redis的基本信息

1.默认有16个数据库  在redis.conf里

2.数据库键的数量

```
dbsize
8
keys *
```

#### 2.redis的安装

去redis的官网下载redis

```
tar zxf redis-6.0
make
vim redis.conf  #deamoned 改为yes  可以后台启动
src/redis-server redis.conf  #启动redis
```

#### 3.redis的基本操作



##### 1.键相关(key)

###### 1.模糊查询

```
keys k*

keys *k*

#匹配2个字符
keys k?? 
"kk3"
"k11"

[]通配括号内的某一个字符
记得其他字母,就第二个字母可能是a或e
keys r[ae]dis
"radis"
"redis"
```

###### 2.查看数据库里key的数量

```
dbsize
8
keys *
```

###### 3.查看key还有多久过期

```
ttl key     -1永不过期,-2已过期
```

###### 4.设置一个key带过期时间的

```
expire k1 10
```

5.查看key的数据类型

```
type k1
```



##### 2.关闭redis

```
redis-cli shutdown
redis-cli -p 6379 shutdown
```

##### 3.检查6379端口是否在监听

```
netstat -lntp | grep 6379
```

##### ######4.检查是否连接成功

```
redis-cli

ping
```

##### 5.切换库

```
select 15  #切换到16号数据库
```

##### 5.清空数据库

```
清空当前库
flushdb

清空所有(16个)库,慎用!!
flushall
```



###### 7.测试性能

redis-benchmark



###### 8.判断某一个键是否存在

```
exists k1
1
```



###### 9.挪库

```
move x1 8  #把x1移动到8号库
```



#### 4.五大基本数据类型详细操作

##### 1.String类型

![1619086640028](redis.assets/1619086640028.png)

###### 1.incr/decr/incrby加减操作

![1619087529825](redis.assets/1619087529825.png)

###### 2.getrange和setrange  替换

![1619087884183](redis.assets/1619087884183.png)

###### 3.设置存在的键

![1619088336972](redis.assets/1619088336972.png)



###### 4.getset命令

因为没有k6,所以get为null,然后将k6v6的值添加到数据库

获取值,显示的是旧值,然后再对值进行set

![1619089079027](redis.assets/1619089079027.png)



###### 5.mest/mget/..

![1619089235602](redis.assets/1619089235602.png)



##### 2.List类型

###### 1.lpush/rpush lrange

![1619090072115](redis.assets/1619090072115.png)

###### 2.lpop/rpop

![1619090205334](redis.assets/1619090205334.png)

###### 3.lindex

![1619090245311](redis.assets/1619090245311.png)

###### 4.Irem 删除

移除2个3

![1619090350220](redis.assets/1619090350220.png)

###### 5.Itrim

![1619090483559](redis.assets/1619090483559.png)

###### 6.rpoplpush

![1619090600623](redis.assets/1619090600623.png)

###### 7.lset 

![1619090690864](redis.assets/1619090690864.png)

###### 8.linsert(插入某元素之前之后)

头尾操作效率高,中介操作效率低

![1619090806335](redis.assets/1619090806335.png)



##### 3.Set集合类型

###### 1.sadd/smembers/sismember: 添加/查看/判断是否存在

注意:1和0不是下标,而是布尔. 1:true存在,2false不存在

![1619091151159](redis.assets/1619091151159.png)

###### 2.scard:获得集合中的元素个数

![1619091210054](redis.assets/1619091210054.png)

###### 3.srem:删除集合中的元素

![1619091549986](redis.assets/1619091549986.png)

###### 4.srandmember:从集合中随机获取几个元素

![1619091573784](redis.assets/1619091573784.png)

###### 5.spop:级出栈(移除)

![1619091665784](redis.assets/1619091665784.png)

###### 6.smove:移动元素:将key1某个值赋值给key2

![1619091762330](redis.assets/1619091762330.png)

###### 7.数学集合类

![1619091938823](redis.assets/1619091938823.png)

