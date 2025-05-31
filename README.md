# 源包码包链接

> https://pub.dev/packages/pointycastle/versions   
> https://pub.dev/packages/encrypt/versions   

修改的版本：   

- pointycastle-3.8.0     
- encrypt-5.0.3 

# 修改的源代码位置

- pointycastle-3.8.0/lib/block/modes/cfb.dart
- encrypt-5.0.3/lib/src/algorithms/aes.dart

# 使用示例

```
import 'dart:typed_data';
import 'package:encrypt/encrypt.dart' as encrypt;

String decryptAES(String encryptedBase64, String key) {
  try {
    // 将Base64编码的加密数据解码为字节数组
    Uint8List encryptedBytes = base64.decode(encryptedBase64);

    // 提取前16字节作为IV
    Uint8List iv = encryptedBytes.sublist(0, 16);

    // 剩余的字节是实际的加密数据
    Uint8List encryptedData = encryptedBytes.sublist(16);

    // 创建AES密钥
    final aesKey = encrypt.Key(Uint8List.fromList(utf8.encode(key)));

    // 创建IV
    final initVector = encrypt.IV(iv);

    // 创建AES加密器，使用CFB模式
    final encrypter = encrypt.Encrypter(
        encrypt.AES(aesKey, mode: encrypt.AESMode.cfb128, padding: null));

    // 解密数据
    var decrypted =
        encrypter.decrypt(encrypt.Encrypted(encryptedData), iv: initVector);

    print(decrypted);
    return decrypted;
  } catch (e) {
    print('解密失败: $e');
    return '';
  }
}
```

# AI 改进后的版本

```
int _decryptBlock(Uint8List inp, int inpOff, Uint8List out, int outOff) {
    // Calculate how many bytes we can actually process (may be less than blockSize)
    final availableBytes = inp.length - inpOff;
    final bytesToProcess =
        availableBytes < blockSize ? availableBytes : blockSize;

    if (bytesToProcess <= 0) {
      return 0; // No data to process
    }

    // Ensure output buffer has enough space
    if (outOff + bytesToProcess > out.length) {
      throw ArgumentError('Output buffer too short');
    }

    // Process the current CFB state to get the keystream
    _underlyingCipher.processBlock(_cfbV!, 0, _cfbOutV!, 0);

    // XOR the keystream with ciphertext to get plaintext
    for (var i = 0; i < bytesToProcess; i++) {
      out[outOff + i] = _cfbOutV![i] ^ inp[inpOff + i];
    }

    // Update CFB state (shift left and insert new ciphertext)
    final offset = _cfbV!.length - blockSize;
    _cfbV!.setRange(0, offset, _cfbV!.sublist(bytesToProcess)); // Shift left
    _cfbV!.setRange(offset, offset + bytesToProcess,
        inp.sublist(inpOff, inpOff + bytesToProcess)); // Insert new ciphertext

    return bytesToProcess; // Return actual bytes processed
}
```

# flutter 项目引入私有包示例

```
dependencies:
  flutter:
    sdk: flutter

  # The following adds the Cupertino Icons font to your application.
  # Use with the CupertinoIcons class for iOS style icons.
  cupertino_icons: ^1.0.2
  http: ^1.1.0
  alipay_kit: ^6.0.0
  fluwx: ^4.6.3
  # encrypt: 5.0.3
  # pointycastle: 3.8.0
  encrypt:
    git:
      url: https://github.com/tgyday/flutter_aes_cfb_128.git
      path: encrypt-5.0.3  # 子目录路径
      ref: master

dependency_overrides:
  pointycastle:
    git:
      url: https://github.com/tgyday/flutter_aes_cfb_128.git
      path: pointycastle-3.8.0 # 子目录路径
      ref: master
```
