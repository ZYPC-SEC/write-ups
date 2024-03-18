---
title: BeginCTF2024个人解题报告
date: 2024-02-13 18:35:45
tags: 解题报告
mathjax: true
---

# BeginCTF2024个人解题报告

## xor

```c++
#include <cstdio>
#include <cstring>
using namespace std;
char inputStr4[32],key2_1[32],key2_2[32],key1_1[32],key1_2[32],inputStr3_1[32],inputStr3_2[32],inputStr2[32],inputStr1_1[32],inputStr1_2[32];
int main(void){
    strcpy(inputStr4,"`agh{^bvuwTooahlYocPtmyiijj|ek'p");
    strcpy(key2_1,"4180387362590136");
    strcpy(key2_2,"3092606632787947");
    strcpy(key1_1,"6329079420771558");
    strcpy(key1_2,"7679621386735000");
    for(register int i=0;i<16;i++)
        inputStr3_1[i]=inputStr4[i+16];
    for(register int i=0;i<16;i++)
        inputStr3_2[i]=inputStr4[i];
    for(register int i=0;i<16;i++)
        inputStr3_1[i]^=key2_1[16-i];
    for(register int i=0;i<16;i++)
        inputStr3_2[i]^=key2_2[16-i];
    for(register int i=0;i<16;i++)
        inputStr3_1[i]^=key2_2[16-i];
    for(register int i=0;i<16;i++)
        inputStr3_2[i]^=key2_1[16-i];
    for(register int i=0;i<16;i++)
        inputStr3_1[i]^=key2_1[i];
    for(register int i=0;i<16;i++)
        inputStr3_2[i]^=key2_2[i];
    for(register int i=0;i<16;i++)
        inputStr3_1[i]^=key2_2[i];
    for(register int i=0;i<16;i++)
        inputStr3_2[i]^=key2_1[i];
    for(register int i=0;i<16;i++)
        inputStr2[i]=inputStr3_1[i];
    for(register int i=0;i<16;i++)
        inputStr2[i+16]=inputStr3_2[i];
    for(register int i=0;i<16;i++)
        inputStr1_2[i]=inputStr2[i];
    for(register int i=0;i<16;i++)
        inputStr1_1[i]=inputStr2[i+16];
    for(register int i=0;i<16;i++)
        inputStr1_1[i]^=key1_1[16-i];
    for(register int i=0;i<16;i++)
        inputStr1_2[i]^=key1_2[16-i];
    for(register int i=0;i<16;i++)
        inputStr1_1[i]^=key1_2[16-i];
    for(register int i=0;i<16;i++)
        inputStr1_2[i]^=key1_1[16-i];
    for(register int i=0;i<16;i++)
        inputStr1_1[i]^=key1_1[i];
    for(register int i=0;i<16;i++)
        inputStr1_2[i]^=key1_2[i];
    for(register int i=0;i<16;i++)
        inputStr1_1[i]^=key1_2[i];
    for(register int i=0;i<16;i++)
        inputStr1_2[i]^=key1_1[i];
    printf("%s%s\n",inputStr1_1,inputStr1_2);
    return 0;
};
```

## 红白机

8085汇编指令，这里LDA含义是向A寄存器地址赋值，这个程序使用X寄存器进行像素地址遍历。“#$”开头为十六进制数据，“\$”为地址。

先用黑色清屏，之后指令主要有：

STA向地址立即数1+立即数2赋值寄存器X；INX寄存器X自加1；LDX寄存器X赋值。

从源代码37行开始，先提取所有数据为0的地址：

```python
f=open("inputFile.txt","r")
val_x=0
for i in range(369):
    opr=f.readline()
    if opr[:3]=='STA':
        print(int(opr[5:8],16)+val_x-0x200)
        continue
    if opr[:3]=='INX':
        val_x+=1
        continue
    if opr[:3]=='LDX':
        val_x=int(opr[6:],16)
        continue
f.close()
```

再绘制图形：

```c++
#include <cstdio>
using namespace std;
int pix[1001],n;
int main(void){
    freopen("dots.txt","r",stdin);
    for(register int i=0;i<169;i++){
        scanf("%d",&n);
        pix[n]=1;
    };
    for(register int i=0;i<1024;i++){
        if(pix[i]==0)
            putchar(' ');
        else
            putchar('#');
        if((i+1)%32==0)
            putchar('\n');
    };
    return 0;
};
```

大约长这样：

```
##  #           ##
 #  #           #   #  ###  #
### # ###  ###  #  # # #   # #
 #  # # #  # # ##  #   ##  # #
 #  # #### ###  #  ###   # # #
             #  #  # #   # # #
           ###  ##  #  ##   #

###     ###     #   ### # # ### 
  #      #      #   # # # # #
 ##      #      #   # # # # ###
##       #      #   # # # # #
#        #      #   # # # # #
### ### ### ### ### ###  #  ###


        ##
         #
         #
    # #  ##
    # #  #
    # #  #
### ### ##



                                




         #              # #
```

## Tupper

文件拼起来：

```python
s=''
for i in range(0,673,4):
    f=open(str(i)+".txt","r")
    s+=f.readline()
    f.close()
print(s)
```

Base64解码然后去这里：https://tuppers-formula.ovh/

## beginner_Forensics!!!!

出的烂题！

```python
# -*- coding: utf-8 -*-
#
# Batch Decryption 202009 (BatchEncryption Build 201610)
#
import os
def decryption(data):
    if not (data[0] == 0xFF and data[1] == 0xFE):
        print('Batch decryption bom error!')
        return
    if str(data[2:9], encoding="utf-8") != ' &cls\r\n':
        print('Batch decryption cls error!')
        return
    if str(data[9:60], encoding="utf-8") != '::BatchEncryption Build 201610 By gwsbhqt@163.com\r\n':
        print('Batch decryption build error!')
        return
    vars = {}
    # decryption line
    i = 60
    l = len(data)
    while i < l:
        i = run(vars, data, i)
def run(vars, data, i):
    buf = ''
    f = 0
    t = 0
    x = False
    l = len(data)
    while(True):
        if data[i] == 0x0d and data[i+1] == 0x0a:
            i += 2
            break
        # get %var:~x,y% %0
        if data[i] == 0x25:
            if not x:
                x = True
                f = i
            else:
                x = False
                t = i
                rst = var_percent(data[f:t+1], vars)
                buf += rst
        else:
            if not x:
                buf += str(data[i:i+1], encoding="utf-8")
            else:
                if (f + 1 == i) and ((data[i] >= 0x30 and data[i] <= 0x39) or data[i] == 0x2a):
                    x = False
                    t = i
                    rst = str(data[f:t+1], encoding="utf-8")
                    buf += rst
        i += 1
        if i >= l:
            break
    print(buf)
    bufs = buf.split('&@')
    for var in bufs:
        if var[0:4] == 'set ':
            var = var[4:]
            b = var.find('=')
            vars[var[0:b]] = var[b+1:].replace('^^^', '^')
    return i
def var_percent(data, vars):
    full = str(data, encoding="utf-8")
    buf = full[1:len(full)-1]
    buf = buf.split(':~')
    var = buf[0]
    if not var in vars:
        vars[var] = os.getenv(var)
    ent = vars[var]
    if (len(buf) > 1):
        l = len(ent)
        buf = buf[1].split(',')
        f = int(buf[0])
        t = int(buf[1])
        if f < 0:
            f, t = l + f, t
        rst = ent[f: f+t]
    else:
        rst = full
    return rst
encrypt_file = 'D:\\CTF-Workbench\\forensics'
if __name__ == '__main__':
    try:
        file = open(encrypt_file, "rb")
        data = file.read()
    except Exception as err:
        print('Batch decryption read error:', err)
        exit
    else:
        file.close()
    decryption(data)
```

## 俄语学习

有两道题中间有个rc4的初始化函数，后面flag通过rc4加密。但因为密文和输入的flag都被rc4加密过了，所以就省去了。

```python
ans=[0xA7,0xDF,0xA7,0xD6,0xA7,0xE9,0xA7,0xD6,0xA7,0xD4,0xA7,0xE0,0xA7,0xDF,0xA7,0xD6,0xA7,0xE9,0xA7,0xD6,0xA7,0xD4,0xA7,0xE0,0xA7,0xDF,0xA7,0xD6,0xA7,0xE9,0xA7,0xD6,0xA7,0xD4,0xA7,0xE0]
array6_rc4_key=[0 for i in range(len(ans))]
array1=[0 for i in range(len(ans))]
for i in range(len(ans)):
    array6_rc4_key[i]=ans[i]-ord('r')
for i in range(len(ans)):
    array1[i]=array6_rc4_key[i]
enc="+i&[@Y:g8[&l$f8S8v$Y&e>{"
enc=[ord(ch)for ch in enc]
for i in range(len(enc)):
    enc[i]+=ord('p')
    enc[i]-=array1[i]
    print(chr(enc[i]),end='')
```

## 逆向工程(reverse)入门指南

```bash
pdftotext asdf.pdf 1.txt
cat 1.txt
```

## where is crazyman v1.0

看到图中有秋叶原店。

## zupload

```
?action=../../../../flag
```

## zupload-pro

传一句话木马exp.zip，bp抓包改后缀，访问uploads路由，蚁剑连接即可。

## zupload-pro-plus

传一句话木马为exp.zip.zip，抓包改后缀为exp.zip.php，蚁剑连接。

## 出题人的密码是什么

一大堆嵌套的空函数里藏了俩加密函数，先逆第二个：

```python
enc=[0xB4,0xBB,0xD8,0xEB,0xD0,0x6E,0xAB,0xCA,0x65,0x8E,0x4B,0xE9,0x4D,0xD4,0x4A,0xF3,0x7D,0x29,0xC2,0xF9,0x95,0x89,0xA4,0x85,0x9D,0xCD,0xDF,0x77,0xFD,0x45,0xCB,0x5D,0x7D,0xFD,0x93,0x4B,0xBC,0xF6,0x7C,0xF3,0x24,0x42,0xF5,0xD2,0xDD,0xE3,0x56,0xAE]
for i in range(len(enc)):
    enc[i]=((enc[i]^0x25)-5)%256
for i in range(0,48,8):
    tmp=enc[i:i+8]
    int_data=int.from_bytes(tmp,byteorder='little',signed=False)
    print(str(hex(int_data)),end=',')
```

再逆第一个：

```c++
#include <cstdio>
using namespace std;
unsigned long long int enc[6]={0xea8946f0c9f8998c,0xd16aec63c769a63b,0x9b7ca7abd7e20753,0x73e95bd34df5e3b3,0xd154ce9469b1d353,0x866ec1f3f2cb62fc},v1=0x33077d;
int main(void){
    for(register int i=0;i<6;i++){
        for(register int j=0;j<64;j++)
            if(enc[i]&0x01==1)
                enc[i]=((enc[i]^v1)>>1)|0x8000000000000000;
            else
                enc[i]>>=1;
        printf("%llx",enc[i]);
    };
    return 0;
};
```

## devil's word

```
leu 6
lia 2
ng 5
cai 7
jau 9
sa 3
leng 0
bo 8
sii 4
```

替换后十六进制转字符串即可。
