---
title: 随笔-Python-Print输出到文件
date: 2016-10-23 16:00:00
tags: [Note, Python]
thumbnail: /2016/10/23/Note-Python-Print/logo.jpg
banner: 
---
最近的python项目Python2和Python3都需要用，写个随笔记下来方便找

------
Python3.x
```Python
#!/usr/bin/env python3  
#coding:utf-8  
year = 1  
years = 5  
bj = 10000  
rate = 0.05  
f = open("./source/interest.bak", 'w+')  
while year < years:  
	bj = bj * (1 + rate)  
	print("第{0}年,本金息总计{1}".format(year, bj), file=f)  
	year += 1  
```
------
Python2.x
```Python
#!/usr/bin/env python  
#coding:utf-8  
year = 1  
years = 15  
bj = 10000  
rate = 0.05  
f = open("./source/interest.bak", 'w+')  
while year < years:  
	bj = bj * (1 + rate)  
	print >> f, "第%d年,本金息总计%0.2f" % (year, bj)  
	year += 1   
```