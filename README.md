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
