# 文本情感分类：使用循环神经网络
文本分类是自然语言处理的一个常见任务，它把一段不定长的文本序列变换为文本的类别。本节关注它的一个子问题：使用文本情感分类来分析文本作者的情绪。这个问题也叫情感分析（sentiment analysis），并有着广泛的应用。例如，我们可以分析用户对产品的评论并统计用户的满意度，或者分析用户对市场行情的情绪并用以预测接下来的行情。
同求近义词和类比词一样，文本分类也属于词嵌入的下游应用。本节将应用预训练的词向量和含多个隐藏层的双向循环神经网络，来判断一段不定长的文本序列中包含的是正面还是负面的情绪。
在实验开始前，导入所需的包或模块。
## 文本情感分类数据集
我们使用斯坦福的IMDb数据集（Stanford's Large Movie Review Dataset）作为文本情感分类的数据集 [1]。这个数据集分为训练和测试用的两个数据集，分别包含25,000条从IMDb下载的关于电影的评论。在每个数据集中，标签为“正面”和“负面”的评论数量相等。
###  读取数据集
首先下载这个数据集到`../data`路径下，然后解压至`../data/aclImdb`路径下。
接下来，读取训练数据集和测试数据集。每个样本是一条评论及其对应的标签：1表示“正面”，0表示“负面”。
### 预处理数据集
我们需要对每条评论做分词，从而得到分好词的评论。这里定义的`get_tokenized_imdb`函数使用最简单的方法：基于空格进行分词。
现在，我们可以根据分好词的训练数据集来创建词典了。我们在这里过滤掉了出现次数少于5的词。
因为每条评论长度不一致所以不能直接组合成小批量，我们定义`preprocess_imdb`函数对每条评论进行分词，并通过词典转换成词索引，然后通过截断或者补“&lt;pad&gt;”（padding）符号来将每条评论长度固定成500。
### 创建数据迭代器
现在，我们创建数据迭代器。每次迭代将返回一个小批量的数据。
打印第一个小批量数据的形状以及训练集中小批量的个数。
## 使用循环神经网络的模型
在这个模型中，每个词先通过嵌入层得到特征向量。然后，我们使用双向循环神经网络对特征序列进一步编码得到序列信息。最后，我们将编码的序列信息通过全连接层变换为输出。具体来说，我们可以将双向长短期记忆在最初时间步和最终时间步的隐状态连结，作为特征序列的表征传递给输出层分类。在下面实现的`BiRNN`类中，`Embedding`实例即嵌入层，`LSTM`实例即为序列编码的隐藏层，`Dense`实例即生成分类结果的输出层。
创建一个含两个隐藏层的双向循环神经网络。
### 加载预训练的词向量
由于情感分类的训练数据集并不是很大，为应对过拟合，我们将直接使用在更大规模语料上预训练的词向量作为每个词的特征向量。这里，我们为词典`vocab`中的每个词加载100维的GloVe词向量。
然后，我们将用这些词向量作为评论中每个词的特征向量。注意，预训练词向量的维度需要与创建的模型中的嵌入层输出大小`embed_size`一致。此外，在训练中我们不再更新这些词向量。
### 训练模型
这时候就可以开始训练模型了。
最后，定义预测函数。
下面使用训练好的模型对两个简单句子的情感进行分类。
## 小结
* 文本分类把一段不定长的文本序列变换为文本的类别。它属于词嵌入的下游应用。
* 可以应用预训练的词向量和循环神经网络对文本的情感进行分类。
## 练习
* 增加迭代周期。训练后的模型能在训练和测试数据集上得到怎样的精度？再调节其他超参数试试？
* 使用更大的预训练词向量，如300维的GloVe词向量，能否提升分类精度？
* 使用spaCy分词工具，能否提升分类精度？你需要安装spaCy（`pip install spacy`），并且安装英文包（`python -m spacy download en`）。在代码中，先导入spaCy（`import spacy`）。然后加载spaCy英文包（`spacy_en = spacy.load('en')`）。最后定义函数`def tokenizer(text): return [tok.text for tok in spacy_en.tokenizer(text)]`并替换原来的基于空格分词的`tokenizer`函数。需要注意的是，GloVe词向量对于名词词组的存储方式是用“-”连接各个单词，例如，词组“new york”在GloVe词向量中的表示为“new-york”，而使用spaCy分词之后“new york”的存储可能是“new york”。
## 参考文献
[1] Maas, A. L., Daly, R. E., Pham, P. T., Huang, D., Ng, A. Y., & Potts, C. (2011, June). Learning word vectors for sentiment analysis. In Proceedings of the 49th annual meeting of the association for computational linguistics: Human language technologies-volume 1 (pp. 142-150). Association for Computational Linguistics.
## 扫码直达[讨论区](https://discuss.gluon.ai/t/topic/6155)
![](../img/qr_sentiment-analysis.svg)