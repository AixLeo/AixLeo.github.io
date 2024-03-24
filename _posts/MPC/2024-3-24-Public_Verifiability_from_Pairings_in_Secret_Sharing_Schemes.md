---
layout:     post
title:      Public Verifiability from Pairings in Secret Sharing Schemes
subtitle:   
date:       2024-3-24
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - PVSS
    - Secret Sharing
    - Pairing
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

# Public Verifiability from Pairings in Secret Sharing Schemes

作者：Somayeh Heidarvand and Jorge L. Villar

机构：Universitat Politècnica de Catalunya, Spain

来源：Selected Areas in Cryptography 2008 [[原文链接]](https://link.springer.com/chapter/10.1007/978-3-642-04159-4_19)


## 1. Introduction

&emsp;&emsp;Stadler在1996年首次提出了PVSS的概念[[Sta96]](#Sta96)，并基于ElGamal密码体制和Double Discrete Logarithms问题提出了两种PVSS方案。但是这种DDL假设并不是标准的假设，因此，1999年，Schoenmakers[[Sch99]](#Sch99)基于标准离散对数假设，提出了新的PVSS方案。根据[[Sta96]](#Sta96)中共享 $e$ 次根的方法，Fujisaki和Okamoto提出了基于RSA假设的PVSS方案。但是他们的方案中，都需要基于Fiat-Shamir变换[[FS87]](#FS87)的非交互式知识证明。然而，Goldwasser和Tauman[[GT03]](#GT03)证明Fiat-Shamir变换存在一定的弱安全性。因此，2005年，Ruiz和Villar[[RV05]](#RV05)基于RSA假设进一步提出了无需NIZK的PVSS方案，但前提是需要在分发阶段之前进行一轮交互协商随即秘密值；同时，该方案在决策性复合剩余假设（Decisional Composite Residuosity Assumption, DCRA）下提出了秘密不可区分性（Indistinguishability of Secrets）。

&emsp;&emsp;然而上述方案都需要复杂的指数运算，而在密钥长度相同的情况下，基于ECC的加密比RSA加密效率更高；同时，自2001年Boneh等提出了基于配对的密码体制后，越来越多的研究者着眼于基于配对的密码学方案。2008年，Heidarvand和Villar首次提出了基于配对的PVSS方案。

### 1.1 主要贡献
&emsp;&emsp;文章首次提出了基于配对的PVSS方案，同时方案还具有以下优势：
1. 在决策性双线性平方假设（Decisional Bilinear Square Assumption, DBSA）下定义并证明了**IND1-Secrets**和**IND2-Secrets**；
2. 方案具有**计算安全性**以及**无条件可验证性**；
3. 方案**无需（NI）ZK**，从而降低了通信开销以及分发者的计算开销；
4. 方案具有**乘法同态性**；
5. 方案可以**抵抗主动和适应性敌手**（在诚实大多数的假设下）；
6. 推广：通过**公开信道进行秘密重构**；
7. 推广：从 $(t,n)$ 门限访问结构推广到**一般线性访问结构**。

### 1.2 双线性映射
&emsp;&emsp;对于素数 $q$ 阶群 $G_ 1, G_ 2$，其中 $g,h$ 分别是 $G_ 1, G_ 2$ 的生成元，存在双线性映射 $\hat{e}:G_ 1\times G_ 2\rightarrow G_ T$。特别的，如果 $G_ 1=G_ 2$，我们称为对称双线性映射，如果 $G_ 1\ne G_ 2$，我们称为非对称双线性映射。一般而言，**双线性映射具有以下性质**：
- 双线性性：对 $\forall \alpha,\beta\in \mathbb{F}_ q$，有 $\hat{e}(g^\alpha,h^\beta)=\hat{e}(g,h)^{\alpha\beta}$；
- 非退化性：$\hat{e}(g,h)\ne 1$，其中 $1$ 为 $G_ T$ 群的生成元；
- 可计算性：存在有效算法，使得在给定 $g,h$ 的情况下，计算 $\hat{e}(g,h)$ 是可行的。

&emsp;&emsp;双线性映射还具有以下的运算法则：
- $\hat{e}(g,h+z)=\hat{e}(g,h)\cdot\hat{e}(g,z)$
- $\hat{e}(g+z,h)=\hat{e}(g,h)\cdot\hat{e}(z,h)$
- $\hat{e}(\alpha g,h)=\hat{e}(g,\alpha h)=\hat{e}(g,h)^{\alpha}$


### 1.3 PVSS on Pairings
&emsp;&emsp;文中方案是在Schoenmakers[[Sch99]](#Sch99)方案的基础上结合了配对的加密方法，主要由初始化阶段，分发阶段和重构阶段构成。
- 初始化阶段：确定群 $G=<g,h>$，双线性映射 $\hat{e}:G\times G\rightarrow G_ 1$，每一个参与者 $P_ i\quad (i=1,\cdots,n,\quad n\ge 2t-1)$ 生成公私钥对 $(sk_ i,pk_ i)=(d_ i,h_ i=h^{d_ i})$，其中 $d_ i\overset{\$}{\leftarrow}\mathbb{F}_ q^*$。
- 分发阶段。分发阶段由以下两部分构成：
  - 份额分发。
    - 分发者确定秘密为 $S=\hat{e}(h,h)^{z_ 0}\in G_ 1$，然后随机选取 $t-1$ 次多项式 $P(x)=\sum\nolimits_ {j=0}^{t-1}\alpha_ jx^j$，其中 $\alpha_ j\in\mathbb{F}_ q$ 并且 $\alpha_ 0=z_ 0$。
    - 分发者发布系数的承诺值 $C_ j=g^{\alpha_ j},\quad 0\le j<t$。
    - 分发者公布用接收者公钥加密的份额 $Y_ i=h_ i^{P(i)},\quad 1\le i\le n$。
  - 份额验证。
    - 验证者（所有人）通过公开的承诺值计算 $X_ i=\prod\nolimits_ {j=0}^{t-1}C_ j^{i^j}$。这里有 $X_ i=g^{P(i)}$ 的隐藏关系。
    - 验证者验证等式 $\hat{e}(X_ i,h_ i)\overset{?}{=}\hat{e}(g,Y_ i)$。
- 重构阶段。重构阶段也由两部分构成：
  - 份额解密。参与者 $P_ i$ 使用私钥 $d_ i$ 解密 $Y_ i$ 得到原始份额 $S_ i=Y_ i^{d_ i^{-1}}=h^{P(i)}$。
  - 交换份额。
    - 参与者 $P_ i$ 将解密后的份额 $S_ i$ 发送给 $P_ j$，对方可以通过验证等式 $\hat{e}(S_ i,h_ i)=\hat{e}(Y_ i,h)$ 来判断份额的有效性。
    - 将通过验证的参与者加入集合 $B$。
    - 通过拉格朗日插值恢复 $h^{z_ 0}$：
    
    $$\prod_ {P_ i\in B}S_ i^{\lambda_ i}=\prod_ {P_ i\in B}(h^{P(i)})^{\lambda_ i}=h^{\sum\nolimits_ {P_ i\in B}\lambda_ iP(i)}=h^{P(0)}=h^{z_ 0},\qquad\lambda_ i=\prod_ {P_ j\in B\backslash\{P_ i\}}\frac{j}{j-i}$$

  - 恢复秘密 $S=\hat{e}(h^{z_ 0},h)$。


### 1.4 无条件验证性
&emsp;&emsp;对于分发阶段，假设存在多项式 $\tilde{P}(x)$ 使得 $\hat{e}(g,h)^{d_ iP(i)}=\hat{e}(g,h)^{d_ i\tilde{P}(i)}$，则存在 $P(x)=\tilde{P}(x)$，但是根据Shamir方案，当参与者为诚实大多数时，多项式 $P(x)$ 唯一，因此，分发阶段的验证是不基于任何假设无条件的。

&emsp;&emsp;对于重构阶段，假设存在多项式 $\tilde{P}(x)$ 使得 $\hat{e}(h,h)^{d_ iP(i)}=\hat{e}(h,h)^{d_ i\tilde{P}(i)}$，则存在 $P(x)=\tilde{P}(x)$，但是根据Shamir方案，当参与者为诚实大多数时，多项式 $P(x)$ 唯一，因此，重构阶段的验证也是不基于任何假设无条件的。

&emsp;&emsp;综上，整个方案都是无条件可验证的。或者，也可以理解为Pedersen承诺一样，因为指数上是 $d_ iP(i)$，相当于份额乘上了一个随机数，因此不依赖于任何安全假设。

### 1.5 计算安全性
&emsp;&emsp;对于PVSS方案，无条件安全是达不到的，因为份额需要加密，并通过公开信道发送，具有无限计算能力的敌手是可以破解密文获得实际份额的。因此，文中在DBS假设下，提出了IND-Secrets安全性。

#### 1.5.1 DBSA
&emsp;&emsp;设 $G,G_ 1$ 为阶为 $q$ 的群，$g$ 是群 $G$ 的生成元，存在 $\hat{e}:G\times G\rightarrow G_ 1$。随机选取 $\mu,v,s\in \mathbb{F}_ q^*$，且令 $h=g^\mu,\ u=g^v$。以下概率分布是多项式不可区分的：

$$D_ 0=(g,h,u,T_ 0=\hat{e}(h,h)^v=\hat{e}(g,g)^{\mu^2v})$$

$$D_ 1=(g,h,u,T_ 1=\hat{e}(h,h)^s=\hat{e}(g,g)^{\mu^2s})$$

> 这里之所以要加上**平方**的特性，是因为在方案的验证过程中，会出现平方的形式，不同于标准的DBDH假设，因此需要特殊提出DBS假设。

#### 1.5.2 IND1-Secrets
&emsp;&emsp;**定义1：**如果概率多项式时间敌手 $\mathcal{A}$ 在与挑战者 $\mathcal{C}$ 进行以下博弈中具有可忽略不计的优势，我们就说 $(t,n)$ 门限PVSS方案是 **IND1-Secrets**。
1. $\mathcal{C}$ 运行初始化阶段，向 $\mathcal{A}$ 发送**公共参数**以及**未被敌手腐败的参与者的公钥**，$\mathcal{C}$ 存储对应的私钥；
2. $\mathcal{A}$ 向 $\mathcal{C}$ 发送已经腐败的参与者的公钥；
3. $\mathcal{C}$ 随机选择两个秘密 $x_ 0,x_ 1$，以及随机比特 $b\in\{0,1\}$。然后 $\mathcal{C}$ 针对 $x_ 0$ 运行分发阶段，将得到的份额等输出结果发送给 $\mathcal{A}$，同时发送 $x_ b$；
4. $\mathcal{C}$ 为所有未被敌手腐败的参与者集合运行重构阶段，并将通过公共信道交换的所有信息发送给 $\mathcal{A}$；
5. $\mathcal{A}$ 猜测 $b^\prime$。
&emsp;&emsp;$\mathcal{A}$ 猜测成功的概率为 $\vert \text{Prob}[b^\prime=b]-\frac{1}{2}\vert\le\epsilon$

#### 1.5.2 IND2-Secrets
&emsp;&emsp;更强的安全性IND-Secrets主要针对敌手的能力不同进行刻画，主要区别在于敌手 $\mathcal{A}$ 可以选择被共享的秘密。

&emsp;&emsp;**定义2：**如果概率多项式时间敌手 $\mathcal{A}$ 在与挑战者 $\mathcal{C}$ 进行以下博弈中具有可忽略不计的优势，我们就说 $(t,n)$ 门限PVSS方案是 **IND2-Secrets**。
1. $\mathcal{C}$ 运行初始化阶段，向 $\mathcal{A}$ 发送**公共参数**以及**未被敌手腐败的参与者的公钥**，$\mathcal{C}$ 存储对应的私钥；
2. $\mathcal{A}$ 向 $\mathcal{C}$ 发送已经腐败的参与者的公钥；
3. $\mathcal{A}$ 随机选择两个秘密 $x_ 0,x_ 1$，并将他们发送给 $\mathcal{C}$。$\mathcal{C}$ 随机选择比特 $b\in\{0,1\}$，然后针对 $x_ b$ 运行分发阶段，并将得到的份额等输出结果发送给 $\mathcal{A}$；
4. $\mathcal{C}$ 为所有未被敌手腐败的参与者集合运行重构阶段，并将通过公共信道交换的所有信息发送给 $\mathcal{A}$；
5. $\mathcal{A}$ 猜测 $b^\prime$。
&emsp;&emsp;$\mathcal{A}$ 猜测成功的概率为 $\vert \text{Prob}[b^\prime=b]-\frac{1}{2}\vert\le\epsilon$

> IND2-Secrets中包含着IND1-Secrets，反之不然。然而，可以通过PVSS方案共享随机会话密钥 $K$ 来将IND1-Secrets方案升级为IND2-Secrets方案。分发者发布 $K\oplus s$，其中 $s$ 为要共享的秘密。这种方案可以称为**混合PVSS方案（Hybrid PVSS Scheme）**。

### 1.6 通过公开信道重构份额
&emsp;&emsp;传统的PVSS方案，在重构阶段需要参与者之间通过安全信道交换份额，本文中提出了通过公开信道交换份额的方法，其实质是对解密后的份额再次加密隐藏。具体步骤如下：
- 参与者解密得到原始份额 $S_ i=Y_ i^{d_ i^{-1}}$；
- 随机选择 $\rho\in\mathbb{F}_ q^*$，计算 $(r,z,w)=(h_ i^\rho,Y_ i^\rho,h_ j^{(d_ i\rho)^{-1}})$；
- 通过等式 $\hat{e}(r,Y_ i)\overset{?}{=}\hat{e}(z,h_ i)$ 来验证份额的有效性；
- 参与者 $P_ i$ 计算 $P_ j$ 的份额：
  
  $$\hat{e}(h,h)^{P(j)}=\hat{e}(z,w)^{d_ i^{-1}}$$
- 对 $t$ 个通过验证的有效份额，$P_ i$ 可以通过拉格朗日插值恢复秘密 $S=\hat{e}(h,h)^{P(0)}$。

### 1.7 乘法同态性
&emsp;&emsp;假设两个不同的秘密 $S$ 和 $\tilde{S}$，他们的共享信息分别为 $(C_ 0,\cdots,C_ {t-1},Y_ 1,\cdots,Y_ n)$ 和 $(\tilde{C}_ 0,\cdots,\tilde{C}_ {t-1},\tilde{Y}_ 1,\cdots,\tilde{Y}_ n)$。则对 $\forall\alpha,\beta\in\mathbb{F}_q^*$，$(C_ 0^\alpha\tilde{C}_ 0^\beta,\cdots,C_ {t-1}^\alpha\tilde{C}_ {t-1}^\beta,Y_ 1^\alpha\tilde{Y}_ 1^\beta,\cdots,Y_ n^\alpha\tilde{Y}_ n^\beta)$ 与秘密 $S^\alpha\tilde{S}^\beta$ 具有相同的概率分布。
> 因为方案中的运算都是在指数上，因此可以由指数上的加法同态性得到。


## Reference
<!-- 这边文章是介绍如何在 Markdown 中增加文献引用。[<sup>1</sup>](#refer-anchor-1) -->
<div id="Sta96"></div>

- [Sta96] [Stadler, Markus. "Publicly verifiable secret sharing." International Conference on the Theory and Applications of Cryptographic Techniques. Berlin, Heidelberg: Springer Berlin Heidelberg, 1996.](https://link.springer.com/chapter/10.1007/3-540-68339-9_17)

<div id="Sch99"></div>

- [Sch99] [Schoenmakers, Berry. "A simple publicly verifiable secret sharing scheme and its application to electronic voting." Annual International Cryptology Conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 1999.](https://link.springer.com/chapter/10.1007/3-540-48405-1_10)

<div id="FO98"></div>

- [FO98] [Fujisaki, Eiichiro, and Tatsuaki Okamoto. "A practical and provably secure scheme for publicly verifiable secret sharing and its applications." International Conference on the Theory and Applications of Cryptographic Techniques. Berlin, Heidelberg: Springer Berlin Heidelberg, 1998.](https://link.springer.com/chapter/10.1007/BFb0054115)

<div id="RV05"></div>

- [RV05] [Ruiz, Alexandre, and Jorge L. Villar. "Publicly verifiable secret sharing from Paillier's cryptosystem." WEWoRC 2005 - Western European Workshop on Research in Cryptology, July 5-7, 2005, Leuven, Belgium, 2005.](https://www.researchgate.net/publication/221538990_Publicly_Verfiable_Secret_Sharing_from_Paillier%27s_Cryptosystem)

<div id="GT03"></div>

- [GT03] [Goldwasser, Shafi, and Yael Tauman Kalai. "On the (in) security of the Fiat-Shamir paradigm." 44th Annual IEEE Symposium on Foundations of Computer Science, 2003. Proceedings.. IEEE, 2003.](https://ieeexplore.ieee.org/abstract/document/1238185)

<div id="FS87"></div>

- [FS87] [Fiege, Uriel, Amos Fiat, and Adi Shamir. "Zero knowledge proofs of identity." Proceedings of the nineteenth annual ACM symposium on Theory of computing. 1987.](https://dl.acm.org/doi/abs/10.1145/28395.28419)



