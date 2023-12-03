---
layout:     post
title:      VSS from Distributed ZK Proofs and Applications
subtitle:   
date:       2023-12-02
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - ZK proof
    - Secret Sharing
    - VSS
---

<html>
<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  },
  svg: {
    fontCache: 'global'
  }
};
</script>

<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js">
</script>
</html>

# VSS from Distributed ZK Proofs and Applications

作者：Shahla Atapoor<sup>1</sup>， Karim Baghery<sup>1</sup>， Daniele Cozzo<sup>1,2</sup>，Robi Pedersen<sup>1</sup>

机构：<sup>1</sup> COSIC, KU Leuven;  <sup>2</sup> IMDEA Software Institute 

来源：Asiacrypt 2023 [[原文链接]](https://eprint.iacr.org/2023/992)


## 1. Introduction

&emsp;&emsp;标准的秘密共享方案（如Shamir方案[[SHA79]](#SHA79)）假设诚实方的存在，不提供对恶意参与者的安全性。因此，研究人员提出了**可验证秘密共享(Verifiable Secret Sharing, VSS)**的概念，来抵御包括“恶意分发者分发错误份额”以及“恶意参与者提交错误份额”在内的攻击。但是，这种VSS往往需要分发者和参与者之间进行多轮交互，因此会增加大量的通信开销。为了解决这一问题，研究人员又进一步提出了**非交互式可验证秘密共享(Non-Interactive Secret Sharing, NI-VSS)**：即分发者可通过公开信道，向参与者广播一条消息即可完成验证。

### 1.1 VSS方案基本构造
&emsp;&emsp;常见的VSS方案一般在普通的秘密共享方案的基础上，增加可验证的特性。 比如基于离散对数的VSS方案(DL-based VSS)：[[Feldman]](#Feldman),[[Pedersen]](#Pedersen),[[Schoenmakers]](#Schoenmakers)。DL-based VSS方案中，分发者公布秘密多项式的份额和系数（在指数上），然后参与者利用同态性质来验证共享份额的正确性。DL-based VSS方案通常是非交互式的，并且可进一步支持公开可验证。

&emsp;&emsp;然而，近年来，量子计算对传统基于数学困难问题的秘密学的威胁越来越大，比如 Shor 算法可以快速求解一个可分解的正奇数 $N$ 的素数质因子。因此，越爱越多的研究人员着眼于研究具有**后量子(post-quantum)**安全性的密码算法。[[GHL22]](GHL22)中就提出了一个非交互式的PVSS方案。该方案同时使用基于格和基于离散对数的困难问题，但遗憾的是该方案无法在保证后量子安全的前提下，应用到其他门限协议中。

&emsp;&emsp;因此，设计高效的具有后量子安全的VSS方案，以及进一步在保证后量子安全的前提下将其应用到门限协议中，就成了文章研究的重点。

### 1.2 BGW VSS方案和TZK
&emsp;&emsp;是否存在具有后量子安全的VSS方案呢？答案是肯定的。BGW VSS方案[[BGW]](#BGW)采用基于二元多项式的分布式零知识证明来增加Shamir秘密共享方案的可验证性。并且该方案不依赖于困难问题假设，具有信息论安全，因此可以在经典和后量子安全门限协议中使用。然而，该方案的验证过程，需要参与者两两交互，并且需要保证三分之二的验证者是诚实的。

&emsp;&emsp;Boneh等人[[BBC+19]](#BBC+19)在Crypto2019上给出了秘密共享数据的ZK证明的如下正式定义，并给出了具体构造。该方案满足对证明者和 $t<2/n$ 个恶意验证者的计算可靠性，并且即使 $t$ 个参与者合谋，仍然能保证“零知识性”，因此Boneh等人将这种性质称为“Strong zero-knowledge”（文中作者引用为“**Threshold zero-knowledge(TZK)**”）。
>- A single prover $P$ and $n$ (designated) verifiers $\lbrace V_ i \rbrace _ {i=1}^n$.
>- Each verifier $V_ i$ holds a piece (share) $x_ i$ of an input (statement) $x$.
>- The prover’s task is to convince the (designated) verifiers that the main input $x$ belongs to a specific language $\mathbf{L}$.

## 2. Post-Quantum Secure NI-TZK Scheme
&emsp;&emsp;针对Boneh等人研究的可行性结果，文中为 $n$-分布式关系 $R_ 1,\cdots, R_ n$ 构造了一个高效的非交互式TZK证明(Non-Interactive TZK Proof, NI-TZKP)方案，其中 $R_ i=\lbrace (x_ i,f(X))\vert f(i)=x_ i\rbrace$，这里 $f(X)\in \mathbb{Z}_ N[X]_ t$ 是关于 $X$ 的（最高） $t$ 次多项式，并且其系数定义在环 $\mathbb{Z}_ N$ 上。该方案具备以下性质：

- NI-TZK建立在诚实大多数的假设前提下；
- NI-TZK不依赖于任何困难问题假设，并且方案中用到的是量子计算性隐藏的承诺方案，因此具备量子安全；
- NI-TZK满足完备性(completeness)、门限零知识性(threshold zero knowledge)以及针对证明者和 $t$ 个恶意敌手的稳健性(Soundness)。

&emsp;&emsp;下图是该方案的具体细节：

![图 0](/assets/res/VSS_from_ZKP/2023-12-03-37.png)  

&emsp;&emsp;简单来说，该方案是利用一个证明者生成的随机多项式 $b(X)$，来隐藏关于witness $f(X)$ 的信息。同时将 $b(i)$ 放入到承诺值 $C_ i$ 中，方便接收到的参与者通过 $r(X)\leftarrow b(X)-d\cdot f(X)$ 来进行验证。当参与者拿到正确的份额 $x_ i$ 后，可以计算出正确的 $d$，进而可以通过 $b(i)\leftarrow r(i)+d\cdot x_ i$ 来计算出 $b(i)$，并检查 $C_i=\mathcal{C}(b(i),y_ i)$ 的正确性。

> 值得注意的是，这样的验证方式，只能验证 $x_ i$ 的正确性，并不能满足文中所提到的 $x_ i$ 与 $f(X)$ 的一致性。

&emsp;&emsp;方案安全性质的证明，可以参考[[BDPV21]](#BDPV21)。

## 3. A NI-VSS Scheme from NI-TZK Proofs
&emsp;&emsp;$(n,t,x_ 0)$ 非交互式VSS方案由四部分算法构成，包括：

- $\texttt{Initialization}$: 生成系统参数，并共享给各个参与方；
- $\texttt{Share}(n,t,x_ 0)\rightarrow (x_ 1,\cdots,x_ n,\pi)$：给定参与者数量 $n$，门限 $t$ 以及秘密 $x_ 0$，生成随机份额 $x_ 1,\cdots,x_ n$，并运行前文中提到的NI-TZK方案，给出proof $\pi$；
- $\texttt{Verification}(n,t,x_ 1,\cdots,x_ n,\pi)\rightarrow \texttt{true/false}$：参与者 $P_ i$ 通过给定的 proof $\pi$ 和自己的份额 $x_ i$  来验证份额的正确性，输出 true/false；
- $\texttt{Reconstruction}(n,t,x_ 1,\cdots,x_ {t+1})\rightarrow x_ 0/\lbrace \texttt{true/false} \rbrace$：可以看到这里的重建阶段与一般的重建阶段不同，这里提供了两种方式。第一种和普通的重建阶段一样，给定任意 $t+1$ 个份额，参与者就可以恢复并返回秘密 $x_ 0$；第二种，针对特殊的分布式门限协议，分发者会给定秘密 $x_ 0$ 的候选值（或者关于 $x_ 0$ 的函数），参与者通过NI-TZK来验证 $x_ 0$ 的正确性，以及 $x_ 0$ 与 $x_ i$ 的一致性。

&emsp;&emsp;具体算法简述如下：

![图 1](/assets/res/VSS_from_ZKP/2023-12-03-75.png)  

![图 2](/assets/res/VSS_from_ZKP/2023-12-03-68.png)  

> VSS方案是通过NI-TZK来证明份额的正确性，同时通过验证 $C_0^′=C(x_ 0, y_ 0^′)\wedge C_ 0==C(r(0)+d\cdot x_ 0,y_ 0)$ 来判断一致性，但是不可行。因为公开了 $x_ 0$ 和 $r_ 0$ 能确定唯一的 $d$，但是在保持 $b(i)=r(i)+d\cdot x_ i$ 不变的情况下，式中的 $r(i)$ 和 $x_ i$ 仍然可以被篡改。

## 4. An Efficient DKG Protocol for DL
&emsp;&emsp;由NI-TZK和VSS方案，可以构造出多种门限协议，文中给出了**分布式密钥生成(Distribute Key Generation, DKG)协议**和门限签名(Threshold Signature)协议，这里就简要介绍下DKG协议。

&emsp;&emsp;文中提出的DKG协议，是基于离散对数问题，其主要是借鉴了Pedersen DKG协议[[PedersenDKG]](#PedesenDKG)，各参与方公布关于秘密值的函数值 $g^{x_ i}$，进而计算公钥 $g^x=g^{(x_ 1\Vert \cdots \Vert x_ n)}$。

&emsp;&emsp;为了进一步贴合DKG协议设计，文中构建了基于DL问题的NI-TZK构造，其中对各方秘密值 $x_ i$ 的验证，就相当于做了 Schnorr 签名：$b(i)=r(i)+d\cdot x_ i$，计算 $g^{b(i)}$。见下图：

![图 3](/assets/res/VSS_from_ZKP/2023-12-03-56.png)  

&emsp;&emsp;完整的DKG协议包括两轮，第一轮主要是共享各方秘密值 $x_ i$，以及公开 $g^{x_ i}$，以及对这些值做承诺；第二轮是各方打开承诺，验证收到的份额的正确性，同时计算公钥。

> 在Pedersen DKG方案中，私钥是需要对各方持有的秘密值重新构建多项式，并通过VSS方案重构出来，在本文中，因为已经对秘密值进行了共享，且他们在某一个多项式上，因此只要至少 $t+1$ 个人合作就可恢复。

![图 4](/assets/res/VSS_from_ZKP/2023-12-03-90.png)  

## 5. More Efficient Threshold Signatures from Schnorr’s Scheme
&emsp;&emsp;基于Schnorr方法的门限签名方案，这里就不介绍了，与前面的方法类似，只要将明文消息 $m$ 放进 $d$ 中（其本身就是一个哈希值）即可。详细见下图：

![图 5](/assets/res/VSS_from_ZKP/2023-12-03-60.png)  




## Reference
<!-- 这边文章是介绍如何在 Markdown 中增加文献引用。[<sup>1</sup>](#refer-anchor-1) -->
<div id="SHA79"></div>

- [SHA79] [Shamir, Adi. "How to share a secret." Communications of the ACM 22.11 (1979): 612-613.](https://dl.acm.org/doi/abs/10.1145/359168.359176)

<div id="Feldman"></div>

- [Feldman] [Feldman, Paul. "A practical scheme for non-interactive verifiable secret sharing." 28th Annual Symposium on Foundations of Computer Science (sfcs 1987). IEEE, 1987.](https://ieeexplore.ieee.org/abstract/document/4568297)

<div id="Pedersen"></div>

- [Pedersen] [Pedersen, Torben Pryds. "Non-interactive and information-theoretic secure verifiable secret sharing." Annual international cryptology conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 1991.](https://link.springer.com/chapter/10.1007/3-540-46766-1_9)

<div id="Schoenmakers"></div>

- [Schoenmakers] [Schoenmakers, Berry. "A simple publicly verifiable secret sharing scheme and its application to electronic voting." Annual International Cryptology Conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 1999.](https://link.springer.com/chapter/10.1007/3-540-48405-1_10)

<div id="GHL22"></div>

- [GHL22] [Gentry, Craig, Shai Halevi, and Vadim Lyubashevsky. "Practical non-interactive publicly verifiable secret sharing with thousands of parties." Annual International Conference on the Theory and Applications of Cryptographic Techniques. Cham: Springer International Publishing, 2022.](https://link.springer.com/chapter/10.1007/978-3-031-06944-4_16)

<div id="BGW"></div>

- [BGW] [Ben-Or, Michael, Shafi Goldwasser, and Avi Wigderson. "Completeness theorems for non-cryptographic fault-tolerant distributed computation." Providing sound foundations for cryptography: on the work of Shafi Goldwasser and Silvio Micali. 2019. 351-371.](https://dl.acm.org/doi/abs/10.1145/3335741.3335756)

<div id="BBC+19"></div>

- [BBC+19] [Boneh, Dan, et al. "Zero-knowledge proofs on secret-shared data via fully linear PCPs." Annual International Cryptology Conference. Cham: Springer International Publishing, 2019.](https://link.springer.com/chapter/10.1007/978-3-030-26954-8_3)

<div id="BDPV21"></div>

- [BDPV21] [Beullens, Ward, et al. "CSI-RAShi: distributed key generation for CSIDH." International Conference on Post-Quantum Cryptography. Cham: Springer International Publishing, 2021.](https://link.springer.com/chapter/10.1007/978-3-030-81293-5_14)

<div id="PedersenDKG"></div>

- [PedersenDKG] [Pedersen, Torben Pryds. "A threshold cryptosystem without a trusted party." Advances in Cryptology—EUROCRYPT’91: Workshop on the Theory and Application of Cryptographic Techniques Brighton, UK, April 8–11, 1991 Proceedings 10. Springer Berlin Heidelberg, 1991.](https://link.springer.com/chapter/10.1007/3-540-46416-6_47)