#### 一、Paxos算法简述
Paxos算法是一种基于消息传递的分布式一致性算法，解决的是分布式系统中各个节点如何就某个值（决议）达成一致。Paxos算法允许在宕机故障的异步系统中，不要求可靠的消息传递、可容忍消息丢失、延迟、乱序以及重复。在2n+1个节点的系统中最多允许n个节点同时出现故障。

一个或多个提议进程（proposer）可以发起提案（Proposal），Paxos算法使所有提案中的某一个提案在所有进程中达成一致，系统中多数节点同时认可该提案即达成一致。

Paxos将系统中的角色分为提议者（Proposer）、决策者（Acceptor）和最终决策学习者（Learner）：

+ **Proposer**：提出提案（Proposal），提案信息包括提案编号（Proposal ID）和提案的值（Value）。
+ **Acceptor**：参与决策投票，收到提案后可以接受提案，如果多数Acceptor接受，则称该提案被批准。
+ **Learner**：不参与决策，从Proposers/Acceptors学习最新达成一致的提案（Value）。

通常在一个分布式系统中，每个节点同时具有Proposer、Acceptor和Learner三种角色。

#### 二、算法流程
Paxos算法通过一个决议分为三个阶段：
+ 1.第一阶段：Prepare阶段。Proposer向Acceptor发出Prepare请求，Acceptor针对接收到的Prepare请求进行 **两个承诺和一个应答**：
    - 两个承诺
        + a.不再接受Proposal ID **小于等于** 当前请求的Prepare请求
        + b.不再接受Proposal ID **小于** （只接受等于）当前请求的Propose请求
    - 一个应答
        + 回复已批准的提案中Proposal ID最大的那个提案的Value和ID值，没有则返回空值
+ 2.第二阶段：Accepe阶段。Proposer收到过半数Acceptor承诺后，从应答中选择提案号最大的提案的Value，作为本次要发起的提案。如果所有应答的提案都是空值，则可以自己决定提案，然后向Acceptor发出Propose请求，请求包含当前提案号和提案值。Acceptor针对收到的Propose请求进行Accept处理，如果接收到的提案号等于本地提案号，则接受并持久化当前提案号和提案值
+ 3.第三阶段：Learn阶段。Proposer收到多数Acceptor的accept后，决议形成，将形成的决议发送给所有Learner。

#### 三、存在问题
原始的Paxos算法（Basic Paxos）只能对一个值形成决议，且极端情况下，如果两个提议者交替阶段一成功，而阶段二失败，从而形成活锁（Live Lock）。

#### 四、Multi-Paxos算法
Multi-Paxos首先需要选举Leader，可以通过执行一次Basic Paxos来选举一个Leader。选出Leader以后只能由Leader提交提案给决策者进行投票表决，这样没有竞争者，解决了活锁问题。

如果Leader宕机，可以重新选举Leader继续服务。

Multi-Paxos允许有多个自认为是Leader的节点并发提交提案，这样的场景退化为Basic Paxos。
