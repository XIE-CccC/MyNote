Open vSwitch是在开源Apache2.0许可下的产品级质量的多层虚拟交换标准，旨在通过编程扩展，使庞大的网咯自动化（配置、管理、维护），同时还支持标准的管理接口和协议

安全：VLAN隔离、流量过滤  监控：NetFlow、Qos、自动控制

**ovs交换机的角色**

在SDN的架构下，ovs作为SDN交换机，向上连接控制器，向下连接主机。并且Open vSwitch交换机是能够与真实物理机交换通信，相互交流数据。

**ovs交换机的组成**

![img](assets/pu23yoc`z[aaz`}vny@0.png)

**ovs-vswitchd：**ovs守护进程，实现基于流的交换,实现内核datapath upcall 处理以及ofproto 查表，同时是dpdk datapath处理程序。与ovsdb-server通信使用OVSDB协议，与内核模块使用netlink机制通信，与controller通信使用OpenFlow协议。

**ovsdb-server：**OVS轻量级的数据库服务器的服务程序，用于保存整个OVS的配置信息。数据库服务程序, 使用目前普遍认可的ovsdb 协议。

**ovs-vsctl：**网桥、接口等的创建、删除、设置、查询等。 

**ovs-dpctl：**配置vswitch内核模块

**ovs-appctl：**发送命令消息到ovs-vswithchd, 查看不同模块状态

**ovs-ofctl：**下发流表信息。该命令可以配置其他openflow 交换机（采用openflow 协议）

**datapath:** Datapath把流的match和action结果缓存，避免后续同样的流继续upcall到用户空间进行流表匹配。

 **ovs-db：**开放虚拟交换机数据库是一种轻量级的数据库，它是一个JSON文件，默认路径:/etc/openvswitch/conf.db;

每一个ovs交换机中，数据库中存在的表如下：

![img](assets/1060878-20190601123518263-731585823.png)

**数据包处理流程**

1、ovs的datapath接收到从ovs连接的某个网络设备发来的数据包，从数据包中提取源/目的IP、源/目的MAC、端口等信息。

2、ovs在内核状态下查看流表结构（通过Hash），观察是否有缓存的信息可用于转发这个数据包。

3、假设数据包是这个网络设备发来的第一个数据包，在OVS内核中，将不会有相应的流表缓存信息存在，那么内核将不会知道如何处置这个数据包。所以内核将发送upcall给用户态。

4、ovs-vswitchd进程接收到upcall后，将检查数据库以查询数据包的目的端口是哪里，然后告诉内核应该将数据包转发到哪个端口，例如eth0。

5、内核执行用户此前设置的动作。即内核将数据包转发给端口eth0，进而数据被发送出去。

**ovs在OpenStack中的应用**

OpenStack中的网桥br-int、br-tun、br-ex都由ovs创建，其中保存流表，用于指定数据流方向

![img](assets/1060878-20190601123555808-1514748166.png)
