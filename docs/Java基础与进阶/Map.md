### Map Interface

map接口存储了键值对的映射，一个mpa不能包含重复的key，每一个建最多能映射到一个值，基于Map接口的类，分别是：

- HashMap  可以存储null值
- Hashtable 与HashTable类似，是线程安全的，是遗留类不应该去使用它
- TreeMap  保证按照添加的键值对进行排序，实现排序遍历，按照key排序，底层是红黑树
- LinkedHashMap  在HashMap的基础上添加了一对指针，指向前一个和后一个

### Map的结构

key，Value是一个Entry

- Key不可重复，无序的
- Value可重复的，无序的  

#### HashMap底层实现原理

jdk7

```java
HashMap map = new HashMap();
```

在实例化之后，底层创建了长度是16的Entry[] table

```java
map.put(k, v);
```

调用k所在类的hashCode()计算k的哈希值，然后根据算法计算后，得到在table数组中的位置

- 如果此位置上为空，那么添加成功

- 如果不为空（意味着此位置存在一个或者多个数据，比较k与这些数据的哈希值）

  - 如果k的哈希值与已经存在的数据的哈希值都不相同，添加成功
    - 如果k的哈希值与已经存在的数据的哈希值相同，继续比较equals方法，如果相同就使用v替换当前相同k的v，否则添加成功

  扩容机制，扩容为原来的两倍，并把原来的复制过来，并重新计算哈希值

jdk8与jdk7的不同

在实例化的时候，没有创建一个长度为16的数组，底层的数组为Node[]

首次调用put方法时，才会创建大小为16的数组，原来jdk7中的底层结构只有：数组和链表，jdk8加了一个红黑树，当数组的某一个索引位置上的元素的个数大于8的时候，且当前这个数组的长度超过64，此时此索引位置上的所有数据改为使用红黑树存储

### 重要成员变量的值

- ```java
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //HashMap的默认容量16
  ```

- ```java
  static final float DEFAULT_LOAD_FACTOR = 0.75f; //装载因子0.75
  ```

- ```java
  static final int TREEIFY_THRESHOLD = 8;
  //Bucket中链表长度大于该默认值。转化为红黑树
  ```

- ```java
  static final int MIN_TREEIFY_CAPACITY = 64;
  ```

- threshold：扩容的临界值：容量*填充因子

#### Collection常用方法：同步控制

- ```java
  public static <T> List<T> synchronizedList(List<T> list) {
      return (list instanceof RandomAccess ?
              new SynchronizedRandomAccessList<>(list) :
              new SynchronizedList<>(list));
  }
  ```

