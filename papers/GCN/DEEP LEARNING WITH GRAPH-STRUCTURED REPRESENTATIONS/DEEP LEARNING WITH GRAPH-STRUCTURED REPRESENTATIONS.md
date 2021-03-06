

# DEEP LEARNING WITH GRAPH-STRUCTURED REPRESENTATIONS



contributions:

* 提出*图卷积网络GCNs*，用于图结构数据中的节点的半监督分类。GCNs是一种在图上实现参数化信息传递操作的图神经网络，形式为谱图卷积的一阶近似。
* 提出*图自编码器GAEs*，用于无监督学习和将图结构数据的预测连接起来。GAEs利用一个基于图神经网络的编码器和一个解码器，根据一个逐对评分函数来重构图中的连接。进一步提出一个模型的变体，形式为一个概率生成模型，使用变分推理进行训练，命名为变分GAE. GAEs和变分GAEs特别适合缺少节点标签情况的图表达学习。
* 提出*关系型GCNs*，在有向的、关系型的多变的图上拓展了GCN模型。关系型GCNs适用于对关系型数据进行建模，我们展示了一个在知识库上的关于半监督实体分类的应用。
* 提出*神经关系型推理NRI(neural relational inference)*，用于发现相互作用系统中潜在的关系结构。NRI将图神经网络和一个关于图中边的类型的概率隐变量模型结合在一起。使用NRI对动态交互系统进行建模，如物理中的多粒子系统。
* 提出*CompILE(compositional imitation learning and execution)*，一个用于探索序列行为数据结构的模型。CompILE使用一个新的差分序列分割机制来发现并自动编码模仿学习上下文中行为的有意义的子序列或子程序。可以执行和重组隐编码来生成新的行为。
* 提出*对比训练的结构化全局模型C-SWMs(contrastively-trained structured world models)*



## 1 INTRODUCTION

### 1.1 STRUCTURE AND HUMAN COGNITION

结构围绕着我们：从基础的物理交互到如原子、分子、生命体、社交网络、生态系统、天体系统以及宇宙中更广范围的其他例子的新兴结构。

在这样复杂且结构化的环境中进行的数百万年的进化塑造了人类的心灵：一种高度优化的系统，擅长在这个世界导航并达成目标，也适应这个环境提供的最无法预见的变化——换句话说，人类是*智能体*。

不仅我们周围的这个世界是充满结构的，而且我们自己对世界的理解——我们的*世界模型*——也是高度结构化的：我们通常依据概念、抽象和关系进行思考、推理和沟通。我们可以从原始感知输入中识别物体，并根据物体的组成和关系来理解视觉画面。我们可以将两个概念联系到一起。以一只长翅膀的大象为例：我们可以轻易地想象出这一组合的概念，尽管我们从未遇到过这样的生物。

我们如何将这样的结构形式化？一个自然的方法是将结构化形式的信息表示为一个图。图是一种数据结构，将一个集合的实例表示为节点，将它们之间的关系表示为边。

随着现代技术的发展和互联网的兴起，图已到处可见：社交网络、万维网、街道地图、搜索引擎中使用的知识库以及甚至化学分子都经常表示为一组实体和实体间的关系的形式。我们所处世界的这种图结构描述的普遍性要求开发高效利用并学习理解以这种结构形式所表达的数据的方法——而且当我们达成目标的时候，在这一过程中学到的只是也许可以帮助我们开发更好的、对世界的结构性理解与我们人类类似的智能体。



### 1.2 ARTIFICIAL INTELLIGENCE AND DEEP LEARNING

理解人类认知的渴望导致了各种各样的科学分支的出现。认知科学、神经科学和机器学习以及人工智能的研究是科学领域研究人类心智（认知科学）、物理神经基础（神经科学）和人类某些行为的副本以及人工机器的核心算法（机器学习和人工智能）的最突出的例子。

本论文中的工作定位于机器学习领域，机器学习是AI研究中被最为广泛追求的分支。机器学习解决的问题是如何建构系统和设计算法，使其从数据和经验中学习，这与传统计算机科学的方法正相反，传统系统是通过明确的编程使其遵循精确指示进行动作。关于学习的问题通常近似为将一个模型与数据进行匹配，目标是使这个学习后的模型可以泛化到新的数据或经验。



