# 通过TensorSlow识别手写数字

## 摘要

近年来，手写识别技术的应用越来越广泛，如：邮政编码、输入法识别等，手写识别技术也隐然称为了当前的热门领域，高精度的手写识别技术是人机交互最自然、最方便的手段。由于书写风格因人而异，所以需要运用机器学习理论，制作高效的手写数字识别系统。本文在TensorFlow平台下，通过对模型的建立及训练，达到了预测算法的高精度，建立了简洁高效的手写数字识别模型。

## 分析

* 首先我们有训练数据train.csv与测试数据test.csv文件，均置于input文件夹下；
* 其中train.csv中包含样本结果和多列属性值，显然我们可以通过将数据划分为划分训练集和测试集，以样本结果为因变量，其他列的值为自变量；
* 通过TensorFlow方便简洁地建立softmax回归模型；
* 通过梯度下降算法来不断自学习，实现交叉熵的最小化；
* 当训练集训练完毕后，以模型去预测测试集中的数据，预测结果与实际结果做对比，评估模型的正确率；
* 可进行模型拓展，追求更高正确率的模型；
* 模型应用，将模型应用于测试数据test.csv中，得出预测结果；





## 过程

### 数据读取

 ```python
# 导入相关库
# 线性代数的处理
import numpy as np
# 数据处理；CSV文件的输入输出
import pandas as pd 
# 图像处理
import matplotlib.pyplot as plt
df = pd.read_csv( './input/train.csv' )
 ```

`df`看出数据格式，如下：

![](D:\Work\TyporaNotes\note\python\pict\1-1.png)

![](D:\Work\TyporaNotes\note\python\pict\1-2.png)

可以看到csv文件中总共有42000行，785列数据，其中第一列label为标签值，即对应的阿拉伯数字“1、2、3...、9”，而2-785列为图像像素列；

```python
# 显示数据摘要描述信息
print(df.describe())
```

![](D:\Work\TyporaNotes\note\python\pict\1-3.png)

可以看到label列min值为0，max值为9，即对应该行像素点组合而成的图案对应的阿拉伯数字为“0-9”；

### 数据准备

首先导入sklearn库

```python
from sklearn.model_selection import train_test_split
# 划分训练集和测试集
train_x, test_x, train_y, test_y = train_test_split( data[:,1:].astype( 'float32' ), 
                                                    data[:,0].astype( 'float32' ), 
                                                    test_size=0.33, 
                                                    random_state=4 )
```

其中被划分的样本特征集为后面所有的pixelX列，而被划分的样本标签为第一列，且样本占比33%，随机种子为4；

查看train_y

![](D:\Work\TyporaNotes\note\python\pict\1-4.png)

### 图像输出

试着输出训练集第一个图像

```python
plt.imshow( train_x[0].reshape((28, 28)), cmap='gray' )
plt.title( int( train_y[0] ) )
```

![](D:\Work\TyporaNotes\note\python\pict\1-5.png)

可以看到，输出结果为6，像素点也组成了阿拉伯数字“6”；

随机输出训练集中50个图像（以5行10列格式）

```python
# 画布大小
plt.figure( figsize=( 14,10 ) )
for i in range( 50 ):
	# 生成随机数
    sample = np.random.randint( 0, train_x.shape[0], 1 )[0]
    # 当前画布位置
    plt.subplot(5, 10, i+1)
    # 设置图像描述的一系列参数
    plt.tick_params( axis='both', which='both', bottom='off', left='off', labelbottom='off', labelleft='off' )
    # 以各个像素点绘制图像
    plt.imshow( train_x[sample].reshape((28, 28)), cmap='gray' )
    # 添加标题
    plt.title( int( train_y[sample] ) )
```

![](D:\Work\TyporaNotes\note\python\pict\1-6.png)

### 模型定义

首先需要导入TensorFlow库，由于需要使用到TensorFlow1.0版本中的placeholder，而本地安装的是TensorFlow的2.0版本，故需要引入1.0的库及禁用2.0

```python
import tensorflow.compat.v1 as tf
tf.disable_v2_behavior()
```

首先定义特征数据和标签数据的占位符

```python
# 784个特征数据
x = tf.placeholder( tf.float32, [None, 784] )
# 生成shape=(784,10)随机数据矩阵
W = tf.Variable( tf.random_normal( [784, 10], mean=0.1, seed=4 ) )
b = tf.Variable( tf.random_normal( [10], mean=0.1, seed=4 ))
# 将图像向量x与权重矩阵W相乘再加上b之后的结果作为参数传给softmax函数，y表示模型的实际预测输出利用softmax可以得到模型对某个图片应该划分为哪个数字的概率
y = tf.nn.softmax( tf.matmul( x, W ) + b )
y_ = tf.placeholder( tf.float32, [None, 10] )
```

这里简单介绍一下softmax函数；`tf.matmul(X，W)`表示`x`乘以`W`，对应之前等式里面的Wx，这里`x`是一个2维张量拥有多个输入，然后再加上`b`，把和输入到`tf.nn.softmax`函数里面。其实对应的算法可以用下图表示：

![](D:\Work\TyporaNotes\note\python\pict\1-11.png)

更进一步，可以写成更加紧凑的方式：

![](D:\Work\TyporaNotes\note\python\pict\1-12.png)

TensorFlow不仅仅可以使softmax回归模型计算变得特别简单，它也用这种非常灵活的方式来描述其他各种数值计算，从机器学习模型对物理学模拟仿真模型。

定义损失函数

```python
# loss function
cross_entropy = tf.reduce_mean( -tf.reduce_sum( y_ * tf.log( y ), reduction_indices=[1] ) )
```

其中损失函数的过程为：计算张量tensor沿着指定的数轴（tensor的某一维度）上的的平均值，其主要用作降维或者计算tensor（图像）的平均值。

选择优化器

```python
# 创建优化器，使用梯度下降算法
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
```

准确率计算函数

```python
# y_是预期输出和实际预测输出对比，得到一个true or false矩阵代表本轮预测的正确与否
correct_pred = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
# 计算true所占的比例得到准确率
accuracy = tf.reduce_mean(tf.cast(correct_pred, tf.float32))
```

### 模型训练

首先需要将train_y、test_y由表格转换为矩阵格式

```python
train_y = pd.get_dummies( train_y ).as_matrix()
test_y = pd.get_dummies( test_y ).as_matrix()
```

声明会话并启动会话

```python
sess = tf.InteractiveSession()
tf.global_variables_initializer().run()
```

开始训练模型

```python
# training
# 迭代最终位置
train_size = train_x.shape[0]
# 步长
batch_size = 100
for i in range( 0, 500000, batch_size ):
    idx = i%train_size
    # feed数据必须和placeholder的shape保持一致
    sess.run( train_step, feed_dict={x: train_x[idx:idx+batch_size], 
                                     y_: train_y[idx:idx+batch_size]} )
    # 每达到10000，输出其损失率和准确率
    if i%10000==0:
        loss, acc = sess.run( [cross_entropy, accuracy], feed_dict={x: train_x[idx:idx+batch_size], y_: train_y[idx:idx+batch_size]} )
        print( 'Step: ' + str(i) + ' loss: ' + str(loss) + ' accuracy: ' + str(acc) )
```

每达到10000，输出其损失率和准确率，输出结果如下：

```
Step: 0 loss: 14.71599 accuracy: 0.08
Step: 10000 loss: 1.4100455 accuracy: 0.71
Step: 20000 loss: 0.89814126 accuracy: 0.83
Step: 30000 loss: 1.4107716 accuracy: 0.79
Step: 40000 loss: 0.5116478 accuracy: 0.84
Step: 50000 loss: 0.63071173 accuracy: 0.85
Step: 60000 loss: 0.23561214 accuracy: 0.92
Step: 70000 loss: 0.3821353 accuracy: 0.89
Step: 80000 loss: 0.31462497 accuracy: 0.91
Step: 90000 loss: 0.586833 accuracy: 0.88
.............
Step: 480000 loss: 0.35350525 accuracy: 0.92
Step: 490000 loss: 0.31811258 accuracy: 0.94
```

### 模型评估

获取测试集的预测结果序列tested_y，同时与真正结果进行对比，得到序列correct_prediction

```python
tested_y = sess.run( tf.argmax( y, 1 ), feed_dict = {x: test_x} )
correct_prediction = np.equal( tested_y, np.argmax( test_y, 1 ) )
```

根据correct_prediction计算其准确率，具体如下：

```python
# 设置占位符
p = tf.placeholder(tf.bool, [None,])
# 获取其准确率
accuracy = tf.reduce_mean( tf.cast( p, tf.float32 ) )
# 执行训练并输出结果
print( 'Accuracy (on unseen data):', sess.run( accuracy, feed_dict={ p: correct_prediction } ) )
# 输出其正确数，及总测试量
print('Correct:',
      sum(correct_prediction.astype(int)), 
      ' out of', 
      len(test_x) )
```

输出结果如下：

```
Accuracy (on unseen data): 0.90252525
Correct: 12509  out of 13860
```

可以看到模型的准确率还是较高的。

尝试查看预测结果错误的数据，具体如下：

```python
# 合并预测数组和训练数组
test_combine = np.concatenate( ( np.array( [correct_prediction] ).T,np.array([tested_y]).T,test_x ),axis=1 )
# 找到其中错误的数组
failures = np.array( list( x for x in test_combine if x[0]==0 ) )
# 输出长度
print( 'Number of failures: %s'%failures.shape[0] )
```

输出结果为：

```
Number of failures: 1351
```

绘制图像

```python
# 画布定义
plt.figure( figsize=( 14,10 ) )
# 随机抽取50个错误图像
for i in range( 50 ):
    sample = np.random.randint( 0, failures.shape[0], 1 )[0]
    plt.subplot(5, 10, i+1)
    # 基本参数设置
    plt.tick_params( axis='both', which='both', bottom='off', left='off', labelbottom='off', labelleft='off' )
    # 画图
    plt.imshow( failures[sample,2:].reshape((28, 28)), cmap='gray' )
    # 标题
    plt.title( int( failures[sample, 1] ) )
```

得到绘制结果如下：

![](D:\Work\TyporaNotes\note\python\pict\1-7.png)

### 模型应用

模型一般应用于预测新的样本值；

本例中有test.csv，首先读取其数据，看下其格式

```python
df = pd.read_csv( './input/test.csv' )
df
```

![](D:\Work\TyporaNotes\note\python\pict\1-8.png)

查看df的行列数

```python
df.shape
```

![](D:\Work\TyporaNotes\note\python\pict\1-9.png)

可以看出，test.csv文件中含有784列像素列，通过上面训练出的模型，与像素点数据结合，即可以推测出每行图像所对应的阿拉伯数字；

应用模型，如下

```python
# x值处理
predict_x = df.as_matrix() / 255
# y值处理
predict_y = tf.argmax( y, 1 )
# 模型的应用，得出预测结果
predicted_y = sess.run( predict_y, feed_dict = {x: predict_x} )
```

同样，随机绘制50个图像结果，具体代码如下：

```python
plt.figure( figsize=( 14,10 ) )
for i in range( 50 ):
    sample = np.random.randint( 0, predict_x.shape[0], 1 )[0]
    plt.subplot(5, 10, i+1)
    plt.tick_params( axis='both', which='both', bottom='off', left='off', labelbottom='off', labelleft='off' )
    plt.imshow( predict_x[sample].reshape((28, 28)), cmap='gray' )
    plt.title( predicted_y[sample] )
```

输出图像如下：

![](D:\Work\TyporaNotes\note\python\pict\1-13.png)

可以看到尽管有少量的错误出现，但是图像和对应的数字匹配的正确率还是较高的。

### 模型拓展

以上的模型准备率为90%左右，并不算高，我们可以通过卷积神经网络来改善效果。

首先需要将图像数据进行修改，从原来的1 * 784的行向量变为28 * 28 * 1的张量（tensor）。

```python
x = tf.placeholder(dtype=tf.float32, shape=[None, 784])
x_in = tf.reshape(x, [-1, 28, 28, 1])
```

同时需要定义如下的各个函数：

```python
# 将权重参数初始化为拥有固定标准差的高斯分布的张量
def weight_variable(shape):
    #权重的初始化
    initial = tf.truncated_normal(shape, stddev=0.1)
    return tf.Variable(initial)
# 将偏置项初始化为某一固定常量
def bias_variable(shape):
    #偏置项的初始化
    initial = tf.constant(0.5, shape=shape)
    return tf.Variable(initial)
# 将最大池化操作、2d卷积操作封装在函数里
def conv2d(x, w):
    # 定义卷积的函数
    return tf.nn.conv2d(x, w, strides=[1, 1, 1, 1], padding='SAME')
def max_pooling(x):
    # 定义池化操作的函数
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')

```

定义卷积层的方法

```python
def convlutional(x, keep_prob):
    
    with tf.name_scope('first_layer'):
        
        w1 = weight_variable([3, 3, 1, 32])
        b1 = bias_variable([32])
        h1 = tf.nn.relu(conv2d(x, w1) + b1)
        p1 = max_pooling(h1)
        L1 = tf.nn.dropout(p1, keep_prob=keep_prob)
    
    with tf.name_scope('second_layer'):
        
        w2 = weight_variable([3, 3, 32, 64])
        b2 = bias_variable([64])
        h2 = tf.nn.relu(conv2d(L1, w2) + b2)
        p2 = max_pooling(h2)
        L2 = tf.nn.dropout(p2, keep_prob=keep_prob)
    
    with tf.name_scope('third_layer'):
        
        w3 = weight_variable([3, 3, 64, 128])
        b3 = bias_variable([128])
        h3 = tf.nn.relu(conv2d(L2, w3) + b3)
        p3 = max_pooling(h3)
        L3 = tf.nn.dropout(p3, keep_prob=keep_prob)
    
    with tf.name_scope('firth_layer'):
        
        L3_ = tf.reshape(L3, [-1, 128 * 4 * 4])
        w4 = weight_variable([128 * 4 * 4, 625])
        b4 = bias_variable([625])
        h4 = tf.nn.relu(tf.matmul(L3_, w4) + b4)
        L4 = tf.nn.dropout(h4, keep_prob=keep_prob)
    
    with tf.name_scope('output_layer'):
        
        w5 = weight_variable([625, 10])
        b5 = bias_variable([10])
        h5 = tf.nn.softmax(tf.matmul(L4, w5) + b5)
        
        output = h5
    return output

```

定义超参数、标签、梯度下降优化器、损失函数等。

```python
# 超参数定义
keep_prob = tf.placeholder(tf.float32)
learning_rate = 0.0001
max_step = 10000
# 输入数据
x = tf.placeholder(tf.float32, [None, 784])
x_in = tf.reshape(x, [-1, 28, 28, 1])
# 标签
y_ = tf.placeholder(tf.float32, [None, 10])
y = convlutional(x_in, keep_prob)
# 损失函数
cross_entropy = - tf.reduce_sum(y_ * tf.log(y))
train_step = tf.train.GradientDescentOptimizer(learning_rate).minimize(cross_entropy)
correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```

最终迭代代码如下：

```python
batch_size=100
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for i in range(max_step):
        idx = i%train_size
        sess.run( train_step, feed_dict={x: train_x[idx:idx+batch_size],y_: train_y[idx:idx+batch_size]} )
        if i % 100 == 0:
            acc =  sess.run( train_step, feed_dict={x: train_x[idx:idx+batch_size], y_: train_y[idx:idx+batch_size]} )
            print('准确率：'+str(acc))
            
    acc = []
    for i in range(max_step):
        batch = data.test.next_batch(50)
        a = sess.run( train_step, feed_dict={x: test_x[idx:idx+batch_size], y_: test_y[idx:idx+batch_size]} )
        acc.append(a)
    
    mean_acc = np.mean(acc)
    print('平均精度为：' + str(mean_acc))
```

展示部分结果：

```tex
准确率：0.14
准确率：0.26
准确率：0.54
......
准确率：0.88
准确率：0.9
准确率：0.94
准确率：0.98
平均精度为：0.9812
```

可以看到，通过卷积网络来改进后，最终精度有了不小的提高，达到了98%。

## 结论

通过以上分析和模型建立过程，可以看到，Tensorflow在对回归处理的高效之处，其是一个非常强大的用来做大规模数值计算的库，同时也利用Tensorflow来训练深度神经网络。

在模型中，如果使用回归模型并加以训练的话，我们的模型能达到90%的正确率，证明了模型有较高的准确性，但90%的正确率还是有不小提升空间的，在模型扩展中，通过卷积神经网络来改善，使得模型最终达到了98%的正确率，也证明了卷积神经网络在扫描图像、图像识别、图像处理等方面的高效性。

## 心得

在此次课设中，我学到了很多新的知识。

首先是TensorSlow的使用，无论是在实验课上，还是此次课设上，我都使用了TensorFlow进行了实践，在课设的选题上，由于自己对TensorSlow的应用不太清楚，自己上网搜索了下，大致可选题目可以分为两大类：房价预测、股票预测等预测性问题，和手写数字识别、图像分类识别等识别性问题。而自己对手写数字识别较为感兴趣，故选择了此题目。

尽管网络上关于利用TensorFlow进行手写数字识别的论文有很多，但自己在尝试数据处理、建立模型时也遇到了一些阻碍，具体如下：

* 在一开始使用 tf.placeholder时，发现会报错AttributeError: module 'tensorflow' has no attribute 'placeholder'，后面经过搜索发现，我tensorflow本地版本为2.0，而placeholder为1.0的API，故需要引入v1的库，即

  ```python
  import tensorflow.compat.v1 as tf
  tf.disable_v2_behavior()
  ```

* 在训练模型时，发现sess.run运行时报错，仔细研究后发现，sess.run中feed数据必须和placeholder的shape保持一致，而我一开始placeholder设置的行列数有所疏忽，后面经过调整，才得以解决。

* 在回归模型完成后，发现准确率为90%，存在着不小的失误率：

  ![](D:\Work\TyporaNotes\note\python\pict\1-14.png)

  后面通过查阅资料，使用了卷积神经网络来改进模型，准确率提升到了98%。

在本次课设中，无论是对TensorSlow库，还是对手写数字识别的技术，自己都有了更进一步的认识，在实践中也遇到一些问题，并通过自己的努力得以解决，希望在大四的毕设中，自己此次课设所学的知识能够有所应用。





