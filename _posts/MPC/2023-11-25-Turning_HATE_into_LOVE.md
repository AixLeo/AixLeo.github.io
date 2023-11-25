---
layout:     post
title:      Turning HATE Into LOVE | Compact Homomorphic Ad Hoc Threshold Encryption for Scalable MPC
subtitle:   
date:       2023-11-25
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - MPC
    - Secret Sharing
    - Threshold Encryption
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

# Turning HATE Into LOVE: Compact Homomorphic Ad Hoc Threshold Encryption for Scalable MPC

作者：Leonid Reyzin<sup>1</sup>， Adam Smith<sup>1</sup>， Sophia Yakoubov<sup>2</sup>  

机构：<sup>1</sup> Boston University;  <sup>2</sup> Aarhus University  

来源：International Symposium on Cyber Security Cryptography and Machine Learning 2021 [[原文链接]](https://link.springer.com/chapter/10.1007/978-3-030-78086-9_27)


## 1. Introduction

文章[[RSY21]](#RSY21)介绍了一种新的门限加密（Threshold Encryption, TE）的构造：利用不可区分混淆（Indistinguishability Obfuscation, iO），在Ad-hoc网络下，实现ATE及HATE；同时介绍了该构造在可扩展MPC场景下的应用。

### 1.1 TE & ATE
&emsp;&emsp;门限加密（Threshold Encryption, TE）方案[[DF89]](#DF89)是一种加密方案，其中消息被加密给到一组接收者 $R$，而解密必须由该组的至少 $𝑡+1$ 个成员协作完成。然而传统的门限加密方案需要设置可信中心。该可信中心需要生成主密钥 $msk$，并据此生成参与者的公私钥对，进而将消息的随机份额以加密的方式分发给各参与方。  

&emsp;&emsp;然而，在Ad-hoc门限加密（Ad-hoc Threshold Encryption, ATE）方案中，各参与方是对等实体，每一方都能够独立生成自己的公私钥对，而不需要可信中心以及其主密钥 $msk$。
 
>门限加密由以下6个算法构成：
>1. $\texttt{Setup}(1^\lambda, t)\rightarrow (\texttt{params}, msk)$. 生成系统参数和主私钥。
>2. $\texttt{KeyGen}(\texttt{params}, msk)\rightarrow (pk,sk)$. 生成接收者公私钥对。
>3. $\texttt{KeyGen}_ {Sndr}(\texttt{params}, msk)\rightarrow (pk_ {Sndr},sk_ {Sndr})$. 生成发送者公私钥对。
>4. $\texttt{Enc}(\texttt{params},sk_ {Sndr},\{pk_ i\}_ {i\in R,\vert R \vert > t},m)\rightarrow c$. 使用发送者私钥和接收者公钥对消息进行加密。
>5. $\texttt{PartDec}(\texttt{params},pk_ {Sndr},\{pk_ i\}_ {i\in R},sk_ j,c)\rightarrow d_ j$. 各参与者对部分密文进行解密，得到对应份额。
>6. $\texttt{FinalDec}(\texttt{params},pk_ {Sndr},\{pk_ i\}_ {i\in R},c,\{d_ i\}_ {i\in R'\subseteq R, \vert R' \vert > t})\rightarrow m$. 根据至少 $t+1$ 个参与者提交的解密份额，恢复出明文 $m$。

<br />

&emsp;&emsp;值得注意的是，上述门限加密基本构造存在弹性，即存在可减少/增加的输入，比如：
- 在Ad-hoc网络下，ATE方案不需要可信中心，因此不需要使用主密钥 $msk$。
- 在一些特殊情况下（比如recipient-set-obliviousness方案中），PartDec和FinalDec不需要接收者的公钥 $\{pk_ i\}_ {i\in R}$。
- 大部分情况下，我们假设门限 $t$ 在全局公共参数 $\texttt{params}$ 内是固定不变的。然而，当需要门限可变时，我们允许发送者在加密时选择 $t$，这称之为“ $t$ -flexible”.

### 1.2 TE with Homomorphism
![图 0](/assets/res/LOVE_MPC/2023-11-24-94.png)  

>HATE在安全多方计算的应用中特别实用！

### 1.3 TE Compactness
&emsp;&emsp;在传统的“Share-and-Encrypt”（即先对明文进行秘密分享，再分别对份额进行加密）构造中，因为每一个接收者得到的密文都不同，因此密文总的大小是 $\Theta(n)$。密文随着参与者数量的变化而变化，无疑增加了通信开销，因此，作者定义了几种不同类型的“Compactness”。
- **Sender-compact**：密文大小与接收者数量无关；
- **Recipient-compact**：每个接收者在PartDec阶段所需的密文部分大小与接收者数量无关；
- **Recipient-set-obliviousness**：在PartDec和FinalDec阶段都不使用接收者的公钥 $\{pk_ i\}_ i\in R$；
- **Compactness with homomorphism**：评估算法 **Eval** 的输出（即 $c^*$ ）大小与参与者的数量无关。

## 2. Sender-compact ATE
&emsp;&emsp;传统的“Share-and-Encrypt”方法，需要的密文总大小为 $\Theta(n)$。若想压缩密文大小，自然而然想到的方法是对密文进行压缩混淆，但即使如此，密文大小依然和接收者人数成线性关系。因此，本文的方法，不是在密文上应用混淆程序，而是将混淆程序作为发送者的公钥。如此一来，虽然公钥很长，但是只需要创建和传播一次，不占用消息空间。

&emsp;&emsp;这种构造需要用到以下的基础知识，读者感兴趣可以自行了解，篇幅有限，这里不再赘述。
- 不可区分混淆（Indistinguishability Obfuscation, iO）[[BGI+01]](#BGI+01)是一种形式化定义了程式混淆的密码原语。
- 可穿孔伪随机函数(Puncturable Pseudorandom Function, PPRF)[[KPTZ13]](#KPTZ13)具有主密钥 $k$，使得能够在域的所有点上评估PPRF，并且具有穿孔密钥 $k_ x$，使得能够在除了一点(点 $(x,y)$ )之外的所有点上评估PPRF。
- 受限签名（Constrained Signatures）[[BZ14]](#BZ14)拒绝与某些约束条件 $\mathcal{C}$ 不匹配的所有签名。

基于混淆的ATE构造如下图：
![图 1](/assets/res/LOVE_MPC/2023-11-25-95.png)  

其中 $\texttt{ObfFunc}$ 构造如下图：
![图 2](/assets/res/LOVE_MPC/2023-11-25-14.png)  

&emsp;&emsp;这里简要说明一下，文章主要思想，就是将混淆程序作为发送者的公钥。发送者只是harddcode一个程序，这个程序具有 $f$ 的功能，发送者不具有任何的输入输出；而当接收者拿到公钥（混淆程序）时，输入自己的私有输入，就能得到对应的输出，而程序相对接收者来说是“黑河”，即接收者不知道程序具体的内容，也不知道程序里隐藏的发送者的秘密。
&emsp;&emsp;这里说明一下发送者的公钥——混淆程序中的函数功能 $f_ {k_ w,k_ {Share}, SIG.pk(\overrightarrow{pv}=\{pv_ i\}_ {i\in R}, \texttt{idx}, sv, \texttt{nonce},\sigma )}$。发送者在打包程序时，将PPRF密钥 $k_ w$ ， $k_ {Share}$ 和 $SIG.pk$ 隐藏其中，其中：
-  $k_ w$ 用于生成秘密值 $w$，此秘密值后续将用于加密明文消息 $m$。
-  $k_ {Share}$ 用于秘密共享 $w$。
-  $SIG.pk$ 用于验证颁发给接收者的受限签名。
-  $f$ 还包含5个输入，分别是接收者公钥集合构成的向量、该接收者公钥在集合中的索引、该接收者的私钥、随机值和发送者颁发的受限签名。
&emsp;&emsp;当接收者输出参数后，程序判定接收者是否是特定集合中的成员，判定通过，则为其生成 $w$ 的份额。

&emsp;&emsp;在完整构造中，Enc阶段，发送者用 $e=w+m$ 来隐藏明文，并对特定集合的接收者公钥发布签名。PartDec阶段，接受收利用发送者的公钥（混淆程序）生成关于 $w$ 的份额。FinalDec阶段，从至少 $t+1$ 个接收者的份额中，重构出 $w$，再恢复出明文 $m$。该方案可以做进一步提升：
- $t$-Flexibility: 方案中的门限是固定在发送者公钥中，如果想要门限可变，可以将门限 $t$ 设置在 Enc 阶段的输入中。
- Reducing the Public Key Size: iO程序的输出是比较大的，因此即使只产生和分发一次，该方案仍具有很大的计算和通信开销，可以使用 differing-inputs obfuscation （diO）[[BGI+01]](#BGI+01)[[ABG+13]](#ABG+13)来减小公钥大小，进一步提高效率。

## 3. HATE
### 3.1 Share-and-Encrypt
&emsp;&emsp;对于传统的“Share-and-Encrypt”方法来说，只要秘密共享方案和加密方案具有相同的同态性质，即可构造成如下图所示的HATE方案，并且该方案具有“Recipient-set-oblivious”和“Recipient-compact”性质。特别的，如果秘密共享方案和加密方案都具有全同态性质，那构造出来的HATE方案也同样是全同态TE方案。
![图 3](/assets/res/LOVE_MPC/2023-11-25-66.png)  

&emsp;&emsp;文中给出了两种可能的构造：
1. Shamir-and-ElGamal. 因为ElGamal加密方案具有乘法同态性质，而Shamir秘密共享方案具有加法同态性质，因此，我们需要使用指数Shamir秘密共享（Exponential Shamir SS）方案实现HATE。
2. CRT-and-Paillier. 使用Camenisch-Shoup的加法同态加密算法[[CS03]](#CS03)和中国剩余定理（CRT）秘密共享构造HATE。

### 3.2 Obfuscation-Based HATE
&emsp;&emsp;OB-HATE方案与前文介绍的ATE方案类似，只不过在发送者的公钥（混淆程序）中，输出结果不再是明文的 $w$，而是经过同态加密方案加密后的关于 $w$ 的密文 $c_ w$。
&emsp;&emsp;而PartDec解密阶段，接收者需要对接收到的密文消息 $c_ e$ 进行同态评估，从而计算 $c_ m=c_ e-c_ w$，进而解密出明文份额，再FinalDec阶段通过重构算法重构出完整明文 $m$。

## 4. LOVE MPC
&emsp;&emsp;文章将上述OB-HATE应用到了“大规模、单服务器、参与者可消失的高效多方计算”（Large-scale One-server Vanishing-participants Efficient MPC, LOVE MPC）中。应用场景描述为：一个具有大规模用户的、具有单个服务器的APP，想要收集用户的使用信息。用户在不泄露个人数据的情况下将数据交给服务器，服务器利用同态性质计算全部用户的数据和。该MPC包括三轮消息，并且具有以下性质：
- 与传统MPC不同，该MPC方案允许部分参与者消失（主要是应用了门限秘密共享方案）；
- 计算结果只有服务器知道。

&emsp;&emsp;具体构造如下图：
![图 4](/assets/res/LOVE_MPC/2023-11-25-69.png)  

>简单来说，各参与方将自己的明文数据 $x_ i$ 加密发送给服务器；
>服务器对多个接收者的密文进行同态评估，计算出具有同态性质的新的密文 $c$;
>各参与方对新的密文 $c$ 进行部分解密，将份额发送给服务器；
>服务器在FianlDec阶段对明文进行重构，得到的输出结果是关于各参与方明文 $x_ i$ 的计算结果。



## Reference
<!-- 这边文章是介绍如何在 Markdown 中增加文献引用。[<sup>1</sup>](#refer-anchor-1) -->
<div id="RSY21"></div>

- [RSY21] [Reyzin, Leonid, Adam Smith, and Sophia Yakoubov. "Turning hate into love: compact homomorphic ad hoc threshold encryption for scalable MPC." International Symposium on Cyber Security Cryptography and Machine Learning. Cham: Springer International Publishing, 2021.](https://link.springer.com/chapter/10.1007/978-3-030-78086-9_27)

<div id="DF89"></div>

- [DF89] [Frankel, Yvo Desmedí Yair, and Y. Desmedt. "Threshold cryptosystems." CRYPTO. Vol. 89. 1998.]

<div id="BGI+01"></div>

- [BGI+01] [Barak, Boaz, et al. "On the (im) possibility of obfuscating programs." Annual international cryptology conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 2001.](https://link.springer.com/chapter/10.1007/3-540-44647-8_1)

<div id="KPTZ13"></div>

- [KPTZ13] [Kiayias, Aggelos, et al. "Delegatable pseudorandom functions and applications." Proceedings of the 2013 ACM SIGSAC conference on Computer & communications security. 2013.](https://dl.acm.org/doi/abs/10.1145/2508859.2516668)

<div id="BZ14"></div>

- [BZ14] [Boneh, Dan, and Mark Zhandry. "Multiparty key exchange, efficient traitor tracing, and more from indistinguishability obfuscation." Algorithmica 79 (2017): 1233-1285.](https://link.springer.com/article/10.1007/s00453-016-0242-8)

<div id="ABG+13"></div>

- [ABG+13] [Ananth, Prabhanjan, et al. "Differing-inputs obfuscation and applications." Cryptology ePrint Archive (2013).](https://eprint.iacr.org/2013/689)

<div id="CS03"></div>

- [CS03] [Camenisch, Jan, and Victor Shoup. "Practical verifiable encryption and decryption of discrete logarithms." Annual International Cryptology Conference. Berlin, Heidelberg: Springer Berlin Heidelberg, 2003.](https://link.springer.com/chapter/10.1007/978-3-540-45146-4_8)
