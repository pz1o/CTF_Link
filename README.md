# CTF_Link

记录一下一些比赛，附件没做出来的上传



## PWNhub五月内部赛

https://hexagonal-soursop-831.notion.site/PWNHUB-93f73ed8b00d4a8ea7ee0c76357d5a63

（VM逆向，onegadget写ret）

## DASCTF2022 MAY

https://hexagonal-soursop-831.notion.site/DASCTF-may-a87c7f0235694934a764c39ef38b2580

1. 山重水复：无show函数，off by one，需要构造tcache堆块重叠打tcache struct，两次爆破（看脸）第一次爆破tcache struct地址，第二爆破IO stdout，最后劫持exit hook即可
2. gift：格式化字符串，需要写堆指针和程序文件名，造成free错误从而stack smash带出flag（在2.23下有用）
3. twists and turns：负数溢出，可分配任意大小堆块，劫持tcache_struct，之后house of  kiwi过沙箱。

## CISCN 2022

考研摆烂人，属实有点寄

llvm pass差点把我pass掉，看了一下，等等别的大师傅的博客，或者哪天考研想摆烂了来重新看一下pass

偶然看到记录一下

[CISCN 2022 初赛 satool writeup – 数字的秘密基地 (lakwsh.net)](https://lakwsh.net/?p=457)

newest_note：libc 2.34 UAF，我直接狂喜，这不乱打，fastbin reverse to tcache + fastbin double free。后来发现限制了free次数。数组越界我确实没看到，一直在想怎么少free，感觉做不出来就得重新看是不是哪里少看了漏洞。第二天早上重新看，直接看到数组越界，下面说一下思路

https://hexagonal-soursop-831.notion.site/CISCN-2022-e4d13d876716434497bbc9ebed4e066e

1. login_normal：学弟手很快，直接拿字符shellcode拿了一血，我还在翻博客...
2. newest_note：数组越界，我们可以利用这个来完成edit功能，然后想到tcache double free，下面思路就清晰了，没开沙箱 我们可以不用kiwi，然后高版本ogg其实不太好用，FSOP也可以，但有点麻烦，好像是有个emma（还没细看）我这里用的是rop栈，总共11次free，我们tcache double free两次，一次泄露基地址，另一次直接分配到栈上 system sh即可

# PWNhub 六月

星期六学高数去了，星期一晚上做一下

**sandbox**

本来准备写libc_aexit，迁移栈，发现没有好用的gadget，于是想fsop

泄露基地址，可以往任意地方写0x220字节,写stdin结构体，同时写malloc_hook，利用IO_str_overflow的malloc调用setcontext，rop过沙箱拿到flag

https://hexagonal-soursop-831.notion.site/PWNHUB-5278a519b2a044feb9d42a52f4efa59e

