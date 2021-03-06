---
layout: post
title: "Java 实现 AES 算法"
date: 2014-10-31 15:20:33 +0800
comments: true
tags: [Java, AES]
---

密码学课上学习了AES算法，balabala原理上有些听不懂，如果是日常应用，可以用java实现好的东西来搬过来用。

<!--more-->

首先建了一个自己的AES工具类，里面写上了静态的加密和解密函数供之后使用。

AES工具类和加解密函数如下：
{% codeblock lang:java %}
public class MyAESUtils {
	public static byte[] encrypt(String content, String password) {
		try {
			KeyGenerator kgen = KeyGenerator.getInstance("AES");
			kgen.init(128, new SecureRandom(password.getBytes()));			SecretKey secretKey = kgen.generateKey();
			byte[] enCodeFormat = secretKey.getEncoded();
			SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
			Cipher cipher = Cipher.getInstance("AES");
			byte[] byteContent = content.getBytes("utf-8");
			cipher.init(Cipher.ENCRYPT_MODE, key);
			byte[] result = cipher.doFinal(byteContent);
			return result;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
	
	public static byte[] decrypt(byte[] content, String password) {
		try {
			KeyGenerator kgen = KeyGenerator.getInstance("AES");
			kgen.init(128, new SecureRandom(password.getBytes()));
			SecretKey secretKey = kgen.generateKey();
			byte[] enCodeFormat = secretKey.getEncoded();
			SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
			Cipher cipher = Cipher.getInstance("AES");
			cipher.init(Cipher.DECRYPT_MODE, key);
			byte[] result = cipher.doFinal(content);
			return result;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}	
}
{% endcodeblock %}

以上的类库不需要额外的jar包，直接在Eclipse里按`Ctrl+O`快捷键自动导入就好了。接下来实现主函数：

	public class AESMain {
		public static void main(String[] args) {
			Scanner in = new Scanner(System.in);
			System.out.println("请输入明文");
			String content = in.nextLine();
			System.out.println("请输入密钥");
			String key = in.nextLine();
			
			byte[] encryptResult = MyAESUtils.encrypt(content, key);
			System.out.println("加密后密文为： " + encryptResult);
			
			byte[] decryptResult = MyAESUtils.decrypt(encryptResult, key);
			System.out.println("解密后原文为： " + new String(decryptResult));
	
		}
	}

很简单的一段代码就实现了基于AES算法的加解密，不过仅仅这样做还是不懂AES的原理的，就需要阅读源码看一下了。不过应用的话，这样基本上就行了。