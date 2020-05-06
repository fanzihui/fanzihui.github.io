---
title: pingyin4jUtil 简单封装
date: 2020-03-03
author: 范了饭饭
categories:
 - backEnd
tags: 
 - util
 - 插件
---

这里使用的是 pinyin4j 这个包,可以把 汉字转换成拼音

## 1. 添加依赖
```xml
<dependency>
	<groupId>com.belerweb</groupId>
	<artifactId>pinyin4j</artifactId>
	<version>2.5.1</version>
</dependency>

```
## 2. 封装成 Util 

```java
public class PinyinUtil {

  /**
   *  返回一个拼音字符串
   */
  public static String getPingYin(String inputString) {
      HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
      // 小写
      format.setCaseType(HanyuPinyinCaseType.LOWERCASE);
      // 不加声调
      format.setToneType(HanyuPinyinToneType.WITHOUT_TONE);
      // ü 以 v 表示
      format.setVCharType(HanyuPinyinVCharType.WITH_V);
      String output = "";
      if (inputString != null && inputString.length() > 0 && !"null".equals(inputString)) {
          char[] input = inputString.trim().toCharArray();
          try {
              for (int i = 0; i < input.length; i++) {
                  if (java.lang.Character.toString(input[i]).matches("[\\u4E00-\\u9FA5]+")) {
                      String[] temp = PinyinHelper.toHanyuPinyinStringArray(input[i], format);
                      output += temp[0];
                  } else {
                      output += java.lang.Character.toString(input[i]);
                  }
              }
          } catch (BadHanyuPinyinOutputFormatCombination e) {
              e.printStackTrace();
          }
      } else {
          return "*";
      }
      return output;
  }
}


```