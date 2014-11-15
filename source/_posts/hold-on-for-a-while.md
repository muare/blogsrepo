title: 再坚持一下，可能就可以那
date: 2014-11-15 22:50:39
tags:
- computer vision
- patience
---
![坚持未必胜利，不坚持就只有失败](http://xintent-blog.qiniudn.com/xintent-blogs-photosimages.jpeg)
今天折腾Mac 上的opencv，整整一天，各种组合，各种姿势，差一点就放弃那，准备就在ubuntu的虚拟机里面混混，不过总有一点不甘心，mbp retina显示器看东西，写代码都明显是要舒服很多，不愿意就这么算鸟，顶一顶，结果在晚上10点左右总算找到方法。

先说结论

1. 关键点是安装正确的numpy包，这个是编译opencv需要依赖的
2. numpy安装方法比较多
	- pip/easy_install
	- github source code
	- brew
3. 要使用brew安装opencv，那么numpy也需要使用brew来安装
	- brew tap homebrew/python
	- brew install numpy
	- brew intall scipy
	- brew install matplotlib
<!--more-->

# 编译opencv 3.0 beta

和官网方法略有区别，区别在于生成配置过程中，加入的宏应该添加PYTHON2_LIBRARY和PYTHON2_INCLUDE_DIR，缺一不可。

如果缺少，最后不会生成opencv的python2模块，也就不会有cv2.so，那么在验证过程中就会出现找不到cv2的提示：
```python
$python
import cv2
```

具体命令如下：
```
$cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local  -D CMAKE_FIND_FRAMEWORK=LAST -D PYTHON2_LIBRARY=/usr/local/Cellar/python/2.7.8_2/Frameworks/Python.framework/Versions/2.7/lib/libpython2.7.dylib -D PYTHON2_INCLUDE_DIR=/usr/local/Cellar/python/2.7.8_2/Frameworks/Python.framework/Versions/2.7/Headers/ ..
```

# 崩溃之处

本来按照上面的方法，添加那cmake的两个宏开关，一路顺风顺水，生成那cv2.so，结果在验证过程中发现，会出现矩阵运算的assert问题，主要是c++的代码里面对于传出参数的校验有问题，直接断言出错。这下就傻眼那，非常沮丧，搞那大半天又冷又饿，英雄气短。翻那一下opencv的代码，也不少,短时间看完他们来排查问题是不现实的，这个时候必须运用脑力来想那。

1. 既然是opencv对于传入参数的校验
2. 参数来至于python
3. python负责矩阵运算的是numpy
4. numpy我在安装的时候，参考的是ScipySuperpack，它选择的是下载代码编译，而非brew，这就是这个问题的关键之处

解决方法就是先用pip uninstall numpy,然后添加home-brew/python tap,安装bumpy，依赖它的scipy，在顺势装上matplotlib，最后愉快的安装好opencv 2.4.9，✌️。
