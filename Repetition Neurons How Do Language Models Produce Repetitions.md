# 论文精读调研

> 调研论文，并撰写下列内容
> 调研人：王伟柳

## 论文名称

名称：Repetition Neurons: How Do Language Models Produce Repetitions?

链接：https://aclanthology.org/2025.naacl-short.41.pdf

## 基础信息

| 工作类别（攻击/可解释性分析/防御）       | 具体信息 |
| ---------------------------------------- | -------- |
| 模态（llm/推理llm/多模态）               |llm          |
| 时间（年份，arxiv文章需要月份，如25.11） |2025          |
| 刊物                                     |NAACL          |
| 首作者                                   |Tatsuya Hiraoka          |
| 首作单位                                 |Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)          |
| 研究目标（输出增长/循环输出）            |循环输出          |
| 一句话方法概括                           |通过比较重复现象开始前后的激活值来识别重复神经元          |

## 方法概述

> 1. 自己阅读文章，不要使用ai总结，有看不懂的地方可以问模型，但是不能依靠模型阅读。
> 2. 方法概述不需要过于详细的形式化公式，使用语言表述，以【研究目标-研究手段】为线路进行总结。其中研究手段可以稍作展开，分点按步骤进行撰写。
### 研究目标
1. 定位llm前馈神经网络层中负责重复输出的神经元
2. 证明通过干预这些神经元可以控制llm的重复输出行为

### 研究手段
1. 通过对比重复文本开始前和开始后的神经元激活值差来定位负责重复输出的神经元，差值大于设定值的神经元被认定为重复神经元
2. 抑制（将神经元的激活值暴力置0）重复神经元的激活值；人为给重复神经元的激活值增加1.0

## 实验效果

> 概括介绍实验结果，主要关注作者采取的实验指标、baseline和实际效果。
> 
Model：使用了3个英文预训练模型Gemma-2B、Pythia-2.8B-Deduped、LLaMA-3.2-3B和一个日语预训练模型LLM-jp-3-1.8B。
数据集构建：使用以上四个模型在温度为1.0的情况下随机生成文本，然后定义重复文本的标准：10-gram序列以相等的间隔出现3次。筛选出符合这一标准的1000条文本。
1. 满足条件的神经元大多出现于llm的中间层和和最后一层。作者猜测，模型的中间负责重复模式的检测，最后一层负责重复模式的输出。
2. 指标：人为干预后包含重复的样本数量、模型的困惑度。 通过抑制重复神经元，发现减少约30%的重复样本，且llm的困惑度没有明显上升（baseline：抑制随机神经元，发现llm的重复现象没有下降）。通过增强重复神经元，会导致模型陷入重复（baseline：增强随机神经元，诱发重复的效率低于增强重复神经元，但其困惑度大幅提高）。


## 引用论文

> 关注论文的`Introduction`、`related work`和`experiment setup`部分，追踪查找作者列举的**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|Repetition In Repetition Out: Towards Understanding Neural Text Degeneration from the Data Perspective          |NeurIPS          |2023      |LLM 重复输出的数据视角解释与缓解|
|From loops to oops: Fallback behaviors of language models under uncertainty | NeurIPS          |2024       |LLM回退行为（包括重复输出、幻觉等等）的可解释性                                                   |
|Induction Heads as an Essential Mechanism for Pattern Matching in In-context Learning|NAACL|2025|LLM ICL的可解释性（涉及归纳头对模型重复输出的作用）|
|Mitigating the Language Mismatch and Repetition Issues in LLM-based Machine Translation via Model Editing|emnlp|2024|使用模型编辑技术缓解LLM 翻译的重复输出问题|

## 被引论文

> 在 https://scholar.google.com/ 和 上进行当前论文搜索，以此查看所有引用本论文的研究，提取其中**关于资源消耗和输出长度异常**的相关研究。列出其基本信息

| 论文题目 | 发表刊物 | 时间 | 一句话概括研究领域和方向（例：llm 可解释性+防御） |
| -------- | -------- | ---- | ------------------------------------------------- |
|Understanding and Controlling Repetition Neurons and Induction Heads in In-Context Learning          |arxiv          |2025.7      |LLM 可解释性 + 从神经元角度分析重复生成与ICL之间的关系|
|Repetitions are not all alike: distinct mechanisms sustain repetition in language models          |arxiv          |2025.4      |LLM 可解释性 + 重复生成研究（自然重复 vs ICL 复制）|
|Induction Head Toxicity Mechanistically Explains Repetition Curse in Large Language Models|ACL|2025|LLM 可解释性 + Induction Heads导致的重复机制分析与相关缓解方法|
|In-Context Learning Without Copying|arxiv|2025|LLM 机制可解释性 + Induction Heads（存在复制行为）与抽象ICL能力的关系研究|