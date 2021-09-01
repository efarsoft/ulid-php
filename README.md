# ulid-php 
## ULID - 一种比UUID更好的方案

ULID：Universally Unique Lexicographically Sortable Identifier（通用唯一词典分类标识符）

UUID：Universally Unique Identifier（通用唯一标识符）


# 使用教程

### 安装 
```
composer require efarsoft/ulid
```
### 使用

```
<?php
use Efarsoft\Ulid;

$ulid = Ulid::generate();
echo (string) $ulid; 
// 01B8KYR6G8BC61CE8R6K2T16HY

// 输出小写字符串
$ulid = Ulid::generate(true);
echo (string) $ulid; 
// 01b8kyr6g8bc61ce8r6k2t16hy

// 转换为Unix 时间戳
$ulid = Ulid::generate();
echo $ulid->toTimestamp(); 
// 1561622862

// 使用毫秒时间戳生成Ulid
$ulid = Ulid::fromTimestamp(1593048767015);
//$ulid = Ulid::fromTimestamp(1593048767015, true);
echo (string) $ulid; 
// 01EBMHP6H7TT1Q4B7CA018K5MQ
```


为什么不选择UUID
UUID 目前有 5 个版本：

- 版本1：在许多环境中是不切实际的，因为它需要访问唯一的，稳定的MAC地址，容易被攻击；
- 版本2：将版本 1 的时间戳前四位换为 POSIX 的 UID 或 GID，问题同上；
- 版本3：基于 MD5 哈希算法生成，生成随机分布的ID需要唯一的种子，这可能导致许多数据结构碎片化；
- 版本4：基于随机数或伪随机数生成，除了随机性外没有提供其他信息；
- 版本5：通过 SHA-1 哈希算法生成，生成随机分布的ID需要唯一的种子，这可能导致许多数据结构碎片化；

这里面常用的就是 UUID4 了，但是，即使是随机的，但是也是存在冲突的风险。

和 UUID 要么基于随机数，要么基于时间戳不同，ULID 是既基于时间戳又基于随机数，时间戳精确到毫秒，毫秒内有1.21e + 24个随机数，不存在冲突的风险，而且转换成字符串比 UUID 更加友好。

ULID特性：
```
ulid() # 01ARZ3NDEKTSV4RRFFQ69G5FAV
```

- 与UUID的128位兼容性
- 每毫秒1.21e + 24个唯一ULID
- 按字典顺序(也就是字母顺序)排序！
- 规范地编码为26个字符串，而不是UUID的36个字符
- 使用Crockford的base32获得更好的效率和可读性（每个字符5位）
- 不区分大小写
- 没有特殊字符（URL安全）
-  单调排序顺序（正确检测并处理相同的毫秒）

————————————————

## ULID规范

以下是在php(ulid-php)中实现的ULID的当前规范。二进制格式已实现

```
01AN4Z07BY      79KA1307SR9X4MV3

|----------|    |----------------|
 Timestamp          Randomness
  10chars            16chars
   48bits             80bits
```

## 组成

- 时间戳
    - 48位整数
    - UNIX时间（以毫秒为单位）
    - 直到公元10889年，空间都不会耗尽。

- 随机性
    - 80位随机数
    - 如果可能的话，采用加密技术保证随机性

## 排序
最左边的字符必须排在最前面，最右边的字符必须排在最后（词汇顺序）。必须使用默认的ASCII字符集。在同一毫秒内，不能保证排序顺序

## 编码方式
如图所示，使用了Crockford的Base32。该字母表不包括字母I，L，O和U，以避免混淆和滥用。
```
0123456789ABCDEFGHJKMNPQRSTVWXYZ
```

> 关于两个方案的具体分析,以及Python版的实现可参照：

> 版权声明：本文为CSDN博主「pushiqiang」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

> 原文链接：https://blog.csdn.net/pushiqiang/article/details/117365290