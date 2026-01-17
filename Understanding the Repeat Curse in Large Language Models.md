# 论文精读调研

> 调研论文，并撰写下列内容
> 调研人：王伟柳

## 论文名称

名称：Understanding the Repeat Curse in Large Language Models from a Feature Perspective

链接：https://aclanthology.org/2025.findings-acl.406.pdf

## 基础信息

| 工作类别（攻击/可解释性分析/防御）       | 具体信息 |
| ---------------------------------------- | -------- |
| 模态（llm/推理llm/多模态）               |llm          |
| 时间（年份，arxiv文章需要月份，如25.11） |2025          |
| 刊物                                     |ACL|
| 首作者                                   |Junchi Yao|
| 首作单位                                 |Provable Responsible AI and Data Analytics (PRADA) Lab          |
| 研究目标（输出增长/循环输出）            |循环输出          |
| 一句话方法概括                           |抑制LLM中负责重复的特征来缓解大模型的重复生成问题         |

## 方法概述

> 1. 自己阅读文章，不要使用ai总结，有看不懂的地方可以问模型，但是不能依靠模型阅读。
> 2. 方法概述不需要过于详细的形式化公式，使用语言表述，以【研究目标-研究手段】为线路进行总结。其中研究手段可以稍作展开，分点按步骤进行撰写。
### 研究目标
1. 定位LLM中负责重复的layer
2. 定位LLM中负责重复的feature
3. 抑制这些feature对LLM的影响

### 研究手段
1. 将LLM每层残差激活与$l_{diffdirection}$点积（衡量两个向量的相似度），量化每一层对重复结果输出的贡献
2. 在贡献最大和次大的layer上使用预训练的SAE（稀疏自编码机）,将特征引导系数$λ$设置为原始激活水平的 1.5-2倍进行干预，观测生成文本的重复分数（RS）.若$RS≥ρ$，则将该特征判定为重复特征
3. 观察抑制后LLM的RepeatScore、Entropy、Perplexity这三个指标

$l_{diffdirection}$计算方法：带有重复输出的残差流向量减去不带有重复输出的残差流向量，得到的向量是在空间中指向重复方向的向量

## 实验效果

> 概括介绍实验结果，主要关注作者采取的实验指标、baseline和实际效果。

1. 作者构造了一个诱导复读的输入“He hit Jack Jack Jack Jack Jack”。定义重复输出的残差流向量：继续复读，即输出 "Jack"。定义不带有重复输出的残差流向量：正常说话，停止复读。遍历所有层后，作者发现中间层和最后一层的贡献最大
2. 在三个数据集（Academic ShortQA、Natural Questions、Diversity-Challenge-Dataset），三个模型（GPT2-small、Gemma-2-2B、Llama-3.1-8B）进行特征定位实验。发现导致模型复读的特征集中在名字、时间和数学三大语义类别。其中，名字是最容易诱发复读的特征。同时发现模型越强，内部特征越稳定，识别出的重复特征越多且覆盖全部三类；较弱的模型由于数学推理能力不足，未识别出数学相关的重复特征。
3. 采用与点2相同的数据集和模型。和Greedy、Beam、Top-k、Top-p这些采样方法相比。Repeat Score显著下降，Information Entropy显著上升，表明复读消除，文本多样性恢复。Perplexity保持稳定或有所上升，证明该方法在抑制重复特征的同时，未损害模型的正常生成能力。


## 引用论文

> 关注论文的`Introduction`、`related work`和`experiment setup`部分，追踪查找作者列举的**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|A Theoretical Analysis of the Repetition Problem in Text Generation |AAAI          |2021      |LLM 防御：提出重平衡编码方法缓解重复问题|
|          |          |      |                                                   |

## 被引论文

> 在 https://scholar.google.com/ 和 上进行当前论文搜索，以此查看所有引用本论文的研究，提取其中**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|DTS: Enhancing Large Reasoning Models via Decoding Tree Sketching          |arxiv          |2025.11      |推理llm的解码策略：缓解过度思考与重复|
|Word Salad Chopper: Reasoning Models Waste ATonOfDecoding Budget On Useless Repetitions，Self-Knowingly          |emnlp          |2025      |推理llm 检测无效重复+给出解决方法|