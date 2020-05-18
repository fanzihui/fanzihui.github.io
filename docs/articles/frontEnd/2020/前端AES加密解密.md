---
title: 前端AES加密解密
date: 2020-05-18
author: FANLE
categories:
 - 前端
tags:
 - Node
---

使用前端的AES进行数据加密,其实加密解密的结果在 pcks7 和 后端java 中的 pkcs5 是相一致的

前端代码如下:

```js

var CryptoJS = require("crypto-js");

var key = CryptoJS.enc.Utf8.parse("2550e1be21ba49a1");

var word = CryptoJS.enc.Utf8.parse("123456");

var keyStr = "LmRayww3y/dB2CZxnfdr4w==";

var enKenStr = CryptoJS.enc.Utf8.parse(keyStr);


var en = CryptoJS.AES.encrypt(word, key,{
  mode: CryptoJS.mode.ECB,
  padding: CryptoJS.pad.Pkcs7
})

var de = CryptoJS.AES.decrypt(keyStr, key,{
  mode: CryptoJS.mode.ECB,
  padding: CryptoJS.pad.Pkcs7
})

// en LmRayww3y/dB2CZxnfdr4w==
console.log("en" , en.toString());

// de 123456
console.log("de" , CryptoJS.enc.Utf8.stringify(de).toString());

// true
console.log(en.toString() == keyStr)

```


后端代码如下:

```java

// 加密
    public static String aesEncrypt(String str, String key) throws Exception {
        if (str == null || key == null) return null;
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        cipher.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key.getBytes("utf-8"), "AES"));
        byte[] bytes = cipher.doFinal(str.getBytes("utf-8"));
        return new BASE64Encoder().encode(bytes);
    }

    // 解密
    public static String aesDecrypt(String str, String key) throws Exception {
        if (str == null || key == null) return null;
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        cipher.init(Cipher.DECRYPT_MODE, new SecretKeySpec(key.getBytes("utf-8"), "AES"));
        byte[] bytes = new BASE64Decoder().decodeBuffer(str);
        bytes = cipher.doFinal(bytes);
        return new String(bytes, "utf-8");
    }

```