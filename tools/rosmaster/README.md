# rosmaster

rosmaster这个包实现了ROS当中的Master. Node与Master的通信调用了XMLRPC的API，因此ROS开发者不用指定通信的具体细节. 大多数程序运行时都也不会直接调用rosmaster，因为运行roscore时会自动启动master.

rosmaster这个工具包抛弃了之前的zenmaster，要启动master也不建议直接用rosmaster来启动，因为现在都用roscore或者roslaunch，他们会启动rosmaster以及rosout,param service。

rosmaster的可选参数有：
```
parser.add_option("--core",
                  dest="core", action="store_true", default=False,
                  help="run as core")
parser.add_option("-p", "--port",
                  dest="port", default=0,
                  help="override port", metavar="PORT")
parser.add_option("-w", "--numworkers",
                  dest="num_workers", default=NUM_WORKERS, type=int,
                  help="override number of worker threads", metavar="NUM_WORKERS")
parser.add_option("-t", "--timeout",
                  dest="timeout",
                  help="override the socket connection timeout (in seconds).", metavar="TIMEOUT")
```

配置好了参数，rosmaster会执行
```
master = rosmaster.master.Master(port, options.num_workers)
master.start()
import time
  while master.ok():
  time.sleep(.1)
```
从而开启master

在rosmaster这个module的master子模块里，定义了Master这个类，具有两个属性`port`和`num_workers`，
也就是指定要开的socket端口，以及工作线程数
