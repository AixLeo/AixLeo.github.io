---
layout:     post
title:      A non-interactive (t, n)-publicly verifiable multi-secret sharing scheme
subtitle:   
date:       2023-09-23
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - MPC
    - Secret Sharing
    - PVMSS
    - non-interactive
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

# A non-interactive (t, n)-publicly verifiable multi-secret sharing scheme

作者：Samaneh Mashahdi， Bagher Bagherpour， Ali Zaghian  

机构：Cryptography and Data Security Lab., School of Mathematics, Iran University of Science & Technology  

来源：Designs, Codes and Cryptography, 2022, Springer (CCF B) [[原文链接]](https://link.springer.com/article/10.1007/s10623-022-01082-8)

## 1. 摘要

非交互式公开可验证多秘密共享（PVMSS）方案是一种秘密共享方案，即分发者在参与者之间共享多个秘密，任何人都可以利用公开信息验证共享的有效性。作者首次提出了一种基于齐次线性递归（**Homogeneous Linear Recursions, HLR**）和离散对数相等（**Discrete Logarithms Equality, DLEQ**）的新型 PVMSS 方案。

<br />

## 2. 背景介绍

$(t,n)$ 门限秘密共享方案可能存在恶意分发者/参与者，导致被篡改的份额无法恢复出秘密，因此需要对份额的正确性进行验证。  

研究者提出了**verifiable SS（VSS）**的概念。VSS方案中参与者可以发现分发者或其他参与者的任何作弊行为。在文献中，有两种不同类型的 VSS 方案。
- 交互式interactive：通过参与者和分发者之间的一些通信来证明份额和秘密的有效性。
- 非交互式non-interactive：参与者可以通过分发者公布的公共值来验证份额和秘密的有效性。

而**non-interactive publicly verifiable secret sharing** (**PVSS**)方案是对非交互式 VSS 方案的扩展，不仅参与者，而且任何外部验证者都可以通过使用公开信息来验证共享的有效性。

**multi secret sharing**（**MSS**）方案，使得分发者可以在参与者之间同时分配多个秘密，每个参与者只保留一份。

**publicly verifiable multi secret sharing** (**PVMSS**) 方案是一种同时具有 MSS 方案和 PVSS 方案优点的 SS 方案。目前大多数PVMSS方案大都基于双线性配对。

本文使用**Discrete Logarithms Equality DLEQ**代替双线性配对，以提高PVMSS 方案的性能。另一方面，使用**Homogeneous Linear Recursions HLR**替代了多项式的使用。

### HLR

[[齐次线性递推， Homogeneous Linear Recursions， HLR]](https://aixleo.github.io/2023/09/20/Homogeneous_Liner_Recursions/)

之前的文章介绍过，这里不再赘述，点击上方链接查看  

### DLEQ

通过离散对数相等（Discrete Logarithms Equality）协议[[BZS19]](#BZS19)[[SCH99]](#SCH99)，可以证明在阶为素数 $q$ 的乘法群 $G_ q$ 中，对于生成元 $g_ 1, g_ 2, y_ 1,y_ 2\in G_ q$ ，存在： $x=\text{log }_ {g_ 1}{y_ 1}=\text{log }_ {g_ 2}{y_ 2}$ 。

![图 2](/assets/res/20230923/2023-09-23-93.png)  

上述协议，其实是一个 $\Sigma$ 协议：
>P和V的交互过程为：
 1.首先P计算一个承诺(commitment)，将其发送给V；
 2.在收到了来自P的消息后，V在有限的挑战空间中随机选取一个挑战元素(challenge)，并将其发送给P；
 3.在接收到来自V的挑战后，P计算出一个反馈(response) ,将其发送给V；
 4.在收到了来自P的消息后,V输出accept或者reject。

<br />

借助于Fiat-Shamir变换[[F-S86]](#F-S86)，上述协议可以设计为非交互式协议：

1. 证明者 $P$ 选取 $r\in _ R \mathbb{Z}_ q$ ，并计算 $a_ 1=g_ 1^{r}$，$a_ 2=g_ 2^r$，$c=H(g_ 1\Vert g_ 2\Vert y_ 1\Vert y_ 2\Vert a_ 1\Vert a_ 2)$ 和 $b=(r+c){x^{-1}}\text{ mod }q$ ，其中 $H$ 是抗碰撞的hash函数。证明者 $P$ 将 $(b,c)$ 发送给验证者 $V$。
2. 如果 $c=H(g_ 1\Vert g_ 2\Vert y_ 1\Vert y_ 2\Vert y_ 1^bg_ 1^{-c}\Vert y_ 2^bg_ 2^{-c})$ ，则验证者 $V$ 接受证明者。


## 2. A new (l, t, n)-PVMSS scheme

### 2.1 Ini-Phase

1. 分发者 $D$ 选择阶为素数 $q\ (\vert q \vert \ge \lambda)$ 的乘法群 $G_ q$，$g,\ g_ 1$ 为两个生成元。
2. 分发者 $D$ 选择 $\alpha \in _ R \mathbb{Z}_ q$ 。
3. 每个参与者 $p_ i\in P$ 选择其私钥 $x_ i\in _ R \mathbb{Z}_ q$，发布公钥 $y_ i=(g_ 1)^{x_i}$ 。
4. 分发者 $D$ 发布系统参数 $SP=(\lambda,\alpha,P,t,l,G_ q,g,g_ 1,H)$ 。其中 $H:\{0,1\}^*\rightarrow\{0,1\}^\lambda$ 是密码学安全的hash函数。

### 2.2 SD-phase

假设需要共享的秘密 $K=\{g_ 1^{k_ 0},\cdots,g_ 1^{k_ l -1}\}$ ，则存在以下两种情况。

**门限值大于等于秘密数**，即 $\mathbf{t\ge l}$：

![图 3](/assets/res/20230923/2023-09-23-91.png)  

**门限值小于秘密数**，即 $\mathbf{l> t}$：

![图 4](/assets/res/20230923/2023-09-23-3.png)  

然后，$D$ 执行 $\texttt{Proof-algorithm}$，并获得witness。 之后，$D$ 公布witness、加密后的份额 $\{Y_ i \}_ {i=1}^n$ 和 public values $\delta$（当 $l > t$ 时）。任何人都可以通过 $\texttt{Ver-algorithm}$ 算法验证份额的正确性。

![图 8](/assets/res/20230923/2023-09-24-31.png)  


![图 9](/assets/res/20230923/2023-09-24-85.png)  


### 2.3 Dec-phase

如果 $\texttt{Ver-algorithm}$ 输出 1，则每个参与者 $p_ i\in P$ 恢复出自己的份额 $S_ i = (Y_ i )^{x_ i^{-1}}$。否则，abort。

### 2.4 SR-phase

假设 $A\subset P$ 是参与者授权子集，则每个参与者 $p_ i\in A$ 通过 $\texttt{SRProof-algorithm}$ 生成Proof $(w_ i,z_ i)$。

![图 5](/assets/res/20230923/2023-09-24-70.png)  

之后，每个参与者 $p_ i\in A$ 提交份额 $S_ i$ 和 $(w_ i,z_ i)$。任何人都可以使用 $\texttt{SRVer-algorithm}$ 检查份额 $S_ i$ 的正确性。

![图 6](/assets/res/20230923/2023-09-24-73.png)  

如果验证通过，那么 $A$ 中的参与者就可以使用 $\texttt{Rec1-algorithm}$ 恢复秘密，否则abort。以下 $ϒ(A)$ 表示 $A$ 中元素的索引集。

![图 7](/assets/res/20230923/2023-09-24-71.png)  




## Reference

<div id="F-S86"></div>

- [F-S86] [Fiat A., Shamir A.: How to Prove Yourself: Practical Solutions to Identification and Signature Problems, Advances in Cryptology-CRYPTO’86. Lecture Notes in Compute Science, vol. 263, pp. 186–194 (1986).]


<div id="BZS19"></div>

- [BZS19] [Bagherpour, Bagher, Ali Zaghian, and Mahdi Sajadieh. "Sigma protocol for faster proof of simultaneous homomorphism relations." IET information security 13.5 (2019): 508-514.](https://ietresearch.onlinelibrary.wiley.com/doi/full/10.1049/iet-ifs.2018.5167)

<div id="SCH99"></div>

- [SCH99] [Schoenmakers B.: A Simple Publicly Verifiable Secret Sharing Scheme and Its Application to Electronic Voting, Advances in Cryptology-CRYPTO 99. Lecture Notes in Computer Science, pp. 148–164 (1999).]


