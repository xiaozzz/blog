---
title: 随笔-NodeJs-Forever篇
date: 2016-09-18 11:00:00
tags: [Note, NodeJs, Forever]
thumbnail: /2016/09/18/Note-NodeJs-Forever/nodejs.jpg
banner: 
---
**forever让nodejs应用后台执行**

```
$ sudo npm install forever -g   #安装
$ forever start app.js          #启动
$ forever stop app.js           #关闭
$ forever start -l forever.log -o out.log -e err.log app.js   #输出日志和错误
```

[Github链接](https://github.com/foreverjs/forever)