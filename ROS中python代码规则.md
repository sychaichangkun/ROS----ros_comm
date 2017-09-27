# ROS中Python代码规则

http://wiki.ros.org/PyStyleGuide

http://docs.ros.org/api/catkin/html/howto/format2/installing_python.html

在ROS项目中，有一些代码需要用到自定义的python模块，也就是有些ROS程序(python脚本)需要import自定义的python module。这就需要在ROS中组织好python的源代码。 而ROS并不是用python原生的module组织方式，而是对其进行了扩展。

ros_comm中tools下的命令行工具都用python实现，就是以上述方式实现。


--------
### 原生Python Module的实现方法

原本的python是可以自定义module的，需要在一个叫做模块名称的文件夹下建立__init__.py文件，这样python会认定这个文件夹是一个module。

module写法：https://stackoverflow.com/questions/15746675/how-to-write-a-python-module

------
### ROS中的Python Module实现方法

在ROS中，python的module有类似于原生module的组织方式：

两种建议的标准：
+ 不带msg/srv的小模块

```
packagename
| - src/
    | - packagename.py
| - scripts/
    | - non-exported python files
```

+ 带msg/srv的模块

```
packagename
| - src/
    | - packagename/
        | - __init__.py
        | - yourfiles.py
| - scripts/
    | - non-exported python files
```

所有python代码都要放在模块的命名空间下。 ROS会export我们的python源代码路径， 官方强烈建议这个模块的名称就用package的名称。

如果涉及msg/srv文件，必须用第二种方式，这时Python msg/srv的生成器会在你package的明明看你命名空间中生成文件。

---------
### 安装Python scripts和modules

即使你的package只有Python代码，它也需要一个`CMakeLlists.txt`文件来安装可执行脚本，并且export module，这样他们可以被import到其他的ROS package里。

##### Scripts

ROS可执行文件安装在每一个package的路径中，而不是distribution的 全局 bin路径下。 ROS可执行文件可以通过rosrun和roslaunch来运行，

标准ROS的做法是把所有的python可执行程序放在/scripts下， 为了用户API clean，可执行脚本一般不包括`.py`后缀。 你的CMakeLists.txt应该显示的安装脚本，用`catkin_install_python`安装函数。
```
catkin_install_python(PROGRAMS nodes/your_node scripts/another_script
                      DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
```

另一种好的做法是保证可执行脚本短小，把大多数代码都放到module里，在可执行脚本中import这个module然后调用
```
#! /usr/bin/env python
import your_package.main
if __name__ == '__main__':
    your_package.main()
```

##### Modules
