# 论文精读调研

> 调研论文，并撰写下列内容
> 调研人：王伟柳

## 论文名称

名称：Learning to Break the Loop: Analyzing and Mitigating Repetitions for Neural Text Generation

链接：https://papers.nips.cc/paper_files/paper/2022/file/148c0aeea1c5da82f4fa86a09d4190da-Paper-Conference.pdf

## 基础信息

| 工作类别（攻击/可解释性分析/防御）       | 具体信息 |
| ---------------------------------------- | -------- |
| 模态（llm/推理llm/多模态）                |  llm      |
| 时间（年份，arxiv文章需要月份，如25.11）   |  2022     |
| 刊物                                     | NeurIPS     |
| 首作者                                   | Jin Xu     |
| 首作单位                                 | Tsinghua University     |
| 研究目标（输出增长/循环输出）              | 为什么大语言模型会生成连续的句子级重复，并给出解决方法    |
| 一句话方法概括                            |  修改loss函数，添加对重复token的惩罚     |

## 方法概述

> 1. 自己阅读文章，不要使用ai总结，有看不懂的地方可以问模型，但是不能依靠模型阅读。
> 2. 方法概述不需要过于详细的形式化公式，使用语言表述，以【研究目标-研究手段】为线路进行总结。其中研究手段可以稍作展开，分点按步骤进行撰写。

### 重复输出
#### 研究目标
现在大模型（GPT2、BART）使用贪婪解码算法，存在重复输出句子的现象（get stuck in sentence-level loops），作者希望调查清楚模型生成连续的句子级重复的背后原因。
#### 研究手段
研究重复token的概率与其在上下文中先前重复之前的关系
具体做法：作者将一个*正常的句子*和一个由*随机词汇构成的句子*分别重复多次输入模型，观察随着重复次数的增加，被重复词的概率与当前预测的最大概率的变化情况。x轴是重复次数，y轴是作者设计的三个指标。
### DITTO微调
#### 研究目标
提出一个简单并且有效的方法（training-based）解决前文提到的现象。
#### 研究手段
DITTO 训练方法：首先，作者构造了一个伪重复数据集（从训练语料库中随机挑选句子，将句子重复N+1次，直到达到模型的最长输入长度）。然后定义了一个重复惩罚损失函数，在微调的时候搭配构造的数据集使用。损失函数的形式：$-\log(1 - X)$，其中 $X = P_{当前token} + \lambda \cdot P_{上次相同token}$。
作者首先使用标准的极大似然估计（MLE）在 Wikitext-103 基准数据集上训练模型，然后使用 DITTO 训练方法对其进行10k步的微调。
## 实验效果

> 概括介绍实验结果，主要关注作者采取的实验指标、baseline和实际效果。

### 重复输出
三个指标：
*句子平均token概率*（TP，计算重复第n遍的句子中的每一个token的平均生成概率）
*token概率提升比例*（IP，计算与重复0遍的初始句子的概率相比，重复n遍后句子中有多少比例的token概率增加了）
*胜者比例*（WR，它要求两个条件：token的概率变高了且它是当前概率最高的词）

实验发现，随着重复次数的增加，三个指标均单调上升，并最终趋于一个稳定的值。
结论：1、拥有较高似然度的句子容易被重复；2、一旦模型重复了几次先前生成的句子，它就会因为自我强化效应陷入句子重复循环；3、基于先前生成的句子，模型更有可能生成重复的句子。这是因为模型对自己生成的历史语境充满自信，并且总是试图从这些语境中寻找线索来指导当前的生成。
### DITTO微调
#### 实验一（开放式生成任务，open-ended）
三个指标：*MAUVE*、*Perplexity and Accuracy*、*Repetition*（作者设计的衡量句子重复度的指标）

与training-based methods进行对比，而不是decoding-based methods，因为这种方法可以直接应用于经过训练的模型。
baseline：经过MLE训练的基准模型、UL-token、UL-token+seq、SG
实际效果：其他算法通过牺牲ppl的方式来减少重复，而DITTO不仅能缓解重复问题，还能帮助模型提升语言建模能力和生成质量。在添加了Top-p和Top-k随机采样策略后，DITTO依旧表现最好。
#### 实验二（定向生成任务，directed）
三个指标：*ROUGEE-1*、*ROUGEE-2*、*ROUGEE-L*
baseline：BART-large（SOTA）
实际效果：使用DITTO微调后的模型在三个指标上均优于其他方法和模型
疑惑：作者构造伪重复数据集的方式，给定一篇文档及其摘要，首先从摘要中随机抽取一个句子，然后重复该句子直到达到解码器模型的最大摘要长度，同时保持输入文档不变。这么做，模型不是相当于提前看过答案的一部分了吗？请问该文档最后参与指标结果生成吗？
## 不足
在开放式生成任务和定向生成任务中，作者使用了不同的参数来微调训练模型，体现了该方法的通用性不够强
## 引用论文

> 关注论文的`Introduction`、`related work`和`experiment setup`部分，追踪查找作者列举的**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
| Neural text generation with unlikelihood training         |    ICLR      |   2020   | llm 重复输出+训练                                                  |
| Learning to use novel tokens for neural text generation         | ICML         |2021      |llm 重复输出+训练                                                   |

## 被引论文

> 在 https://scholar.google.com/ 和 上进行当前论文搜索，以此查看所有引用本论文的研究，提取其中**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
| Sled: Self logits evolution decoding for improving factuality in large language models| NeurIPS|2024|llm 输出可靠性|
|Repetition In Repetition Out: Towards Understanding Neural Text Degeneration from the Data Perspective          |    NeurIPS      |   2023   |  从数据视角解决llm重复输出  |
|From Self-Attention to Markov Models:Unveiling the Dynamics of Generative Transformers|ICML|2024|llm 重复输出的可解释性|
|Rethinking Repetition Problems of LLMs in Code Generation|ACL|2025|通过解码方式解决llm代码生成的结构性重复问题|
|Planner: Generating diversified paragraph via latent language diffusion model|NeurIPS|2023|提议出一种新的模型来解决自回归llm重复且低质量的输出问题|