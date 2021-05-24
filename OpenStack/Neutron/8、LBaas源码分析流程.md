web操作页面对应命令行操作：

创建负载均衡器：neutron lbaas-loadbalancer-create

创建监听器：neutron lbaas-listener-create、neutron lbaas-pool-create

开启健康检查：neutron lbaas-healthmonitor-create

添加云主机：neutron lbaas-member-create

![img](assets/257d95b30b9c44f398e052683027648a.jpg)

**源码分析**

**同步数据库部分**

创建loadbalancer：

![img](assets/c6701e9d9fa4444aa648ec4d23b53f36.jpg)

创建listener：

![img](assets/0b8ba55c4b6a468989e3c2c01c084d68.jpg)

创建pool：

![img](assets/clipboard.png)

添加云主机：

![img](assets/766e92a1660f40b594c7940e4fe3e841.jpg)

创建函数调用过程：

LbaasAgentManager中对于loadbalancer、listener、pool、member、healthmonitor的create、update、delete都有对应的入口函数，对应调用LoadBalancerManager、ListenerManager、PoolManager、MemberManager、HealthMonitorManager。

![img](assets/601dda93c7b84ca6a043280c065e65fc.jpg)

进入相应的Manager后，有对应的create、update、delete方法。其中，对于Listener、Pool、Member、HealthMonitor的操作都需要经过LoadBalancerManager，对响应的负载均衡器进行refresh。

![img](assets/4a2beadc031b4a3b9d8e303ec4b5bd5a.jpg)

refresh函数中，有对应的调用HaproxyNSDriver，部署和解部署方法。

![img](assets/95fdc3f4170f41bf8a00f85b1200dfdd.jpg)

在部署（deploy）方法中有create和update。其中，create需要的_plug方法主要是获取信息和初始化网络。

![img](assets/c0cba52887e34f0ca85c41b9bdafcf74.jpg)

而_spawn方法则是配置haproxy.conf文件并保存，_spawn方法中包含了callback函数，用于审核对应信息，进行登记和保存。

![img](assets/6e49ecf778ca409e96344f07b4a06e3d.jpg)