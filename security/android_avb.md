### 什么是avb
* avb全称 android verified boot,是为android设计的一套安全启动软件,用来保证软件的完整性
* avb的上一版本是来自chrome的verified boot,简称vb1.0,所以也称avb是vb2.0
* avb较上一版本改动较大，增加了vbmeta的结构，重新设计了校验流程，下一节详细介绍

### vbmeta结构说明
* 假设有4个image链式启动
    * 在vb1.0中，从A到D，安全启动链上每一个image都包含一个签名，按照顺序一级一级的校验签名。
    * 在vb2.0中，A负责校验vbmeta结构的签名，vbmeta结构除了自己的签名和公钥，还包含了B/C/D的描述（descriptor），可以用于验证B/C/D的完整性和合法性。

![vb 1.0 vs vb 2.0](images/v1_vs_v2.png)

* vbmeta包含了需要验证的image对应的描述，描述有三种类型：
    * vbmeta header
    ```
      Minimum libavb version:   1.0
      Header Block:             256 bytes
      Authentication Block:     320 bytes
      Auxiliary Block:          2048 bytes
      Algorithm:                SHA256_RSA2048
      Rollback Index:           0
      Flags:                    0
      Release String:           'avbtool 1.1.0'
    ```
    * Hash descriptor
    ```
      Hash descriptor:
      Image Size:            14884864 bytes
      Hash Algorithm:        sha256
      Partition Name:        boot
      Salt:                  d0f619c40f9a51b1c575a415b918534c9a3dab5ec2508780e0e22b8029c3b48b
      Digest:                b4b6fe48c770101cda91a7188b275cfbbf2ec0ec68ee7d1cd3534cb7205066d0
      Flags:                 0
    ```
    * Hash tree descriptor
    ```
      Hashtree descriptor:
      Version of dm-verity:  1
      Image Size:            1056714752 bytes
      Tree Offset:           1056714752
      Tree Size:             8327168 bytes
      Data Block Size:       4096 bytes
      Hash Block Size:       4096 bytes
      FEC num roots:         2
      FEC offset:            1065041920
      FEC size:              8421376 bytes
      Hash Algorithm:        sha1
      Partition Name:        vendor
      Salt:                  d0f619c40f9a51b1c575a415b918534c9a3dab5ec2508780e0e22b8029c3b48b
      Root Digest:           caaf016ed3990356e476ccdcf455cef5823e1b87
      Flags:                 0
    ```
    * Chained descriptor
    ```
      Chain Partition descriptor:
      Partition Name:          system
      Rollback Index Location: 1
      Public key (sha1):       cdbb77177f731920bbe0a0f94f84d9038ae0617d
    ```

下面这个图是最常见的情景

![avb vbmeta struct](images/vbmeta_struct.png)

下面这个图是带有chained image的情景，主要用于分开验证某些image

![avb vbmeta struct with chained](images/vbmeta_struct_chain.png)

### avb状态说明
avb启动标准中有4中状态

    | 状态  | 说明 |
    | ---  | --- |
    | green |  有锁态，官方合法签名 |
    | yellow | 有锁态，用户合法签名 |
    | orange | 解锁态 |
    | red    | 有锁态，既不是官方合法签名也不是用户合法签名 |

![avb state](images/verified-boot-flow.png)
