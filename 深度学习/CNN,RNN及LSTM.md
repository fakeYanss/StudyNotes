## CNN

##### 特点

局部连接

权值共享

降维



##### 组成

卷积部分：不需要固定尺寸的图像

全连接部分：需要固定大小的输入

* 输入层，对数据取均值，做data augmentation等工作
* 卷积层，局部关联抽取feature
* 激活层，非线性变化
* 池化层，下采样
* 全连接层，增加模型非线性
* 高速通道，快速连接
* BN层，缓解梯度弥散



### RNN

rnn的目的是用来处理序列数据。在传统的神经网络模型中，是从输入层到隐含层再到输出层，层与层之间是全连接的，每层之间的节点是无连接的。但是这种普通的神经网络对于很多问题却无能无力。例如，你要预测句子的下一个单词是什么，一般需要用到前面的单词，因为一个句子中前后单词并不是独立的 。



rnn叫循环神经网络，即一个序列当前的输出与前面的输出也有关。

具体表现为，网络会对前面的信息进行记忆并应用与当前输出的计算中，即隐藏层之间的节点不再是无连接二十有连接的，并且隐藏层的输入包括输入层的输入还包括上一时刻隐藏层的输出。



理论上，RNNs能够对任何长度的序列数据进行处理。但是在实践中，为了降低复杂性往往假设当前的状态只与前面的几个状态相关 。



* GRU直接将hidden state传给下一个单元
* LSTM用memory cell把hidden state 包装起来

##### LSTM

三个门

* forget
* input
* output

##### GRU

gated recurrent units,循环神经网络的一种，有两个门

* update

* reset

  





