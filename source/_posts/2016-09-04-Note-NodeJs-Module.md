---
title: 随笔-NodeJs-Module篇
date: 2016-09-04 10:00:00
tags: [Note, NodeJs, Module]
thumbnail: /2016/09/04/Note-NodeJs-Module/nodejs.jpg
banner: 
---
**好用的module分享：**

 * node-schedule:定时任务
	例如：每个小时的42分钟执行任务
	```JavaScript
	var schedule = require('node-schedule');
	var rule = new schedule.RecurrenceRule();
	rule.minute = 42;
	var j = schedule.scheduleJob(rule, function(){
		console.log('The answer to life, the universe, and everything!');
	});
	```
* request:简化http请求
	```JavaScript
	var request = require('request');
	request('http://www.google.com', function (error, response, body) {
		if (!error && response.statusCode == 200) {
			console.log(body) // 打印google首页
		}
	})
	request.post('http://service.com/upload', {form:{key:'value'}})
	request.post('http://service.com/upload').form({key:'value'})
	```
* crypto:处理加密
	例如：使用MD5加密
	```JavaScript
	var str = "123456";
	var crypto = require('crypto');
	var crypto_md5 = crypto.createHash('md5');
	crypto_md5.update(str, 'utf8'); // UTF8
	var password = crypto_md5.digest('hex');
	```