# 论文精读调研

> 调研论文，并撰写下列内容
> 调研人：王伟柳

## 论文名称

名称：Repetition In Repetition Out: Towards Understanding
Neural Text Degeneration from the Data Perspective

链接：https://proceedings.neurips.cc/paper_files/paper/2023/file/e6c2e85db1f1039177c4495ccd399ac4-Paper-Conference.pdf

## 基础信息

| 工作类别（攻击/可解释性分析/防御）       | 具体信息 |
| ---------------------------------------- | -------- |
| 模态（llm/推理llm/多模态）               |llm          |
| 时间（年份，arxiv文章需要月份，如25.11） |2023          |
| 刊物                                     |NeurIPS         |
| 首作者                                   |Huayang Li|
| 首作单位                                 |Nara Institute of Science and Technology|
| 研究目标（输出增长/循环输出）                |循环输出          |
| 一句话方法概括                            |作者发现训练数据中的重复是导致生成的文本退化的根本原因，并提出Repetition Dropout方法，在训练时的注意力计算中随机掩盖重复词，从而打破模型对重复模式的依赖|

## 方法概述

> 1. 自己阅读文章，不要使用ai总结，有看不懂的地方可以问模型，但是不能依靠模型阅读。
> 2. 方法概述不需要过于详细的形式化公式，使用语言表述，以【研究目标-研究手段】为线路进行总结。其中研究手段可以稍作展开，分点按步骤进行撰写。

### 研究目标
解决神经语言模型在开放式文本生成中容易陷入重复循环（Degeneration）的问题

### 研究手段
1. 数据重复率相关性分析，选取5个不同领域的文本数据集，将每个数据集的训练样本按rep-2分数进行排序。将排序后的数据切分为6个分片，每个分片包含相同数量的词，但具有不同的重复率比例。在每个分片上独立训练GPT-2模型，并评估其生成文本的重复率，以分析训练数据重复特征与模型生成退化之间的联系。
2. 提出Repetition Dropout。在训练阶段，算法会识别输入序列中出现的重复n-gram；在计算Self-Attention时，利用一个掩码矩阵$M_{rep}$，对于被识别为重复的词，根据预设的丢弃率$p$ ，将其在注意力机制中的分数设为负无穷

## 实验效果
> 概括介绍实验结果，主要关注作者采取的实验指标、baseline和实际效果。
> 

数据集：Wikitext-103，OpenWebText2，FreeLaw

模型：GPT-2，OPT，Llama-2

评价指标：rep-n，rep-w，rep-r，ppl

Baselines：MLE

1. 训练数据的重复率与生成文本的重复率表现出极强的正相关性。llm不仅会模仿重复，还会将数据中的重复率放大超过10倍

2. 显著降低重复率，例如在Wikitext-103上，rep-4指标从MLE的32.64%降至2.14%，且效果优于专门设计的ScaleGrad（4.44%）和UL（22.88%）方法，但ppl有所上升。即使随着模型规模增大或经过指令微调，标准训练的模型仍存在退化问题，而Repetition Dropout依然能有效缓解这一现象



## 引用论文

> 关注论文的`Introduction`、`related work`和`experiment setup`部分，追踪查找作者列举的**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|A Theoretical Analysis of the Repetition Problem in Text Generation|AAAI|2021|llm文本生成重复问题理论分析+优化方法|
|Neural Text Generation With Unlikelihood Training|ICLR|2020|llm文本生成重复问题优化+训练目标函数设计|
|Straight to the Gradient: Learning to Use Novel Tokens for Neural Text Generation|ICML|2021|llm文本生成重复问题优化+梯度缩放训练方法|



## 被引论文

> 在 https://scholar.google.com/ 和 上进行当前论文搜索，以此查看所有引用本论文的研究，提取其中**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|Rethinking Repetition Problems of LLMs in Code Generation|ACL|2025|llm代码生成重复问题+基于语法的解码惩罚方法|
