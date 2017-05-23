---
title: 随笔-Node.js-koa
date: 2017-05-23 20:00:00
tags: [Note, Node.js, Module, koa]
thumbnail: /2017/05/23/Note-NodeJs-koa/nodejs.jpg
banner: 
---
由 [Express](http://expressjs.com/) 原班人马打造的 [koa](http://koajs.com/)，致力于成为一个更小、更健壮、更富有表现力的 Web 框架。使用 koa 编写 web 应用，通过组合不同的 generator，可以免除重复繁琐的回调函数嵌套，并极大地提升常用错误处理效率。Koa 不在内核方法中绑定任何中间件，它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

### 安装
* 要求：Node >=7.6.0
```bash
$ nvm install 7
$ npm i koa
$ node my-koa-app.js
```

### 简易教程

#### Hello World
```Javascript
const Koa = require('koa');
const app = new Koa();

app.use(ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

#### 代码级联
```Javascript
const Koa = require('koa');
const app = new Koa();

// x-response-time

app.use(async function (ctx, next) {
  const start = new Date();
  await next();
  const ms = new Date() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
});

// logger

app.use(async function (ctx, next) {
  const start = new Date();
  await next();
  const ms = new Date() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}`);
});

// response

app.use(ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```