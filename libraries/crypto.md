# crypto

[返回总览](../README.md)

crypto 是 Haxe 的跨目标密码学工具库，提供数据摘要、消息认证、对称加密、密码哈希、密钥派生和安全随机数，适合用于资源完整性、存档保护及协议中的密码学处理。

## 功能总览

| 功能 | 支持的能力 |
| --- | --- |
| 摘要与校验 | 提供 SHA、BLAKE、RIPEMD、MD5、CRC-32 和 Adler-32 等算法。 |
| 消息认证 | 提供 HMAC、Poly1305、CMAC 和 BLAKE3 带密钥摘要。 |
| 对称加密 | 提供 AES、Twofish、Blowfish、ChaCha、Salsa20 及旧格式兼容算法。 |
| 认证加密 | 提供 ChaCha20-Poly1305、XChaCha20-Poly1305、XSalsa20-Poly1305 和多种 AES 认证模式。 |
| 密码与密钥派生 | 提供 Argon2、BCrypt、SCrypt、PBKDF2 和 BLAKE3 密钥派生。 |
| 安全随机数 | 基于目标环境提供的安全随机源生成密钥材料、随机字节和其他随机值。 |

## 特色功能

- **组合多种加密处理**：同一库提供摘要、消息认证、认证加密和安全随机数，可用于组织资源或存档保护流程；具体安全性仍取决于密钥管理、nonce 和所选目标的实现。
- **面向不同数据形式的 AES 模式**：除常见加密与认证模式外，还包括面向数据单元的 XTS、用于密钥包装的 KW/KWP，以及保持字符集和长度的 FF1。FF1 需要 Haxe 5，对输入域有长度要求，且不提供完整性认证。
- **密码哈希与密钥派生**：提供 Argon2、BCrypt、SCrypt 等密码哈希，以及 PBKDF2 和 BLAKE3 密钥派生，可用于管理密码验证和不同用途的密钥材料；密码哈希支持配置相应的计算成本。

## 摘要、校验与消息认证

摘要算法包括 SHA-1、SHA-224/256/384/512、RIPEMD-160、BLAKE2b、BLAKE2s、BLAKE3 和 MD5。其中 SHA-224/256/384/512 与 BLAKE3 支持增量处理，适合分块处理数据；CRC-32 和 Adler-32 可用于发现普通传输或存储错误。

HMAC、Poly1305、CMAC 和 BLAKE3 带密钥摘要用于消息认证；BLAKE3 还支持按上下文派生密钥与指定输出长度。MD5、SHA-1、CRC-32 和 Adler-32 主要适合兼容或普通错误检测，不适合作为新安全协议的完整性认证手段。

## 对称加密与认证加密

分组和流密码包括 AES、Twofish、Blowfish、DES、TripleDES、ChaCha、XChaCha20、Salsa20、XSalsa20 和 RC4。AES 支持 ECB、CBC、PCBC、CFB、OFB、CTR、XTS、KW、KWP 和 FF1，覆盖常见的分组模式、数据单元加密、密钥包装及格式保留加密。填充方式包括 PKCS#7、ANSI X9.23、ISO 10126、TBC、Bit、Null、Space 和不填充。

Salsa20、XSalsa20 以及 ChaCha 系列流密码支持分段处理数据，并提供流位置定位与跳转能力；RC4 也支持连续分段处理，主要用于旧数据兼容。认证加密包括 ChaCha20-Poly1305、XChaCha20-Poly1305、XSalsa20-Poly1305，以及 AES-GCM、GCM-SIV、CCM、SIV 和 EAX。这些方案对明文加密并生成认证标签；部分方案还支持认证不加密的附加数据。DES、TripleDES、RC4 与 ECB 主要用于兼容旧数据；CBC、CTR 等未认证模式需要另行提供消息认证。

## 密码、密钥与随机数

密码哈希支持 Argon2d、Argon2i、Argon2id、BCrypt 和 SCrypt；PBKDF2 与 BLAKE3 也可用于密钥派生。Argon2 支持配置内存成本、迭代次数、并行度与输出长度，并支持可选密钥和附加数据。

安全随机数能力覆盖密钥材料、盐、随机字节、整数、浮点数、布尔值和字符串；根据目标使用 Web Crypto、Node.js、JVM 或操作系统提供的安全随机源。库还提供基于 AES-CTR 的确定性随机数生成器，支持注入熵、重新注入熵和清除内部状态。

## 版本与使用条件

不同算法对密钥、nonce、输入长度和填充方式有各自要求；例如 XChaCha20-Poly1305 使用 32 字节密钥和 24 字节 nonce，同一密钥下的 nonce 需要保持唯一。FF1 依赖 Haxe 5 的大整数能力，当前算法版本为 2，旧版密文需要通过相应兼容方式迁移。

库的算法主体以 Haxe 实现，未作为 FIPS 认证密码模块发布；各目标的随机源和侧信道特性可能不同。密钥管理、认证失败处理和数据格式版本由使用方负责。

[查看 crypto 项目](https://github.com/heaps-lib-save/crypto)