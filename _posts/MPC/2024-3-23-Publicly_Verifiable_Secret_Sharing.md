---
layout:     post
title:      Publicly Verifiable Secret Sharing
subtitle:   
date:       2024-3-23
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - PVSS
    - Secret Sharing
    - ElGamal
    - DDL
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

# Publicly Verifiable Secret Sharing

作者：Markus Stadler

机构：Institute for Theoretical Computer Science, ETH Zurich

来源：EUROCRYPT'96 [[原文链接]](https://link.springer.com/chapter/10.1007/3-540-68339-9_17)


## 1. Introduction

&emsp;&emsp;标准的秘密共享方案（如Shamir方案[[SHA79]](#SHA79)）假设诚实方的存在，不提供对恶意分发者和参与者的安全性。因此，研究人员提出了**可验证秘密共享(Verifiable Secret Sharing, VSS)**的概念，来抵御包括“恶意分发者分发错误份额”以及“恶意参与者提交错误份额”在内的攻击。然而，VSS只能保证收到份额的参与者验证自己的份额的有效性。因此Stadler在1996年欧密会上提出了**公开可验证秘密共享（Publicly Verifiable Secret Sharing, PVSS）**的概念，使得所有人（不仅仅是收到份额的参与者）可以验证所有份额的有效。

### 1.1 VSS与PVSS对比
&emsp;&emsp;VSS是一种特殊的秘密共享，附加了交互式/非交互式的验证算法 $\texttt{Verify}$，该算法允许接收到份额的参与者验证其份额的有效性。也就是说，如果份额是有效的，那么所有参与者都可以得到相同的值 $u$，并且这个值等于秘密 $s$。特别的，如果 $\texttt{Verify}$ 算法不需要参与者之间交互，则VSS方案为非交互式。
> $\exists u\ \forall A\in \mathcal{A}:(\forall i\in A:\texttt{Verify}(s_ i)=1)\Rightarrow \texttt{Recover}(\{s_ i\vert i\in A\})=u$, and $u=s$ if the dealer was honest.

&emsp;&emsp;在PVSS方案中，对于每个参与者 $P_ i$，都存在一个加密函数 $E_ i$ 和解密函数 $D_ i$，使得 $S_ i=E_ i(s_ i),\ i=1,\cdots,n$，分发者发布加密后的份额 $S_ i$。同时，存在一个有效的验证算法 $\texttt{PubVerify}$ 使得任何人都可以验证加密份额的有效性。也就是说，如果加密份额是有效的，那么所有的验证者都可以得到相同的值 $u$，并且 $u=s$。特别的，如果 $\texttt{PubVerify}$ 算法不需要和分发者之间交互，则PVSS方案为非交互式。
> $\exists u\ \forall A\in 2^{\{1,\cdots,n\}}:(\texttt{PubVerify}(\{S_ i\vert i\in A\})=1)\Rightarrow \texttt{Recover}(\{D_ i(S_ i)\vert i\in A\})=u$, and $u=s$ if the dealer was honest.

&emsp;&emsp;从上面的表达式中我们可以看出，VSS和PVSS的不同之处在于，**验证算法的输入**（VSS针对的是原始份额 $s_ i$，而PVSS方案针对的是加密份额 $S_ i$）以及**验证算法的对象**（VSS针对的是接收到份额的参与者，而PVSS方案针对的是所有人（从 $A$ 的定义范围可以看出））不同。


### 1.2 两个PVSS方案
&emsp;&emsp;文章分别介绍了两种PVSS方案，一种是基于共享离散对数方法的方案，一种是基于共享 $e$ 次根的方案。这两种方案都是基于ElGamal密码体制[[ElGamal]](#ElGamal)，但加密的方式不同。两个方案都是基于双离散对数问题。

#### 1.2.1 双指数运算和双离散对数问题
&emsp;&emsp;假设 $p$ 是大素数，且 $q=(p-1)/2$ 仍然是素数，同时 $h\in \mathbb{Z}_ p^*$ 是 $q$ 阶元素。另外，$g$ 是 $q$ 阶群 $G$ 的生成元，在群 $G$ 上存在离散对数问题。双指数运算是指： 
$$
\mathbb{Z}_ q\rightarrow G:x\mapsto g^{h^x}
$$
&emsp;&emsp;双离散对数问题是指给定 $y=g^{h^x}$ 和 $g,h$，计算唯一的 $x\in\mathbb{Z}_ q$ 是困难的。

#### 1.2.2 基于共享离散对数的PVSS
&emsp;&emsp;初始化阶段：$g$ 是群 $G$ 的生成元，$h$ 是 $q$ 阶元素。每个参与者拥有公私钥对 $(sk_ i,pk_ i)=(z_ i,h^{z_ i})$。
&emsp;&emsp;分发者执行以下步骤：
- 确定秘密 $s$，确定 $t-1$ 次多项式 $F(x)=s+\sum_ {j=1}^{k-1}f_ jx_ i^j$，其中 $x_ i\in\mathbb{Z}_ p$，$f_ j\in\mathbb{Z}_ p,\ j=1,\cdots,k-1$。计算份额 $s_ i$
- 公开 $S=g^s$ 以及 $F_ j=g^{f_ j}$。
> 如果是VSS方案，参与者只需要根据收到的份额和公开信息计算 $g^{s_ i}=S\cdot\prod_ {j=1}^{k-1}F_ j(x_ i^j)$。
- 使用参与者的公钥计算加密份额：
  - 随机选择 $\alpha\in\mathbb{Z}_ q$;
  - 计算密文 $(A,B)=(h^{\alpha},s_ i^{-1}\cdot y^{\alpha})$;
  > 解密时计算 $s_ i=\frac{A_ z}{B}$
- 分发者利用零知识证明，假设 $V=S_ i=g^s_ i$，则可以证明 $V^B=g^{vB}=g^{(y^{\alpha})}$。证明过程如下图：
  ![图 0](/assets/res/20240323PVSS/2024-03-23-19.png)  
  > 利用Fiat-Shamir变换[[FS87]](#FS87)，可以将上述证明过程设计成非交互式，只需要计算 $t_ hi=h^{w_ i}$，$t_ {gi}=g^{y^{w_ i}}$ 以及 $R=(r_ 1,\cdots,r_ l)=(w_ 1-c_ 1\alpha,\cdots,w_ l-c_ l\alpha)$，然后计算挑战 $c=H_ l(V\Vert A\Vert B\Vert t_ {h1}\Vert t_ {g1}\Vert \cdots \Vert t_ {hl}\Vert t_ {gl})$。

#### 1.2.3 基于共享 $e$ 次根的PVSS
&emsp;&emsp;与上面的方法相比，主要修改的地方是加密方法：
- 计算密文 $(A,B)=(g^\alpha,s_ i\cdot y^\alpha)$。
  > 解密时计算 $s_ i=\frac{B}{A^z}$。
- 分发者利用零知识证明，证明 $M=s_ i^e$。证明过程如下图：
  ![图 1](/assets/res/20240323PVSS/2024-03-23-3.png)  
  > 利用Fiat-Shamir变换[[FS87]](#FS87)，可以将上述证明过程设计成非交互式，只需要计算 $t_ g=g^{w}$，$t_ y=y^{ew}$ 以及挑战 $c=H(M\Vert A\Vert B\Vert t_ g\Vert t_ y)$。




## Reference
<!-- 这边文章是介绍如何在 Markdown 中增加文献引用。[<sup>1</sup>](#refer-anchor-1) -->
<div id="SHA79"></div>

- [SHA79] [Shamir, Adi. "How to share a secret." Communications of the ACM 22.11 (1979): 612-613.](https://dl.acm.org/doi/abs/10.1145/359168.359176)

<div id="FS87"></div>

- [FS87] [Fiege, Uriel, Amos Fiat, and Adi Shamir. "Zero knowledge proofs of identity." Proceedings of the nineteenth annual ACM symposium on Theory of computing. 1987.](https://dl.acm.org/doi/abs/10.1145/28395.28419)


<div id="ElGamal"></div>

- [ElGamal] [ElGamal, Taher. "A public key cryptosystem and a signature scheme based on discrete logarithms." IEEE transactions on information theory 31.4 (1985): 469-472.](https://ieeexplore.ieee.org/abstract/document/1057074)

