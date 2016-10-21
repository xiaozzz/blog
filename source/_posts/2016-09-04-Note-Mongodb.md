---
title: 随笔-Mongodb篇
date: 2016-09-04 11:00:00
tags: [Note, Mongodb]
thumbnail: /2016/09/04/Note-Mongodb/mongodb.jpg
banner: 
---
1. [update()](https://docs.mongodb.com/manual/reference/method/db.collection.update/)
	db.collection.update(criteria, objNew, upsert, multi)
	criteria : update的查询条件，类似sql update查询内where后面的
	objNew   : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
	upsert   : 这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
	multi    : mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
	例：
	```
	db.test0.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } ); //只更新了第一条记录
	db.test0.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true ); //全更新了
	db.test0.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false ); //只加进去了第一条
	db.test0.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true ); //全加进去了
	db.test0.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );//全更新了
	db.test0.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );//只更新了第一条
	```
	常用的upsert方法:
	* $set
	用法：{ $set : { field : value } }
	* $inc
	用法：{ $inc : { field : value } }

2. [find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/)
	db.collection.find(query, projection)
	例:
	```
	db.products.find( { qty: { $gt: 25 } } )
	db.bios.find(
	   {
	      _id: { $in: [ 5,  ObjectId("507c35dd8fada716c89d0013") ] }
	   }
	)
	```

3. [mongodump](https://docs.mongodb.com/manual/reference/program/mongodump/)与[mongorestore](https://docs.mongodb.com/manual/reference/program/mongorestore/)(数据库备份与恢复)
	* mongodump -h dbhost -u username -p password -d dbname -c collectionname -o dbdirectory
	参数说明 ：
	-h:指明数据库宿主机的IP
	-u:指明数据库的用户名
	-p:指明数据库的密码
	-d:指明数据库的名字
	-c:指明collection的名字
	-o:指明到要导出的文件夹名
	-q:指明导出数据的过滤条件
	* mongorestore -h dbhost -u username -p password -d dbname -c collectionname --directoryperdb dbdirectory
	参数说明：
	-h:指明数据库宿主机的IP
	-u:指明数据库的用户名
	-p:指明数据库的密码
	-d:指明数据库的名字
	-c:指明collection的名字
	--directoryperdb:指明到备份的文件夹名

4. 添加[index](https://docs.mongodb.com/manual/core/index-single/)
	增查删索引：
	db.collection.createIndex({"username":1})
	db.collection.getIndexes()
	db.collection.dropIndex({"username":1})
	唯一索引：
	db.collection.ensureIndex({"userid":1},{"unique":true,"dropDups":true})
	使用explain:
	db.collection.find().explain()