# 共识算法演变史

## 一、共识算法及评价体系

共识算法主要是解决分布式系统中多个节点之间对某个状态达成一致性结果的问题。分布式系统都是由多个服务节点共同完成对事务的处理，分布式系统中多个副本对外呈现的数据状态需要保持一致性。但是由于节点的不可靠性和节点间通讯的不稳定性，甚至节点作恶伪造信息进行恶意响应，节点之间就存在数据状态不一致性的问题。通过共识算法，可以实现将多个不可靠的单独节点组建成一个可靠的分布式系统，实现数据状态的一致性，提高系统的可靠性。

区块链系统本身是一个超大规模的分布式系统，但又与传统的分布式系统存在明显区别。区块链系统建立在去中心化的点对点网络基础之上，在整个系统中没有中央权威，并由共识算法实现在分散的节点间对交易的处理顺序达成一致，这是共识算法在区块链系统中起到的最主要作用。

另外，与企业分布式系统不同，区块链系统中的共识算法还承担着区块链系统中激励模型和治理模型中的部分功能，包括每个区块中对哪些矿工进行激励发放，网络中所有交易手续费的结算和分配，区块链网络共识周期的切换等。

共识算法根据容错能力不同，即在考虑节点故障不响应的情况下，再考虑节点是否会伪造信息进行恶意响应，可以分为CFT（Crash Fault Tolerance）类和BFT（Byzantine Fault Tolerance）类共识算法。 CFT共识算法只保证分布式系统中节点发生宕机错误时整个分布式系统的可靠性，而当系统中节点违反共识协议的时候（比如被黑客攻占，数据被恶意篡改等）将无法保障分布式系统的可靠性，因此CFT共识算法目前主要应用在企业内部的封闭式分布式系统中，目前流行的CFT共识算法主要有Paxos算法及其衍生的Raft共识算法。采用BFT共识算法的分布式系统，即使系统中的节点发生了任意类型的错误，只要发生错误的节点少于一定比例，整个系统的可靠性就可以保证。因此，在开放式分布式系统中，比如区块链网络，必须采用BFT共识算法。在区块链网络发展前，BFT共识算法主要为PBFT共识算法，目前部分联盟链采用PBFT共识算法。由于公有链的开放性，任意节点都可以随时参与和退出网络并都有作恶的可能，近两年公有链的快速发展也带动了BFT共识算法的巨大进步。

另外，因为共识算法都建立在底层的网络模型基础上，所以从网络同步模型的角度来看，共识算法可以分为三种，即同步共识算法，半同步共识算法，和异步共识算法。同步共识算法要求网络中任一消息能够在已知的限定时间内到达所有的共识节点，因此主要应用在限定规模的网络环境中，大多是联盟链采用同步共识算法。异步共识算法对于消息在网络中的传播延迟没有任何限制，消息可以在无限长时间后才能发送到其他共识节点，由于FLP不可能定理，异步共识算法无法确定性保证共识终局，因此几乎没有高效的全异步共识算法，即使Bitcoin的PoW算法也是基于同步网络保证一致性，基于异步网络保证可用性。半同步共识算法在前两者之间做了权衡，要求网络中消息某限定时间后到达所有共识节点的的概率与时间的关系是已知的，目前主流的区块链共识算法都是基于半同步的网络模型，即半同步共识算法。

评价一个区块链共识算法的优劣，可以从以下四个方面进行，即容错性能、终局性性能、扩展性（消息复杂度）以及网络模型性能。

容错性能：指共识算法的容错能力，比如Raft只能支持节点故障错误。而在区块链中，特别公有链中，由于节点间存在利益博弈，同时又是一个非中心化的网络状态，其共识算法必须支持节点作恶的容错，所以区块链的共识算法必然是BFT算法。

终局性性能：指区块链网络对一个候选区块完成终局一致性所需要的时间，这对于面向用户的DApp应用是非常重要的参数。

扩展性：指随着区块链网络节点数目与共识算法性能的相关关系，比如PBFT算法随着节点数目增加，完成一轮共识需要在网络中传播的消息数目呈平方比例增加，因此PBFT算法的天然无法支持大规模网络。

共识算法的网络模型性能对其容错性能和终局性能都有很大的影响。在区块链大规模网络条件下，同步共识算法要求所有节点在规定时间内响应对其他节点的消息，否则将被认为是故障节点，因此受网络波动影响较大，从而进一步导致算法容错性能的降低；而由于FLP不可能定理（在网络可靠，存在节点失效，即便只有一个的最小化异步模型系统中，不存在一个可以解决一致性问题的确定性算法。）异步共识算法无法给出确定的终局性性能。当前主流区块链共识算法都是基于半同步模型。
单一共识算法由于其本身具有的局限性，例如PoW共识速度偏慢等，区块链研究者尝试将两种或者多种共识算法融合起来，取长补短，来达到更好的共识特性。一般来说，混合共识有PoW+PoS，PoW+BFT以及PoS+BFT等种类。可以看到新一代的共识算法，比如Algorand，DFINITY， BUMO的BU Firework以及Ontology的VBFT，都属于混合共识算法。

## 二、当前主流的共识算法

在区块链发展初期，主流区块链网络都是基于PoW共识算法，包括Bitcoin，Ethereum，Litecoin，Zcash等。由于PoW存在挖矿的资源浪费问题，2017年后基于PoS的共识算法研究得到了迅猛的发展，并在2018年各种基于PoS共识算法的公有链都逐步上线。

对于当前主流共识算法可以采用如下几种方式分类：

基于挖矿方式分类：

* PoW : 所有节点通过解决某个计算难题（例如哈希难题）参与共识。包括：Bitcoin，Eth，LTC
* PoS：所有节点通过质押代币的方式参与共识。包括：Eth-PoS，Tendermint，Algorand，EOS DPoS，DFINITY，VBFT

基于终局性分类：

* GHOST：PoW，Eth-PoS
* BFT：Tendermint，EOS DPoS，Algorand，DFINITY，VBFT


基于节点选择方式：

* 所有节点参与：PoW，Eth PoS，Tendermint
* 随机选择部分节点参与：Algorand，Dfinity，VBFT

|	| 容错性能 | 终局性性能 | 扩展性(O为消息复杂度、N为网络规模）| 代表网络 |
| --- | --- | --- | --- | --- |
| PoW | 49% | 算法不提供终局性 | O(N) |	Bitcoin |
| 基于GHOST的PoS算法 | 33% | 通过周期性Checkpoint实现终局性 | O(N) | Ethereum | 
| Tendermint | 33% | 通过BFT实现 | O(N^2) | Cosmos |
| Algorand | 33% | 通过Byzantine Agreement实现 | 通过VRF随机选择出块节点 | 
O(N\*log N) | Algorand |
| EOS DPoS | 33% | 通过BFT实现 | 限定21个出块节点 O(1) | EOS |
| DFINITY | 33% | 对若干历史区块的加权评估 | 通过VRF随机选择出块节点 O(N\*log N) | DFINITY |
| VBFT | 33% | 通过BFT实现 | 通过VRF随机选择出块节点 O(N*log N) | Ontology|
| PoW-DAG | 49% | 算法不提供终局性 | O(N) | PHANTOM, Conflux |

从上述的分类过程中，我们可以看出当前区块链共识算法在性能，扩展性，去中心化等方向上的演进过程。

在Bitcoin创建了区块链技术时，创建了PoW共识算法，通过计算哈希难题和最长链规则，实现去中心化的共识算法。随着Bitcoin网络规模的增大，后续区块在网络中传播延迟的增加，基于最长链规则造成了大量伪分叉，极大的浪费了网络中的算力和PoW共识算法的性能。

针对于伪分叉的问题，区块链社区后续提出了通过DAG方式扩展PoW共识算法，比如PHANTOM，Conflux等。但是针对于PoW的算力浪费的问题，区块链社区后续更多转向了基于PoS的共识算法。Ethereum也计划逐渐减少PoW激励，直至最终完全取消对PoW的共识激励，完成到PoS共识算法的切换。同时，也有大多数新兴区块链平台都采用了PoS的共识方式，其中最著名为EOS的DPoS共识算法。

与此同时，随着区块链应用的增加，区块链共识算法的扩展性问题也日益凸显。图灵奖获得者Micali教授提出了Algorand算法，提出基于VRF随机选择部分节点参与共识的方式，通过BFT的方式极大降低了共识算法的消息复杂度，在保证去中心化安全性的同时实现共识算法的可扩展性。在此基础之上，VBFT等共识算法增加了基于PoS治理机制，并基于此解决了随机节点选择的抽样陷阱问题，在保证算法扩展性的同时实现优秀的终局性性能。

## 三、共识算法的发展趋势

总体来看，目前主流共识算法逐渐由PoW转向PoS共识算法，并且采用VRF随机选择节点的方式实现算法的扩展性，即使Ethereum后续的宁静版本，也将是基于PoS的共识算法，并在其信标链中实现VRF随机选择验证者节点。另外，Avalanche共识算法也是采用随机选择节点的方式实现区块链共识算法的扩展性，不过目前还没有上线的区块链采用此共识算法。

随着区块链社区对区块链共识算法的研究，技术社区已经发现在Internet规模的网络中可达到的性能上限不仅取决于共识算法的性能参数，更取决于在此规模的网络中完成消息传播的时间延迟等物理上限。因此，主流区块链的研究团队都将下一步区块链网络性能扩展方向投向了分片技术，状态通道和二层网络等方向。

