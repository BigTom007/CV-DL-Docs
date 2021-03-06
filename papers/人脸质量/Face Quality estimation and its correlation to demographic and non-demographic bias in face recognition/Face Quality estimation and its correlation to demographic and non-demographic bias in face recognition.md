# Face Quality estimation and its correlation to demographic and non-demographic bias in face recognition

人脸质量估计和其与人口学和非人口学偏差在人脸识别中的相关性

**Abstract**

人脸质量评估的目标是估计一张人脸图片在识别任务中的可用性。是得到高性能人脸识别的关键因素。最近，这些人脸识别系统的高性能伴随着一个相对于人口学和非人口学子组的强偏差。最近的成果展示了人脸质量评估算法应该适应于已部署的人脸识别系统以获取高准确率和鲁棒的质量估计结果。但是这可能导致一个关于人脸质量评估的偏差变换，从而发生有差别现象，例如在注册阶段。本作对人脸识别和人脸质量评估的偏差间的相关性进行了深入的分析。实验使用两个公开数据集，限制和非限制场景，使用两个广泛使用的embedding特征提取方法。评估4个人脸质量评估的sota方案，偏差为姿态，人种和年龄。实验表明，人脸质量评估方案为受识别偏差影响的子组指派特别低的质量评分，这证明这些方法同样有偏差。这样引起了公平和歧视的道德问题。



## 1. Introduction

人脸识别系统在世界范围内的到广泛应用，对每个人的生活造成的影响日益增长。而且这些系统越来越多地应用于决策性进程，如法医学和执法过程。当前的生物识别解决方案主要的优化方向是最大整体精度，且受人口学分组偏差影响严重。人脸识别的性能由拍摄图像的质量决定。生物识别样本的质量由该样本用于识别的目的定义且对不同应用很严格。最近的研究表明，人脸质量评估的准确度和鲁棒性可以通过人脸识别模型使用的人脸质量评估算法大幅度增强。但是这同样可能引起偏差。

一些政治法规blahblahblah

人脸质量评估方法能够控制计划和非计划的偏差，例如非人口学和人口学偏差。非人口学偏差增强了质量评估流程，没有歧视后果，将人口学偏差无意地转换成人脸质量评估算法会对社会造成严重的影响。在一个新id注册的时候或者基于质量的融合方法（例如，在监控场景）都需要人脸质量评估。结果就是质量评估中经过转换的偏差会直接这个基于质量的子系统的增加歧视性决策的数量。而且在实际应用中，人脸质量估计会被当作一个独立的流程步骤使用，而且可以用与实际使用的不同人脸识别系统进行训练。因此，有偏差的人脸质量评估会对人脸识别系统加入偏差，因为它们的偏差不同。

本文中我们分析了人脸识别系统中的偏差和对应人脸质量评估间的关系。相关性分析通过两个不同的人脸识别方法和学术界和工业界的四个sota人脸质量评估算法实现。调查不同头部姿态、种族和年龄，我们发现对于两个人脸识别系统，性能的降低亦即偏差，出现在特定的子类中。实验展示了人脸识别偏差和人脸质量评估的强相关性。有偏差的类别的图片的质量估计要比无偏差者低。

本文的目的是要指出当前人脸图像评估方法要解决与人脸识别中相似的偏差问题。我们指出一张人脸图片的质量指出一个有缺陷的决策的有偏见的ground

blahblahblah



## 2. Related work

### 2.1. Bias in face recognition

在人脸生物识别领域，偏差通常是由于训练数据中不平衡的类别分布的存在而出现的。有人证实了性别和种族会对识别效果有很大影响。



blahblahblah



### 2.2. Face quality assessment

提出了一些标准通过限制拍摄条件来保证人脸质量，例如ISO/IEC 19794-5和ICAO 9303. 这些标准中，质量被分成基于图像的质量（如光照，遮挡）和基于个体的质量（如姿态，表情，配件）。这些标准影响了很多人脸质量评估方法。

第一代人脸质量评估算法将质量度量定义为基于图片质量因素。但是这些方法需要人为地考虑每一个可能的因素，由于人类也许并不知道人脸识别系统最好的特征是什么样，所以最近的研究关注于基于学习的方法。

人脸质量评估的端到端的学习方法于2011年首次提出。Aggarwal等提出一个用于预测人脸识别性能的方法，使用一个多维缩放的方法将空间特征映射成真实评分。

blahblahblah

到目前为止，最好的质量估计是通过利用使用中的人脸识别模型实现的。但是将人脸识别偏差转换为人脸估计是有风险的。因此，本文分析人脸质量评估和人脸识别偏差间的相关性。



## 3. Evaluated face quality assessment solutions

人脸质量评估的目的是估计一张图片用于识别目的的可用性。选择了工业界和学术界最新的4个人脸质量评估方法。



### 3.1. COTS

COTS是 Neurotechnology的off the shelf的工业产品，使用的版本于2019年出版。不幸的是，它只提供了应用而没提任何关于其工作原则的信息。但是有人展示了COTS预测的质量结果于FaceNet匹配程度较高，而对ArcFace的效果较差。

### 3.2. Best-Rowden

2018年Best-Rowden和Jain发表了两个人脸质量估计的方法，包含人类估计和不包含人类估计。我们评估了他们基于（对比评分得到的）质量标签的方法，因为特征和对比评分是相互依赖的，因此它适用于已经部署的人脸识别模型。他们将个体$i$的查询$j$的质量标签定义为：
$$
z_{ij}=(s_{ij}^G - \mu_{ij}^I)/\sigma_{ij}^I, \quad\quad\quad(1)
$$
其中$s_{ij}^G$是真实分数，$\mu_{ij}^I$和$\sigma_{ij}^I$是虚假分数的均值和标准差。使用部署的人脸识别模型的embedding特征向量，基于这些特征训练了一个支持向量回归器来估计一张输入图片的质量得分。



### 3.3. FaceQnet

于2019年提出，使用了3.2.中的对比评分标签（公式1）。将其与基于ResNet的深度神经网络架构结合起来。他们的方法用的是FaceNet的embedding特征，且用VGGFace2数据集进行训练。



### 3.4. SER-FIQ

随机特征鲁棒性（SER, Stochastic embedding robustness）是一个人脸图片质量（FIQ, face image quality）估计概念，避免使用不准确的质量标签。通过深度学习的特征来定义人脸图片的质量。计算人脸识别系统随机子网络输出特征的方差，这个方法将方差的数量级定义为一个鲁棒性的度量，亦即图片质量。给定一个输入图片$I$和部署的人脸识别模型$\mathcal{M}$，该方法使用神经网络的$m=100$个不同的dropout路径。得到$m$个随机子网络的结果，每一个子网络产生一个随机embedding$x_i$.一个输入$I$的质量定义为所有随机embedding对的负平局欧氏距离的sigmoid值：
$$
q(I)=2\sigma\bigg(-\frac{2}{m^2}\sum_{i<j}d(x_i,x_j)\bigg), \quad\quad\quad(2)
$$
随机embedding间较大的方差代表较低的表达鲁棒性，亦即样本质量$q$.较低的方差代表较高的鲁棒性，也就是较高的质量。因为这个方法可以直接应用于已部署的人脸识别模型，就完全避免了训练和进一步适应模型的决策模式。作者展示这个概念与以前的方法相比大幅提升了质量估计的效果。



## 4. Experimental setup

**Database：**为了在受控场景和非限制场景下考察人脸质量评估和人脸识别系统偏差的相关性，采用两个公开数据集做实验，分别是ColorFERET和Adience.

**Evaluation metrics：**使用error versus reject曲线来衡量人脸质量评估的性能。这些曲线展示了识别错误率（y轴）与不考虑一定百分比的人脸图片（x轴）间的关系。根据预测的质量值，这些不考虑的图片是哪些最低评分的图片，然后再剩余的图片上计算错误率。Error versus reject曲线指示了当增大不考虑图片比例时验证错误持续降低。

为了证明一个人脸识别系统对某些类别存在偏差，报告所有类别的验证错误。验证错误以固定FMR（false match rate）时对应的FNMR（false non-match rate）的形式呈现。FMR的阈值设定为0.1%. 如果人脸质量评估算法将有偏见的子组标记为比无偏见子组更低的质量的话，有偏见的子组的比率会快速下降。

**Face recognition networks：**人脸经过对齐、缩放和剪裁等预处理后进入人脸识别模型提取embedding特征。本作使用两个人脸识别模型，FaceNet和ArcFace.

**Investigations：**首先分析人脸质量评估的性能，第二步展示使用的人脸识别系统有偏差。因此基于姿态、种族和年龄的类别分析人脸验证性能。在明确这些类别的偏差之后，调查人脸质量评估和人脸识别偏差的相关性。



## 5. Results

### 5.1 Face quality assessment performance



