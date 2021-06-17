## 										filecoin存储一笔存储Deal的流程

------

涉及进程：daemon、miner

（1）中间件所在机器通过jsonRPC调用发起存储一笔存储交易请求， 此时发送对象为中间件所在服务器的daemon进程（当然，daemon可以和中间件不在同一台服务器上）。

（2）当daemon收到存储交易命令时会执行相关的存储交易操作，此时会尝试着寻找我们指定的存储miner，并与该miner建立通信。（这一步有要求，要求对应的daemon和存储miner libp2p可互相访问, 此值需要设置，当前发觉miner升级后需要重新设置，这点很重要）。

（3）存储miner收到中间件对应daemon发来的存储请求后，会验证该笔交易的合法性（StorageDealValidating），若该笔交易是一笔合法的交易，而非一笔伪交易或者其它非法交易，则交易状态变更为（StorageDealAcceptWait），等待对方发送存储交易相关的信息，存储miner接收到存储交易信息后，存储miner会发送一个数据请求，请求中间件所在daemon发送其需要存储的数据至存储miner （StorageDealWaitingForData）。接下来，存储miner会建立一个transfer通道，用于传输中间件daemon传输请求存储的数据，通道建立后，便开始传输数据，直至数据传输完成（StorageDealTransferring）。

（4）数据传输至存储miner后，存储miner开始检测数据的完整性，检测数据在传输过程中是否有损坏（StorageDealVerifyData）。

（5）证明数据完整无误后便开始核对存储数据金额（StorageDealReserveProvider Funds），因为每一笔数据的存储都需要一定的金额和费用，所以在确定和哪一个存储矿工进行存储交易前我们会事先询问对应的矿工出价几何，然后根据存储数据的大小计算存储费用。

（6）存储费用核对无误后，存储矿工会进行publish初始化，接下来广播该笔交易（StorageDealPublish、StorageDealPublishing）。广播成功后若有人打包对应的message则该笔交易便上链成功，此时方才算是该笔交易正式成立（StorageDeal Staged）。

（7）接下来存储矿工会在一定时间内打包，封装，证明，存储该笔交易所要存储的数据。直至封装结束，则该笔交易状态变为DealActive（至此，一笔存储交易算是正式结束）（StorageDealAwaitingPreCommit、StorageDealFinalizing）。

（8）存储矿工成功存储数据后，会进行最后的清理操作，清理不必要的内容（有哪些？）（StorageDealActive）。

注：（1）在上述每一步完成后或者存在deal状态变化存储miner都会和中间件所依赖的daemon进行交易状态的同步和变更，也就是说，理论上，正常情况下中间件所依赖的daemon查询到的交易状态理应和存储miner查询到的deal状态一致。

------

##         一笔交易对应的所有状态，以及出现该状态对应的情形

------



```
StorageDealUnknown:              "Unknown",

StorageDealProposalNotFound:     "Proposal not found",

StorageDealProposalRejected:     "Proposal rejected",

StorageDealProposalAccepted:     "Proposal accepted",

StorageDealAcceptWait:           "AcceptWait",

StorageDealStartDataTransfer:    "Starting data transfer",

StorageDealStaged:               "Staged",

StorageDealAwaitingPreCommit:    "Awaiting a PreCommit message on chain",

StorageDealSealing:              "Sealing",

StorageDealActive:               "Active",

StorageDealExpired:              "Expired",

StorageDealSlashed:          	 "Slashed",

StorageDealRejecting:            "Rejecting",

StorageDealFailing:              "Failing",

StorageDealFundsReserved:        "FundsReserved",

StorageDealCheckForAcceptance:   "Checking for deal acceptance",

StorageDealValidating:           "Validating",

StorageDealTransferring:         "Transferring",

StorageDealWaitingForData:       "Waiting for data",

StorageDealVerifyData:           "Verifying data",

StorageDealReserveProviderFunds: "Reserving provider funds",

StorageDealReserveClientFunds:   "Reserving client funds",

StorageDealProviderFunding:      "Provider funding",

StorageDealClientFunding:        "Client funding",

StorageDealPublish:              "Publish",

StorageDealPublishing:           "Publishing",

StorageDealError:                "Error",

StorageDealFinalizing:           "Finalizing",

StorageDealClientTransferRestart: "Client transfer restart",

StorageDealProviderTransferAwaitRestart: "ProviderTransferAwaitRestart",
```

------

## 									Miner封装流程

------

当一笔交易正式成立（StorageDealStaged），进入StorageDealAwaiting PreCommit后，此时miner便开始密封客户端发来的存储数据，密封过程对应的基本流程如下:

```
				PreCommitPhase1
				PreCommitPhase2
				WaitSeed
				CommitPhase1
				CommitPhase2
				Finalize
				Proving
				Unknown
```

注：详细可参考https://github.com/ABMatrix/filecoin-docs/tree/main/%E8%B5%84%E6%96%99%E6%95%B4%E7%90%86

