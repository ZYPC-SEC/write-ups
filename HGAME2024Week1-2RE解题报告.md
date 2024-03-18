---
title: HGAME2024Week1-2个人解题报告
date: 2024-02-13 18:31:44
tags: 解题报告
mathjax: true
---

# HGAME2024Week1-2个人解题报告

## Week1

### ezIDA

IDA打开就看到。

### ezASM

将db区域定义的每个字节数据异或0x22即可。

### ezPYC

pyinstxtractor解包.exe，得到ezPYC改后缀.pyc。应该是3.10以上编译的，Magic Number为0xa7，从解包出来的其他.pyc中找到16字节文件头，再pycdc反汇编，发现简单异或加密。

### ezUPX

upx -d直接解包，发现简单异或加密。

### EzSignIn

nc连上就给。

### 签到

关注公众号然后HGAME2024。

### SignIn

直接画图调整图片尺寸，改水平缩放为500%能勉强看出来。

## Week2

其实从Week2往后都没有参加，以下两道是帮学长解的...

### ezcpp

4次TEA加密，只不过明文地址挺尴尬的。

```cpp
#include <cstdio>
using namespace std;
unsigned char enc[32] = {
    0x88, 0x6A, 0xB0, 0xC9, 0xAD, 0xF1, 0x33, 0x33, 0x94, 0x74, 0xB5, 0x69, 0x73, 0x5F, 0x30, 0x62, 
    0x4A, 0x33, 0x63, 0x54, 0x5F, 0x30, 0x72, 0x31, 0x65, 0x6E, 0x54, 0x65, 0x44, 0x3F, 0x21, 0x7D
};
void TEA_decrypt1(const int ptr1,const int ptr2){
    unsigned int sum=(-0x21524111)*32,enc1=0,enc2=0;
    enc1=(enc[ptr1])|(enc[ptr1+1]<<8)|(enc[ptr1+2]<<16)|(enc[ptr1+3]<<24),
    enc2=(enc[ptr2])|(enc[ptr2+1]<<8)|(enc[ptr2+2]<<16)|(enc[ptr2+3]<<24);
    unsigned int v0=enc1,v1=enc2;
    for(int i=0;i<32;i++){
        v1-=((((v0<<4)+3412)^(v0+sum)^((v0<<5)+4123)));
        v0-=((((v1<<4)+1234)^(v1+sum)^((v1<<5)+2341)));
        sum+=0x21524111;
    };
    enc[ptr1]=v0&0xFF,enc[ptr1+1]=(v0>>8)&0xFF,enc[ptr1+2]=(v0>>16)&0xFF,enc[ptr1+3]=(v0>>24)&0xFF,
    enc[ptr2]=v1&0xFF,enc[ptr2+1]=(v1>>8)&0xFF,enc[ptr2+2]=(v1>>16)&0xFF,enc[ptr2+3]=(v1>>24)&0xFF;
    return;
};
void TEA_decrypt2(const int ptr1,const int ptr2){
    unsigned int sum=0xDEADBEEF*32,enc1=0,enc2=0;
    enc1=(enc[ptr1])|(enc[ptr1+1]<<8)|(enc[ptr1+2]<<16)|(enc[ptr1+3]<<24),
    enc2=(enc[ptr2])|(enc[ptr2+1]<<8)|(enc[ptr2+2]<<16)|(enc[ptr2+3]<<24);
    unsigned int v0=enc1,v1=enc2;
    for(int i=0;i<32;i++){
        v1-=((((v0<<4)+3412)^(v0+sum)^((v0<<5)+4123)));
        v0-=((((v1<<4)+1234)^(v1+sum)^((v1<<5)+2341)));
        sum-=0xDEADBEEF;
    };
    enc[ptr1]=v0&0xFF,enc[ptr1+1]=(v0>>8)&0xFF,enc[ptr1+2]=(v0>>16)&0xFF,enc[ptr1+3]=(v0>>24)&0xFF,
    enc[ptr2]=v1&0xFF,enc[ptr2+1]=(v1>>8)&0xFF,enc[ptr2+2]=(v1>>16)&0xFF,enc[ptr2+3]=(v1>>24)&0xFF;
    return;
};
int main(void){
    TEA_decrypt2(3,7);
    TEA_decrypt2(2,6);
    TEA_decrypt2(1,5);
    TEA_decrypt1(0,4);
    for(register int i=0;i<32;i++)
        printf("%c",enc[i]);
    return 0;
};
```

### babyre

sem的4个函数调用没有确切顺序，所以题目出的不太好，不是每一次输入正确flag都能输出正确，选择爆破：

```cpp
#include <cstdio>
#include <cstring>
using namespace std;
unsigned int enc[33] = {
    0x00002F14, 0x0000004E, 0x00004FF3, 0x0000006D, 0x000032D8, 0x0000006D, 0x00006B4B, 0xFFFFFF92, 
    0x0000264F, 0x0000005B, 0x000052FB, 0xFFFFFF9C, 0x00002B71, 0x00000014, 0x00002A6F, 0xFFFFFF95, 
    0x000028FA, 0x0000001D, 0x00002989, 0xFFFFFF9B, 0x000028B4, 0x0000004E, 0x00004506, 0xFFFFFFDA, 
    0x0000177B, 0xFFFFFFFC, 0x000040CE, 0x0000007D, 0x000029E3, 0x0000000F, 0x00001F11, 0x000000FF,
    0x000000FA
};
unsigned char key[6] = {
    0x77, 0x74, 0x78, 0x66, 0x65, 0x69
};
bool chk(const char ch){
    char table[69];
    memcpy(table,"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789{}?!@_-",69*sizeof(char));
    for(register int i=0;i<strlen(table);i++)
        if(table[i]==ch)
            return true;
    return false;
};
void dfs(const int k){
    if(k<=-1){
        for(register int i=0;i<32;i++)
            printf("%c",enc[i]);
        putchar('\n');
        return;
    };
    int tmp=0,rec=enc[k];
    tmp=enc[k]-(key[(k+1)%6]*enc[k+1]);
    if(chk(tmp)==true){
        enc[k]=tmp;
        dfs(k-1);
        enc[k]=rec;
    };
    tmp=enc[k]+(key[(k+1)%6]^enc[k+1]);
    if(chk(tmp)==true){
        enc[k]=tmp;
        dfs(k-1);
        enc[k]=rec;
    };
    tmp=enc[k]/(enc[k+1]+key[(k+1)%6]);
    if(chk(tmp)==true){
        enc[k]=tmp;
        dfs(k-1);
        enc[k]=rec;
    };
    tmp=enc[k]^(enc[k+1]-key[(k+1)%6]);
    if(chk(tmp)==true){
        enc[k]=tmp;
        dfs(k-1);
        enc[k]=rec;
    };
    return;
};
int main(void){
    dfs(31);
    return 0;
};
```
