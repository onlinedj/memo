## 什么是avb
* avb全称 android verified boot,是为android设计的一套安全启动软件,用来保证软件的完整性
* avb的上一版本是来自chrome的verified boot,简称vb1.0,所以也称avb是vb2.0
* avb较上一版本改动较大，增加了vbmeta的结构，重新设计了校验流程，下一节详细介绍

### vbmeta结构说明
* 在vb1.0中，从A到D，安全启动链上每一个image都包含一个签名，按照顺序一级一级的校验签名（此处先不考虑dmverity）
* 在vb2.0中，A负责校验vbmeta image的签名，vbmeta包含了BCD的描述（descriptor），通常情况下是保存BCD的hash，可以用于验证BCD的完整性，解决了多次校验签名启动慢的问题，同时可以集中在更底层去校验来保证合法性和多images的一致性。
![vb 1.0 vs vb 2.0](images/v1_vs_v2.png)
vbmeta包含了需要验证的image对应的描述，描述有三种类型：
* Hash descriptor
* Hash tree descriptor
* Chained descriptor

下面这个图是最常见的情景

![avb vbmeta struct](images/vbmeta_struct.png)

下面这个图是带有chained image的情景，主要用于分开验证某些image

![avb vbmeta struct with chained](images/vbmeta_struct_chain.png)

### avb状态说明
avb启动标准中有4中状态

    | 状态  | 说明 |
    | ---  | --- |
    | green |  有锁态，官方合法签名 |
    | yellow | 有锁态，用户合法签名 |
    | orange | 解锁态 |
    | red    | 有锁态，既不是官方合法签名也不是用户合法签名 |

![avb state](images/verified-boot-flow.png)
