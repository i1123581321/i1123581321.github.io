---
title: 计算理论：可判定性与复杂度
mathjax: true
documentclass: ctexart
classoption: UTF8
geometry:
  - margin=1in
  - a4paper
date: 2021-11-21 16:56:21
tags: "theory"
categories: ["course notes", "theory of computation"]
---

# Decidability and Complexity

## Decidability

### Diagonalization method

[Diagonalization method](https://en.wikipedia.org/wiki/Cantor%27s_diagonal_argument) 是康托尔提出的一种数学证明方法，用于证明某个集合是不可数的（即无法建立起该集合和自然数集之间的双射）

#### Uncountable sets

自然数集 $\mathbb{N} = \{1, 2, \dots \}$ 是 countable 的

一个集合 $S$ 是 countable，满足其是有限集，或其是无限集且存在一个 bijection

$$
f: \mathbb{N} \to S
$$

E.g. 有理数集是 countable 的，但是实数集是 uncountable 的

Proof.

如果实数集是 countable，则存在一个双射 $f$ ，使得每一个实数都有一个唯一的整数 $i$ 对应，则将所有实数按照自然数的顺序排列，则令

$$
x = 0.a_{1}a_{2}\dots
$$

其中 $a_{i}$ **不等于**$f(i)$ 小数点后的第 $i$ 位数字。显然 $x$ 是一个实数，且如果存在满足要求的双射，则 $x$ 也有一个对应的自然数 $j$ ，则产生矛盾，如果 $x$ 是第 $j$ 个实数，则其小数点后第 $j$ 位应当等于 $f(j)$ 小数点后第 $j$ 位，而这与 $x$ 的定义矛盾，故实数集是 uncountable

Q.E.D.

#### Non-RE languages

根据之前的结论，语言之间的包含关系为

regular language $\subset$ CFL $\subset$ recursive language $\subset$ RE $\subset$ all language

前两个真包含关系已经可以根据形如 $a^{n}b^{n}, a^{n}b^{n}c^{n}$ 的语言得出，现在首先证明最后一个真包含关系，即：存在语言不是 RE language

证明分为三步

* 证明所有 TM 的集合是 countable 的
* 证明所有 language 的集合是 uncountable 的
* 根据上述结论，不可能存在从 TM 集合到 language 集合的满射

1). 所有 TM 的集合是 countable 的

Proof.

所有 string 的集合 $\Sigma^{*}$ 是 countable 的，因为可以将所有 string 按照其长度和字典序排成一列，为其标号

而每个图灵机 $M$ 可以表示为一个有限长度的 string，设其为 $<M>$ ，则从所有 string 的集合中删去所有不是 TM 的 string，则得到 TM 的集合，显然这个集合是 countable 的

Q.E.D.

2). 所有 language 的集合是 uncountable 的

Proof.

所有 string 的集合是 countable 的，则可以将每个 string 编号，得到 $s_{1}, s_{2}, \dots$ ，则一个 language $L$ 可以由一个特征向量 $x^{L}$ 描述，如果 $s_{i} \in L$ 则 $x^{L}_{i} = 1$ ，否则 $x^{L}_{i} = 0$ ，则同样使用对角法，假设所有 language 的集合是 countable，则对每个特征向量存在一个唯一的自然数与其对应。则考虑语言 $L$

$$
x^{L}_{i} = \begin{cases}
1 & f(i)_{i} = 0\\
0 & f(i)_{i} = 1
\end{cases}
$$

即如果第 $i$ 个语言包含 $s_{i}$ 则 $L$ 不含 $s_{i}$ ，反之亦然

则如果所有 language 的集合是 countable 则产生矛盾，若 $L$ 对应的自然数为 $j$ ，则按照映射如果 $s_{j} \in L$ 则 $f(j)_{j} = 1$ ，按照定义则 $x^{L}_{j} = 0$ ，即 $s_{j} \notin L$ ，矛盾

Q.E.D.

由于 TM 的集合是 countable 而 language 的集合是 uncountable，故存在语言不会被任何 TM 接受

### The halting problem is undecidable

#### Halting problem

停机问题的定义是语言

$$
\text{HALT} = \{<M, x> : \text{TM } M \text{ halts on input }x \}
$$

其中 $<M, x>$ 是 TM $M$ 和 $x$ 的字符串形式

显然这个语言是 RE，模拟 $M$ 对于 $x$ 为输入的运行即可，现在需要证明 HALT 是否是 decidable，即是否存在一个 algorithm 接受 HALT

HALT 不是 decidable 的

Proof.

以下给出大致的证明流程

假设存在 TM $H$ decides HALT，即如果 $M$ 在 $x$ 上 halt，$H$ 接受，否则 $H$ 拒绝

则定义一个新的 TM $H^{\prime}$ ，对于输入 $<M>$ ，如果 $H$ 接受 $<M, <M>>$ ，则无限循环，否则 halt

现在考虑将 $<H^{\prime}>$ 输入 $H^{\prime}$ ，则如果 $H^{\prime}$ halt，按照定义说明 $H$ 拒绝 $<H^{\prime},<H^{\prime}>>$ ，而按照 $H$ 的定义，$H^{\prime}$ 不应当 halt，反之亦然，矛盾

根据反证法，$H, H^{\prime}$ 均不存在

Q.E.D.

使用对角法也可以证明该结论，思路与上文类似

#### RE and co-RE

一个 RE 的补是叫做 co-RE，而所有 co-RE 的集合组成了 co-RE 语言（注意 co-RE 不是非 RE）

则有结论：一个语言 $L$ 是 decidable $\iff$ $L$ 是 RE 且 $L$ 是 co-RE

Proof.

$\Rightarrow$ 显然 $L$ 如果是 decidable 则 $L$ 一定是 RE，则 $L$ 的补只需要反转 TM 的接受/拒绝，则 $L$ 的补同样是 RE，得出 $L$ 是 co-RE

$\Leftarrow$ 易得存在 TM $M$ 接受 L，以及存在 TM $M^{\prime}$ 接受 $L$ 的补，则只需要构造 TM 平行模拟 $M, M^{\prime}$ ，当 $M$ 接受则接受，当 $M^{\prime}$ 接受则拒绝，即得到一个 algorithm 接受 $L$， $L$ 为 decidable

Q.E.D.

根据上述结论，可以得出 HALT 的补不是 RE。如果 HALT 的补是 RE，则 HALT 是 co-RE，根据上文结论，HALT 为 decidable，而这与之前的结论相矛盾

![2019-11-24_19-26-40.png](https://i.loli.net/2019/11/24/jxEOCa12h8mA36f.png)

上述图片阐明了各个语言之间的关系

### Undecidable Problems about TM

#### Reductions

归约（reduction）是一种转换。如果存在一个转换将问题 $P_{1}$ 的实例转换为问题 $P_{2}$ 的实例，且输出相同（如果该实例在 $P_{1}$ 中是 YES，则转换后的实例在 $P_{2}$ 中也是 YES），则称 $P_{1}$ reduces to $P_{2}$，一般记为

$$
P_{1} \leqslant P_{2}
$$

归约可以说明 $P_{2}$ 至少和 $P_{1}$ **一样难**，因为如果存在一个可以解决 $P_{2}$ 的 oracle，可以通过归约解决 $P_{1}$ ，但反之不成立

归约不要求一定是满射和单射，事实上一般 $P_{1}$ 映射过去后的集合只是 $P_{2}$ 的一个子集。

更为正式的定义如下：

Many-one reduction: 考虑 $A, B$ 是 alphabet $\Sigma, \Gamma$ 上的形式语言，则从 $A$ 到 $B$ 的一个 **Many-one reduction** 是一个可计算的函数 $f: \Sigma^{*} \to \Gamma^{*}$ 满足

$$
\forall w , w \in A \iff f(w) \in B
$$

> Computable: $f$ is computable if there exists a TM $M_{f}$ s. t. on every $w \in \Sigma^{*}$ , $M_{f}$ halts on $w$ with $f(w)$ written on its tape

若存在满足的 $f$ 则称 $A$ m-reduces to $B$ ，记为

$$
A \leqslant_{m} B
$$

如果 $f$ 是 injective 则称 $A$ 1-reduces to $B$ ，记为

$$
A \leqslant_{1} B
$$

如果存在一个 $A$ 到 $B$ 的 reduction，则有

* 如果 $A$ undecidable，则 $B$ undecidable
* 如果 $A$ 是 non-RE，则 $B$ 是 non-RE

Proof.

考虑陈述 1，如果 $A$ 是 undecidable，但 $B$ 是 decidable，则可以通过 reduction 将 $A$ 的实例 $w$ 转换为 $f(w)$ ，然后使用 $B$ 的 decider 判定  $f(w)$ ，从而 $A$ 也可判定，矛盾

陈述 2 同理，可以通过 $B$ 的 recognizer 和 reduction 构造 $A$ 的 recognizer，导出矛盾

Q.E.D.

因此 reduction 可以用来证明语言的 undecidable/non-RE

假设已知 $A$ 是已知的 undecidable 的语言，欲证 $B$ 是 undecidable，步骤为

* 假设 $B$ 是 decidable，则存在 decider $M_{B}$
* 使用 $M_{B}$ 判定 $A$ （即构造从 $A$ 到 $B$ 的 reduction $f$，对于任意 $w$ ，使用 $M_{B}$ 判定 $f(w)$ ）
* 这样得出 $A$ 是 decidable，导出矛盾，根据归谬法，$B$ 是 undecidable

#### $A_{TM}$ is undecidable

$A_{TM}$ 的定义为

$$
A_{TM} = \{<M, w>: w \in L(M)\}
$$

而根据上文结论，已知 HALT 是 undecidable 的，那么通过 reduction 可以证明 $A_{TM}$ 是 undecidable

Proof.

将 HALT 归约到 $A_{TM}$

如果 $A_{TM}$ 可判定，可以通过 $A_{TM}$ 判定 HALT，从而导出矛盾

对于 HALT 的实例 $<M, w>$ ，可以构造一个 TM $H$ ，对于输入 $<M, w>$ 判断 $<M, w>$ 是否属于 $A_{TM}$

* 如果属于，则说明 $M$ halt 并接收 $w$，$H$ accept
* 如果不属于，则说明 $M$ 或是 halt 并 reject $w$ ，或是在 $w$ 上无限循环
  * 构造 $M^{\prime}$ ，模拟 $M$ 的运行，且当 $M$ halt 时，若 $M$ 结果为 accept 则 $M^{\prime}$ reject，反之若 $M$ 结果为 reject 则 $M^{\prime}$ accept，若 $M$ 无限循环则 $M^{\prime}$ 同样会无限循环
  * 判断 $<M^{\prime}, w>$ 是否属于 $A_{TM}$ ，如果属于则说明 $M$ 在 $w$ 上 halt 并 reject，则 $H$ accept，否则说明无论 $M, M^{\prime}$ 在 $w$ 上都循环，$H$ reject

由于 $A_{TM}$ 可判定，故 $H$ 最终总会 halt，且有 $HALT = L(H)$ ，而 HALT 为 undecidable，矛盾。

Q.E.D.

除了归约的证明，也可以直接用归谬法证明

Proof.

如果 $A_{TM}$ decidable，假设其 decider 为 $H$ ，则构造 TM $D$ 接受 $<M>$ 输入

* 如果 $<M,<M>> \in A_{TM}$ 则 $D$ reject
* 如果 $<M, <M>> \notin A_{TM}$ 则 $D$ accept

则考虑 $D$ 以 $<D>$ 为输入，如果 accept，说明 $<D, <D>> \notin A_{TM}$ ，即 $<D> \notin D$ ，导出矛盾。反之亦然，故根据归谬法，不存在 $D$ 和 $H$，亦即 $A_{TM}$ 为 undecidable

Q.E.D.

#### $E_{TM}$ is undecidable

$E_{TM}$ 的定义为

$$
E_{TM} = \{<M>:L(M) = \varnothing \}
$$

$E_{TM}$ 是 undecidable 的

Proof.

将 $A_{TM}$ 归约到 $E_{TM}$

对于 $A_{TM}$ 的实例 $<M, w>$ ，可以构造一个 TM $M^{\prime}$ ，$M^{\prime}$ 模拟 $M$ 在 $w$ 为输入时的运行

* 如果 $M$ accept $w$ ，则 $M^{\prime}$ accept 其输入 $x$
* 否则 $M^{\prime}$ reject 其输入 $x$

如此构造后 $<M, w> \in A_{TM} \iff L(M^{\prime}) \neq \varnothing \iff <M^{\prime}> \notin E_{TM}$

如果 $E_{TM}$ decidable 则 $A_{TM}$ 也 decidable，导出矛盾，故 $E_{TM}$ undecidable

Q.E.D.

事实上 $E_{TM}$ 是 non-RE 的，因为其补是 RE（构造 NTM 猜测一个输入 $x$ 判断其是否属于 $L(M)$ ，只要 $L(M)$ 非空则一定能猜到一个 $x$ 并接受 $<M>$），而如果 $E_{TM}$ 也是 RE 则 $E_{TM}$ 为 decidable

### Rice's Theorem

Rice's Theorem: all *non-trivial*, *semantic properties* of programs are undecidable

semantic property: Rice's theorem 适用范围是关于语言的属性而非关于机器/程序的属性。E.g. 一个程序是否会在所有输入上 halt 就是一个 semantic property，而程序是否会运行超过 1000 步或 TM 是否有超过 5 个状态就不是 semantic property

如果将 property 看作一个接受 TM 为输入的 language $P$，则如果 $L(M_{1}) = L(M_{2})$ ，有

$$
<M_{1}> \in P \iff <M_{2}> \in P
$$

non-trivial: 一个 trivial 的 property 满足：或是所有语言都有该 property ，或是没有语言有该 property。否则其为 non-trivial

$P$ is non-trivial if

$$
\begin{gathered}
\exists M_{1}, <M_{1}> \in P\\
\exists M_{2}, <M_{2}> \notin P
\end{gathered}
$$

要注意 Rice's Theorem 的适用范围仅限 **non-trivial semantic properties**

$P$ is non-trivial $\iff$ $P$ is undecidable

Proof.

将 $A_{TM}$ 归约到 $P$

思路为如果 $P$ non-trivial 则一定存在 $<M_{L}> \in P$ ，对于 $A_{TM}$ 的实例 $<M, w>$ ，构造 TM $M^{\prime}$ ，模拟 $M$ 在 $w$ 上的运行，如果 accept，则模拟 $M_{L}$ 在输入 $x$ 上的运行，如果 accept 则 accept，这样 $<M, w> \in A_{TM} \iff L(M^{\prime}) = L(M_{L}) \iff <M^{\prime}> \in P$

$P$ 如果 trivial 则证明过程可以推出矛盾，e.g. $P$ 包含所有 TM，则

$$
<M, w>\notin A_{TM} \iff L(M^{\prime}) = \varnothing \iff M^{\prime} \in P
$$

Q.E.D.

以下 properties 都是 undecidable 的

* 一个 language 是否为空
* 一个 language 是否有限
* 一个 language 是否是 regular language
* 一个 language 是否是 CFL

## Complexity

Intractable Problems: problems that cannot to be solvable in **polynomial** time

### Time Complexity

在现实问题中，时间和空间的资源都是有限的，因此需要分析一个 algorithm 所需求的资源

在分析时一般进行 worst-case analysis，一个算法需要的资源是 input string 的长度的函数，而其值是对所有给定长度的 input，算法需要的**最大**资源数

一个 TM $M$ 的 time complexity 是一个函数 $f: \mathbb{N} \to \mathbb{N}$ ，$f(n)$ 是 $M$ 对于任意长度为 $n$ 的 input string，所需要运行的最多的步数

在考虑 time complexity 时，通常不考虑精确的步数，且只考虑在输入规模很大的情况下的复杂度。具体而言，使用 asymptotic notation (big-Oh notation)，只关注 $f$ 的最高项且忽略其常系数

> 对于函数 $f, g: \mathbb{N} \to \mathbb{R}^{+}$ ，如果存在正整数 $c, n_{0}$ 满足对任意 $n \geqslant n_{0}$
>
> $$
> f(n) \leqslant cg(n)
> $$
>
> 则称 $f(n) = O(g(n))$

具体含义是规模较大的时候 $f$ 的增长不会超过 $g$

根据 time complexity 即可定义 time complexity class

$$
\mathrm{TEXT}(t(n)) = \{L:\text{there exists a TM }M \text{ that decides } L \text{ in time } O(t(n)) \}
$$

这里的 TM 指的是 single-tape TM，而对于 multi-tape TM 来说，对于任意 $t(n) \geqslant n$ ，任何 $t(n)$ 的 multi-tape TM 都有一个等价的 $O(t(n)^{2})$  的 single-tape TM

### $\mathcal{P} \text{ and } \mathcal{NP}$

$\mathcal{P}$ 是所有能在**确定性**单带 TM 上以**多项式时间**判定的语言的集合

$$
\mathcal{P} = \bigcup_{k \geqslant 1}\mathrm{TIME}(n^{k})
$$

$\mathcal{NP}$ 是所有能在**非确定性**单带 TM 上以**多项式时间**判定的语言的集合

如果定义

$$
\mathrm{NTIME}(t(n)) = \{L:\text{there exists a NTM }M \text{ that decides } L \text{ in time } O(t(n)) \}
$$

则可以定义

$$
\mathcal{NP} =\bigcup_{k \geqslant 1}\mathrm{NTIME}(n^{k})
$$

对于 $\mathcal{NP}$ 中的语言来说，有一个很重要的性质

$$
L \in \mathcal{NP} \iff L = \{x:\exists y, |y| \leqslant |x|^{k}, <x, y> \in R \}
$$

其中 $R \in \mathcal{P}$

Proof.

$\Leftarrow$ 可以构造一个 NTM 判定 $L$，首先用 $|x|^{k}$ 步去猜测一个 $y$ ，然后用 $R$ 判定 $<x, y>$

$\Rightarrow$ 假设 $L$ 被一个 $n^{k}$ 的 NTM $M$ 判定，则定义 $R$ 是所有满足“$y$ 是 $M$ 接受 $x$ 的一个 accepting computation history” 的 $<x, y>$ ，则如果 $M$ accept $x$ ，一定存在一个满足条件的 $y$ ，且 $|y| \leqslant |x|^{k}$ （因为 $M$ 是一个 $n^{k}$ 的 NTM），且 $<x, y> \in R$

Q.E.D.

$y$ 被称为 certificate，即对于一个 $\mathcal{NP}$ 问题来说，验证其解是一个 $\mathcal{P}$ 问题。

E.g. 最大团问题被定义为

$$
CLIQUE = \{<G, k>\}
$$

满足图 $G$ 中存在大小为 $k$ 的团，这是一个 $\mathcal{NP}$ 问题，但是

$$
R = \{<<G, k>, S>\}
$$

满足 $S$ 是 $G$ 中顶点的一个子集且 $S$ 是大小为 $k$ 的团，这是一个 $\mathcal{P}$ 问题，其中 $S$ 就扮演了 certificate 的角色

$\mathcal{NP}$ 不是一个有实践意义的计算模型，因为现实生活中的计算不是非确定性的，但是 $\mathcal{NP}$ 中语言的性质说明了这类问题的一个特征，即可以找到问题的一个实例并且可以有效地测试其是否是一个满足要求的解

如果定义

$$
\mathcal{EXP} =\bigcup_{k \geqslant 1}\mathrm{TIME}(2^{n^{k}})
$$

则各个 time complexity class 之间的关系为

$$
\mathcal{P} \subseteq \mathcal{NP} \subseteq \mathcal{EXP}
$$

而这些语言都是 decidable languages 的子集

### Polynomial-Time Reductions

reduction 是一个 computable function $f$ ，而如果 $f$ 是一个 poly-time computable function，则称这个 reduction 是 poly-time reduction，记为

$$
A \leqslant_{p}B
$$

poly-time computable: 对于函数 $f$，对于 $g(n) = n^{O(1)}$，存在一个 $g(n)$ TM $M_{f}$ 满足对任意输入 $w$，$M_{f}$ 都会 halt 且在 tape 上留下 $f(w)$

同样的，这个 reduction 隐含的意义为 $B$ 至少和 $A$ 一样难

则有，如果 $A \leqslant_{p} B, B \in \mathcal{P}$，则 $A \in \mathcal{P}$ 

Proof.

可以在 poly-time 将 $A$ 的实例 $w$ 转换为 $B$ 的实例 $f(w)$，且可以在 poly-time 使用 $B$ 的 decider 判定 $f(w)$ （由于 poly-time reduction，$|f(w)| \leqslant |w|^{k}$），由于多项式运算的封闭性，总体的时间仍是 poly-time 的

Q.E.D.

### $\mathcal{NP}\text{-complete}$

称一个语言 $L$ 是 $\mathcal{NP}$-complete ，如果其满足

* $L \in \mathcal{NP}$
* $\forall L^{\prime} \in \mathcal{NP}, L^{\prime} \leqslant_{p} L$

如果仅满足第二条约束则称 $L$ 是 $\mathcal{NP}$-hard

证明一个语言 $L$ 属于 $\mathcal{NPC}$ 可以分为两步

1. 证明 $L \in \mathcal{NP}$
2. 对于已知的 $M \in \mathcal{NPC}$ ，有 $M \leqslant_{p} L$ （根据 poly-time reduction 的传递性可得其满足第二条约束）

对于 $\mathcal{NPC}$ 有如下结论

如果 $B \in \mathcal{NPC}$ 且 $B \in \mathcal{P}$ ，则 $\mathcal{P} = \mathcal{NP}$

Proof.

任取 $L \in \mathcal{NP}$ ，有 $L \leqslant_{p} B$ ，而由于之前的结论，$B \in \mathcal{P}, L \in \mathcal{P}$ ，则 $\mathcal{P} = \mathcal{NP}$

Q.E.D.

#### SAT problem

SAT 指的是一个布尔表达式是否为 satisfiable 的问题，这个问题是一个 $\mathcal{NPC}$ 问题

一个布尔表达式是 satisfiable 的，说明存在一个对其中 variable 的 assignment 使得整个表达式为真

其证明的思路分为两部分

* SAT 是 $\mathcal{NP}$ 问题：只需要构造一个 NTM 猜测并验证 assignment
* $\forall A \in \mathcal{NP}, A \leqslant_{p} SAT$ ：令 $M$ 为判定 $A$ 的 $n^{k}$ NTM，则对于输入 $w$ ，可以构造一个布尔表达式 $\varphi_{M, w}$ ，满足 $\varphi_{M, w} \text{ is satisfiable } \iff w \in L(M) $，具体构造过程见课本 10.2.3 节

3SAT 是 SAT 问题的一个特例，即一个 3-cnf 的布尔表达式是否为 satisfiable

3SAT 也是 $\mathcal{NPC}$ 问题

#### More $\mathcal{NPC}$ problems

图中的最大独立集（independent set, IS）：独立集 $I$ 是一个点集，满足其中任意两点都没有边相连

图中的最大团（clique）：团 $S$ 是一个点集满足其中任意两点都有边相连

图中的最小顶点覆盖（vertex cover, VC）：覆盖集 $C$ 是一个点集，满足对任意边 $e$，其至少有一个顶点在 $C$ 中

图中的 Hamilton 回路：存在一个环经过每个顶点且仅经过一次，类似的问题还有 TSP 问题

子集和问题（subset sum）：对于一个集合 $N$ 存在一个子集 $S$ 满足 $S$ 中的数的和等于给定值 $k$