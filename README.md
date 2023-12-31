### 4.1 数据集与评价指标

**现有STS基准数据集：** 我们主要在几个广泛采用的STS数据集上评估我们的模型，包括：MRPC、QQP、QNLI[^3]、STS 2012-2016[^5]、SICK-R[^5]和STS-B[^5]。这些数据集主要由短文本组成，但实际应用场景通常涉及长文档。因此，我们引入了一个名为GitHub Issues Similarity Dataset的新的长文本数据集，以全面评估STS任务。

**GitHub Issues Similarity Dataset：** 我们观察到GitHub上存在许多重复的问题。通常，开源组织的维护者倾向于以“closing as a duplicate of #id”的注释方式关闭这些重复的问题。因此，这些重复问题本质上是STS任务的信息源。值得注意的是，大多数问题包含大量代码，因此文本较长。为了编制这个数据集，我们使用GitHub API[^4]从55个热门开源项目中提取了重复的问题。重复的问题被用作正例样本，而其余问题被视为负例样本。表1展示了GitHub Issues Similarity Dataset的统计信息，图3显示了一个小提琴图，展示了标记级别文本长度分布。可视化结果显示存在大量长文本。具体来说，训练、验证和测试集中文本长度超过512的比例分别为61.03%、60.85%和60.50%。

**评价指标：** 为了确保公平比较，我们遵循以往的研究，并使用Spearman相关系数进行评估。我们使用SentEval[^5]计算Spearman相关系数，并在“all”设置下报告结果，这与基线方法保持一致。

| 数据集                    | 正例数   | 负例数   | 总数    |
|---------------------------|---------|---------|---------|
| 训练集                    | 347,681 | 343,715 | 691,396 |
| 验证集                    | 17,114  | 16,675  | 33,789  |
| 测试集                    | 15,000  | 15,000  | 30,000  |

**图表 1：GitHub Issues Similarity Dataset统计信息**

* 图表1显示了GitHub Issues Similarity Dataset的统计信息，包括训练集、验证集和测试集中的正例和负例数量。

**图 3：标记级别文本长度分布**

* 图3展示了GitHub Issues Similarity Dataset中标记级别文本长度的分布，显示了大量的长文本示例。

**评价指标：**

为了评估模型性能，我们采用Spearman相关系数作为评价指标。我们使用SentEval[^5]库计算Spearman相关系数，并在“all”设置下报告结果，以确保与基线方法的一致性比较。

以上是有关"使用微调进行俄语新闻排序"论文中的数据集与评价指标部分的详细信息。这些数据集和评价指标对于评估模型性能和进行比较分析非常关键。
### 4.2 实验设计细节

在本论文中，我们使用预训练的未分大小写BERT基础模型（110M参数）作为骨干模型。为了进行公平比较，所有基于BERT的基线模型也采用了这一设置。我们将余弦目标和批内负目标的τ值设置为0.05，这基于先前的研究成果。此外，我们通过网格搜索确定了角度目标的τ值为1.0。

**表格 1：实验设计参数**

| 参数               | 值      |
|--------------------|---------|
| BERT模型          | BERT base（未分大小写，110M参数） |
| 余弦目标τ值         | 0.05    |
| 批内负目标τ值       | 0.05    |
| 角度目标τ值         | 1.0     |

以上是有关"使用微调进行俄语新闻排序"论文中实验设计细节部分的详细信息。这些参数设置对于确保实验的准确性和可重复性非常关键。
## 4.3 主要结果

在本部分中，我们首先介绍基线模型，然后介绍迁移STS任务的结果，接着介绍非迁移STS任务的结果，最后进行总结。

### 基线模型

我们将我们提出的模型与广泛使用的基线模型进行比较，包括无监督和监督模型。无监督模型包括：

- 平均GloVe（Pennington等，2014）
- BERT-flow（Li等，2020）
- BERT-whitening（Su等，2021）
- 具有avg/max/last pooling的LLaMA2（Touvron等，2023b）
- 对比学习模型，包括IS-BERT（Zhang等，2020）、CT-BERT（Carlsson等，2020）、SimCSE（Gao等，2021）、ConSERT（Yan等，2021）和DiffCSE（Chuang等，2022）。

选择的监督模型包括：

- InferSent（Conneau等，2017）
- USE（Cer等，2018）
- SBERT（Reimers＆Gurevych，2019）
- CoSENT（Su，2022）
- SLLaMA（Sentence-LLaMA）
- SimCSE和ConSERT的监督版本。

### 迁移STS任务

为了进行公平比较，我们使用NLI数据集MNLI（Williams等，2018）和SNLI（Bowman等，2015）对AnglE进行训练，然后将其迁移用于评估七个STS基准数据集。评估结果如表2所示。很明显，AnglE-BERT和AnglE-LLaMA始终优于基线模型，在平均分数上分别获得了0.80%和0.72%的提高，超过了以前的SOTA SimCSE-BERT和SimCSE-LLaMA。

#### 表2：STS任务上的文本嵌入性能

| 模型                | STS12 | STS13 | STS14 | STS15 | STS16 | STS-B | SICK-R | 平均  |
|--------------------|-------|-------|-------|-------|-------|-------|--------|-------|
| 无监督模型         |       |       |       |       |       |       |        |       |
| GloVe（avg.）        | 55.14 | 70.66 | 59.73 | 68.25 | 63.66 | 58.02 | 53.76  | 61.32 |
| BERT-flow           | 58.40 | 67.10 | 60.85 | 75.16 | 71.22 | 68.66 | 64.47  | 66.55 |
| BERT-whitening      | 57.83 | 66.90 | 60.90 | 75.08 | 71.31 | 68.24 | 63.73  | 66.28 |
| IS-BERT             | 56.77 | 69.24 | 61.21 | 75.23 | 70.16 | 69.21 | 64.25  | 66.58 |
| CT-BERT             | 61.63 | 76.80 | 68.47 | 77.50 | 76.48 | 74.31 | 69.19  | 72.05 |
| ConSERT-BERT        | 64.64 | 78.49 | 69.07 | 79.72 | 75.95 | 73.97 | 67.31  | 72.74 |
| DiffCSE-BERT        | 72.28 | 84.43 | 76.47 | 83.90 | 80.54 | 80.59 | 71.23  | 78.49 |
| SimCSE-BERT         | 68.40 | 82.41 | 74.38 | 80.91 | 78.56 | 76.85 | 72.23  | 76.25 |
| LLaMA2-7B           | 50.66 | 73.32 | 62.76 | 67.00 | 70.98 | 63.28 | 67.40  | 65.06 |
| 监督模型             |       |       |       |       |       |       |        |       |
| InferSent-GloVe     | 52.86 | 66.75 | 62.15 | 72.77 | 66.87 | 68.03 | 65.65  | 65.01 |
| USE                 | 64.49 | 67.80 | 64.61 | 76.83 | 73.18 | 74.92 | 76.69  | 71.22 |
| ConSERT-BERT        | 74.07 | 83.93 | 77.05 | 83.66 | 78.76 | 81.36 | 76.77  | 79.37 |
| CoSENT-BERT         | 71.35 | 77.52 | 75.05 | 79.68 | 76.05 | 78.99 | 71.19  | 75.69 |
| SBERT               | 70.97 | 76.53 | 73.19 | 79.09 | 74.30 | 77.03 | 72.91  | 74.89 |
| SimCSE-BERT         | 75.30 | 84.67 | 80.19 | 85.40 | 80.82 | 84.25 | 80.39  | 81.57 |
| SimCSE-LLaMA2-7B    | 78.39 | 89.95 | 84.80 | 88.50 | 86.04 | 87.86 | 81.11  | 85.24 |
| AnglE-BERT          | 75.09 | 85.56 | 80.66 | 86.44 | 82.47 | 85.16 | 81.23  | 82.37 |
| AnglE-LLaMA2-7B     | 79.00 | 90.56 | 85.79 | 89.43 | 87.00 | 88.97 | 80.94  | 85.96 |

### 非迁移STS任务

为了提供全面的分析，我们还在非迁移设置中评估了

基线模型的性能。我们在训练集上对基线模型进行训练，然后在测试集或验证集上进行评估。我们将对比具有对比学习和监督学习的两种典型模型，SimCSE和SBERT。非迁移STS任务的结果列在表3中。

#### 表3：STS任务的结果

| 模型        | MRPC  | STS-B | QQP   | QNLI  | GitHub Issues | 平均  |
|------------|-------|-------|-------|-------|---------------|-------|
| SimCSE-BERT| 48.13 | 76.27 | 65.84 | 33.00 | 60.38         | 56.72 |
| SBERT      | 46.19 | 84.67 | 73.80 | 65.98 | 69.50         | 68.03 |
| AnglE-RAN  | 58.70 | 80.23 | 74.87 | 63.04 | 71.25         | 69.62 |
| AnglE-BERT | 62.20 | 86.26 | 76.54 | 72.19 | 70.55         | 73.55 |

这是关于"使用微调进行俄语新闻排序"论文中实验设计结果表格部分的详细信息。这些结果表格提供了模型性能的具体数据，以便读者能够清晰地了解实验结果。
## 4.4 消融实验

为了更深入地了解AnglE，我们进行了一项消融实验，研究了不同目标及其效果。表4中的结果表明，AnglE在所有三个目标下表现出了改进的性能。特别地，我们观察到，在没有角度目标的情况下，AnglE的性能下降更大，而没有批内负（ibn）目标的情况下下降较小。这表明角度优化对于改善文本嵌入比ibn更为重要。此外，我们发现仅使用角度目标的性能接近仅使用余弦目标的性能，这证明了角度优化的有效性。我们还评估了五种不同的池化策略，发现“cls”策略表现最佳。最后，我们比较了带有/不带有相同句子对（ISP）检测的ibn，并发现没有ISP检测的ibn性能下降约0.18%。这表明具有ISP检测的ibn是有效的。

#### 表4：AnglE的消融实验

| 模型              | Spearman相关性 |
|--------------------|-----------------|
| AnglE-BERT-all     | 86.26           |
| - 无ibn           | 86.00           |
| - 无角度         | 85.30           |
| 仅余弦           | 85.28           |
| 仅ibn             | 72.48           |
| 仅角度           | 85.15           |

### 模型比较

| 模型           | Spearman相关性 |
|-----------------|-----------------|
| 无监督模型     |                 |
| SimCSE-BERT     | 76.85           |
| ConSERT-BERT    | 73.97           |
| DiffCSE-BERT    | 80.59           |
| 监督模型       |                 |
| AnglE-BERT + ChatGPT  | 81.52 |
| AnglE-BERT + LLaMA    | 79.29 |
| AnglE-BERT + ChatGLM  | 81.11 |
| AnglE-BERT + Ensemble | 82.01 |

这是有关"使用微调进行俄语新闻排序"论文中的消融实验部分的详细信息。消融实验有助于分析模型的不同组成部分对性能的影响，以更全面地理解模型的表现。
## 4.5 讨论与分析

### LLM-监督学习的讨论

AnglE作为一个监督学习模型，必须在带标签的数据上进行训练。然而，在现实应用中，领域监督数据的有限可用性构成了一个挑战。为了解决这个问题，我们提出了LLM-监督学习。这种方法将LLMs作为数据标注者，为AnglE训练标记伪监督数据。图4概述了LLM-监督学习所涉及的过程。

在本研究中，我们比较了LLM-监督的AnglE和无监督对比学习模型。为了模拟领域应用，我们从STS-B训练集中提取了所有“sentence1”文本，并采用LLM-监督学习来训练AnglE模型。表5显示了在STS-B测试集上的结果。从结果中可以明显看出，LLM-监督的AnglE表现比无监督对比基线好，而LLMs的集成表现最佳。这一证据表明了LLM-监督学习的有效性，表明它可以缓解领域监督数据稀缺问题。

### 文本检索的讨论

我们还通过对flickr30k数据集（Young等人，2014）的测试拆分进行实验，评估了文本检索任务的性能。该数据集包含每张照片的五个标题文本，这些文本相互相似。我们使用第一个标题文本向量来使用faiss检索相似的前5个句子。

AnglE、SimCSE（监督）和SBERT的严格准确性分别为12.9%、10.4%和5.2%。这一证据表明了在检索任务中使用AnglE的有效性。

### 转移任务的讨论

此外，我们评估了文本嵌入在转移任务中的性能。具体而言，我们的方法涉及在STS任务上训练文本嵌入，然后将其转移到其他七种任务。值得注意的是，AnglE在基线模型上表现出色，分别比DiffCSE和SimCSE提高了显著的4.34%和4.48%。这些结果表明，AnglE可以生成更好的嵌入，有效提高各种任务的性能。有关实验的更详细描述可以在A.2节中找到。

### 文本嵌入分布的分析

图5a展示了STS-B测试集中句子对之间余弦相似度的密度图，提供了文本嵌入质量的直观可视化。图5b显示了相同句子对的金标分数。通过分析余弦相似度的整体密度，我们发现AnglE的分布更接近金标分布，而不像SimCSE（监督）和SBERT。

图5b呈现了0-1范围内的一个峰值；然而，只有AnglE在图5a中显示了该范围内的明显峰值。此外，图5b中4-5范围内的峰值要高于4.8范围内的峰值，只有AnglE在图5a中恰当地展示了这一特征。值得注意的是，图5b中的0-1范围和4-5范围代表了余弦函数的两个饱和区域。这一证据表明，AnglE可以减轻饱和区域的负面影响。

总之，我们可以自信地断言，AnglE产生了更好的文本嵌入，其余弦相似度密度更接近实际分布，而不像基线模型。