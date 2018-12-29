## 什么是avb
* avb全称 android verified boot,是为android设计的一套安全启动软件,用来保证软件的完整性
* avb的上一版本是来自chrome的verified boot,简称vb1.0,所以也有人叫avb是vb2.0
* avb较上一版本改动较大，增加了vbmeta的结构，重新设计了校验流程，下一节详细介绍

### avb状态说明
avb启动标准中有4中状态
green   有锁态，官方合法签名
yellow  有锁态，用户合法签名
orange  解锁态
red     有锁态，既不是官方合法签名也不是用户合法签名

![android vb state](/images/verified-boot-flow.png)
