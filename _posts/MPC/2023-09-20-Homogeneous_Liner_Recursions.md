---
layout:     post
title:      Homogeneous Liner Recursions
subtitle:   
date:       2023-09-20
author:     AIX
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - MPC
    - Secret Sharing
    - Discrete Mathematics
---

<head>
<script id="MathJax-script" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/MathJax.js"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        jax: ["input/TeX", "output/CommonHTML"],
        extensions: ["tex2jax.js"],
        tex2jax: {
            skipTags: [],
            inlineMath: [['$', '$']],
            displayMath: [['$$', '$$']],
        },
        menuSettings: {
            zoom: 'Click',
        }
    });
</script>
    <!-- <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script> -->
    <!-- <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']],
            displayMath: [['$$','$$']]
            }
        });
    </script> -->
</head>

# Homogeneous Liner Recursions (HLR)

## 1. HLR定义

设 $t \in N^+$，$c_ 0,\cdots ,c_ {t-1}, a_ 1,\cdots ,a_ t \in R^+$，次数为 $t$ 的齐次线性递推可以定义为：  

$$HLR：\left\{\begin{matrix}
  u_ 0=c_ 0,\ u_ 1=c_ 1,\ \cdots , \ u_ {t-1}=c_ {t-1}, \\
  u_ {i+t}+a_ 1u_ {i+t-1}+\cdots +a_ tu_ i=0, i\ge 0
\end{matrix}\right.$$

即前 $t-1$ 项是由 $c_ j,\ j\le t-1$ 直接赋值，后面的项由递推公式逐项计算得到。  
<br />

### 辅助公式的定义
由以上齐次线性递推公式的定义，我们可以定义以下辅助方程：

$$
x^ t+a_ 1x^ {t-1}+\cdots +a_ {t-1}x+ a_ t=0
$$

上述辅助方程为 $t$ 次线性方程，因此存在 $t$ 个根。需要注意的是，这 $t$ 个根可能出现重根。  

假设 $\alpha_ 1,\alpha_ 2,\cdots, \alpha_ l$ 是上述辅助方程的根，则方程可以表示为 $$(x-\alpha_ 1)^{m_ 1}(x-\alpha_ 2)^{m_ 2}\cdots (x-\alpha_ l)^{m_ l}=0$$ 

其中， $m_ 1,m_ 2,\cdots m_ l$ 分别为对应根的重复度（multiplicity），即重根的个数，且 $m_ 1+m_ 2+\cdots +m_ l=t$ 。  
<br />

## 2. 定理
对于 $i\ge 0$，由上述辅助方程，可以得到：

$$
u_ i=\sum_ {j=1}^{l}p_ j(i)\alpha_ j^i
$$

其中， $j\in [1,l]$，$p_ j(i)$ 是形为 $\sum_ {k=0}^{m_ j-1}A_ ki^k$ 的多项式，即 $A_ 0+A_ 1i+\cdots +A_ {m_ j-1}i^{m_ j-1}$ 。  

<br />

特殊形式，假设上述辅助方程存在一个 $t$ 重根 $\alpha$，则方程可以表示为 $(x-\alpha)^t=0$，此时，**定理**可以表示为:  

$$
u_ i=p(i)\alpha^i=(A_ 0+A_ 1i+\cdots +A_ {t-1}i^{t-1}) \tag{1}
$$

关于定理的证明，可以参考[[Biggs89]](#Biggs89)
<br />

## 3. 在秘密共享中的用法

在Shamir秘密共享中， 多项式 $f(x)$ 定义为：

$$
f(x)=a_ 0+a_ 1x+\cdots +a_ {t-1}x^{t-1}
$$

其中秘密 $s=f(0)=a_ 0$， 份额 $s_ i=f(x_ i)=a_ 0+a_ 1x_ i+\cdots +a_ {t-1}x_ i^{t-1}$，多项式系数 $a_ i$ 为秘密值，当恢复秘密时，利用插值定理，计算：

$$
f(0)=\sum_ {i=1}^ts_ i\prod_ {\substack{j=1\\j\ne i}}^t\frac{i}{i-j}
$$

当在**式1**两边乘上 $\alpha^{-i}$ 时，可得到：

$$
u_ i\alpha^{-i}=p(i)=(A_ 0+A_ 1i+\cdots +A_ {t-1}i^{t-1})\alpha^{-i}
$$

此时，虽然不知道 $A_ k$的具体值，但 $u_ i\alpha^{-i}$ 即可等同于Shamir方案中的份额 $s_ i$，在恢复阶段，可计算：

$$
s=\sum_ {i=1}^t(u_ i\alpha^{-i})\prod_ {\substack{j=1\\j\ne i}}^t\frac{i}{i-j}
$$

## Reference

<div id="Biggs89"></div>

- [Biggs89] [Biggs, N.L.: Discrete Mathematics, revised ed, Oxford University Press, New York. (1989).]


<div id="DM08"></div>

- [DM08] [Dehkordi, Massoud Hadian, and Samaneh Mashhadi. "New efficient and practical verifiable multi-secret sharing schemes." Information Sciences 178.9 (2008): 2262-2274.](https://www.sciencedirect.com/science/article/pii/S0020025507005622)




