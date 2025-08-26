# 网络连通性及吞吐性能测试

## 概述

本示例演示了基于RT-Thread 网络连通性测试和吞吐性能测试

## 硬件设置

* 使用USB Type-C线缆连接PC USB端口和PWR DEBUG端口
* 使用以太网线缆连接PC以太网端口和开发板RGMII或RMII端口

## 软件设置

* 使用flash_release编译运行，可以获得更好的网络性能

## 运行示例

* 编译下载程序
* 串口终端显示

```console
 \ | /
- RT -     Thread Operating System
 / | \     5.0.2 build Jul 14 2025 16:25:04
 2006 - 2022 Copyright by RT-Thread team
lwIP-2.1.2 initialized!
[29] I/sal.skt: Socket Abstraction Layer initialize success.
msh />[4038] I/NO_TAG: ENET0
[4041] I/NO_TAG: PHY Status: Link up
[4045] I/NO_TAG: PHY Speed: 1000Mbps
[4049] I/NO_TAG: PHY Duplex: full duplex
```

## 功能验证

### 1. IP分配查询及DHCP状态确认

```console
msh />ifconfig
network interface device: ET (Default)
MTU: 1500
MAC: 98 2c bc b1 9f 17
FLAGS: UP LINK_UP INTERNET_DOWN DHCP_ENABLE ETHARP BROADCAST
ip address: 192.168.100.6
gw address: 192.168.100.1
net mask  : 255.255.255.0
dns server #0: 192.168.100.1
dns server #1: 0.0.0.0

```

**注： 若DHCP开启，则DHCP状态为“DHCP_ENABLE”，需要将网口连接至路由器或具有DHCP服务的PC  **   

### 2. PING测试

  （1）Windows系统中，打开cmd, 运行ping

```console
C:\Users>ping 192.168.100.6

正在 Ping 192.168.100.6 具有 32 字节的数据:
来自 192.168.100.6 的回复: 字节=32 时间<1ms TTL=255
来自 192.168.100.6 的回复: 字节=32 时间<1ms TTL=255
来自 192.168.100.6 的回复: 字节=32 时间<1ms TTL=255
来自 192.168.100.6 的回复: 字节=32 时间<1ms TTL=255

192.168.100.6 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 0ms，最长 = 0ms，平均 = 0ms
```

  （2）开发板Ping PC

```console
msh />ping 192.168.100.5
ping: not found specified netif, using default netdev ET.
60 bytes from 192.168.100.5 icmp_seq=0 ttl=64 time=0 ms
60 bytes from 192.168.100.5 icmp_seq=1 ttl=64 time=0 ms
60 bytes from 192.168.100.5 icmp_seq=2 ttl=64 time=0 ms
60 bytes from 192.168.100.5 icmp_seq=3 ttl=64 time=0 ms

```

### 3. **iperf测试**

- **TCP服务端模式**

  - MCU端输入命令

    ```console
    msh /> iperf -s
    ```

  -  PC端输入命令

    ```console
    C:\Users>iperf -c 192.168.100.6 -i 1
    ```

  - 观察PC端结果

    ```console
    ------------------------------------------------------------
    Client connecting to 192.168.100.6, TCP port 5001
    TCP window size: 64.0 KByte (default)
    ------------------------------------------------------------
    [360] local 192.168.100.5 port 56135 connected with 192.168.100.6 port 5001
    [ ID] Interval       Transfer     Bandwidth
    [360]  0.0- 1.0 sec  30.8 MBytes   258 Mbits/sec
    [360]  1.0- 2.0 sec  34.6 MBytes   291 Mbits/sec
    [360]  2.0- 3.0 sec  37.8 MBytes   317 Mbits/sec
    [360]  3.0- 4.0 sec  38.1 MBytes   320 Mbits/sec
    [360]  4.0- 5.0 sec  38.1 MBytes   319 Mbits/sec
    [360]  5.0- 6.0 sec  38.9 MBytes   326 Mbits/sec
    [360]  6.0- 7.0 sec  39.0 MBytes   327 Mbits/sec
    [360]  7.0- 8.0 sec  38.9 MBytes   326 Mbits/sec
    [360]  8.0- 9.0 sec  38.4 MBytes   322 Mbits/sec
    [360]  9.0-10.0 sec  37.1 MBytes   312 Mbits/sec
    [360]  0.0-10.0 sec   372 MBytes   311 Mbits/sec
    ```

  - 观察MCU端结果

    ```console
    msh />[13819] I/iperf: new client connected from (192.168.100.5, 56135)
    [18826] I/iperf: iperfd01: 301.4200 Mbps!
    [23833] I/iperf: iperfd01: 321.7510 Mbps!
    [23839] W/iperf: client disconnected (192.168.100.5, 56135)
  ```
    
    

- **TCP客户端模式**

  - PC端输入命令

    ```console
    C:\Users>iperf -s -i 1
    ```

  - MCU端输入命令

    ```console
    msh />iperf -c 192.168.100.5
    ```

  - 观察PC端结果

    ```console
    ------------------------------------------------------------
    Server listening on TCP port 5001
    TCP window size: 64.0 KByte (default)
    ------------------------------------------------------------
    [412] local 192.168.100.5 port 5001 connected with 192.168.100.6 port 52432
    [ ID] Interval       Transfer     Bandwidth
    [412]  0.0- 1.0 sec  34.7 MBytes   291 Mbits/sec
    [412]  1.0- 2.0 sec  36.4 MBytes   305 Mbits/sec
    [412]  2.0- 3.0 sec  35.4 MBytes   297 Mbits/sec
    [412]  3.0- 4.0 sec  34.7 MBytes   291 Mbits/sec
    [412]  4.0- 5.0 sec  32.9 MBytes   276 Mbits/sec
    [412]  5.0- 6.0 sec  35.0 MBytes   294 Mbits/sec
    [412]  6.0- 7.0 sec  36.8 MBytes   309 Mbits/sec
    [412]  7.0- 8.0 sec  32.8 MBytes   275 Mbits/sec
    [412]  8.0- 9.0 sec  34.0 MBytes   285 Mbits/sec
    [412]  9.0-10.0 sec  33.8 MBytes   284 Mbits/sec
    [412] 10.0-11.0 sec  33.5 MBytes   281 Mbits/sec
    [412] 11.0-12.0 sec  34.3 MBytes   288 Mbits/sec
    [412] 12.0-13.0 sec  34.5 MBytes   289 Mbits/sec
    [412] 13.0-14.0 sec  33.2 MBytes   279 Mbits/sec
    [412] 14.0-15.0 sec  34.8 MBytes   292 Mbits/sec
    [412] 15.0-16.0 sec  33.4 MBytes   280 Mbits/sec
    [412] 16.0-17.0 sec  34.1 MBytes   286 Mbits/sec
    [412] 17.0-18.0 sec  35.3 MBytes   296 Mbits/sec
    [412] 18.0-19.0 sec  36.4 MBytes   305 Mbits/sec
    [412] 19.0-20.0 sec  34.7 MBytes   291 Mbits/sec
    ```

  - 观察MCU端结果

    ```console
    msh /[17583] I/iperf: Connect to iperf server successful!
    >[22588] I/iperf: iperfc01: 292.1520 Mbps!
    [27588] I/iperf: iperfc01: 289.3610 Mbps!
    [32588] I/iperf: iperfc01: 285.8350 Mbps!
    [37588] I/iperf: iperfc01: 291.7200 Mbps!
    ```

- **UDP服务端模式**

  - MCU端输入命令

    ```console
    msh />iperf -u -s
    ```

  - PC端输入命令

    ```console
    C:\Users>iperf -u -c 192.168.100.6 -i 1 -b 1000M -t 20
    ```

  - 观察PC端结果

    ```console
    ------------------------------------------------------------
    Client connecting to 192.168.100.6, UDP port 5001
    Sending 1470 byte datagrams
    UDP buffer size: 64.0 KByte (default)
    ------------------------------------------------------------
    [360] local 192.168.100.5 port 55498 connected with 192.168.100.6 port 5001
    [ ID] Interval       Transfer     Bandwidth
    [360]  0.0- 1.0 sec   102 MBytes   854 Mbits/sec
    [360]  1.0- 2.0 sec  95.3 MBytes   800 Mbits/sec
    [360]  2.0- 3.0 sec  95.7 MBytes   803 Mbits/sec
    [360]  3.0- 4.0 sec  99.8 MBytes   837 Mbits/sec
    [360]  4.0- 5.0 sec  97.2 MBytes   815 Mbits/sec
    [360]  5.0- 6.0 sec  92.9 MBytes   780 Mbits/sec
    [360]  6.0- 7.0 sec  94.7 MBytes   795 Mbits/sec
    [360]  7.0- 8.0 sec  96.7 MBytes   811 Mbits/sec
    [360]  8.0- 9.0 sec  95.2 MBytes   798 Mbits/sec
    [360]  9.0-10.0 sec  95.7 MBytes   803 Mbits/sec
    [360] 10.0-11.0 sec  94.5 MBytes   793 Mbits/sec
    [360] 11.0-12.0 sec  92.9 MBytes   779 Mbits/sec
    [360] 12.0-13.0 sec  92.9 MBytes   779 Mbits/sec
    [360] 13.0-14.0 sec  91.5 MBytes   767 Mbits/sec
    [360] 14.0-15.0 sec   102 MBytes   858 Mbits/sec
    [360] 15.0-16.0 sec   101 MBytes   849 Mbits/sec
    [360] 16.0-17.0 sec  99.6 MBytes   835 Mbits/sec
    [360] 17.0-18.0 sec   102 MBytes   857 Mbits/sec
    [360] 18.0-19.0 sec   100 MBytes   841 Mbits/sec
    [360] 19.0-20.0 sec  87.4 MBytes   734 Mbits/sec
    [ ID] Interval       Transfer     Bandwidth
    [360]  0.0-20.0 sec  1.88 GBytes   809 Mbits/sec
    [360] WARNING: did not receive ack of last datagram after 10 tries.
    [360] Sent 1376598 datagrams
    ```

  - 观察MCU端结果

    ```console
    msh />[99519] I/iperf: iperfd01: 24.6040 Mbps! lost:24740 total:35200
    
    [104525] I/iperf: iperfd01: 298.4470 Mbps! lost:221713 total:348604
    
    [109532] I/iperf: iperfd01: 312.1990 Mbps! lost:205946 total:338684
    
    [114539] I/iperf: iperfd01: 306.1850 Mbps! lost:210402 total:340583
    
    [119789] I/iperf: iperfd01: 256.6510 Mbps! lost:-2554090 total:-2439669
    
    ```
    
    

- UDP客户端模式

  - PC端输入命令

    ```console
    C:\Users>iperf -u -s -i 1
    ```

  - MCU端输入命令

    ```console
    msh />iperf -u -c 192.168.100.5
    ```

  - 观察MCU端结果

    ```console
    ------------------------------------------------------------
    Server listening on UDP port 5001
    Receiving 1470 byte datagrams
    UDP buffer size: 64.0 KByte (default)
    ------------------------------------------------------------
    [344] local 192.168.100.5 port 5001 connected with 192.168.100.6 port 62510
    [ ID] Interval       Transfer     Bandwidth       Jitter   Lost/Total Datagrams
    [344]  0.0- 1.0 sec  72.7 MBytes   610 Mbits/sec  0.015 ms 1550340/1602212 (97%)
    [344]  1.0- 2.0 sec  72.6 MBytes   609 Mbits/sec  0.030 ms    5/51812 (0.0097%)
    [344]  2.0- 3.0 sec  72.7 MBytes   610 Mbits/sec  0.045 ms    0/51846 (0%)
    [344]  3.0- 4.0 sec  72.7 MBytes   610 Mbits/sec  0.035 ms    0/51853 (0%)
    [344]  4.0- 5.0 sec  72.0 MBytes   604 Mbits/sec  0.009 ms  451/51825 (0.87%)
    [344]  5.0- 6.0 sec  72.6 MBytes   609 Mbits/sec  0.033 ms    0/51820 (0%)
    [344]  6.0- 7.0 sec  72.7 MBytes   610 Mbits/sec  0.027 ms    0/51853 (0%)
    [344]  7.0- 8.0 sec  72.6 MBytes   609 Mbits/sec  0.058 ms    0/51821 (0%)
    [344]  8.0- 9.0 sec  70.8 MBytes   594 Mbits/sec  0.018 ms 1278/51811 (2.5%)
    [344]  9.0-10.0 sec  72.7 MBytes   610 Mbits/sec  0.019 ms    0/51850 (0%)
    [344] 10.0-11.0 sec  72.7 MBytes   610 Mbits/sec  0.030 ms    0/51840 (0%)
    [344] 11.0-12.0 sec  72.1 MBytes   605 Mbits/sec  0.012 ms  390/51826 (0.75%)
    [344] 12.0-13.0 sec  72.5 MBytes   608 Mbits/sec  0.012 ms   98/51827 (0.19%)
    [344] 13.0-14.0 sec  71.6 MBytes   601 Mbits/sec  0.020 ms  750/51835 (1.4%)
    [344] 14.0-15.0 sec  72.2 MBytes   605 Mbits/sec  0.032 ms  355/51841 (0.68%)
    [344] 15.0-16.0 sec  72.1 MBytes   605 Mbits/sec  0.008 ms  407/51829 (0.79%)
    [344] 16.0-17.0 sec  72.6 MBytes   609 Mbits/sec  0.059 ms   20/51818 (0.039%)
    [344] 17.0-18.0 sec  72.4 MBytes   608 Mbits/sec  0.053 ms  163/51840 (0.31%)
    [344] 18.0-19.0 sec  72.2 MBytes   605 Mbits/sec  0.012 ms  365/51839 (0.7%)
    [344] 19.0-20.0 sec  71.3 MBytes   598 Mbits/sec  0.014 ms  978/51836 (1.9%)
    ```

    

  - 观察MCU端结果

    ```console
    [66131] I/iperf: iperf udp mode run...
    ```

    **注：此模式下，MCU端无统计信息输出，且无退出机制，需要按reset键重启MCU。**

  

  

