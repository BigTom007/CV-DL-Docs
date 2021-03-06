# Deep Multi-Center Learning for Face Alignment

面部关键点之间的关系是高度相关的，因为一个关键点的位置可以由其相邻点来估计得到。大多数现有的深度学习方法都使用一个叫做形状预测层的全连接层来估计面部关键点的位置。本文提出一个用于面部对齐的新的深度学习框架，称为Multi-Center Learning，它有多个形状预测层。具体来说，每一个预测层分别增强一个特定的语义相关的关键点集群的检测结果。首先关注检测难度大的关键点，然后每一个关键点集群都会分别优化。而且为了降低模型的复杂度，提出一个模型组装方法将多个形状预测层整合到一个形状预测层中。



## I. INTRODUCTION

人脸对齐涉及面部关键点检测，是很多人脸分析任务（如人脸动画，美颜等）的预处理阶段内容。由于严重遮挡和外观多样性的存在，一个健壮且准确的人脸对齐算法仍是非限制场景下的一大挑战。

![Figure 1](1.png"Figure 1")

每个关键点都不是孤立的而是与其相邻关键点有相关性的。如Figure 1(a)所示，下巴上的关键点全被挡住，嘴周围的关键点部分被挡住。Figure 1(b)的图片中右边脸的关键点几乎都不可见。因此，同一个区域的关键点有相近的性质，包括封闭和可见性。可以观察到鼻子的位置可以通过双眼和嘴的位置大致估计到。在不同面部区域之间同样有结构的相关性。由于这个事实，面部关键点可以根据其各自的语义相关性分成几个集群。

本作中，提出一个新的深度学习框架称为Multi-Center Learning（MCL）用于探索关键点之间的强相关性。具体来说，我们的网络使用多个形状预测层来预测关键点的位置，每一个位置预测层都会增强一个特定关键点集群的预测结果。通过对每个关键点进行加权，首先关注较难的关键点，随后分别对每个关键点集群进行优化。而且为了降低模型的复杂度，提出一个模型组装方法将多个形状预测层整合到一个形状预测层中。整个框架增强了在低模型复杂度下每个关键点的学习过程



## II. RELATED WORK

### A. Conventional Face Alignment

传统人脸对齐方法可以分成两类：模板拟合方法和基于回归的方法。

模板拟合方法通过构建形状模板来匹配人脸。但是模板不能覆盖复杂情况，对于没见过的人脸难以泛化。

基于回归的方法通过学习一个从面部特征到形状的回归函数来预测关键点位置。大对数回归方法给定一个初始形状然后迭代地进行调整，如果初始值不合理，其最终结果可能会陷入局部最优。

### B. Unconstrained Face Alignment

大姿态变化和严重遮挡（封闭）是非限制环境下的主要挑战。非限制人脸对齐方法基于3D模型或者显式处理遮挡。

### C. Face Alignment via Deep Learning

深度学习方法可以分成两类：单一网络和多网络。



![Figure 2](2.png"Figure 2")

## III. MULTI-CENTER LEARNING FOR FACE ALIGNMENT

### A. Network Architecture

MCL网络的架构如Figure 2所示。MCL包含3个max-pooling层，其每一个层前面都是VGGNet提出的两个卷积层stack.第四个stack后使用一个$D$通道特征图的卷积层。在每个卷积层后使用BN和ReLU加速网络收敛。大多数现有的深度学习方法（如TCDCN）使用全连接层来提取特征，这种方式有过拟合的倾向，且会阻碍网络的泛化能力。为了避开这一问题，在最后一个卷积层后使用全局平均池化来提取高级特征表示$\textbf{x}$，其计算每一个特征图的平均值。这一改进使MCL获得了更强的表示能力而且其参数量更小。

人脸对齐可以视为一个非线性回归问题，将外观转换成形状。转换$\Phi^S(\cdot)$用于对这个高度非线性函数进行建模，它从输入的人脸图像$I$提取特征$\textbf{x}$:
$$
\textbf{x}=\Phi^S(I) \quad\quad\quad(1)
$$
其中$\textbf{x}=(x_0,x_1,...,x_D)^T \in \mathbb{R}^{(D+1)\times1}$，$x_0=1$对应偏置，$\Phi^S(\cdot)$是一个包含卷积，BN，ReLU和池化在内的复合函数。

传统的方式只是用一个形状预测层，这样做限制了性能。MCL使用多个形状预测层，每一个用于增强一个特定集群的关键点的检测。前几层由后面的形状预测层共享，称为共享层，它们构成了复合函数$\Phi^S(\cdot)$.对于第$i$个形状预测层，$i=1,...,m$，使用一个权重矩阵$\textbf{W}^i=(\textbf{w}^i_1,\textbf{w}^i_2,...,\textbf{w}^i_{2n}) \in \mathbb{R}^{(D+1)\times2n}$来连接特征$\textbf{x}$，其中$m,n$分别代表形状预测层的数量和关键点的个数。之所以每个形状预测层都要训练预测$n$个关键点位置而不是一个集群的关键点是因为不同面部区域是有关联的，如Figure 1所示。

为了降低模型复杂度，使用一个模型组装函数$\Phi^a(\cdot)$将多个形状预测层组合成一个形状预测层：
$$
\textbf{W}^a=\Phi^a(\textbf{W}^1,...,\textbf{W}^m), \quad\quad\quad(2)
$$
其中$\textbf{W}^a=(\textbf{w}^a_1,\textbf{w}^a_2,...,\textbf{w}^a_{2n})^T \in \mathbb{R}^{(D+1)\times 2n}$是组装后的权重矩阵。特别地，$\textbf{w}^a_{2j-1}=\textbf{w}^i_{2j-1},\textbf{w}^a_{2j}=\textbf{w}^i_{2j},j\in P^i,i=1,...,m$，其中$P^i$是第$i$个关键点下标的集群。最终的预测结果$\hat{\textbf{y}}=(\hat{y}_1,\hat{y}_2,...,\hat{y}_{2n})$定义如下
$$
\hat{\textbf{y}}={\textbf{W}^a}^T\textbf{x}, \quad\quad\quad(3)
$$
其中$\hat{y}_{2j-1}$和$\hat{y}_{2j}$代表预测的第$j$个关键点的x坐标和y坐标。

与其他经典卷积网络（如VGGNet，GoogLE-Net和ResNet）相比，MCL总体上更小也更浅。我们相信这样的一个简单的结果对于估计面部关键点位置来说是高效的。首先，人脸对齐的目标一般来说是回归少于100个关键点的坐标，这对模型复杂度的需求远低于超过1000类的分类任务。其次，一个很深的网络由于信息丢失可能会失效。其他视觉定位任务，比如人脸检测，通常使用多个级联的浅网络而非一个很深的网络。

### B. Learning Algorithm

本学习算法的概要在Algorithm 1中展示。

![Algorithm 1](a1.png"Algorithm 1")

$\Omega^t$ 和$\Omega^v$是训练集和验证集。$\Theta$是MCL的参数集合，包括权重和偏置，每一次迭代使用Mini-Batch Stochastic Gradient Descent（SGD）更新参数。loss定义为：
$$
E=\sum\limits_{j=1}^nu_j[(y_{2j-1}-\hat{y}_{2j-1})^2+(y_{2j}-\hat{y}_{2j})^2]/(2d^2), \quad\quad\quad(4)
$$
其中$u_j$是第$j$个关键点的权重，$y_{2j-1}$和$y_{2j}$代表第$j$个关键点的gt坐标x和y，$d$是gt眼间距。

归一化的眼间距提供了不同尺寸人脸间的合理的对照，降低了loss的数量级，加速学习过程。训练过程中，过高的学习率会引起错过最优以至于使网络发散，学习率过低会导致无法到达局部最优。我们使用较小的初始学习率来避免发散，当loss显著下降后提高学习率继续训练。

1) *Pre-Training and Weighting Fine-Tuning:* 在Step 1中，训练一个带一个形状预测层的基础模型（BM）学习一个较好的初始模型。公式4中所有$j$的$u_j=1$.BM模型在训练集$\Omega^v$上的每个关键点的平均对齐误差分别为$\epsilon^b_1,...,\epsilon^b_n$,在全部图片上取平均。误差更大的关键点视为困难关键点。

在第二步和第三步中，通过给困难关键点分配较大的权重来关注困难关键点的检测。第$j$个关键点的权重与其对齐误差成比例：
$$
u_j=n\epsilon^b_j/ \sum\limits_{j=1}^n\epsilon^b_j. \quad\quad\quad(5)
$$
使用两步来平滑地搜索解而非直接微调BM的全部层。步骤二寻找没有过度偏离BM的解。步骤三在前一步的较大范围的基础上进行搜索。这一阶段称为权重微调，学到一个权重模型（WM），其对于困难关键点的定位准确度更高。

2) *Multi-Center Fine-Tuning and Model Assembling:*  根据其自身语义结构将人脸分成七个部分：左眼，右眼，鼻子，嘴，左面颊，下巴和右面颊。如Figure 3所示，5点、29点和68点的不同标签模式其分别对应4个、5个和7个部分关键点集群。对于第$i$个形状预测层，第$i$个关键点集群视为优化中心，其他关键点下标的集合表示为$Q^i$.

![Figure3](3.png"Figure 3")

从步骤四到步骤六，固定共享层的参数$\Theta^S$，每个形状预测层都使用WM中的形状预测层参数来初始化。当微调第$i$个形状预测层的时候，$P^i$和$Q^i$中关键点的权重定义为：
$$
u_{P^i}=\alpha u_{Q^i}, \quad\quad\quad(6)
$$
其中$\alpha \gg 1$是一个系数，它使第$i$个形状预测层增强第$i$个关键点集群的预测值。$u_{P^i}$和$u_{Q^i}$间的约束条件表示为：
$$
u_{P^i}|P^i|+u_{Q^i}(n-|P^i|)=n, \quad\quad\quad(7)
$$
其中$|\cdot|$代表一个集群中元素的数量。通过公式6和7可以得到权重表达式：
$$
u_{P^i}=\alpha n/[(\alpha-1)|P^i|+n], \\
u_{Q^i}=n/[(\alpha-1)|P^i|+n]. \quad\quad\quad(8)
$$
WM在训练集$\Omega^v$上每个关键点的平均对齐误差分别是$\epsilon^w_1,...,\epsilon^w_n$.与公式5相似，第$j$个关键点的权重是：
$$
u_j=
\begin{cases}
u_{P^i}|P^i|\cdot \epsilon^w_j/\sum_{j\in P^i}\epsilon^w_j, \quad\quad\quad j\in P^i, \quad\quad\quad(9)\\
u_{Q^i}(n-|p^i|)\cdot \epsilon^w_j/\sum_{j\in Q^i}\epsilon^w_j, \quad j\in Q^i. 
\end{cases}
$$
尽管主要优化的对象是$P^i$中的关键点，但是仍然需要考虑其他关键点，给一个非零的小权重。这样做有利于利用不同面部区域在结构上的隐式的关系，且有利于稳定求解。这一阶段称为multi-center fine-tuning，学习多个形状预测层。

第七步，将多个形状预测层根据公式2组装到一起。在这个模型组装阶段，学习一个组装模型（AM）。在组装过程中不会增加模型的复杂度，所以AM的计算量较小。它通过整合每个形状预测层的优势提高了每个关键点的检测精确度。

3) *Analysis of Model Learning:* 为了调查关键点权重在学习过程中的影响，我们计算了公式4中关于$\hat{y}_k$的导数：
$$
\frac{\partial E}{\partial \hat{y}_k}=u_j(\hat{y}_k-y_k)/d^2, \quad\quad\quad(10)
$$
其中$k\in{2j-1,2j},j=1,...,n$.在学习过程中，公式3中经过组装的权重矩阵$\textbf{W}^a$通过SGD更新参数。特别地，$\textbf{w}^a_k=\textbf{w}^a_k-\eta \frac{\partial E}{\partial \textbf{w}^a_k}=\textbf{w}^a_k-\eta\frac{\partial E}{\partial \hat{y}_k}\frac{\partial \hat{y}_k}{\partial \textbf{w}^a_k}=\textbf{w}^a_k-\eta\frac{\partial E}{\partial \hat{y}_k}\textbf{x}$.总之，$\textbf{W}^a$更新为（$\textbf{x}$是提取的特征）：
$$
\textbf{w}^a=\textbf{w}^a-\eta u_j(\hat y_k-y_k)\textbf{x}/d^2, \quad\quad\quad(11)
$$
其中$\eta$是学习率。如果第$j$个关键点的权重较大，其对应的参数更新的步长就较大。因此对每个关键点的loss进行加权来保证主要优化权重较大的关键点。我们的方法首先使用加权微调阶段来优化困难关键点，然后使用multi-center 微调阶段来分别优化每个关键点集群。



*****

因为人脸关键点间有隐含的结构关系，所以将人脸分成几个区域（七个），每个区域内的关键点构成一个集群。针对每个集群进行关键点回归，使用多个FC层，每个FC层对应一个区域，然后再将它们组装成一个FC层