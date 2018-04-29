# Analyzing python survey 2017 dataset

## 前言

2017 年末，PSF（Python Software Foundation，Python 软件基金会）和 JetBrains 一起进行了一次全球范围内的关于 Python 使用情况的问卷调查，共有来自 153 个国家的 9506 名开发者参与了这次调查，[官方](https://www.jetbrains.com/research/python-developers-survey-2017/)也发布了一份调查报告分析。

这次我从我自己感兴趣的角度来尝试做一些简单的分析，也是一次练手。这个过程断断续续持续了很长时间，中间也是被种种事情打断，现在终于基本完成了，然而还有很多东西可以分析，等以后有时间了再来接着分析。

我个人认为调查报告的分析主要有两点：

- 验证一些常理
- 发现一些现象

本文基本只做到了前者，而且侧重于直接展现分析结果（大多数为图），具体的代码实现见 [analyzing-python-survey-2017.ipynb](https://nbviewer.jupyter.org/github/secsilm/analyzing-python-survey-2017/blob/master/analyzing-python-survey-2017.ipynb)，GitHub 上的内容见 [secsilm/analyzing-python-survey-2017](https://github.com/secsilm/analyzing-python-survey-2017)，包括问卷调查的内容（PDF）和调查结果原始数据（CSV）。

## 数据

我认为拿到数据的第一步就是先看下数据长什么样，这样才能着手分析。先来看下我们的调查结果数据：

![](https://i.imgur.com/MBIZmzA.png)

数据总大小是 9506×162，即 9506 行 162 列，上图是前 5 行，每行代表一个样本，即一个参与调查的开发者。可以看到已经将一道选择题的答案拆分成多列，这一般是用于处理多选题，例如上图中的「What other language(s) do you use?」，而单选题则不用拆分，可以直接写答案，例如上图第一列。

## 问题

分析前，我们需要首先提出一些问题，基于这些问题来作分析。本文提出了以下几个问题：

- 开发者中使用 Python 2 和 Python 3 的情况
- 做数据分析和机器学习的人中分别有多少人使用的是 Python 3？
- 常用框架中使用 Python 2 和 Python 3 的比例
- 做数据分析和机器学习的人常用的框架？
- 团队规模大小和是否使用 Python 3 的关系？
- 开发者年龄和是否使用 Python 3 的关系？
- 使用 Python 3 和 Python 2 的开发者的国别分布？

下面我们依次看下这几个问题。

## 开发者中使用 Python 2 和 Python 3 的情况

这个问题比较好解决，调查问卷中的问题 5 是「Which version of Python do you use the most?」，而且是单选题，那么我们直接那么对这列使用 `value_counts()` 进行处理，然后使用 `plt.pie()` 绘制饼图：

![python version](https://i.imgur.com/VmC9haP.png)

可以看到 Python 开发者中已经有 $\frac{3}{4}$ 的人在使用 Python 3 了。

> Note：2020 年 Python 2 就会停止维护，如果你还在纠结使用 Python 2 还是 Python 3，毋庸置疑 Python 3！

## 做数据分析和机器学习的人中分别有多少人使用的是 Python 3

调查问卷中问题 4 是「What do you use Python for the most?」，这是一道单选题，选项中有「Data analysis」和「Machine learning」两项，因此我们可以直接将这列与「Which version of Python do you use the most?」列进行交叉，就可以得到数据分析和机器学习中使用 Python 3 的比例，而交叉可以使用 `pd.crosstab()` 来做，然后绘制出条形图：

![data analysis and machine learning VS python version](https://i.imgur.com/xq8WUst.png)

可以看到其实相差无几，Python 3 似乎在机器学习中更为流行。数据分析和机器学习中涉及到的字符串处理比较多，而 Python 3 在这方面显然更具有优势。

机器学习尤其是深度学习中最常用的库就是 [TensorFlow](https://www.tensorflow.org/) 了，但是这个库官方只支持 Python 3，可以说这也是机器学习中 Python 3 的比例略微高于数据分析的一个原因。

## 常用框架中使用 Python 2 和 Python 3 的比例

如果我们将开发者常用的框架（库）与所使用的 Python 版本进行交叉，那么将得到各框架中的 Python 2 和 Python 3 使用情况。

在这之前，让我们先来看下框架使用排行（Just for curious 😆）：

![frameworks](https://i.imgur.com/YvMez30.png)

统治 Python 的两大阵营（Web 开发和数据科学）的框架果然占据了前两名 👍

好了回到正题，让我们来看下各框架中的 Python 2 和 Python 3 使用情况：

![frameworks in python version](https://i.imgur.com/nwzwRi3.png)

上图是按照 Python 3 的使用比例由高到低排序的，可以看到排名前五的都是网络和 UI 相关的库，排名第一的 aiohttp Python 2 与 Python 3 的比例甚至达到了 1:9 🙀 由于我本人对这方面不太了解，就不做过多评价了，如有懂得同学可以留言解释下为何网络和 UI 方面的库中 Python 3 的占比如此高。

不过总体上来说，各大框架中 Python 3 的使用比例都已经超过甚至远超 Python 2 👍

## 做数据分析和机器学习的人常用的框架

接着上面，我们可以将框架使用情况与 Python 在数据分析和机器学习方面的应用进行交叉，得到数据分析和机器学习开发者最常用的框架：

![frameworks in data analysis and machine learning](https://i.imgur.com/RvW9Yyp.png)

可以看到数据分析和机器学习开发者所用的框架大体相同，数据分析开发者人数要多于机器学习开发者，但是由于机器学习开发者更常用 Keras、scikit-learn 和 TensorFlow 等框架，反而使用这些框架上的机器学习和数据分析开发者人数基本一致。

乍一看让我有点意外的是 Django 和 Flask 的位置，相对来说这两个用于构建网站的框架排名还是很靠前的，但是仔细一想，我见过很多数据分析和机器学习开发者做一个网站来展示自己的作品，这样一来也不足为奇了。又或者是 Web 开发者兼职数据分析和机器学习？细思极恐……

## 团队规模大小和是否使用 Python 3 的关系

一般来说，团队规模越大，由 Python 2 转型 Python 3 所花费的代价就越大，所以团队规模越大使用 Python 3 的比例可能就会越低，而下图也印证了这一点。

![team scale and python 3](https://i.imgur.com/3Ksk5JQ.png)

但是需要注意的是，有将近 62% 的人是独立开发或者是外部顾问（external consultant）和培训讲师（trainer），也就是说不在一个团队中进行工作。上图就是基于剩下的约 38% 的开发者来绘制的。

## 开发者年龄和是否使用 Python 3 的关系

同上，越年轻使用 Python 3 的比例就越高，毕竟年轻人「情怀」比较少 😆

BUT，物极必反，60 岁以上的开发者使用 Python 3 的比例陡然提升了 10%……难道回光返照？

![age python 3](https://i.imgur.com/HezUSFH.png)

60 岁以上还在 coding 的人应该不多，我们来统计下每个国家内 60 岁以上的开发者人数，然后排名取前十名：

![top10](https://i.imgur.com/fUDmC8a.png)

几乎是美国一家独大，后面的几乎可以忽略不计，所有 153 个国家的 60 岁以上开发者人数为 111，而美国为 56，占到了一半，而这 56 个人中有 41 个人使用的是 Python 3，占比约为 73%，so，上面的疑问也就不是疑问了。

此外，我们来看下开发者人数最多的三个国家（美国、印度和中国）的开发者年龄构成：

![age distribution](https://i.imgur.com/0r7bQR3.png)

结合国内程序圈的情况，大家都想着 30 以后要不转要不升，上图也不足为奇，美国的年龄分布相对来说要好很多。

## 使用 Python 3 和 Python 2 的开发者的国别分布

首先来看下所有开发者的国别分布：

![country counts](https://i.imgur.com/QHllwyy.png)

美国、印度和中国排名前三，上一节的年龄分布图选取的三个国家就是就是根据这个排名取的。

再来看下上图排名前十的国家中的 Python 2 和 Python 3 的使用比例：

![country python version](https://i.imgur.com/rXuzRKi.png)

其实使用比例是差不多的，都是 Python 3 远超 Python 2，这一点也可以用下图佐证：

![use-ratio-of-python3-in-the-world](https://i.imgur.com/IpKZCq3.png)

我们有了国家信息，那么自然而然就会想到直接在地图上显示数据：

![python3-in-the-world](https://i.imgur.com/TAJV1LT.png)
<center><i>在<a href="https://nbviewer.jupyter.org/github/secsilm/analyzing-python-survey-2017/blob/master/analyzing-python-survey-2017.ipynb#%E4%BD%BF%E7%94%A8-Python-3-%E5%92%8C-Python-2-%E7%9A%84%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E5%9B%BD%E5%88%AB%E5%88%86%E5%B8%83%EF%BC%9F">这里</a>查看有更好的交互式体验</i></center>

## 总结

可以看到的趋势是：

- Python 3 已经被大多数人使用，无论是 Web 开发还是数据科学（数据分析和机器学习），无论是美国还是中国还是其他国家
- Web 开发和数据科学是 Python 开发的两大统治者
- 数据分析和机器学习开发者使用的 Python 框架有很大的相似性，只是在 TensorFlow 等机器学习框架上的使用有一定区别
- 年龄和团队规模和使用 Python 3 的比例有一定关系
- 美国的开发者年龄分布要好于中国

总的来说，本文所分析的内容只能说是冰山一角，还有大量内容可以分析，但是时间精力有限，后续可能的话再在此基础上加以分析。

## END