# CTF_Link

记录一下一些比赛

## 2022强网杯

诸神黄昏，肝了一天肝出一道题来，

### **pokemongo**

由于一些傻逼问题，没拿到前三

我愿称之为go绝活哥

https://blog.stalkr.net/2022/01/universal-go-exploit-using-data-races.html?m=1

这个就是作者，只要读完这篇就可以做下这道题了，

回到题目本身，具体程序流程就是让你输入base64编码，然后解码就可以执行你写的go程序

**这道题的核心就是不引入外部包进行逃逸，从而getshell**，但这个题还有些问题我选择了ORW

这是原作者的代码，放链接了https://github.com/StalkR/misc/blob/master/go/gomium/exploit.go

具体来说就是利用race来劫持程序执行流

这道题还有很多坑的地方：

1. 远程环境是1.18的go，其中1.18的传参规则是发生变化的，本地测试了好多版本都可以通过，远程却无法通过
2. 你写的go程序会被执行，需要在执行ORW之后进行exit，否则执行程序的进程就会报错，导致无法输出
3. 玄学问题，无法启shell，可能子进程的问题？

EXP

放这里了

https://pz1o.notion.site/2022QWB-18f3e5a4b3c2439eb45aa500b4f7103b

![image-20220731183942231](README/image-20220731183942231.png)





## CISCN 2022 华东北

堆的狂欢，一个2.33，一个2.34直接house of kiwi一把梭

还有一个因为做misc去了 所以没时间做了，过几天复现

先上传了

https://hexagonal-soursop-831.notion.site/CISCN2022-fb473fa4662d4569aad0ed80e603f612

2022/7/5 

难得抽出一点时间去复现blue

https://pz1o.notion.site/CISCN2022-b9e08a8af21b4e37820b32fdd611256c

大概做了一个小时，先说漏洞没有edit

我们只有一次show和一次UAF的机会

泄露libc：那么只需要填满tcache，然后在申请释放就可以合并堆块，此时从中间分割出一个堆块就可以泄露libc

过沙箱：典型的FSOP等，这里用最朴素的ROP栈

构造任意地址写的话，常见就是写fd，我们可以在合并堆块里面申请堆块，形成overlap，从而任意地址写。

如何overlap，我们可以在上面合并堆块时用一次UAF，然后在合并堆块里面申请小堆块，从而overlap，简单来说就是有在unsortbin chunk里面有一个tcache bin的堆块，我们申请tcache bin的堆块，就可以任意写unsorted bin里面的堆块。

![image-20220705162616002](README/image-20220705162616002.png)

还有一点，我们只有一次show的机会，所以泄露栈时需要写以下stdout结构体，具体如下

```
flag = 0xfbad1800
write_base = environ
write_ptr & write_end = environ+8
```

## PWNhub 六月

星期六学高数去了，星期一晚上做一下

sandbox

本来准备写libc_aexit，迁移栈，发现没有好用的gadget，于是想fsop

泄露基地址，可以往任意地方写0x220字节,写stdin结构体，同时写malloc_hook，利用IO_str_overflow的malloc调用setcontext，rop过沙箱拿到flag

https://hexagonal-soursop-831.notion.site/PWNHUB-5278a519b2a044feb9d42a52f4efa59e



## CISCN 2022

考研摆烂人，属实有点寄

llvm pass差点把我pass掉，看了一下，等等别的大师傅的博客，或者哪天考研想摆烂了来重新看一下pass

偶然看到记录一下

[CISCN 2022 初赛 satool writeup – 数字的秘密基地 (lakwsh.net)](https://lakwsh.net/?p=457)

newest_note：libc 2.34 UAF，我直接狂喜，这不乱打，fastbin reverse to tcache + fastbin double free。后来发现限制了free次数。数组越界我确实没看到，一直在想怎么少free，感觉做不出来就得重新看是不是哪里少看了漏洞。第二天早上重新看，直接看到数组越界，下面说一下思路

https://hexagonal-soursop-831.notion.site/CISCN-2022-e4d13d876716434497bbc9ebed4e066e

1. login_normal：学弟手很快，直接拿字符shellcode拿了一血，我还在翻博客...
2. newest_note：数组越界，我们可以利用这个来完成edit功能，然后想到tcache double free，下面思路就清晰了，没开沙箱 我们可以不用kiwi，然后高版本ogg其实不太好用，FSOP也可以，但有点麻烦，好像是有个emma（还没细看）我这里用的是rop栈，总共11次free，我们tcache double free两次，一次泄露基地址，另一次直接分配到栈上 system sh即可

## DASCTF2022 MAY

https://hexagonal-soursop-831.notion.site/DASCTF-may-a87c7f0235694934a764c39ef38b2580

1. 山重水复：无show函数，off by one，需要构造tcache堆块重叠打tcache struct，两次爆破（看脸）第一次爆破tcache struct地址，第二爆破IO stdout，最后劫持exit hook即可
2. gift：格式化字符串，需要写堆指针和程序文件名，造成free错误从而stack smash带出flag（在2.23下有用）
3. twists and turns：负数溢出，可分配任意大小堆块，劫持tcache_struct，之后house of  kiwi过沙箱。

## PWNhub五月内部赛

https://hexagonal-soursop-831.notion.site/PWNHUB-93f73ed8b00d4a8ea7ee0c76357d5a63

（VM逆向，onegadget写ret）
