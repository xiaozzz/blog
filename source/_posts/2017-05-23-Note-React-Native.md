---
title: 随笔-React Native
date: 2017-05-23 10:00:00
tags: [Note, Node.js, React, React native]
thumbnail: /2017/05/23/Note-React-Native/react.png
banner: 
---
[React Native](http://facebook.github.io/react-native/)是基于Javascript和React的一个跨平台构建原生移动APP的框架，项目由Facebook主导。

### 环境配置
* Android开发环境：
	* Windows/Linux/macOS
	* Node >=4.0
	* Python2
	* JavaJDK >=1.8
	* AndroidStudio >=2.0
	* Watchman(optional)
* iOS开发环境：
	* macOS
	* Xcode >=8.0

### 简易教程

#### Hello World
```Javascript
import React, { Component } from 'react';
import { AppRegistry, Text } from 'react-native';

class HelloWorldApp extends Component {
  render() {
    return (
      <Text>Hello world!</Text>
    );
  }
}

AppRegistry.registerComponent('HelloWorldApp', () => HelloWorldApp);
```

### 特色
* 良好的跨平台编程(iOS/Android)
* Native的性能
* 简单的React/Javascript编程体验
* 活跃的社区