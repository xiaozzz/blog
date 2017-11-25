---
title: "Block-explorer配置教程(支持btc和bch)"
date: 2017-11-25
tags: [Blockchain, Bitcoin, BitcoinCash]
thumbnail: /2017/11/25/Block-explorer/logo.png
banner: 
---
## 此篇博客介绍到目前为止(2017.11.25)如何配置bitcoin以及bitcoin-cash区块浏览器

1. 总览说明
使用bitcore-node作为底层区块链节点，配合insight-api模块和insight-ui模块。

2. 配置要求
	a) 系统Linux可以直接运行，OSX需要自己编译bitcoin/bitcoin-abc，不支持Windows
	b) 硬盘 > 500G 用于区块文件
	c) 内存 > 4G

3. 需要的依赖模块的github地址
	a) [bitcore-node](https://github.com/satoshilabs/bitcore-node)
	b) [bitcoin(btc底层)](https://github.com/satoshilabs/bitcoin)
	c) [bitcoin-abc(bch底层)](https://github.com/satoshilabs/bitcoin-abc)
	d) [insight-api](https://github.com/satoshilabs/insight-api)
	e) [insight-ui](https://github.com/bitpay/insight)
4. 配置说明
	其中加粗为相应工程项目/目录/文件。
	a) 按照上述git地址，clone **bitcore-node** 项目及 **bitcoin/bitcoin-abc** 项目；其中前者可以使用git clone最新版本，后两者直接下载最新release版本;
	b) 进入 **bitcore-node** 目录，打开 **package.json **，将L34  `"preinstall": "./scripts/download" ,` 删除;
	c) 进入 **bitcore-node** 目录，运行 `npm i`  安装依赖，得到完整的 **bitcore-node** 工程；此时得到文件 **bitcore-node/bin/bitcore-node** ； 
	d) 将 **bitcoin/bitcoin-abc** 复制进 **bitcore-node** 目录，需要保证 **bitcore-node/bin** 下有 **bitcoind** 文件；
	e) 运行 `bitcore-node/bin/bitcore-node` ，运行 `./bitcore-node create mynode` ；生成 **mynode** 目录(该步骤中产生的任何错误提示可以忽略)；
	f) 进入 **mynode** 目录，打开 **package.json** 并将依赖中的 **bitcore-node** 删除；运行 `npm i` ，安装依赖；
	g) 运行 `../bitcore-node install https://github.com/satoshilabs/insight-api` 以及 `../bitcore-node install insight-ui` ，安装依赖;
	h) 检查 **bitcore-node/bin/mynode/node_modules/insight-api/node_modules/bitcore-lib/index.js** 及 **bitcore-node/bin/mynode/node_modules/bitcore-message/node_modules/bitcore-lib/index.js** 两个文件，如果有，则在第七行加上 `return;`（只允许一个bitcore-lib实例）;
	i) 回到 **mynode** 目录，运行 `../bitcore-node start` ，启动工程。
5. 配置文件说明(mynode/bitcore-node.json)
	a) datadir: 区块文件，完成的同步需要>500G
	b) port: 暴露端口








