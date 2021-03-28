---
description: 面试高频指数：★★★☆☆
---

# memcpy 函数的底层原理

`memcpy()`指的是C和C++使用的内存拷贝函数，函数原型为`void *memcpy(void *dst, void *src, size_t n)`函数的功能是从源内存地址的起始位置开始拷贝若干个字节到目标内存地址中，即从源source中拷贝n个字节到目标destin中。

在调用memcpy\(\)有三种情况:

1.  **src**所处内存与**dst**所处内存无重叠

![&#x56FE;1. &#x5185;&#x5B58;&#x4E0D;&#x91CD;&#x53E0;](../.gitbook/assets/image%20%284%29.png)

2. **src**所处内存与**dst**所处内存有重叠，但`&dst > &src`

![&#x56FE;2. &#x5185;&#x5B58;&#x91CD;&#x53E0;&#xFF0C;&#x4F46;&amp;dst &amp;lt; &amp;src](../.gitbook/assets/image%20%285%29.png)

3. **src**所处内存与**dst**所处内存有重叠，`(&src < &dst)&&(&src + size > &dst)` 

![&#x56FE;3. &#x5185;&#x5B58;&#x91CD;&#x53E0;&#xFF0C;&#x5E76;&#x4E14;\(&amp;src &amp;lt; &amp;dst\)&amp;&amp;\(&amp;src + size &amp;gt; &amp;dst\) ](../.gitbook/assets/image%20%286%29.png)

> 前两种情况可以直接从前按照memcpy的方式从前往后依次字节拷贝，第三种情况比较特殊，如果还是按照从前往后字节拷贝的话会造成数据覆盖，导致dst最后两个字节数据不是期望的值，所以要进行特殊处理。

```text
#include <iostream>
#include <string.h>

void *memcpy(void* dst, void* src, size_t size)
{
    char* psrc;
    char* pdst;
    if(src == NULL || dst == 0) return NULL;

    if((src < dst) && ((char*)src + size) > (char*)dst ){ //出现地址重叠的情况，自后向前拷贝
        psrc = (char*)src + size - 1;
        pdst = (char*)dst + size - 1;
        while(size--){
            *psrc-- = *pdst--;
        }
    }else{
        psrc = (char*)src;
        pdst = (char*)dst;
        while(size--){
            *pdst++ = *psrc++;
        }
    }
    return dst;
}

int main(int argc, char* argv[])
{
    const char *a = "hello world!";
    
    char b[20];

    memcpy(b, a, strlen(a));

    b[strlen(a)] = '\0';

    printf("%s", b);

    return 0;
}
```



> Reference: \[memcpy的区别以及处理内存重叠问题\]\([https://blog.csdn.net/weixin\_30662109/article/details/94968501?utm\_medium=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control&dist\_request\_id=&depth\_1-utm\_source=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control\)](https://blog.csdn.net/weixin_30662109/article/details/94968501?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control%29)



