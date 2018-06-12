<script type="text/javascript" async="" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

# Quaternion.Slerp
很久之前就看到这个算法了,当年写过程序验证了插值确实正确,一直没有想清楚原理,最近看了PBRT上的说明,其中阐述了三维或二维的slerp,再扩展到四维并应用于四元数上,再结合网上搜索资料,个人感觉总无法把slerp与四元数的旋转插值对应起来,也许这是从更抽象的角度来看待这个问题吧.
为了自己能够理解这个算法为什么会得到正确的插值,最近用自己能理解的数学知识证明了这个算法,在此记录下来,也希望能帮助到那些像我一样数学不太好的朋友.

## 基于四元数的旋转
首先,四元数表示旋转的公式:

$$q = cos\theta + sin\theta*\vec{v}$$

其中$$\vec{v}$$为单位向量,则四元数q为单位四元数,对于一点$$p$$,$${p}'=qpq^{-1}$$ 代表$$p$$绕 $$\vec{v}$$ 旋转$$2\theta$$角度后的位置

## Slerp算法

$$slerp(q_{1}, q_{2}, t) = \frac{a+1}{ b+1}$$


## 证明过程

那么对于从$$q_{1} = q_{1}pq_{1}^{-1}$$到$$q_{2} = q_{2}pq_{2}^{-1}$$,需要经过一个中间四元数为$$q_{m}$$,使得$$q_{m}q_{1}pq_{1}^{-1}q_{m}^{-1} = q_{2}pq_{2}^{-1}$$,

所以这个中间四元数为

$$q_{m} = q_{2} * q_{1}^{-1}$$

把$$q_{m}$$写成旋转形式

$$q_{m} = cos\theta + sin\theta*\vec{v}$$

aaaaaaa

