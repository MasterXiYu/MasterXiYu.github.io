---
layout: post
title: windows安装cuda
categories: bug解决
description: 技术杂谈
keywords: 深度学习，CUDA，CUDNN
---

之前用的深度学习训练都是Linux的显卡，cuda和cudnn都是厂家装好自己设置读取目录的，现在时代变了，
要用自己的windows了，还别说，费点功夫。

### 小心百度

百度现在很不好用，查出来的教程有三个问题：
1.过时，由于有些教程很早就发出来了，很多人点击，最新的教程反而没有人点击，推荐算法倾向于推荐旧的
教程，即使你用时间进行筛选，出来的教程也不如人意，像是显卡驱动这种一直在迭代，百度没有跟上。
2.重复率过大，同样的教程搬来搬去，首页十个结果5个一样，浪费我时间，我油猴子啥时候更新下脚本。
3.有问题，无解决方案。

### 下载CUDA和CUDNN

设备管理器直到自己的显卡型号，NVIDIA驱动程序-》帮助-》系统信息知道自己的驱动版本。然后根据自己的驱动版本
去找自己能下的最新的CUDA，根据CUDA去找自己能匹配的最新的tensorflow和CUDNN，为什么是要最新的？
确实，技术积累以前的技术文章更多，更好借鉴，但是都要淘汰的东西你学他干什么？

去NVIODIA官网下载CUDA和CUDNN，虽然在pycharm中的控制台我们用NVIDIA-smi能知道我们的显卡为2070且
显示11.2，但这不代表我们有了CUDA11.2。老实在官网注册账号并下载CUDA和CUDNN，注意他只给你看
最新版，旧版的在右上角older里面，我下载CUDA11.0和CUDNN8.0。

安装CUDA，解压CUDNN，并把CUDNN文件夹里面的.dll放到CUDA里相应的文件夹，由于安装完CUDA后他自己会
添加环境变量，所以不用管，打开pycharm，pip安装tensorflow：
``` git
pip3 install tensorflow-gpu==2.4.0
```

### 调试tensorflow
新建python文件判断环境是否良好，如果是.dll缺失考虑重启电脑或者换版本。
``` python
import tensorflow as tf
tf.test.is_gpu_available()
```
两条报错：
1.Not creating XLA devices, tf_xla_enable_xla_devices not set
2.None of the MLIR optimization passes are enabled (registered 2)

针对第一条：
``` python
import os
os.environ['TF_XLA_FLAGS'] = '--tf_xla_enable_xla_devices'
``` 
其实就是注册XLA设备而已，但在tf2.4.0版本后这个报错可以忽略无伤大雅，依然能够使用GPU加速。
针对第二条：
暂未找到解决方案，按照意思应该是，没有启用任何MLIR优化过程，依然可以忽略。直接就能跑。
试用一下代码试试加速：
``` python
import tensorflow as tf
import os
os.environ['TF_XLA_FLAGS'] = '--tf_xla_enable_xla_devices'

mnist = tf.keras.datasets.mnist

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0
model = tf.keras.models.Sequential([
	tf.keras.layers.Flatten(input_shape=(28, 28)),
	tf.keras.layers.Dense(128, activation='relu'),
	tf.keras.layers.Dropout(0.2),
	tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
			  loss='sparse_categorical_crossentropy',
			  metrics=['accuracy'])

model.fit(x_train, y_train, epochs=5)

model.evaluate(x_test,  y_test, verbose=2)
``` 
写下的hello world程序用了20s完成，还是挺快的，tf2.4.0直接集成了keras,不用session确实
舒服了一点，开始变得和scikit有点类似了，用起来很不错。只要切分数据集，设定模型，训练+评估，
封装的还是很到位的。

### 杂谈
1. 注意管理cuda版本，因为使用别人的代码时可能需要使用需要低版本的tensorflow，这个时候只用
选用Anacoda的虚拟环境了，但是cuda还得下一个10.0版本甚至9.0版本并使用，当然自己把代码改成高版本的也行，
但那样更麻烦，尤其是当tersorflow低于1.13的时候。
2. 注意tensorflow的API文档，2.0之后有大改，市面上的大部分水书都没有跟上时代。网上不少人甚至
降版本学习，有点本末倒置。多看看：

https://tensorflow.google.cn/api_docs/python/tf









