---
title: Casper技术博客
date: 2018-01-03
tags: [Ethereum, Blockchain, Golang, Casper]
thumbnail: /2018/01/03/casper03_withdrawal_and_consensus/logo.jpg
banner: 
---
# Casper 取钱与共识

上一章讲到了casper的基础数据结构和投注流程，这一章主要讲的是casper的取钱和达成共识的部分。

## 取钱

```go
// Check if we can withdraw
if this.dynasty < this.validators[validator_index].Dynasty_end + 1 {
	return errors.New("validator not gone, can not withdraw")
}
end_epoch := this.dynasty_start_epoch[this.validators[validator_index].Dynasty_end + 1]
if this.current_epoch < end_epoch + Epoch(this.withdrawal_delay) {
	return errors.New("still within delay, can not withdraw")
}
```

取钱比较简单，先进行check，首先当前dynasty必须是在该验证人的end_dynasty+1之后，通过dynasty计算end_epoch，然后检查当前epoch必须是要在end_epoch加上取钱所需要的延时之后。上述代码就是进行检查的过程。

那么取钱为什么要有那么长的延时呢，因为如果不这样，那么大部分验证者解除绑定，他们就可以恶意地创造包含之前的验证者集的第二条链。所以解除绑定保证金后必须要经过一个“解冻”期，才可收回。

![](/2018/01/03/casper03_withdrawal_and_consensus/withdrawDelay.png)

上述检查成功以后，将该验证人的投注乘上一个比例因子获得取钱的金额，然后将该金额发送到对应验证人的取钱地址，最后在验证人池中删除验证人。

## 达成共识

对于一个分布式容错系统而言，核心问题就是所有进程最终达到一个相同的阶段，即“共识”。现实生活中的去中心化系统可能会出现任意的错误，共识就需要考虑很多异常情况。现在我们先考虑简单的情况，在没有failure的情况下，casper是如何达成共识的。

接下来讨论一下达成共识的过程，也就是一个区块的最终确定，主要是通过两轮的投票过程来确定一个区块。一次是PREPARE，一次是COMMIT，对应一个块的justified和finalized，justified("审判")可以认为一个块被最终确定的前置条件，finalized即为块的最终确定。

共识信息也是一个重要的数据结构，用Go语言可以表示为

```go
type Consensus_message struct {
	// 这个 hash 现在有多少 prepares (hash of message hash + view source) 在目前的 dynasty
	Cur_dyn_prepares map[uint32]big.Rat
	// Bitmap of which validator IDs have already prepared
	Prepare_bitmap: num256[num][bytes32]
	// 之前的 dynasty
	Prev_dyn_prepares map[uint32]big.Rat
	// 是否是被确认过的 hash
	Ancestry_hash_justified map[uint32]bool
	// hash 有多少 commits
	Cur_dyn_commits map[uint32]big.Rat
	// 之前的 dynasty
	Prev_dyn_commits map[uint32]big.Rat
}
```

主要是当前dynasty和前一个dynasty中PREPARE和COMMIT的数量，以及父区块是否被justified的信息。

PREPARE消息解析出来是以下一些字段

**[validator_index, epoch, ancestry_hash, source_epoch, source_ancestry_hash, sig]** 记录验证人的序号，当前块和父块的时间戳和哈希值，签名等信息。

首先需要验证签名以及检查该PREPARE消息是第一次发出。然后在对应epoch上的共识信息中PREPARE字段中加上该验证人的投注金额，如果PREPARE的数目大于等于总投注金额的2/3那么该块就认为是justified，即被审判。

COMMIT消息与PREPARE消息类似，之前共识信息数据结构记录了父区块是否被justified的信息，这时就派上了用场。确认以后同样地在共识信息中COMMIT子段中加上该验证人的投注金额，如果COMMIT的数目大于等于总投注金额的2/3，那么父区块就被认为是finalized，即最终确定。

所以简单地总结一下，一个区块的确定经历两个过程

- 大多数(超过2/3)验证人在周期1的时候给区块1进行了投票，区块1被审判(justified)。
- 大多数(超过2/3)验证人在周期二的时候给区块1的子区块叫区块2进行了投票，所以在周期二区块1被确定(finalized)。

关于共识的达成想法还是很清晰的，通过大多数验证人对同一个区块的投票来最终确定一个块。然而，问题在于不像工作量证明中电力资源的“自然惩罚”，验证人的行为主观性更强，共识的最终形成需要严格定义惩罚方案，而这是casper共识算法的最重要部分，也决定了算法能否在真实系统中work，这部分会在下一章节详细介绍。

## 参考

\[1\] 共识算法的比较: http://www.jianshu.com/p/df8200207d14

\[2\] 以太坊紫皮书: https://cdn.hackaday.io/files/10879465447136/Mauve%20Paper%20Vitalik.pdf

\[3\] 复杂美 Casper-Go 项目源代码

