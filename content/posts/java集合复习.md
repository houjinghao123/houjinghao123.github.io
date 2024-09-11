+++
title = 'Java集合复习'
date = 2024-09-11T13:57:35+08:00
categories = ["java"]
tags = ["Java基础","集合"]
+++

# java集合简介

## 一、集合基本的关系结构
Collection 接口的接口 对象的集合（单列集合）
- List 接口：元素按进入先后有序保存，可重复
   - LinkedList 接口实现类， 链表， 插入删除， 没有同步， 线程不安全
   - ArrayList 接口实现类， 数组， 随机访问， 没有同步， 线程不安全
   - Vector 接口实现类 数组， 同步， 线程安全
     - Stack 是Vector类的实现类
- Set 接口： 仅接收一次，不可重复，并做内部排序
   - HashSet 使用hash表（数组）存储元素
   - LinkedHashSet 链表维护元素的插入次序
   - TreeSet 底层实现为二叉树，元素排好序

<img src="https://i.postimg.cc/V66ZdcmS/image-20220407203244029.png"/>

Map 接口 键值对的集合 （双列集合）
- Hashtable 接口实现类， 同步， 线程安全
- HashMap 接口实现类 ，没有同步， 线程不安全-
  - LinkedHashMap 双向链表和哈希表实现
  - WeakHashMap
- TreeMap 红黑树对所有的key进行排序
- IdentifyHashMap


## 二、List和Set集合详解
