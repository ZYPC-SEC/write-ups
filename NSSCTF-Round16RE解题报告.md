---
title: NSSCTF-Round16个人解题报告
date: 2024-01-13 15:46:15
tags: 解题报告
mathjax: true
---

# NSSCTF-Round#16 Basic个人解题报告

## test your Debugger

三血！

很简单一个动调，动起来就行了。

## CompileMe!!!

二血！

一个自称为.NET8.0框架的C#工程，改成7.0运行发现类嵌套过多，爆栈了...

改写成C语言发现gcc不理我了，改成Python竟然也爆栈。

搓一个Python脚本把每个return的操作扒下来，记到文件里。

```python
f=open('Program.cs',encoding='utf-8')
content=f.readlines()
ptr=52
res=[0 for i in range(0,20000)]
res[0]=content[ptr][26:45]
fin=res[0]
for i in range(1,18278):
    ptr+=9
    res[i]=content[ptr][26:45]
f.close()
f3=open("res.txt",'w')
for i in range(0,18278):
    f3.write(res[i][0]+' '+str(int(res[i][4:19],16))+'\n')
f3.close()
```

改写成C语言：

```cpp
#include <cstdio>
#include <iostream>
using namespace std;
const unsigned long long keys[4]={0x57656c636f6d6520, 0x746f204e53534354, 0x4620526f756e6423, 0x3136204261736963},delta=0x9E3779B9;
unsigned long long enc[8]={0xc60b34b2bff9d34a, 0xf50af3aa8fd96c6b, 0x680ed11f0c05c4f1, 0x6e83b0a4aaf7c1a3, 0xd69b3d568695c3c5, 0xa88f4ff50a351da2, 0x5cfa195968e1bb5b, 0xc4168018d92196d9};
inline unsigned long long getres(unsigned long long int val){
    freopen("res.txt","r",stdin);
    char opr;
    unsigned long long tmp;
    for(int i=0;i<18278;i++){
        cin>>opr>>tmp;
        switch(opr){
            case '+':
                val+=tmp;
                break;
            case '-':
                val-=tmp;
                break;
            case '^':
                val^=tmp;
                break;
            default:
                break;
        };
    };
    fclose(stdin);
    return val;
};
void TEA_decrypt(unsigned long long enc1,unsigned long long enc2){
    unsigned long long v0=enc1,v1=enc2,sum=32*delta;
    for(int i=0;i<32;i++){
        v1 -= ((( v0 << 4) ^ ( v0 >> 5)) + v0 ) ^ ( sum + keys [( sum >> 11) & 3]);
        sum -= delta ;
        v0 -= ((( v1 << 4) ^ ( v1 >> 5)) + v1 ) ^ ( sum + keys [ sum & 3]);
    };
    printf("%16llx%16llx",getres(v0),getres(v1));
    return;
};
int main(void){
    for(int i=0;i<8;i+=2)
        TEA_decrypt(enc[i],enc[i+1]);
    return 0;
};
```

## nc_pwnre

nc上去给汇编阅读题，发现简单加密逻辑，输入假flag后得shell，然后cat /flag。
