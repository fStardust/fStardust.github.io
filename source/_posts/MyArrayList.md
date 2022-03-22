---
title:  自写MyArrayList
date:  2021/5/21
categories:  Java Learn
tags:  [Code,Java,Learn]
keywords:  Java,集合,数据操作
---

通过对集合数据结构以及对数组操作的认知，利用泛型的不设限性质，自写集合的增删改查操作。

😉

<!-- more -->

## 自写MyArrayList

```java
package com.system.studetn.fs.util;

import java.util.Arrays;

/**
 * 自定义实现MyArrayList
 *
 * @author fStardust
 *
 * @param <E> 自定义泛型
 */
public class MyArrayList<E> {
	/**
	 * 准备一个底层数组，用于存储数据内容
	 */
	private Object[] elements;
	
	/**
	 * 初始化默认容量
	 */
	private static final int DEFAULT_CAPACITY = 10;
	
	/**
	 * 最大数组容量，-8是为了腾出一定的空间，保存数组的必要内容
	 */
	private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
	
	/**
	 * 当前底层数组中保存的有效元素个数
	 */
	private int size = 0;
	
	/**
	 * 无参数构造方法，但是需要提供给用户一个初始化容量来保存必要的数据
	 */
	public MyArrayList()  {
		/*
		 * 使用this关键字调用类内的其它构造方法
		 * 根据实际参数类型来选对应的构造方法，必须在当前构造方法代码块的首行
		 */
		this(DEFAULT_CAPACITY);
	}
	
	/**
	 * 用户指定保存元素容量的初始化过程，要求用户指定的容量范围是有效的
	 * 
	 * @param initCapacity 用户指定的初始化容量，
	 * 	但是不能小于等于0，不能大于MAX_ARRAY_SIZE
	 */
	public MyArrayList(int initCapacity) {
		// 用户传入参数的合法性判断过程
		if (initCapacity < 0 || initCapacity > MAX_ARRAY_SIZE) {
			// 抛出异常
			// IllegalArgumentException 是一个RuntimeException 运行时异常的子类
			// 不需要强制声明抛出异常
			throw new IllegalArgumentException("IllegalArgumentException:" + initCapacity);
		}
		
		elements = new Object[initCapacity];
	}
	
	/*
	 * 增加方法
	 */
	/**
	 * 添加元素到当前集合的末尾
	 * 
	 * @param e 要求实符合泛型约束的指定数据类型 
	 * @return 添加成功返回true，否则返回false
	 */
	public boolean add(E e) {
		// 直接调用在指定下标位置添加元素的方法
		// 只不过这里指定下表位置就是尾插法下表位置
		return add(size, e);
	}
	
	/**
	 * 在底层数组的指定下标位置保存对应的元素
	 * 
	 * @param index 指定下标位置，不能超出有效范围， 0 <= index <= size
	 * @param e 符合泛型约束的数据类型
	 * @return 添加成功返回true，否则返回false
	 */
	public boolean add(int index, E e) {
		if (index < 0 || index >size) {
			throw new ArrayIndexOutOfBoundsException(index);
		}
		
		ensureCapacity(size + 1);
		
		for (int i = size; i > index; i--) {
			elements[i] = elements[i - 1];
		}
		
		elements[index] = e;
		size += 1;
		
		return true;
	}
	
	/*
	 * addAll方法
	 * 	1. 需要得到添加集合中元素内容，有效元素个数
	 * 	2. 确认容量问题
	 * 	3. size = SRCSize + newSize 
	 */
	/**
	 * 添加另一个集合到当前集合末尾
	 * 
	 * @param list MyArrayList类型，自定义ArrayList，要求存储元素和当前集合一致
	 * 	或者是其子类
	 * @return 添加成功返回true，失败返回false
	 */
	public boolean addAll(MyArrayList<? extends E> list) {
		Object[] array = list.toArray();
		int newSize = array.length;
		
		ensureCapacity(size + newSize);
		
		for (int i = 0; i < newSize; i++) {
			elements[i + size] = array[i];
		}
		
		size += newSize;
		return true;
	}
	
	/**
	 * 添加另一个符合当前集合要求的List，到当前集合内 
	 * 
	 * @param index 指定插入的下标位置
	 * @param list 符合当前添加要求的另一个AyArrayList集合
	 * @return 添加成功返回true
	 */
	public boolean addAll(int index, MyArrayList<? extends E> list) {
		
		// 用户指定的下标位置
		if (index < 0 || index >size) {
			throw new ArrayIndexOutOfBoundsException(index);
		}
		
		ensureCapacity(size + 1);
		
		Object[] array = list.toArray();
		int newSize = array.length;
		
		ensureCapacity(size + newSize);
		
		// 移动操作
		for (int i = size -1; i >= index; i--) {
			elements[i + newSize] = elements[i];
		}
		
		// 存入目标元素
		for (int i = index; i < index + newSize; i++) {
			elements[i] = array[i - index];
		}
		
		// 有效元素个数需要修改
		size += newSize;
		
		return true;
	}
	
	/**
	 * 删除指定元素
	 * 
	 * @param index 指定删除的元素
	 * @return 删除成功返回true
	 */
	public boolean remove(Object obj) {
		int index = indexOf(obj);
		
		return null != remove(index);
	}
	
	/**
	 * 删除指定下标元素
	 * 
	 * @param index 指定的下标范围
	 * @return 删除成功返回对应元素，失败返回null
	 */
	public E remove(int index) {
		if (-1 == index) {
			return null;
		}
		
		E e = get(index);
		
		for (int i = index; i < size - 1; i++) {
			elements[i] = elements[i + 1];
		}
		
		// 原本最后一个有效元素位置上的内容赋值为null
		elements[size - 1] = null;
		size -= 1;
		
		return e;
	}
	
	/**
	 * 获取集合中指定下标的元素
	 * 
	 * @param index 指定下标的范围，但是不能超出有效下标位置
	 * @return 返回对应的元素
	 */
	@SuppressWarnings("unchecked")
	public E get(int index) {
		if (index < 0 || index > size) {
			throw new ArrayIndexOutOfBoundsException(index);
		}
		
		return (E) elements[index];
	}
	
	/**
	 * 查询指定元素在集合中的第一次出现下标位置
	 * 
	 * @param obj 指定的元素
	 * @return 返回值大于等于0表示找到元素，否则返回-1
	 */
	public int indexOf(Object obj) {
		int index = -1;
		
		for (int i = 0; i < size; i++) {
			// equals判断对象是否一致
			if (obj.equals(elements[i])) {
				index = i;
				break;
			}
		}
		
		return index;
	}
	
	
	/**
	 * 查询指定元素在集合中的最后一次出现下标位置
	 * 
	 * @param obj 指定的元素
	 * @return 返回值大于等于0表示找到元素，否则返回-1
	 */
	public int lastIndexOf(Object obj) {
		int index = -1;
		
		for (int i = size - 1; i >= 0; i--) {
			if (obj.equals(elements[i])) {
				index = i;
				break;
			}
		}
		
		return index;
	}
	
	/**
	 * 返回MyArrayList集合中所有有效元素的Object数组
	 * 
	 * @return 包含所有集合元素的Object类型数组
	 */
	public Object[] toArray() {
		// size是有效元素个数，通过该方法可以获取到一个只有当前数组有效元素的数组
		return Arrays.copyOf(elements, size);
	}
	
	/**
	 * 判断指定元素是否存在
	 * 
	 * @param obj 指定元素
	 * @return 存在返回true，不存在返回false
	 */
	public boolean contains(Object obj) {
		return indexOf(obj) > -1;
	}
	
	/**
	 * 
	 * 情况1：
	 * 	集合1{1,3,5,9}; 集合2{3,5}是子集合, 集合3{5, 3}不是子集合
	 * 情况2：
	 * 	集合1{1,3,5,9,3,7,8}; 集合2{3,7}这是子集合, 集合3{5,3}不是子集合
	 * 
	 * @param list
	 * @return
	 */
	public boolean containsAll(MyArrayList<?> list) {

		// 判断list是否为空，以及当前list对应的地址是否为null
		if (null == list || list.isEmpty()) {
			throw new NullPointerException();
		}
		
		// 1. 计数器 找出list参数集合中下标为0的元素在集合中出现的位置次数
		int count = 0;
		boolean flag = true;
		
		// 2. 存储当前集合中下标为i的元素和list参数集合中下标为0的元素比较
		int[] indexArr = new int[this.size];
		
		for (int i = 0; i < this.size; i++) {
			// 在当前集合中下标为i的元素和list参数集合中下标为0的元素比较
			if (this.get(i).equals(list.get(0))) {
				indexArr[count] = i;
				count += 1;
			}
		}
		
		// 4. 判定是否存在list.get(0)的元素
		if (0 == count) {
			return false;
		}
		
		// 5. 进入循环，开始匹配
		for (int i = 0; i < count; i++) {
			
			// 6. 遍历操作当前集合
			for (int j = indexArr[i] + 1; j < indexArr[i] + list.size(); j++) {
				
				// list从下标1开始获取元素
				int index = 1;
				if (!this.get(j).equals(list.get(index++))) {
					flag =  false;
					break;
				}
				
				flag = true;
			}
			
			if (flag) {
				break;
			}
		}
		
		return flag;
		
	}
	
	/**
	 * 判断集合是否为空
	 * 
	 * @return 如果为空，返回true
	 */
	public boolean isEmpty()  {
		return size == 0;
	}
	
	/**
	 * 获取当前集合中有效元素个数
	 * 
	 * @return 有效元素个数
	 */
	public int size() {
		return size;
	}
	
	/**
	 * 获取当前集合的子集合 截取范围fromIndex <= n < endIndex
	 * 
	 * @param fromIndex fromIndex <= endIndex 不得小于0
	 * @param endIndex endIndex >= formIndex 小于等于size
	 * @return 截取得到的一个MyArrayList子集合对象
	 */
	public MyArrayList<E> subList(int fromIndex, int endIndex) {
		if (fromIndex > endIndex || fromIndex < 0 || endIndex > size) {
			throw new ArrayIndexOutOfBoundsException();
		}
		
		MyArrayList<E> listTemp = new MyArrayList<E>(endIndex - fromIndex);
		
		for (int i = fromIndex; i < endIndex; i++) {
			listTemp.add(this.get(i));
		}
		
		return listTemp;
	}
	
	/**
	 * 替换指定下标元素
	 * 
	 * @param index 指定下标元素，但是必须在有效范围以内
	 * @param e 符合泛型约束的对应数据类型
	 * @return 被替换的元素
	 */
	public E set(int index, E e) {
		if (index < 0 || index >= size) {
			throw new ArrayIndexOutOfBoundsException(index);
		}
		
		E temp = get(index);
		elements[index] = e;
		
		return temp;
	}
	
	@Override
	public String toString() {
		String str = "[";
		for (int i = 0; i < size -1; i++) {
			str += elements[i] + ", ";
		}
		
		return str + elements[size - 1] + "]";
	}
	
	/*
	 * 这里需要类内使用的可以用于判定当前容量是否满足添加要求的方法，
	 * 如果满足直接进入添加模式，如果不满足，需要执行grow方法
	 * 完成底层数组扩容问题
	 */
	/**
	 * 每一次添加元素，都需要进行容量判断，如果满足可以进行添加操作
	 * 不满足则需要制定grow方法
	 * 
	 * @param minCapacity 要求的最小容量
	 */
	private void ensureCapacity(int minCapacity) {
		if (minCapacity > elements.length) {
			// 完成一个底层数组的扩容方法
			grow(minCapacity);
		}
	}
	
	/**
	 * 底层数组的扩容方法，原理是创建新数组，移植数据，保存新数组地址
	 * 
	 * @param minCapacity 要求的最小容量v         
	 
	 */
	private void grow(int minCapacity) {
		// 1. 获取原数组容量
		int oldCapacity = elements.length;
		
		// 2. 计算得到新数组容量
		int newCapactiy = oldCapacity + (oldCapacity >> 1);
		
		// 3. 判断新数组容量是否满足要求
		if (newCapactiy < minCapacity) {
			newCapactiy = minCapacity;
		}
		// 新数组容量是否大于允许的最大数组容量
		if (newCapactiy > MAX_ARRAY_SIZE) {
			// 二次判断IllegalArgumentException是否小于MAX_ARRAY_SIZE
			if (minCapacity < MAX_ARRAY_SIZE) {
				// 最小要求是不大于MAX_ARRAY_SIZE，代码可以允许
				newCapactiy = minCapacity;
			} else {
				throw new OutOfMemoryError("Overflow MAX_ARRAY_SIZE");
			}
		}
		
		/*
		 * 4. 使用数组工具类完成操作
		 * Arrays.copyOf(源数据数组-可以是任意类型-采用泛型约束, 指定的新数组容量);
		 * 	1. 根据指定的新数组容量创建对应泛型数据类型的新数组
		 * 	2. 从源数据数组中拷贝内容到新数组
		 * 	3. 返回新数组首地址
		 */
		elements = Arrays.copyOf(elements, newCapactiy);
	}
}

```

