# ROS源代码阅读----ros_comm

ros_comm是ROS中的一个metapackage，它主要包括了ROS通信相关(计算图层面)的package，比如node,topic,service,param

具体有：

+ 客户端库(./clients): roscpp, rospy
c++和python两个版本的API

+ 通信查看工具(./tools): rostopic, rosnode, rosservice, rosparam
通信方面调试和查看的命令行工具

这些tools是ROS的命令行工具，及其执行的是script下面的脚本文件，而这些工具的源代码都是用python实现，具体说来是一个个python的module，被脚本调用。

除此之外还有一些测试用例(./test)和实用工具(./utilities)
