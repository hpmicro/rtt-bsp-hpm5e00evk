# lwIP PTP Slave V1

## 概述

本示例展示PTP Slave的功能

- Master与Slave通过PTP协议通讯，从而实现Slave端的时钟同步

## 硬件设置

- 使用USB Type-C线缆连接PC USB端口和PWR DEBUG端口
- 使用以太网线缆连接开发板RGMII或者RMII端口和另一块开发板的RGMII或RMII端口

## 编译设置

- 在双网口的开发板上，默认支持的是RGMII接口
- 在双网口的开发板上，仅支持一个网口，若需要使用另一个网口，可以通过如下两种方式之一：
    - RT-Thread Settings->硬件->芯片设备驱动->Enable Ethernet
    - menuconfig->Hardware Drivers Config->On-chip Peripheral Drivers->Enable Ethernet

## 运行示例

- 编译下载程序（J-Link JTAG）
- 串口终端显示（Slave 与 Master 直连以太网，使能 `LWIP_PTP` 硬件时间戳后）

```console
 \ | /
- RT -     Thread Operating System
 / | \     5.2.2 build Jul 18 2026 14:06:14
 2006 - 2024 Copyright by RT-Thread team
lwIP-2.1.2 initialized!
[29] I/sal.skt: Socket Abstraction Layer initialize success.
(D 0.006568860) event POWER UP
(D 0.009455180) initClock
(D 0.011859280) state PTP_INITIALIZING
(D 0.015406230) manufacturerIdentity: PTPd;1.0.1
(D 0.019821910) netShutdown
(D 0.022392230) netInit
(D 0.024810930) initData
(D 0.027117110) initTimer
(D 0.029503180) initClock
(D 0.031904610) sync message interval: 1000
(D 0.035883660) clock identifier: DFLT
(D 0.039415810) 256*log2(clock variance): -4000
(D 0.043737560) clock stratum: 255
(D 0.046917410) clock preferred?: no
(D 0.050274510) bound interface name:
(D 0.053802360) communication technology: 1
(D 0.057773880) uuid: 98:2c:bc:b1:9f:18
(D 0.061398530) PTP subdomain name: _DFLT
(D 0.065196110) subdomain address: 224.0.1.129
(D 0.069434730) event port address: 3f 1
(D 0.073169710) general port address: 40 1
(D 0.077056010) initClock
(D 0.079444260) state PTP_LISTENING
(D 0.082719230) initClock
(D 0.085103930) state PTP_SLAVE
msh />
[4122] I/NO_TAG: ENET0
[4125] I/NO_TAG: PHY Status: Link up
[4129] I/NO_TAG: PHY Speed: 1000Mbps
[4133] I/NO_TAG: PHY Duplex: full duplex

(D 4.337129310) addForeign: new record (0,1) 1 1 98:2c:bc:b1:9f:17
(D 4.343196130) event MASTER_CLOCK_CHANGED
(D 4.347090080) state PTP_UNCALIBRATED
(D 4.350626830) toState: Q = 0, R = 7
(D 528.500552070) updateOffset
(D 528.503389210) one-way delay:           0s      18900ns
(D 528.508680970) offset from master:      0s         66ns
(D 528.513972820) observed drift:          -5301
(D 529.505141570) updateOffset
(D 529.507984380) one-way delay:           0s      18900ns
(D 529.513277430) offset from master:      0s         23ns
(D 529.518572560) observed drift:          -5300
(D 530.501377280) updateOffset
(D 530.504224170) one-way delay:           0s      18895ns
(D 530.509516430) offset from master:      0s          4ns
(D 530.514809380) observed drift:          -5300
(D 531.500802830) updateOffset
(D 531.503638120) one-way delay:           0s      18895ns
(D 531.508929950) offset from master:      0s         -1ns
(D 531.514221780) observed drift:          -5300
(D 532.501235730) updateOffset
(D 532.504072250) one-way delay:           0s      18895ns
(D 532.509363470) offset from master:      0s          7ns
(D 532.514655230) observed drift:          -5300
(D 533.501667630) updateOffset
(D 533.504499050) one-way delay:           0s      18895ns
(D 533.509790470) offset from master:      0s          1ns
(D 533.515082300) observed drift:          -5300
(D 534.502107530) updateOffset
(D 534.504943050) one-way delay:           0s      18895ns
(D 534.510234850) offset from master:      0s         -2ns
(D 534.515526780) observed drift:          -5300
(D 535.502509240) updateOffset
(D 535.505343930) one-way delay:           0s      18895ns
(D 535.510636950) offset from master:      0s         -8ns
(D 535.515928760) observed drift:          -5300
(D 536.502900670) updateOffset
(D 536.505737910) one-way delay:           0s      18895ns
(D 536.511030280) offset from master:      0s         -7ns
(D 536.516322060) observed drift:          -5300
(D 537.503300750) updateOffset
(D 537.506138390) one-way delay:           0s      18895ns
(D 537.511430060) offset from master:      0s         -6ns
(D 537.516721870) observed drift:          -5300
(D 538.503737350) updateOffset
(D 538.506574520) one-way delay:           0s      18895ns
(D 538.511865640) offset from master:      0s         -5ns
```

同步稳定后，`offset from master` 可收敛并维持在约 ±100ns 以内（上例连续多次采样均在 100ns 内）。
