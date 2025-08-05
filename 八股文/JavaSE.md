## 多态解决的问题

==多态指同一个方法或接口，在不同的类中有不同的实现==

多态的前置条件有三个：

- 子类继承父类

- 子类重写父类的方法

- 父类引用指向子类的对象

  

例子：子类继承父类--定义为F f = new Child();

子类重写了父类方法，f的方法使用时作用的是子类重写的方法。

多态解决了同一个接口或方法在不同的类中有不同的实现，比如说动态绑定，父类引用指向子类对象，方法的具体调用回延迟到运行时决定。

## 访问修饰符public,private,protected以及默认的区别

private：在同一类中可见。只能修饰变量，方法，不能修饰内部类。

protected：对同一包内的类和所有子类可见。可以修饰变量方法，不能修饰内部类。

public：对所有类可见，可以修饰变量，方法，接口，类。

default(默认):对同一包内的可见，可以修饰变量方法，不能修饰类。

  

## static关键字

==加了 `static` 的成员，是**类共享的，全局唯一**，不依赖对象实例存在

首先分析，当使用到你的类的时候，JVM 才会把它加载进“方法区”或“元空间”中

加载是指把类结构加载到**方法区**（或元空间），而实例化是指在 **堆内存中开辟空间**，执行构造方法

类似于一个是图纸，另一个是造出图纸上的东西。

其中 **“加载类”** 是指 JVM 将 `.class` 文件读入内存，并在 **方法区** 中生成对应的 **类的结构信息（Class对象）**。

类加载不一定就会实例化，常见例子为使用类里面的静态变量也会加载类。

static是在加载的时候，把对应的属性还有方法放到元空间，这样我们可以直接访问，而不是需要实例化对象。

  

### 静态变量和实例变量

**静态变量:** 是被 static 修饰符修饰的变量，也称为类变量，它属于类，不属于类的任何一个对象，一个类不管创建多少个对象，静态变量在内存中有且仅有一个副本。--静态变量可以作为计数器，一个类创建多个实例这个静态变量都是一个值。

**实例变量:** 必须依存于某一实例，需要先创建对象然后通过对象才能访问到它。静态变量可以实现让多个对象共享内存。--在实例化后只在该实例生效。

### 静态方法和实例方法

**静态方法**：static 修饰的方法，也被称为类方法。在外部调⽤静态⽅法时，可以使⽤"**类名.⽅法名**"的⽅式，也可以使⽤"**对象名.⽅法名**"的⽅式。==静态方法里不能访问类的非静态成员变量和方法。

**实例⽅法**：依存于类的实例，需要使用"**对象名.⽅法名**"的⽅式调用；可以访问类的所有成员变量和方法。

## final关键字作用

  

final用来修饰类时候，这个类不可被继承。例如String，Integer类和其他包装类都是final修饰的。

final修饰一个方法时，表示这个方法不能被重写。

final修饰变量时，这个变量不允许被修改。
  

## 用static final修饰变量的好处

用 `static final` 定义的常量是 ==**类级别的、全局唯一的、值不可变的、性能最优的常量**。

final表示不可变，static表示属于类本身，无需实例化

==编译器会做常量优化，直接翻译成字节码

命名统一规范

==绝对线程安全（只初始化一次，永远不会被修改）

## == 和 equlas的区别

== 是判断两个引用地址是否相同，但在基本类型比较的是值，例如int。

equals比较是两个对象的内容是否相同，比较的是内容。

Java 中默认的 `equals()` 方法是 `Object` 类中的：

**==也就是默认行为和 `==` 是一样的 —— 比地址。**

但很多时候我们想比较**内容是否一致**，所以我们要**重写 equals() 方法**。

重写equals方法，如果不使用HashMap或HashSet做contails比较的话，是不用写hashcode方法的，但是如果要用Hash的数据类型就需要重写hashcode方法。

hashmap存储的时候，先根绝内容做hash值，然后取余操作等得到hash桶，把对应的内容放到对应桶里。做比较的时候，如果得到的hash桶一样，再比较内容，如果桶都不一样，直接就是不想等了，所以要重写hashcode。

为什么原来的hash code方法不能用了，因为**原来的比的是对象的内存地址，不是内容，所以两个内容一样的对象，hash值不同，就导致比较结果为false。**

  

### hash冲突

hash相等，内容不一定相等。

因为 `hashCode()` 是一个 **int 类型（32位整数）**，取值范围只有：

但是现实中可以创建的对象数量、字段组合远大于 40 亿种可能，**总有可能出现两个不同对象的 hashCode 一样**，这就是哈希冲突。

java怎么解决hash冲突，使用拉链法，如果hash相等，用equals比较，不相等的话追加到尾部，相等的替换。

  
  

## Java是值传递

Java 是值传递，不是引用传递。

当一个对象被作为参数传递到方法中时，参数的值就是该对象的引用。引用的值是对象在堆中的地址。

对象是存储在堆中的，所以传递对象的时候，可以理解为把变量存储的对象地址给传递过去。

  

```

public static void main(String[] args) {

Animal animal = new Animal();

animal.setName("cat");

change(animal);

System.out.println(animal.getName());

}

public static void change(Animal animal){

animal.setName("dog");

}

```

上述代码，去方法里面传的是animal,执行set方法的时候会改变输出，因为一直指向的是一个地址。

```

public static void onChange(Animal animal){

animal = new Animal();

animal.setName("dog");

}

```

如果这种就不会改变，因为annimal执行变成了新的对象，但原来的annimal是没变的，所以说是值传递，而不是饮用。

  

Java 中方法参数的传递规则如下：

- 对于 **基本类型**：传递的是值的副本，修改不影响原值。

- 对于 **对象类型**：传递的是对象引用的副本，**你可以通过这个引用修改对象的内容，但不能修改这个引用本身的指向**

值传递你只能通过引用操作改变原对象的内容，但你不能改变它的指向。

引用传递是传的引用的引用，可以直接改变变量的指向。

  

## 深拷贝和浅拷贝的区别

浅拷贝会创建一个新对象，但这个新对象的属性（字段）和原对象的属性完全相同。**如果属性是基本数据类型，拷贝的是基本数据类型的值**；**如果属性是引用类型，拷贝的是引用地址，因此新旧对象共享同一个引用对象。**

浅拷贝方式：直接实现 Cloneable 接口并重写 `clone()` 方法。

深拷贝也会创建一个新对象，但会递归地复制所有的引用对象，确保新对象和原对象完全独立。新对象与原对象的任何更改都不会相互影响。

深拷贝方式：

1手动复制所有的引用对象，或者使用序列化与反序列化。

2序列化与反序列化

```

public Animal deepClone() throws IOException, ClassNotFoundException {

ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();

ObjectOutputStream stream = new ObjectOutputStream(byteArrayOutputStream);

stream.writeObject(stream);

ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(byteArrayOutputStream.toByteArray());

ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);

return (Animal) objectInputStream.readObject();

}

```

把对象序列化为字节，然后再进行反序列化。

  

## Java创建对象方式

三种

1.new关键字创建

2.反射机制创建，反射机制允许在运行时创建对象，并且可以访问类的私有成员，在框架和工具类中比较常见。

3.clone 拷贝创建，通过 clone 方法创建对象，需要实现 Cloneable 接口并重写 clone 方法。

4.序列化机制创建，通过序列化将对象转换为字节流，再通过反序列化从字节流中恢复对象。需要实现 Serializable 接口。

  

## String
==不可继承，final修饰，引用类型

### string，builder，buffer

`String`、`StringBuilder`和`StringBuffer`在 Java 中都是用于处理字符串的，它们之间的区别是，String 是不可变的，平常开发用得最多，当遇到大量字符串连接时，就用 StringBuilder，它不会生成很多新的对象，StringBuffer 和 StringBuilder 类似，但每个方法上都加了 synchronized 关键字，所以是线程安全的。

  

string不可改变，每次改变都是生成新的对象，线程开销比较大。

builder是在原对象改变的。

stringbuffer线程安全，但牺牲了速度，加大了开销，所以一般不使用这个，一本不会在多线程频繁修改字符串内容。

  

### String a = new String("123") 和String a = "123"的区别

  

直接双引号引用的话，会去常量池查找，找不到的话创建一个新的字符串放入池中，并引用，找的的话直接饮用。

  

使用new String的话

1.先去常量池查找括号里的内容，没有创建，有则引用。

2.在堆中创建一个string对象，并初始化为字符串常量池相同字符串的副本。

  
  

### string为什么不可变

①、不可变性使得 String 对象在使用中更加安全。因为字符串经常用作参数传递给其他 Java 方法，例如网络连接、打开文件等。

  

如果 String 是可变的，这些方法调用的参数值就可能在不知不觉中被改变，从而导致网络连接被篡改、文件被莫名其妙地修改等问题。

  

②、不可变的对象因为状态不会改变，所以更容易进行缓存和重用。字符串常量池的出现正是基于这个原因。

  

当代码中出现相同的字符串字面量时，JVM 会确保所有的引用都指向常量池中的同一个对象，从而节约内存。

  

③、因为 String 的内容不会改变，所以它的哈希值也就固定不变。这使得 String 对象特别适合作为 HashMap 或 HashSet 等集合的键，因为计算哈希值只需要进行一次，提高了哈希表操作的效率。

  

### string是怎么拼接的

传统的是通过两个string生成一个新的对象，指向新的常量池里面的对象。

  

jdk8以后对+号进行优化，Java 会在编译期基于 StringBuilder 的 append 方法进行拼接。

  
  

## Integer

### integer = 127，127和128，128相等吗

  

127相等Integer会在-128-127之间的integer对象使用缓存，a和b实际上引用了常量池中相同的Integer对象

  

128超出了缓存池大小，会创建两个不同的对象，有不同的引用地址。

  

通过== 判断是否指向一个对象

可以通过equals判断值是否相等

  

### integer缓存

我们使用自动装箱来创建这个范围内的Integer对象时，java会直接从缓存池里面返回一个已存在的对象，不用创建。这意味着，对于这个值范围内的所有 Integer 对象，它们实际上是引用相同的对象实例。

  

引用是 Integer 类型，= 右侧是 int 基本类型时，会进行自动装箱，调用的其实是 `Integer.valueOf()`方法，它会调用 IntegerCache。

  
  

## String怎么转换成Integer的原理

调用了parseInt和valueof方法，一个返回封装类，一个返回基本类，但都使用了同一个解析逻辑。

使用了字符串遍历计算。

- **检查字符串是否为 null**

- **判断是否是负号开头**

- **逐字符解析成十进制数字**（用 `Character.digit()`）

- **计算结果**：通过 `result = result * 10 + digit`

- **抛出异常**：如果含非法字符或越界，会抛 `NumberFormatException`

  

## 装箱拆箱

把基本来转换成引用类，把引用类转换成基本类。

编译器自写。

  

为什么需要装箱拆箱：

==集合类只能装引用类型，所以对于一些int类型，编译器自动封箱了

  

==泛型只支持对象也就是引用类型

  

==引用类型有一些方法可以直接使用

  

拆箱

  

==包装类不能直接用于计算

  

==旧代码兼容

  

==减少开发者手动切换

  

ps：装箱拆箱会有性能开销

  
  

## BIO,NIO,AIO

  

BIO：采用阻塞式 I/O 模型，线程在执行 I/O 操作时被阻塞，无法处理其他任务，适用于连接数较少的场景。

  

NIO：采用非阻塞 I/O 模型，线程在等待 I/O 时可执行其他任务，通过 Selector 监控多个 Channel 上的事件，适用于连接数多但连接时间短的场景。

  

AIO：使用异步 I/O 模型，线程发起 I/O 请求后立即返回，当 I/O 操作完成时通过回调函数通知线程，适用于连接数多且连接时间长的场景。

  
  

## 序列化和反序列化

序列化（Serialization）是指将对象转换为字节流的过程，以便能够将该对象保存到文件、数据库，或者进行网络传输。

  

反序列化（Deserialization）就是将字节流转换回对象的过程，以便构建原始对象。

  

`Serializable`接口用于标记一个类可以被序列化。

  

UID用来作为序列化的唯一标识符。确保在序列化和反序列化的过程总，类是一致的。

  

==如果使用默认的UID，有可能我们在反序列化的时候，我们的类经过了一些变化例如加了一个属性，则反序列化失败。

  

==Java序列化并不包含静态变量。

  

==变量不想序列化可以使用transient修饰。

  

## 泛型

  

==**泛型就是参数化类型**==，它允许在类、接口、方法中**定义数据类型为参数**，这样写代码更通用、类型更安全。

  

使用泛型的原因：

编译期间检查，比直接用Object类安全

减少强制类型转换

可以复用，针对不同类型的数据通用一套代码

  

泛型的使用：

泛型类

常用的 Result< t > 就是泛型类，声明这个类是个泛型类

  

泛型接口

泛型接口广泛应用于各种通用框架、回调机制、数据处理等，比如：

  

数据处理策略类

```
public interface Processor< T > {

	T process(T input);

}
```

实现类

```
public class UpperCaseProcessor implements Processor< String> {

	public String process(String input) {

	return input.toUpperCase();

	}

}
```

  

泛型方法：

```
public class UpperCaseProcessor implements Processor< String> {

	public String process(String input) {

	return input.toUpperCase();

	}

}
```
前面的T是声明类型参数，声明是一个泛型方法，后面的T是返回值，再后面是参数

  
  

### 泛型擦除

泛型擦出是指在 Java 编译阶段，编译器会把泛型中的类型参数“擦除”，将泛型信息转换成普通的非泛型代码，从而保证向后兼容性（兼容 Java 5 之前的版本）。

- Java 泛型是在 JDK 5 才引入的，但 JVM 仍然兼容老版本（没有泛型的版本）。

- 为了向后兼容，泛型实现不能在字节码里保留类型参数信息。

- 因此，编译器会把泛型类型替换为其限定的“上界”或者 `Object`。

影响和限制

  

| 限制 | 说明 |

| ---------------- | ----------------------------- |

| 不能在运行时判断泛型类型 | 不能用 `instanceof` 判断泛型类型参数 |

| 不能创建泛型数组 | 如 `new T[]` 报错，类型擦除导致无法确定具体类型 |

| 不能在静态上下文使用泛型类型参数 | 因为泛型类型属于实例相关，静态不属于任何实例 |

  

```

public class Box<T> {

  

private T value; // 实例变量，类型是 T

  

public static T getDefault() { // ❌ 编译错误，静态方法不能使用 T

return null;

}

```

编译错误因为：需要实例化才能确定T是什么类型，你直接静态方法是独立于实例之外的

  

### 泛型通配符

#### ❓ `?` 表示“未知类型”，用于定义方法参数更灵活

`public void printList(List<?> list) { for (Object obj : list) { System.out.println(obj); } }`

  

#### 上界通配符 `<? extends T>`：**T 或 T 的子类**

  

`public void printNumbers(List<? extends Number> list) { // list 可读，但不能添加元素（除了 null） }`

- `printNumbers` 方法参数是 `List<? extends Number>`，

- 编译器只知道 `list` 是某个 Number 的子类列表，具体是哪个不知道，

- 所以不允许往里添加任何具体类型，防止类型安全问题，

- 但可以读出 `Number` 类型的数据。

  

#### 下界通配符 `<? super T>`：**T 或 T 的父类**

`public void addIntegers(List<? super Integer> list) { list.add(1); // ✅ 可以加 Integer 或其子类 }`

  

- `List<? super Integer>` 可以接受 `List<Integer>`、`List<Number>`、`List<Object>` 等；

- 你可以往里面添加 `Integer` 对象（及其子类，但 Integer 是 final 类，所以没子类）；

- 但是读取时只能当 `Object` 类型处理，因为具体类型不确定。

==这个原则叫做 **PECS**，全称是：

  

**Producer Extends, Consumer Super**

解释：

- **Producer Extends（生产者用 extends）**

如果你要从一个泛型集合中**读取数据**（当做生产者），应该用 `? extends T`，表示你不关心具体是哪个子类，只要是 `T` 或其子类都可以安全读取。

- **Consumer Super（消费者用 super）**

如果你要向一个泛型集合中**写入数据**（当做消费者），应该用 `? super T`，表示这个集合可以接受 `T` 类型及其父类的元素，保证写入安全。

extends只能读的原因是因为，最高父类就是T，所以你可以用T去赋值，是安全的。但如果写入的话就不行，因为子类有太多类型，类比Number类，下面可能有double或者float，如果你加的是double，但实际是float就会报错。

super只能写的原因是因为，子类是Integer你至少知道它最起码是可以写入integer的，但是当你读取的时候可能是number甚至object所以接收只能用object接收然后再强壮，会导致错误。
  

## 反射的原理

  

java程序的执行分为编译和运行两步，编译之后会生成字节码文件，JVM进行类加载的时候，会加载字节码文件，将类型相关的信息加载到方法去，反射就是去获取这些信息，然后进行各种操作。

  

## JDK1.8新特性

### 接口默认方法

Java 8 允许在接口中添加默认方法和静态方法。

```

public interface MyInterface { default void myDefaultMethod() { System.out.println("My default method"); } static void myStaticMethod() { System.out.println("My static method"); } }

```

### stream流

Stream 是对 Java 集合框架的增强，它提供了一种高效且易于使用的数据处理方式。

```

List<String> list = new ArrayList<>(); list.add("中国加油"); list.add("世界加油"); list.add("世界加油"); long count = list.stream().distinct().count(); System.out.println(count);

```

### 函数式接口

Lambda 表达式描述了一个代码块（或者叫匿名方法），可以将其作为参数传递给构造方法或者普通方法以便后续执行。

```

public class LamadaTest { public static void main(String[] args) { new Thread(() -> System.out.println("沉默王二")).start(); } }

```

### 新日期时间api

Java 8 引入了一个全新的日期和时间 API，位于`java.time`包中。这个新的 API 纠正了旧版`java.util.Date`类中的许多缺陷

```

LocalDate today = LocalDate.now(); System.out.println("Today's Local date : " + today); LocalTime time = LocalTime.now(); System.out.println("Local time : " + time); LocalDateTime now = LocalDateTime.now(); System.out.println("Current DateTime : " + now);

```

### optional类

引入 Optional 是为了减少空指针异常。

```

Optional<String> optional = Optional.of("沉默王二");

optional.isPresent(); // true

optional.get(); // "沉默王二"

optional.orElse("沉默王三"); // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0))); // "沉"

```