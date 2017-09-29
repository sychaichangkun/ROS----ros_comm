# roslaunch

roslaunch这个工具包实现了`roscore`,`roslaunch`这两个非常重要的命令行工具，用于启动ros的master以及其他node，
无论是`roscore`还是`roslaunch`启动时都会调用`rosmaster`这个module

roslaunch的具体工作是
```
import roslaunch
roslaunch.main()
```
而roscore执行的是roslaunch精简之后的部分
```
import roslaunch
roslaunch.main(['roscore', '--core'] + sys.argv[1:])
```

roslaunch.main()中，首先进行参数解析，规定了如下参数：
```
    usage = "usage: %prog [options] [package] <filename> [arg_name:=value...]\n"
    usage += "       %prog [options] <filename> [<filename>...] [arg_name:=value...]\n\n"
    usage += "If <filename> is a single dash ('-'), launch XML is read from standard input."
    parser = OptionParser(usage=usage, prog=NAME)
    parser.add_option("--files",
                      dest="file_list", default=False, action="store_true",
                      help="Print list files loaded by launch file, including launch file itself")
    parser.add_option("--args",
                      dest="node_args", default=None,
                      help="Print command-line arguments for node", metavar="NODE_NAME")
    parser.add_option("--nodes",
                      dest="node_list", default=False, action="store_true",
                      help="Print list of node names in launch file")
    parser.add_option("--find-node",
                      dest="find_node", default=None, 
                      help="Find launch file that node is defined in", metavar="NODE_NAME")
    parser.add_option("-c", "--child",
                      dest="child_name", default=None,
                      help="Run as child service 'NAME'. Required with -u", metavar="NAME")
    parser.add_option("--local",
                      dest="local_only", default=False, action="store_true",
                      help="Do not launch remote nodes")
    # #2370
    parser.add_option("--screen",
                      dest="force_screen", default=False, action="store_true",
                      help="Force output of all local nodes to screen")
    parser.add_option("-u", "--server_uri",
                      dest="server_uri", default=None,
                      help="URI of server. Required with -c", metavar="URI")
    parser.add_option("--run_id",
                      dest="run_id", default=None,
                      help="run_id of session. Required with -c", metavar="RUN_ID")
    # #1254: wait until master comes online before starting
    parser.add_option("--wait", action="store_true",
                      dest="wait_for_master", default=False,
                      help="wait for master to start before launching")
    parser.add_option("-p", "--port",
                      dest="port", default=None,
                      help="master port. Only valid if master is launched", metavar="PORT")
    parser.add_option("--core", action="store_true",
                      dest="core", default=False, 
                      help="Launch core services only")
    parser.add_option("--pid",
                      dest="pid_fn", default="",
                      help="write the roslaunch pid to filename")
    parser.add_option("-v", action="store_true",
                      dest="verbose", default=False,
                      help="verbose printing")
    # 2685 - Dump parameters of launch files
    parser.add_option("--dump-params", default=False, action="store_true",
                      dest="dump_params",
                      help="Dump parameters of all roslaunch files to stdout")
    parser.add_option("--skip-log-check", default=False, action="store_true",
                      dest="skip_log_check",
                      help="skip check size of log folder")
    parser.add_option("--ros-args", default=False, action="store_true",
                      dest="ros_args",
                      help="Display command-line arguments for this launch file")
    parser.add_option("--disable-title", default=False, action="store_true",
                      dest="disable_title",
                      help="Disable setting of terminal title")
    parser.add_option("-w", "--numworkers",
                      dest="num_workers", default=NUM_WORKERS, type=int,
                      help="override number of worker threads. Only valid for core services.", metavar="NUM_WORKERS")
    parser.add_option("-t", "--timeout",
                      dest="timeout",
                      help="override the socket connection timeout (in seconds). Only valid for core services.", metavar="TIMEOUT")
```

解析参数后，等待rosmaster运行起来，接着创建ROSLaunchParent对象
```
    ROSLaunchParent represents the main 'parent' roslaunch process. It
    is responsible for loading the launch files, assigning machines,
    and then starting up any remote processes. The __main__ method
    delegates most of runtime to ROSLaunchParent.

    This must be called from the Python Main thread due to signal registration.    
```
然后调用ROSLaunchParent对象的start方法。