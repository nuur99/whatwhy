### 是什么

是匿名函数，可以把代码像数据一样传递，本质上是接口的实例

### 怎么用

lambda由三部分组成，我们以Comparator接口的实现为例，在源代码中，Comparator的代码是这样的：

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
    //其它的方法略
}
```

那么我们实现这个接口原来可以这样写

```java
Comparator<Integer> comparator = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return Integer.compare(o1, o2);
            }
        };
```

用lambda我们这样写：

```Java
Comparator<Integer> comp2 = ((o1, o2) -> Integer.compare(o1, o2));
```

->是箭头操作符，右边的是重写的抽象方法的方法体，左边是抽象方法的形参列表

一共有六种情况：

- 有参有返回值
- 有参无返回值
- 数据类型可以忽略，编译器自动进行类型推断
- 如果只有一个参数，小括号可以省
- 拥有多个返回值，而且有返回值
- 只有一条语句时，return与大括号都可以省略

### 什么样的接口能用lambda

需要函数式接口，接口里面只有一个抽象类方法

### 一个Consumer的例子



```java
public class Test03 {

    public void printMoney(double money, Consumer<Double> consumer) {
        consumer.accept(money);
    }

    @Test
    public void test() {
        printMoney(400, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                System.out.println(aDouble);
            }
        });
    }
    
    @Test
    public void test02() {
        printMoney(500, money -> System.out.println(money));
    }
}

```

