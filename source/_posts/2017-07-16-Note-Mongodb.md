---
title: 随笔-Mongodb篇
date: 2017-07-16
tags: [Note, Mongodb]
thumbnail: /2017/07/16/Note-Mongodb/mongodb.jpg
banner: 
---
### mongodb数据库用户名密码设置
1. 使用[db.createUser](https://docs.mongodb.com/manual/reference/method/db.createUser/)来创建用户
一个简单的例子：
```mongodb
use products
db.createUser(
   {
     user: "accountUser",
     pwd: "password",
     roles: [ "readWrite", "dbAdmin" ]
   }
)
```

2. mongodb服务端开启的时候增加参数`--auth`开启用户名密码