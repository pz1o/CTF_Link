# CTF_Link



## PWNhub五月内部赛

https://hexagonal-soursop-831.notion.site/PWNHUB-93f73ed8b00d4a8ea7ee0c76357d5a63（VM逆向，onegadget写ret）

## DASCTF2022 MAY

https://hexagonal-soursop-831.notion.site/DASCTF-may-a87c7f0235694934a764c39ef38b2580

1. 山重水复：无show函数，off by one，需要构造tcache堆块重叠打tcache struct，两次爆破（看脸）第一次爆破tcache struct地址，第二爆破IO stdout，最后劫持exit hook即可
2. gift：格式化字符串，需要写堆指针和程序文件名，造成free错误从而stack smash带出flag（在2.23下有用）
3. twists and turns：负数溢出，可分配任意大小堆块，劫持tcache_struct，之后house of  kiwi过沙箱。

