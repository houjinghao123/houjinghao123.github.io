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
- HashMap 接口实现类 ，没有同步， 线程不安全
  - LinkedHashMap 双向链表和哈希表实现
  - WeakHashMap
- TreeMap 红黑树对所有的key进行排序
- IdentifyHashMap

<img sec="https://i.postimg.cc/hjQ9pNQw/image-20220407203412665.png"/>

##  二、Collection接口及方法
### 2.1 添加
（1）add(E obj)：添加元素对象到当前集合中<br>
（2）addAll(Collection other)：添加other集合中的所有元素对象到当前集合中，即this = this ∪ other

### 2.2 判断
（3）int size()：获取当前集合中实际存储的元素个数<br>
（4）boolean isEmpty()：判断当前集合是否为空集合<br>
（5）boolean contains(Object obj)：判断当前集合中是否存在一个与obj对象equals返回true的元素<br>
（6）boolean containsAll(Collection coll)：判断coll集合中的元素是否在当前集合中都存在。即coll集合是否是当前集合的“子集”<br>
（7）boolean equals(Object obj)：判断当前集合与obj是否相等

 ### 2.3 删除
（8）void clear()：清空集合元素<br>
（9） boolean remove(Object obj) ：从当前集合中删除第一个找到的与obj对象equals返回true的元素。<br>
（10）boolean removeAll(Collection coll)：从当前集合中删除所有与coll集合中相同的元素。即this = this - this ∩ coll <br>
（11）boolean retainAll(Collection coll)：从当前集合中删除两个集合中不同的元素，使得当前集合仅保留与coll集合中的元素相同的元素，即当前集合中仅保留两个集合的交集，即this  = this ∩ coll；<br>

### 2.4其他
（12）Object[] toArray()：返回包含当前集合中所有元素的数组<br>
（13）hashCode()：获取集合对象的哈希值<br>
（14）iterator()：返回迭代器对象，用于集合遍历<br>
### 三、List集合
#### 3.1 List接口特点
List集合类中`元素有序`、且`可重复`，集合中的每个元素都有其对应的顺序索引。<br>
JDK API中List接口的实现类常用的有：`ArrayList`、`LinkedList`和`Vector`。

#### 3.2 List接口方法
List除了从Collection集合继承的方法外，List 集合里添加了一些`根据索引`来操作集合元素的方法。
- 插入元素
  - void add(int index,Object ele); 在哦index位置插入ele元素
  - boolean add(int index ,Collection eles);从index位置开始将eles中的所有元素添加进来
- 获取元素
  - Object get(int inedx);获取指定index位置的元素
  - List subList(int fromIndex, int toIndex):返回从fromIndex到toIndex位置的子集合
- 获取元素索引
  - int indexOf(Object obj):返回obj在集合中首次出现的位置
  - int lastIndexOf(Object obj):返回obj在当前集合中末次出现的位置
- 删除和替换元素
  - Object remove(int index):移除指定index位置的元素，并返回此元素
  - Object set(int index, Object ele):设置指定index位置的元素为ele

#### 3.3 List接口主要实现类：ArrayList
- ArrayList 是 List 接口的`主要实现类`
- 本质上，ArrayList是对象引用的一个”变长”数组
- Arrays.asList(…) 方法返回的 List 集合，既不是 ArrayList 实例，也不是 Vector 实例。 Arrays.asList(…) 返回值是一个固定长度的 List 集合

####  3.4 List的实现类之二：LinkedList

- 对于频繁的插入或删除元素的操作，建议使用LinkedList类，效率较高。这是由底层采用链表（双向链表）结构存储数据决定的。
- 特有方法
  - void addFirst(Object obj)
  - void addLast(Object obj)
  - Object getFirst()
  - Object getLast()
  - Object removeFirst()
  - Object removeLast()


#### 3.5 List的实现类之三：Vector

- Vector 是一个`古老`的集合，JDK1.0就有了。大多数操作与ArrayList相同，区别之处在于Vector是`线程安全`的。
- 在各种List中，最好把`ArrayList作为默认选择`。当插入、删除频繁时，使用LinkedList；Vector总是比ArrayList慢，所以尽量避免使用。

### 四、Set集合
#### 4.1 Set接口概述
- Set接口是Collection的子接口，Set接口相较于Collection接口没有提供额外的方法
- Set 集合`不允许包含相同的元素`，如果试把两个相同的元素加入同一个 Set 集合中，则添加操作失败。
- Set集合支持的遍历方式和Collection集合一样：foreach和Iterator。
- Set的常用实现类有：HashSet、TreeSet、LinkedHashSet。

#### 4.2 Set主要实现类：HashSet

- HashSet 是 Set 接口的主要实现类，大多数时候使用 Set 集合时都使用这个实现类。

- HashSet 按 Hash 算法来存储集合中的元素，因此具有很好的存储、查找、删除性能。

- HashSet 具有以下`特点`：
  - 不能保证元素的排列顺序
  - HashSet 不是线程安全的
  - 集合元素可以是 null

- HashSet 集合`判断两个元素相等的标准`：两个对象通过 `hashCode()` 方法得到的哈希值相等，并且两个对象的 `equals() `方法返回值为true。

- 对于存放在Set容器中的对象，**对应的类一定要重写hashCode()和equals(Object obj)方法**，以实现对象相等规则。即：“相等的对象必须具有相等的散列码”。

- HashSet集合中元素的无序性，不等同于随机性。这里的无序性与元素的添加位置有关。具体来说：我们在添加每一个元素到数组中时，具体的存储位置是由元素的hashCode()调用后返回的hash值决定的。导致在数组中每个元素不是依次紧密存放的，表现出一定的无序性。


#### 4.3 Set实现类之二：LinkedHashSet

- LinkedHashSet 是 HashSet 的子类，不允许集合元素重复。

- LinkedHashSet 根据元素的 hashCode 值来决定元素的存储位置，但它同时使用`双向链表`维护元素的次序，这使得元素看起来是以`添加顺序`保存的。

- LinkedHashSet`插入性能略低`于 HashSet，但在`迭代访问` Set 里的全部元素时有很好的性能。

#### 4.4 Set实现类之三：TreeSet

- TreeSet 是 SortedSet 接口的实现类，TreeSet 可以按照添加的元素的指定的属性的大小顺序进行遍历。
- TreeSet底层使用`红黑树`结构存储数据

## 五 Map接口
现实生活与开发中，我们常会看到这样的一类集合：用户ID与账户信息、学生姓名与考试成绩、IP地址与主机名等，这种一一对应的关系，就称作映射。Java提供了专门的集合框架用来存储这种映射关系的对象，即`java.util.Map`接口。

### 5.1 Map接口概述

- Map与Collection并列存在。用于保存具有`映射关系`的数据：key-value
  - `Collection`集合称为单列集合，元素是孤立存在的。
  - `Map`集合称为双列集合，元素是成对存在的。

- Map 中的 key 和  value 都可以是任何引用类型的数据。但常用String类作为Map的“键”。

- Map接口的常用实现类：`HashMap`、`LinkedHashMap`、`TreeMap`和``Properties`。其中，HashMap是 Map 接口使用`频率最高`的实现类。<br>

<img src="https://i.postimg.cc/kGGdw9Td/image-20220409001015034.png" />

### 5.2 Map接口的常用方法

- **添加、修改操作：**
  - Object put(Object key,Object value)：将指定key-value添加到(或修改)当前map对象中
  - void putAll(Map m):将m中的所有key-value对存放到当前map中
- **删除操作：**
  - Object remove(Object key)：移除指定key的key-value对，并返回value
  - void clear()：清空当前map中的所有数据
- **元素查询的操作：**
  - Object get(Object key)：获取指定key对应的value
  - boolean containsKey(Object key)：是否包含指定的key
  - boolean containsValue(Object value)：是否包含指定的value
  - int size()：返回map中key-value对的个数
  - boolean isEmpty()：判断当前map是否为空
  - boolean equals(Object obj)：判断当前map和参数对象obj是否相等
- **元视图操作的方法：**
  - Set keySet()：返回所有key构成的Set集合
  - Collection values()：返回所有value构成的Collection集合
  - Set entrySet()：返回所有key-value对构成的Set集合

### 5.3 Map的主要实现类：HashMap

#### 5.3.1 HashMap概述

- HashMap是 Map 接口`使用频率最高`的实现类。
- HashMap是`线程不安全`的。允许添加 null 键和 null 值。
- 存储数据采用的哈希表结构，底层使用`一维数组`+`单向链表`+`红黑树`进行key-value数据的存储。与HashSet一样，元素的存取顺序不能保证一致。
- HashMap `判断两个key相等的标准`是：两个 key 的hashCode值相等，通过 equals() 方法返回 true。
- HashMap `判断两个value相等的标准`是：两个 value 通过 equals() 方法返回 true。

### 5.4 Map实现类之二：LinkedHashMap


- LinkedHashMap 是 HashMap 的子类
- 存储数据采用的哈希表结构+链表结构，在HashMap存储结构的基础上，使用了一对`双向链表`来`记录添加元素的先后顺序`，可以保证遍历元素时，与添加的顺序一致。
- 通过哈希表结构可以保证键的唯一、不重复，需要键所在类重写hashCode()方法、equals()方法。

### 5.5 Map实现类之三：TreeMap

- TreeMap存储 key-value 对时，需要根据 key-value 对进行排序。TreeMap 可以保证所有的 key-value 对处于`有序状态`。
- TreeSet底层使用`红黑树`结构存储数据
- TreeMap 的 Key 的排序：
  - `自然排序`：TreeMap 的所有的 Key 必须实现 Comparable 接口，而且所有的 Key 应该是同一个类的对象，否则将会抛出 ClasssCastException
  - `定制排序`：创建 TreeMap 时，构造器传入一个 Comparator 对象，该对象负责对 TreeMap 中的所有 key 进行排序。此时不需要 Map 的 Key 实现 Comparable 接口
- TreeMap判断`两个key相等的标准`：两个key通过compareTo()方法或者compare()方法返回0。

### 5.6 Map实现类之四：Hashtable

- Hashtable是Map接口的`古老实现类`，JDK1.0就提供了。不同于HashMap，Hashtable是线程安全的。
- Hashtable实现原理和HashMap相同，功能相同。底层都使用哈希表结构（数组+单向链表），查询速度快。
- 与HashMap一样，Hashtable 也不能保证其中 Key-Value 对的顺序
- Hashtable判断两个key相等、两个value相等的标准，与HashMap一致。
- 与HashMap不同，Hashtable 不允许使用 null 作为 key 或 value。


### 5.7 Map实现类之五：Properties

- Properties 类是 Hashtable 的子类，该对象用于处理属性文件

- 由于属性文件里的 key、value 都是字符串类型，所以 Properties 中要求 key 和 value 都是字符串类型

- 存取数据时，建议使用setProperty(String key,String value)方法和getProperty(String key)方法