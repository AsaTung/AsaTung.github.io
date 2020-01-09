---
mathjax: true
title: 词向量与word2vec
data: 2019.8.5
tags: NLP
---

# word2vec

顾名思义，词向量是用来表示词的向量，通常也被认为是词的特征向量。近年来，词向量已逐渐成为自然语言处理的基础知识。

在自然语言处理（NLP）的世界里，词是最小的单元，由词组成句，句组成段落、篇章、文档，所以处理NLP问题，必须**从词入手**。在自然语言中词是符号形式的，要对词进行处理，就必须把它们转换成数值形式，或者说，把词嵌入到一个数学空间里，这种嵌入方式，就是**词嵌入**（**word embedding**)。word2vec，就是word embedding的一种。

word2vec工具是2013年谷歌团队开发的。主要包含两个model：**skip-gram**（跳字模型）和**CBOW**（continuous bag of words，连续词袋模型），以及两种高效的训练算法：**负采样**（negative sampling）和**层序softmax**（hierarchical softmax）。值得一提的是，word2vec词向量可以较好地表达不同词之间的相似和类比关系。

# 词向量类型
## one-hot representation

传统的基于规则或基于统计的自然语义处理方法将单词看作一个原子符号，被称作one-hot representation。one-hot representation把每个词表示为一个长向量。这个向量的维度是词表的长度，向量中只有一个值为1，其余值为0，这个向量就代表了当前的词。 例如：苹果 [0，0，0，1，0，0，0，0，0，……] 

one-hot representation相当于给每个词分配一个id，这就导致这种表示方式不能展示词与词之间的关系。另外，one-hot representation将会导致特征空间非常大，这可能会导致维度爆炸。

## distribution representation

distribution representation（分布式表示）将词表示成一个定长的连续的稠密向量。它能使词之间存在相似关系，使词之间存在“距离”概念。

# 语言模型（Language model)
## n元语法（n-gram）
一个语言模型通常构建为字符串$s$的概率分布$p(s)$，这里$p(s)$试图反应的是字符串$s$作为一个句子出现的概率。

对于一个由$l$个基元（“基元”可以为字、词或短语等，为表述方便，用“词”来通指）构成的句子$s=w_1w_2\cdots w_l$，其概率计算公式可以表示为：

$$
\begin{equation}\begin{split}
p(s)&=p(w_1)p(w_2 | w_1)p(w_3 | w_1w_2)\cdots p(w_l | w_1...w_{l-1})\\
    &=\prod_{i=1}^lp(w_i | w_1\cdots w_{i-1})
\end{split}\end{equation}
$$

式$（1）$中，产生第$i(1≤i≤l)$个词的概率是由已经产生的$i-1$个词$w_1\cdots w_{i-1}$决定的。一般地，我们把前$i-1$个词$w_1\cdots w_{i-1}$称为第$i$个词的“历史（history）”。在这种计算方法中，随着历史长度的增加，不同的历史数目按指数级增长。如果历史的长度为$i-1$，那么，就有$L^{i-1}$种不同的历史（假设$L$为词汇集的大小），而我们必须考虑在所有$L^{i-1}$种不同历史情况下，产生第$i$个词的概率。这样的话，模型中就有$L^i$个自由参数$p(w_i|w_1,w_2,\cdots,w_{i-1})$。假设$L=500$，$i=3$，那么自由参数的数目就是1250亿个。

为了解决以上问题，我们提出**n元语法**（n-gram）的概念，它的含义我们可以简单理解成：**一个词的概率只依赖于它前面的$n-1$个词**。当$n=1$时，即词$w_i$独立于历史时，记作unigram；当$n=2$时，词$w_i$仅与它前面的一个历史词$w_{i-1}$有关，记作bigram；当$n=3$时，词$w_i$仅与它前面的两个历史词$w_{i-1}w_{i-2}$有关，记作trigram。

以二元语法模型为例，根据前面的解释，我们可以近似地认为，一个词的概率只依赖于它前面一个词，那么，
$$
p(s)=\prod_{i=1}^lp(w_i|w_1w_2\cdots w_{i-1}) \approx \prod_{i=1}^lp(w_i|w_{i-1})
$$
为了使得$p(w_i|w_{i-1})$对于$i=1$有意义，我们在句子开头加上一个句首标记<<BOS>BOS>，即假设$w_0$就是<<BOS>BOS>。相应地，在句子末尾加上一个句尾标记<<EOS>EOS>。例如，要计算$p(Mark\ wrote\ a\ book)$，我们可以这样计算：
$$
\begin{equation}\begin{split}
p(Mark\ wrote\ a\ book)=&p(Mark|<BOS>)×p(wrote|Mark)\\
&×p(a|wrote)×p(book|a)×p(<EOS>|book)
\end{split}\end{equation}
$$

为了估计$p(w_i|w_{i-1})$条件概率，可以简单地计算$w_{i-1}w_i$在某一文本中出现的频率，然后归一化。如果用$c(w_{i-1}w_i)$表示二元语法$w_{i-1}w_i$在给定文本中的出现次数，我们可以采用以下的计算公式：
$$
\begin{equation}\begin{split}
p(w_i|w_{i-1})={c(w_{i-1}w_i)\over \sum_{w_i}c(w_{i-1}w_i)}
\end{split}\end{equation}
$$

式$（3）$用于估计$p(w_i|w_{i-1})$的方法称为$p(w_i|w_{i-1})$的**最大似然估计**。




















