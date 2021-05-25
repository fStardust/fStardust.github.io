---
title:  通过Java归档文件
categories:  Java实用Code
tags:  Code,Java,Learn,实用
keywords:  Java,文件操作,实用
index_img: #/img/example.jpg -- 文章首页封面图
banner_img: /img/post_banner.jpg -- 文章页顶部大图
date: 

---

文件处理跟文件夹处理有很多相似之处，可以封装成一个方法。🪂

<!-- more -->

# 通过`Java`归档文件

## 文件归档分析

#### 文件归档图示：

![原始情况](https://i.loli.net/2021/05/20/elr43C5nKIXhfWQ.png)

![最终情况](https://i.loli.net/2021/05/20/Kf8lHPaUxvhuBD9.png)

#### 步骤：

1. 获取对应当前文件的所有子文件和子文件夹`list` or `listFiles`

2. 遍历判断是普通文件还是文件夹`isFile` or `isDirctory`

3. 分门别类处理

   3.1 文件处理

   ​	3.1.1 获取文件后缀名 `substring lastindexOf`

   ​	3.1.2 创建对应当前文件后缀名的全大写文件夹

   ​	3.1.3 移动文件到指定文件夹

​	3.2 文件夹处理

​		3.2.1 创建`subDir`文件夹

​		3.2.2 移动文件夹到`subDir`下

3.1.2、3.1.3、3.2.1、3.2.2步骤相似，可以封装成一个方法。

#### 



## 进阶

实现基本功能后，注意到再次执行会将归档后的所有文件都纳入新的`subDir`文件夹中。

解决：

​	首次归档后，添加一个`.lock`文件标记。并添加判断，若有该文件则视为已归档，不进行操作。

```java
package com.fs.e_file;

import java.io.File;
import java.io.IOException;

/**
 * 文件归档
 * 	1. 获取对应当前文件的所有子文件和子文件夹list or listFiles
 * 	2. 遍历判断是普通文件还是文件夹isFile or isDirctory
 * 	3. 分门别类处理
 * 		3.1 文件处理
 * 			3.1.1 获取文件后缀名 substring lastindexOf
 * 			3.1.2 创建对应当前文件后缀名的全大写文件夹
 * 			3.1.3 移动文件到指定文件夹
 * 		3.2 文件夹处理
 * 			3.2.1 创建subDir文件夹
 * 			3.2.2 移动文件夹到subDir下
 * 
 * @author fStardust
 *
 */
public class CollatFile {
	public static void main(String[] args) throws IOException {
		// 目标文件夹的File类对象
		File file = new File("G:\\1\\c");
		
		// 需要创建的文件夹
		File toBeCreateDir = null;
		
		// 判断是否是文件夹
		if (!file.isDirectory()) {
			return;
		}
		
		// 当前文件夹已经归档完成，无需再次归档
		if(new File(file, "1.lock").exists()) {
			System.out.println("已归档文件");
			return;
		}
		
		// 当前文件夹下所有子文件和子文件夹的File类对象组
		File[] listFiles = file.listFiles();
		
		// 遍历File类对象数组
		for (File srcFile : listFiles) {
			// 如果是普通文件。获取文件后缀名
			if (srcFile.isFile()) {
				
				String fileName = srcFile.getName();
				String upperSuffix = fileName.substring(fileName.lastIndexOf('.') + 1)
						.toUpperCase();
				toBeCreateDir = new File(file, upperSuffix);
				
			} else {
				toBeCreateDir = new File(file, "subDir");
			}
			
			// 创建文件夹
			toBeCreateDir.mkdir();
			
			// 移动文件或文件夹
			srcFile.renameTo(new File(toBeCreateDir, srcFile.getName()));
		}
		
		// 创建一个锁文件
		new File(file, "1.lock").createNewFile();
	}
}

```

