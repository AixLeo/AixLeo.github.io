---
layout:     post
title:      A Simple Publicly Verifiable Secret Sharing Scheme and its Application to Electronic Voting
subtitle:   
date:       2024-3-24
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - PVSS
    - Secret Sharing
    - ElGamal
    - DLEQ
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

# A Simple Publicly Verifiable Secret Sharing Scheme and its Application to Electronic Voting

作者：Berry Schoenmakers

机构：Department of Mathematics and Computing Science, Eindhoven University of Technology

来源：CRYPT'99 [[原文链接]](https://link.springer.com/chapter/10.1007/3-540-48405-1_10)


## 1. Introduction

&emsp;&emsp;Stadler在1996年首次提出了PVSS的概念[[Sta96]](#Sta96)，并基于ElGamal密码体制和Double Discrete Logarithms问题提出了PVSS方案。但是这种DDL假设并不是标准的假设，因此，1999年，Schoenmakers基于标准离散对数假设，提出了新的PVSS方案。同时，相较于Sta96方案[[Sta96]](#Sta96)，进一步明确在重构阶段，参与者除了提交份额之外，还需提交份额相对应的证明。

### 1.1 Model for NI-PVSS
&emsp;&emsp;在PVSS方案中，分发者 $D$ 希望在 $n$ 个参与者 $P_ 1,\cdots,P_ n$ 之间分发秘密 $s$ 的份额。对于 $(t,n)$门限访问结构 （$1\leq t\leq n$），至少 $t$ 个参与者合作才能恢复秘密；除非安全假设不成立，否则任何少于 $t$ 个参与者构成的集合都无法获得关于秘密的任何信息。标准的PVSS方案主要包括以下阶段：
- 初始化阶段。生成系统参数，每个参与者生成公私钥对。
- 分发阶段。分发阶段由以下两个步骤构成：
  - 对份额的分发。分发者生成每个参与者的份额 $s_ i$，利用参与者的公钥对份额进行加密得到并发布 $E_ i(s_ i)$，同时参与者对每个加密份额生成并发布证明 $\texttt{PROOF}_ D$，另外还需发布秘密 $s$ 的承诺值。
  - 对份额的验证。任何人（不仅仅是参与重构的参与者）都可以通过 $\texttt{PROOF}_ D$ 验证份额的有效性。如果出现至少1人验证失败的情况，则协议终止。
- 重构阶段。重构阶段也由以下两个步骤构成：
  - 解密份额。参与者使用解密算法计算真实份额 $s_ i=D_ i(E_ i(s_ i))$。生成份额相对应的证明 $\texttt{PROOF}_ {P_ i}$。
  - 交换份额。参与者之间交换份额，同时需要交换份额相对应的证明 $\texttt{PROOF}_ {P_ i}$。当份额验证通过后，通过重构算法恢复秘密 $s$。



### 1.2 DLEQ
&emsp;&emsp;Chaum和Pedersen[[CP92]](#CP92)在1992年提出了盲化的Diffie-Hellman问题，在文中被称为DLEQ问题。作为方案中的子协议，其目的是要证明对于 $q$ 阶群 $G$ 的生成元 $g_ 1,h_ 1,g_ 2,h_ 2$ 而言， $\text{log}_ {g_ 1}h_1=\text{log}_ {g_ 2}h_ 2$。协议详细描述如下：
> 假设证明者知道 $\alpha$ 满足 $h_ 1=g_ 1^\alpha$ 以及 $h_ 2=g_ 2^\alpha$。
> - 证明者向验证者发送承诺 $a_ 1=g_ 1^w$ 和 $a_ 2=g_ 2^w$，其中 $w\in_ R\mathbb{Z}_ q$。
> - 验证者随机选择挑战 $c\in_ R\mathbb{Z}_ q$，并发送给证明者。
> - 证明者发送响应 $r=w-\alpha c$。
> - 验证者验证 $a_ 1\overset{?}{=}g_ 1^rh_ 1^c$ 以及 $a_ 2\overset{?}{=}g_ 2^rh_ 2^c$。


### 1.3 PVSS方案
- 初始化阶段。生成 $q$ 阶群 $G_ q$ 以及生成元 $g,G$。每位参与者 $P_ i$ 生成公私钥对 $(sk_ i,pk_ i)=(x_ i,y_ i=G^{x_ i})$。
- 分发阶段。
  - 份额分发。
    - 分发者生成最多 $t-1$ 次多项式 $p(x)=\sum\nolimits_ {j=0}^{t-1}\alpha_ jx^j$，其中 $\alpha_ j\in \mathbb{Z}_ q$，并且 $s=\alpha_ 0$。秘密为 $S=G^s$。
    - 分发者发布系数的承诺值 $C_ j=g^{\alpha_ j},\ 0\le j\le t-1$。
    - 分发者公布用接收者公钥加密的份额 $Y_ i=y_ i^{p(i)},\ 1\le i\le n$。
    - 分发者计算 $X_ i=g^{p(i)}$，同时利用 $\texttt{DLEQ}(g,X_ i,y_ i,Y_ i)$ 生成关于 $Y_ i$ 的证明。
    > 利用Fiat-Shamir变换，可以计算挑战 $c=H(X_ i\Vert Y_ i\Vert a_ {1i}\Vert a_ {2i}),\ 1\le i\le n$，从而生成非交互式证明。
  - 份额验证。
    - 验证者通过公开的承诺值 $C_ j$ 计算 $X_ i=\prod\nolimits_ {j=0}^{t-1}C_ j^{i^j}$。
    - 将 $y_ i,X_ i, Y_ i,r_ i$ 和 $c$ 作为输入，验证者计算 $a_ {1i}=g^{r_ i}X_ i^{c}$，以及 $a_ {2i}=y_ i^{r_ i}Y_ i^c$。同时验证 $c\overset{?}{=}H(X_ i\Vert Y_ i\Vert a_ {1i}\Vert a_ {2i})$。
- 重构阶段。
  - 份额解密。
    - 参与者利用私钥计算解密后的份额 $S_ i=Y_ i^{x_ i^{-1}}=G^{p(i)}$。
    - 参与者计算 $y_ i=G^\alpha$ 和 $Y_ i=S_ i\alpha$，利用 $\texttt{DLEQ}(G,y_ i,S_ i,Y_ i)$ 生成关于 $S_ i$ 的证明。
  - 交换份额。
    - 参与者交换份额 $S_ i$ 以及相对应的证明。验证通过后，如下利用拉格朗日插值恢复秘密 $G^s$：
    
    $$\prod_ {i=1}^tS_ i^{\lambda_ i}=\prod_ {i=1}^t(G^{p(i)})^{\lambda_ i}=G^{\sum_ {i=1}^tp(i)\lambda_ i}=G^{p(0)}=G^s,\qquad \lambda_ i=\prod_{j\ne i}\frac{j}{j-i}$$





## Reference
<!-- 这边文章是介绍如何在 Markdown 中增加文献引用。[<sup>1</sup>](#refer-anchor-1) -->
<div id="Sta96"></div>

- [Sta96] [Stadler, Markus. "Publicly verifiable secret sharing." International Conference on the Theory and Applications of Cryptographic Techniques. Berlin, Heidelberg: Springer Berlin Heidelberg, 1996.](https://link.springer.com/chapter/10.1007/3-540-68339-9_17)

<div id="CP92"></div>

- [CP92] [Chaum, David, and Torben Pryds Pedersen. "Wallet databases with observers." Annual international cryptology conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 1992.](https://link.springer.com/chapter/10.1007/3-540-48071-4_7)



