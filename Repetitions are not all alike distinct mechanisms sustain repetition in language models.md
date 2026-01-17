# 论文精读调研

> 调研论文，并撰写下列内容
> 调研人：王伟柳

## 论文名称

名称：Repetitions are not all alike distinct mechanisms sustain repetition in language models

链接：https://aclanthology.org/2025.naacl-short.41.pdf

## 基础信息

| 工作类别（攻击/可解释性分析/防御）       | 具体信息 |
| ---------------------------------------- | -------- |
| 模态（llm/推理llm/多模态）               |llm          |
| 时间（年份，arxiv文章需要月份，如25.11） |2025          |
| 刊物                                     |NACCL          |
| 首作者                                   |Matéo Mahaut          |
| 首作单位                                 |Universitat Pompeu Fabra          |
| 研究目标（输出增长/循环输出）            |循环输出          |
| 一句话方法概括                           |通过机械可解释性方法来分析自然重复与ICL重复的内在机理          |

## 方法概述

> 1. 自己阅读文章，不要使用ai总结，有看不懂的地方可以问模型，但是不能依靠模型阅读。
> 2. 方法概述不需要过于详细的形式化公式，使用语言表述，以【研究目标-研究手段】为线路进行总结。其中研究手段可以稍作展开，分点按步骤进行撰写。
### 研究目标
探究llm中重复现象中的背后机理，具体而言验证行为上相似的重复是否源于相同的底层机制，区分自然重复和ICL重复。

### 研究手段
数据集构建：从Pile数据集的Minipile分区中随机采样1000个句子，将每个句子截断为32个token。然后将每个句子（32token）输入Pythia 1.4B模型，生成长度1000token的句子。选择其中能够产生重复的句子（基于贪婪解码）作为natural prompt；将没有产生重复的句子手动重复至少一次作为 ICL prompt。
模型：Pythia 1.4B

观察不同训练检查点下的llm的各项指标。具体而言：
1、分析模型的发展轨迹，将上述构造的提示词输入到模型的不同阶段，分析其重复输出的占比；
2、采用对比方法，计算每个注意力头对复读token和打断token的logits 差值，以此识别是否存在专门负责重复的注意力头；
3、将输入prompt中的token分为实词和结构词，分析重复发生时，模型主要关注哪类token；
4、计算生成重复序列时的熵。


## 实验效果
> 概括介绍实验结果，主要关注作者采取的实验指标、baseline和实际效果。
> 
实验指标：
重复比例、对比度、注意力比率、熵

1、自然重复在训练第1步即大量存在。ICL重复在训练早期不存在，随着训练步数增加，逐渐涌现（emerge）；
2、ICL重复依赖于特定几个注意力头，这些头随训练逐渐specialize，表现出极强的正向对比度（促进重复）。自然重复的注意力头对比度皆接近0；
3、自然重复在复读时过度关注低信息量Token（newline，换行符），这暗示了当模型无法提取有效语义时，注意力机制发生了Fallback。ICL重复模型关注实词，利用上下文信息进行复制；
4、ICL重复的熵值下降更快，自然重复熵较高且分布宽，存在大量离群值，表明模型在“不确定”的状态下陷入死循环。


## 引用论文

> 关注论文的`Introduction`、`related work`和`experiment setup`部分，追踪查找作者列举的**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|From Loops to Oops: Fallback Behaviors of Language Models Under Uncertainty          |arxiv          |2024.7      |LLM 失效（揭示模型在不确定性下产生重复作为一种回退策略）                                                   |
|A Theoretical Analysis of the Repetition Problem in Text Generation          |AAAI          |2021      |LLM 重复生成可解释性性                                                   |
|In-context Learning and Induction Heads|arxv|2022.9|LLM 可解释性 + 机制发现（发现诱导头是模型进行模式复制和上下文学习的基础）|

## 被引论文
这篇文章一共就两篇被引

> 在 https://scholar.google.com/ 和 上进行当前论文搜索，以此查看所有引用本论文的研究，提取其中**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|Word Salad Chopper: Reasoning Models Waste A Ton Of Decoding Budget On Useless Repetitions, Self-Knowingly          |EMNLP           |2025      | LLM推理效率优化 + 可解解释性                                                  |
