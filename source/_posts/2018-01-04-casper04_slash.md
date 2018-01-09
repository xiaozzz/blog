---
title: Casper技术博客04
date: 2018-01-04
tags: [Ethereum, Blockchain, Golang, Casper]
thumbnail: /2018/01/04/casper04_slash/logo.jpg
banner: 
---
# Casper 惩罚（Slash）

上一章讲到了 Casper 的取钱和共识，这一章主要讲的是 Casper 的惩罚机制。

在有了前几章提到的数据结构和Casper算子之后，PoS已经基本成型。然而，缺乏惩罚措施会导致大量参与 PoS 的验证人作弊，从而使整个系统瘫痪。惩罚（Slash）的目的在于限制验证人作弊，作弊的代价将远远高于诚实工作，因此经济利益会驱使绝大部分验证人诚实工作。

我们的意图是使得51%的攻击非常的昂贵，并且即使大部分的验证人一起协力也不能将已经确定的区块回滚，除非他们愿意承受极大的经济损失—这个经济损失如此的大，以至于当攻击成功了，这会提高底层加密货币的价格，因为市场会对总硬币供应的减少做出强烈的反应，而不是启用紧急硬分叉来纠正攻击。

## 惩罚条件需要满足的情况
惩罚条件需要满足下面两个情况：

1. 责任安全：如果两个冲突的哈希值被最终确定，那么至少能够被证实，存在至少三分之一的验证节点违反了惩罚条件
2. 合理的活跃性：必须存在一组至少有三分之二的验证节点发送的一组消息，并且必须在没有违反惩罚条件的情况下能确定一些新的哈希区块，除非有至少三分之一的验证器违反了惩罚条件。

责任的安全给我们带来了“经济确定数”的想法，如果两个互相矛盾的哈希值都得到了确定（比如说，分叉），那么我们有数学的证明说，一大系列的验证节点都违反了一些惩罚条件，我们可以向区块链提交这一项证据并且对这些违反了惩罚条件的验证节点们实行惩罚。

合理的活跃性基本上是说“算法不应该被‘卡住’，并且不应该确定任何的事情”。

## 同一时刻两次prepare惩罚
协议定义了一系列的惩罚条件，诚实的验证人遵循一个协议，保证不触发任何的条件（注意：有时候我们说“违反”一个惩罚条件，他的同义词是“触发”，把惩罚条件当作一个法律，你不应该违反法律）。永远不要发送PREPARE的消息2次，这一点对于一个诚实的验证人来说一点都不难。

如果一个验证人发送了一个表单的签名信息： 
`["PREPARE", epoch, HASH1, epoch_source1]`
与另一个表单的签名信息： 
`["PREPARE", epoch, HASH2, epoch_source2]`
当`HASH1 != HASH2`或者`epoch_source1 != epoch_source2`，但是epoch的值在两个信息中都是一样的，那么这个验证人的保证金就会被取消(slash)（例如：删除此保证金）

此条件可以用如下算子表示：

```go
func double_prepare_slash(prepare1, prepare2 byte) error {
	//解析prepare1与prepare2 -> ["PREPARE", epoch, HASH, epoch_source]
	[validator_index1, epoch1, sig1, epoch_source1] := parse(prepare1)
	[validator_index2, epoch2, sig2, epoch_source2] := parse(prepare2)
	//判断签名是否正确
	if (!hash(validator_index1) || !(validator_index2)) {
		return Slash
	}
	//判断epoch是否相同
	if (epoch1 != epoch2) {
		return Slash
	}
	//判断epoch_source是否不同，如果相同表示违反了规则需要被惩罚
	if (epoch_source1 == epoch_source2) {
		return Slash
	}
	...
	return nil
}
```

## prepare和commit不一致惩罚
“PREPARE”和“COMMIT”是从传统的拜占庭容错共识理论中借用的术语。现在，我们来假设他们代表了两种不同类型的消息，在后面的协议内容中我们会介绍。你可以认为共识协议要求两轮不同的协议，其中PREPARE代表了一轮协议，COMMIT代表了第二轮协议。

如果一个验证人发送了一个表单的commit签名信息： 
`["COMMIT", epoch1, HASH1]`
与另一个表单的prepare签名信息：
`["PREPARE", epoch2, HASH2, epoch_source]`
当`epoch_source < epoch1 < epoch2`，那么无论HASH1是否等于HASH2，验证人都会被惩罚(slash)。该条件限定了，prepare永远在commit之前，即对于一个epoch已经被commit之后，无法再对之前的epoch进行任何prepare操作。commit已经成为确认的历史，验证人不应该篡改历史。

此条件可以用如下算子表示：

```go
func double_prepare_slash(prepare_msg, commit_msg byte) error {
	//解析prepare1与prepare2 -> ["PREPARE", epoch, HASH, epoch_source]
	[validator_index1, prepare_epoch, sig1, prepare_source_epoch] := parse(prepare_msg)
	[validator_index2, commit_epoch, sig2] := parse(commit_msg)
	//判断签名是否正确
	if (!hash(validator_index1) || !(validator_index2)) {
		return Slash
	}
	//判断prepare的epoch应该在commit的epoch之前
	if (prepare_source_epoch >= commit_epoch) {
		return Slash
	}
	//判断prepare应该在commit之前
	if (commit_epoch >= prepare_epoch) {
		return Slash
	}
	...
	return nil
}
```

## 小结
本章讲述的是最基本的slash条件，实际运行中会有很多极端情况，需要详细认证，但是基本的思想可以参考本文以及这篇[博客](https://medium.com/@VitalikButerin/minimal-slashing-conditions-20f0b500fc6c)\[4\]。Casper仍在持续开发中，更多实际运行中出现的问题可以参考[CBC-Casper](https://github.com/ethereum/cbc-casper)\[5\]

## 参考

\[1\] 最新以太坊紫皮书: https://docs.google.com/document/d/1maFT3cpHvwn29gLvtY4WcQiI6kRbN_nbCf3JlgR3m_8/edit 
\[2\] 老版以太坊紫皮书中文版: http://8btc.com/thread-40113-1-1.html  
\[3\] 复杂美 Casper-Go 项目源代码
\[4\] Minimal Slashing Conditions: https://medium.com/@VitalikButerin/minimal-slashing-conditions-20f0b500fc6c
\[5\] CBC-Casper: https://github.com/ethereum/cbc-casper