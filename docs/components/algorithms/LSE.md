LSE类
----
namespace: `lin\algorithms`

包含如下类：

* **lin\algorithms\LSE**

---

### 说明
本组件为算法类中的对称加密类，算法类中每一个算法可能会采取不同的license，具体看文件头部说明。
本类是基于单向散列的对称加密算法，算法本身公开透明。使用简单，具有高安全性，高性能，数据量低等特点。在同一个密钥和同一加密内容情况下，每次生成的加密数据都完全不一样，有效避免碰撞破解。算法全称Lin Symmetric(Secure) Encryption，简称LSE。

---

### 功能
* 加密解密


#### 配置项
无


#### 使用
~~~php
<?php

//使用密钥加密解密
$LSE = new LSE('secret_key'); //密钥为'secret_key'

$LSE->encrypt('data'); //加密并输出十六进制密文
$LSE->encrypt('data', true); //加密并输出二进制密文

$LSE->decrypt('ciphertext'); //解密16进制密文
$LSE->decrypt('ciphertext', true); //解密二进制密文
~~~

---


## API

#### 列表
~~~php
public function __construct(string $secretKey, int $times = 5)
public function encrypt(string $data, bool $isRaw = false):  ? string
public function decrypt(string $data, bool $isRaw = false) :  ? string
~~~

#### 详细说明

**__construct()**: 构造函数
```php
params:
    string $secretKey 用于加解密的密钥
    int    $times=5   内部循环加密次数，不建议修改，一次加解密该值需一致。
return
    mixed|null 失败返回null
```

**encrypt()**: 加密函数
```php
params:
    string $data        待加密数据
    bool   $isRaw=false 是否输出原始二进制数据，否则输出十六进制字符串
return
    string|null 失败返回null
```

**decrypt()**: 解密函数
```php
params:
    string $data        待解密数据
    bool   $isRaw=false 待解密数据是否为原始二进制，否为16进制
return
    string|null 失败返回null
```