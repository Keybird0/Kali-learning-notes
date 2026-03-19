## CTF 基础学习向导
> 方便自己回顾学习, 差缺补漏之用. 即使目前还很难在各ctf 比赛中存活, 有空之时还是要提醒自己不要放弃, 坚持学习.

> [CTF初识与深入](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247484446&idx=1&sn=21f929d5f230c41dc70a3120d7dba9d4&chksm=ec1e3436db69bd20cd2ac8a711edf2d1ebd8b0d83b0694a2e5d57eb2c79bf6c6315c891d2317&scene=21#wechat_redirect)

### 工具学习
> 许多工具地址: https://github.com/zardus/ctf-tools

1. BurpSuit [Burp Suite 实战指南](https://t0data.gitbooks.io/burpsuite/content/)
2. 一些后门工具
    - [Webshell 后门介绍](https://www.secfree.com/756.html)
    - [Weevely](https://github.com/epinna/weevely3) (项目地址)| [weevely入手使用参考](http://www.cnblogs.com/lingerhk/p/4009073.html)
    - [中国菜刀使用](https://blog.csdn.net/lixue20141529/article/details/78024525)
3. IDA
    - [IDA的使用](http://www.freebuf.com/column/157939.html)
    - [IDA的基础汇编](https://blog.csdn.net/re_psyche/article/details/78797689)
    - [使用详例](https://blog.csdn.net/ilnature2008/article/details/54912854)
4. Pwndbg
    - [安装](https://www.jianshu.com/p/47883d16fa30)
    - [使用实例](https://baijiahao.baidu.com/s?id=1594176337945872202&wfr=spider&for=pc)
5. AndroidKiller与JEB
    - 集成工具: [Android 「BombAPK」集成常用工具的脚本](https://www.bodkin.ren/index.php/archives/552/)
    - [AndroidKiller安装设置及使用教程](https://www.52pojie.cn/thread-726176-1-1.html)
    - [了解JEB和IDA使用](https://www.52pojie.cn/thread-414414-1-1.html) | [JEB动态调试apk](https://www.jianshu.com/p/8e8ed503d69b)
    - [Android App常见逆向工具和使用技巧](https://www.anquanke.com/post/id/84776)
6. 古典密码学与编码工具
    - [CTFcrackTools-V3.1](https://github.com/0Chencc/CTFCrackTools)
    - 在线: [ctf编码算法与杂项(很全, 推荐)](http://ctf.sslebye.com/) | [CTFtools](https://www.ctftools.com/down/) | [BugKu-tools](http://tool.bugku.com/) | [CyberChef](https://gchq.github.io/CyberChef/)

### PWN 赛题解析
> [Pwn-知识](https://ctf-wiki.github.io/ctf-wiki/pwn/stackoverflow/stack_intro/)


### 逆向题解析
> [逆向工程-知识](https://ctf-wiki.github.io/ctf-wiki/reverse/introduction/)

1. 安卓逆向
    - [安卓逆向基础系列](https://www.jianshu.com/p/d37c91ff1748)
    - [CTF wiki-Android](https://ctf-wiki.github.io/ctf-wiki/android/basic_develop/basic_develop/)

### Web 题解析

1. PHP弱类型
    - [CTF之PHP黑魔法总结](https://www.cnblogs.com/xishaonian/p/7628152.html)
    - [php 弱类型总结](https://www.cnblogs.com/Mrsm1th/p/6745532.html)
    - [CTF中常见的PHP知识点](https://www.jianshu.com/p/723d24c1fa32)
    - [CTF中常见PHP特性学习笔记](https://hackfun.org/2018/01/09/CTF%E4%B8%AD%E5%B8%B8%E8%A7%81PHP%E7%89%B9%E6%80%A7%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)
2. 反序列漏洞
    - [php 反序列漏洞初识](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247487282&idx=1&sn=413af47f5984336800a43710f9288ab5&chksm=ec1e3f1adb69b60c81636c0956dbe2ecf29d28e3c03445fe6f91101c1b991e1f2961079fd8d5&scene=21#wechat_redirect)
    - [CTF 中的反序列化问题](http://www.freebuf.com/column/151447.html)
    - [CTF题学习PHP反序列化漏洞](http://www.freebuf.com/news/172507.html)
    - [PHP伪协议与PHP反序列化综合运用的CTF](https://blog.csdn.net/qq_38680693/article/details/81430057)
    - [一道反序列化CTF引起的思考](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247485091&idx=1&sn=e3db9f381de54a71e4c5f22d0bb22834&chksm=ec1e368bdb69bf9dd7a04dc0b5f422f74b81a08b5c75736cf634d37e0107fca3220f15466e81&scene=21#wechat_redirect)
    - [Python反序列化漏洞](https://www.anquanke.com/post/id/86800)
3. 文件包含
    - [CTF 文件包含与伪协议](http://www.freebuf.com/column/150028.html)
    - [PHP 文件包含漏洞姿势总结](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247487364&idx=1&sn=28c137a2b75f1b59256741d6c8686fbd&chksm=ec1e3facdb69b6ba3e6fd0593de30a57d3c8684fc3c64d2ecf12d5040e393749d5cf720dca91&scene=21#wechat_redirect)
4. 文件上传
    -  [简单粗暴的文件上传漏洞](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247487032&idx=1&sn=ad8e14888afa9844642e9e7f50d97b52&chksm=ec1e3e10db69b70684f1132cc3734c7b29304d9afe2db9f21e462bfcf5ef4cffe723aef07189&scene=21#wechat_redirect)
    - [上传源码及漏洞利用方式](https://hackfun.org/2017/03/03/%E5%B0%BD%E6%9C%80%E5%A4%A7%E5%8F%AF%E8%83%BD%E5%88%86%E6%9E%90%E4%B8%8A%E4%BC%A0%E6%BA%90%E7%A0%81%E5%8F%8A%E6%BC%8F%E6%B4%9E%E5%88%A9%E7%94%A8%E6%96%B9%E5%BC%8F/)
5. SQL注入
    - [CTF-Wiki: SQL 注入](https://ctf-wiki.github.io/ctf-wiki/web/sqli/)
    - [SQL注入的常规思路及奇葩技巧](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247484535&idx=1&sn=2f6ec86d8c4e88cc7517d1c38c278472&chksm=ec1e345fdb69bd4915b161d0f5d0d90512952d2abcc87d0a7f10b489a22062b57dc471c62f3b&scene=21#wechat_redirect)
6. XXE攻击
    - [DTD 实体 XXE 浅析](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247486487&idx=1&sn=52c85b90fdf6fac327f30afc2279624a&chksm=ec1e3c3fdb69b529e1f124cf80a2d22275a5ed1de1c023353559886fb799b73d100c05857b8b&scene=21#wechat_redirect)
    - [从XML到远程代码执行](https://www.anquanke.com/post/id/151944)
7. ....... (有空再补充了)



### Misc 题解析
> [杂项简介](https://ctf-wiki.github.io/ctf-wiki/misc/introduction/)

1. 取证
    - [CTF取证类题目指南](https://bbs.pediy.com/thread-220021.htm)
    - 流量取证
        - [日志及流量取证](../../016-取证/02-日志及流量取证/01-流量取证介绍/)
    - 镜像取证
        - [死取证](../../016-取证/01-计算机取证/03-死取证/)
    - 内存取证
        - [活取证](../../016-取证/01-计算机取证/02-活取证/)
        - [CTF内存取证入坑](https://www.jianshu.com/p/6438bc3302c8)
2. 隐写
    - [CTF之隐写](https://www.jianshu.com/p/02fdd5edd9fc)
    - [关于隐写术(Steganography)的简单总结](https://smartjinyu.com/ctf/2016/11/29/introduction_to_stereo.html)
    - 压缩包隐写
        - [CTF密码学题目](https://hackfun.org/2017/01/04/%E4%B8%80%E9%81%93%E5%8F%AF%E4%BB%A5%E5%AD%A6%E5%88%B0%E5%AF%86%E7%A0%81%E7%9F%A5%E8%AF%86%E7%9A%84CTF%E5%AF%86%E7%A0%81%E5%AD%A6%E9%A2%98%E7%9B%AE/)
        - [CTF比赛中关于zip的总结](https://www.cnblogs.com/WangAoBo/p/6944477.html)
    - 音频隐写
        - [ctf中MISC之MP3等音频隐写](https://blog.csdn.net/pdsu161530247/article/details/77568807)
        - [CTF 中音频隐写的一些整理总结](http://www.sqlsec.com/2018/01/ctfwav.html)
    - 文本隐写
    - 图片隐写
        - [CTF中图片隐藏文件分离方法总结](https://hackfun.org/2017/01/12/CTF%E4%B8%AD%E5%9B%BE%E7%89%87%E9%9A%90%E8%97%8F%E6%96%87%E4%BB%B6%E5%88%86%E7%A6%BB%E6%96%B9%E6%B3%95%E6%80%BB%E7%BB%93/)
        - [CTF 中图片隐写的一些整理总结](http://www.sqlsec.com/2018/01/ctfimg.html)
        - [CTF中常见图片隐写](http://zjw.dropsec.xyz/uncategorized/2016/08/18/CTF%E4%B8%AD%E5%B8%B8%E8%A7%81%E5%9B%BE%E7%89%87%E9%9A%90%E5%86%99.html)
        - [图片分析](https://ctf-wiki.github.io/ctf-wiki/misc/picture/introduction/)
        - [CTF中的隐写术](https://zhuanlan.zhihu.com/p/30539398)
        - [利用 PhotoShop 解决图片隐写](https://www.bodkin.ren/index.php/archives/257/)

3. 编码
    - [CTF中那些脑洞大开的编码和加密](https://www.tuicool.com/articles/2E3INnm)
    - 网络编码问题
        - [通信领域常用编码](https://ctf-wiki.github.io/ctf-wiki/misc/encode/communication/)
        - 第四题 MISC传感器1 [第十届全国大学生信息安全竞赛writeup](https://www.anquanke.com/post/id/86431)
    - base编码
    - Urlencode
    - ROT13/5/...
    - Shellcode编码
    - Uuencode
    - XXencode
    - PPencode
    - JJencode
    - 核心价值观编码 (我也是服的)
    - ......


### Crypto 赛题解析
> [密码学简介](https://ctf-wiki.github.io/ctf-wiki/crypto/introduction/) | 回顾[CTF中那些脑洞大开的编码和加密](https://www.tuicool.com/articles/2E3INnm) | [再不学点现代密码，CTF就Hold不住啦！](https://zhuanlan.zhihu.com/p/32797492) | [Cheatsheet - Crypto 101](https://pequalsnp-team.github.io/cheatsheet/crypto-101) | [CTF中常见的Web密码学攻击方式](http://seaii-blog.com/index.php/2017/05/13/60.html) | [密码学笔记](https://blog.csdn.net/veritas501/article/details/55257957)

1. 古典密码
    - 凯撒密码
    - 维吉尼亚密码
    - 简单移位密码
    - 栅栏密码
    - 比尔密码
    - .......

2. 编程语言解析与解密
    - Ook(形如: `Ook. ,  !?!!.`)
    - Brainfuck(形如: `+ +[->+` )
    - Jsfuck(形如: `[][(![]+[])[`)
2. AES基本加解密
    - [AES介绍](https://ctf-wiki.github.io/ctf-wiki/crypto/blockcipher/aes/)
    - [AES Mess(AES-ECB分组求解)](https://github.com/shiltemann/CTF-writeups-public/blob/master/ABCTF-2016/writeup.md#cryptography-75-aes-mess)
3. CBC比特翻转攻击
    - [CBC字节翻转攻击](http://zjw.dropsec.xyz/CTF/2017/04/24/CBC%E5%AD%97%E8%8A%82%E7%BF%BB%E8%BD%AC%E6%94%BB%E5%87%BB.html)
4. DES爆破
    - [赛题玩转密码学——第一讲(DES 和 Xor)](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247487243&idx=1&sn=d8b64c4be84ad42f11417d45cdee90f2&chksm=ec1e3f23db69b635f4ceb501ad5ec43372c54fa89d80cd67df4c0c8c3acd391c0e7f9482c0b0&scene=21#wechat_redirect)
5. 哈希长度扩展攻击(Hash Length Extension Attack)
    - [原理及利用工具 ](http://www.cnblogs.com/pcat/p/5478509.html)
    - [另一个利用工具(hexpand)](https://www.cnblogs.com/pcat/p/7668989.html)
