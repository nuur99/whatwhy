### Number Class

当我们在使用数字的时候，大多数时候都是用基本数据类型

基本数据类型：

- int	四字节
- byte  单字节
- short  双字节
- long   八字节
- float   四字节
- double  八字节
- boolean  根据厂商的实现而定，原则上是一个比特就可以，
- char 两字节

但是Java是面向对象语言，如果我们想对这些数据类型进行对象层面的操作怎么办呢？Java堆这些基本数据类型，进行了包装，所以与基本类型对应的就是包装类型，通常的，如果我们在写代码的时候，我们想使用一个基本数据类型的包装类，我们直接把一个基本类型赋值给一个相应基本类型的包装类就行了，为什么这样可以？我们之前在新建对象的时候，后面都是要用new关键字，因为Java使用了自动装箱技术，我们可以看作编译器在不知不觉中给我们的基本数据类型套上了壳子，变成了包装类型，当然与之对应的还有自动拆箱，编译器会把一个包装类型自动拆箱成一个相对应的基本类型

基本类型的包装类如下：

- Integer
- Byte
- Short
- Long
- Float
- Double
- Character

所有的数字包装类，都是Number这个抽象类的子类，Number还有四个其他的包装类

- BigDecimal and BigInteger 这两给被用来高精度的计算

- AtomicInteger andd AtomicLong 被用来多线程应用

  

### String

String 在Java中是非常常见的，String是对象，是引用类型，它是由一串字符组成

#### 怎么新建一个String对象

```java
String string = "string"; //这是一个字符串字面值
String string = new String(string);
```

String是不可变的，

### 在String和Number中转换

在我们写程序的时候，我们需要获得输入，但是我们输入的额值是字符形式的 ，这时候我们要怎么做呢，在Number的子类这种，那些数字基本类型都提供了一个方法，ValueOfXxx,可以把字符串类型转换成数字类型的包装类，parseXxx可以把字符串类型转换成基本类型

相同的，我们想把数字类型转换成字符串类型，这时候我们就可以用String.ValueOf(1)，或者包装类的toString方法

### 包装类的缓存池

当我们把一个int类型转换成Integer时，如果是在-128-127的范围时，并且是

```java
Integer integer = 3;
Integer integer2 = 3;
```

这时候，是从常量池中返回的3的包装类的对象，所以integer 和 integer2引用的同一个对象

```
Integer integer = 3;
Integer integer2 = new Integer(3);
```

但是，如果是上面这样，那integer,integer2就不是引用相同的对象了，因为integer使用了new关键字，所以不是从常量池返回，而是又构造了一个新的对象,,相应的，Byte，Short，Long,也实现了常量池缓存技术，Boolean也实现了常量池缓存技术，只不过它的常量池只有false和true两个值，Character也实现了常量池，它的范围是0-127，