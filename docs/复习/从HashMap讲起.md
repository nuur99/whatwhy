### 从HashMap讲起

- 首先，HashMap**是什么**

  - 它是一个**键值对**的映射，一个**key**映射一个**value**
  - 它实现了**Map**接口
  - 它的底层是与Hash相关的

- 怎么实现的键值对？

  ```java
  transient Node<K,V>[] table;
  ```

  底层是一个table数组，每一个数组是Node<k,v>，Node是HashMap里面的一个内部类

  ```java
  static class Node<K,V> implements Map.Entry<K,V> {
          final int hash;
          final K key;
          V value;
          Node<K,V> next;
  
          Node(int hash, K key, V value, Node<K,V> next) {
              this.hash = hash;
              this.key = key;
              this.value = value;
              this.next = next;
          }
  
          public final K getKey()        { return key; }
          public final V getValue()      { return value; }
          public final String toString() { return key + "=" + value; }
  
          public final int hashCode() {
              return Objects.hashCode(key) ^ Objects.hashCode(value);
          }
  
          public final V setValue(V newValue) {
              V oldValue = value;
              value = newValue;
              return oldValue;
          }
  
          public final boolean equals(Object o) {
              if (o == this)
                  return true;
              if (o instanceof Map.Entry) {
                  Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                  if (Objects.equals(key, e.getKey()) &&
                      Objects.equals(value, e.getValue()))
                      return true;
              }
              return false;
          }
      }
  ```

  这个内部类的四个属性

  ```
  		final int hash;
          final K key;
          V value;
          Node<K,V> next;
  ```

  很显然，里面的两个Key，value，正是实现HashMap的两个字段，还有一个next指针，指向同一个Node类型的指针，说明HashMap内部还使用了链表这个数据结构

  首先，有一个数组，里面存储Node类型的元素，那链表是什么时候用呢？这时候我们就要看hash这个字段了，当我们往HashMap里面添加数据的时候，会根据传入数据的hash值来判断应该放在数组的哪个位置

  ```java
   public V put(K key, V value) {
          return putVal(hash(key), key, value, false, true);
      }
  ```

  ```java
  static final int hash(Object key) {
      int h;
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
  }
  ```

  会根据上面的hash静态方法来算出key的hash值，然后调用putVal方法，把键值对放入HashMap中

  ```java
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                     boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
          if ((p = tab[i = (n - 1) & hash]) == null)
              tab[i] = newNode(hash, key, value, null);
          else {
              Node<K,V> e; K k;
              if (p.hash == hash &&
                  ((k = p.key) == key || (key != null && key.equals(k))))
                  e = p;
              else if (p instanceof TreeNode)
                  e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
              else {
                  for (int binCount = 0; ; ++binCount) {
                      if ((e = p.next) == null) {
                          p.next = newNode(hash, key, value, null);
                          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                              treeifyBin(tab, hash);
                          break;
                      }
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          break;
                      p = e;
                  }
              }
              if (e != null) { // existing mapping for key
                  V oldValue = e.value;
                  if (!onlyIfAbsent || oldValue == null)
                      e.value = value;
                  afterNodeAccess(e);
                  return oldValue;
              }
          }
          ++modCount;
          if (++size > threshold)
              resize();
          afterNodeInsertion(evict);
          return null;
      }
  ```

  添加大致是如下过程：

  - 如果此位置上为空，那么添加成功
  - 如果不为空（意味着此位置存在一个或者多个数据），比较key与这些数据的哈希值
    - 如果key的哈希值与已经存在的数据的哈希值都不相同，添加成功
    - 如果key的哈希值与已经存在的数据的哈希值相同，继续比较equals方法，
      - 如果equals相同，就使用v替换当前的v
      - 如果不相同，就添加成功
  - 在jdk8中，首次调用put方法时，才会创建一个大小为16的数组，并且当数组的以恶个索引位置上的链表的数量大于8个的时候，就会换成红黑树存储，所以HashMap底层的数据结构有数组，链表和红黑树

- 什么是Map接口？

  - 首先什么是**接口**

    Java接口是一系列方法的声明，是一些方法特征的集合，**一个接口只有方法的特征没有方法的实现，因此这些方法可以在不同的地方被不同的类实现，而这些实现可以具有不同的行为（功能）**。那么Map接口就是规定了一些键值对映射的方法的声明，当HashMap实现Map接口时，我们就要实现Map接口中声明的方法

- 什么是hash值？

  - Java中根据对象的内存地址产生的一组数字，在==运算符中，判断的就是hash值的相同与否