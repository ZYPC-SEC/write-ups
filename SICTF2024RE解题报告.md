---
title: SICTF2024个人解题报告
date: 2024-02-23 19:28:03
tags: 解题报告
mathjax: true
---

# SICTF2024个人解题报告

## [签到]Baby_C++

就在flag数组里。

## Ez_pyc

简单代码阅读，发现是个数独，井号空下，0为要填的，解密得：

2456835127469673891564192578837194925486324875196156342796214853

python转16进制直接运行即可。

（好像后来附件换了，结果附件MD5估计是错的...

## ArtBreaker

IDA的Graph最大尺寸开成99999，转为图形界面即可。

## SweetTofu

动调，发现函数sub_55633523BB00，尝试解密。

```python
enc=[0x00,0x0A,0x07,0x01,0x1D,0x3F,0x09,0x13,0x39,0x07,0x08,0x02,0x39,0x2F,0x39,0x21,0x09,0x02,0x41,0x15,0x39,0x25,0x14,0x03,0x07,0x12,0x0F,0x09,0x08,0x47,0x22,0x09,0x08,0x41,0x12,0x39,0x04,0x03,0x39,0x15,0x03,0x14,0x0F,0x09,0x13,0x15,0x47,0x1B]
for i in range(len(enc)):
    print(chr(enc[i]^0x66),end='')
```

## Game

修改配置文件.ldtk，格式为json。看到4关的明明分别为Level_0~3，找到Entities属性，把IronWall Water Tree都改成fuck，然后Home元素周围3个相邻改为IronWall，这样程序无法识别fuck，全场除了Home周围都为空。

玩完之后给个win.png

## [签到]Vigenere

https://www.guballa.de/vigenere-solver 一把梭

## 日志分析2

看到access.log.1中从第20行开始一大堆POST请求就知道10.11.35.95为攻击主机，方式为暴力破解。从2199行开始出现各种SQL语句，怀疑为sqlmap，搜索字符串可得sqlmap 1.2.4.18。在22238行出现antSword/v2.1字样，即为蚁剑。

SICTF{10.11.35.95|暴力破解|sqlmap|1.2.4.18|蚁剑|2.1}

## 真的签到

把摩天轮截出来单独识图搜索，SICTF{广东省\_珠海市\_斗门区_大信新都汇}

发现这个：https://www.xiaohongshu.com/explore/633a9da2000000001d00ebcb

## 树木的压迫

把圈出来的部分截出来，百度识图，找到：SICTF{四川省\_达州市\_通川区\_凤凰大道376号\_达州市体育中心}

## [签到]OSINT签到

百度识图可得红城湖公园
