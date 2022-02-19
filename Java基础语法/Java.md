# Java基础语法

## 注释

1. 单行注释
2. 多行注释
3. 文档注释

## 数据类型

#### 基本数据类型

**注意：**

1. `long`的数据后要加L，`float`后要加F

2. `String`不是关键词，是一个类
3. 最好避免完全使用浮点数进行比较
4. 数学工具类：`BigDecimal`
5. 所有的字符本质是注释，转换时遵循Unicode表

**Unicode编码表**

区间范围：`U0000 - UFFFF`

转义字符`\u`

#### 引用数据类型

包括类，接口和数组

#### 类型转换

`byte,short,char ->int->long->float->double`

高 `->` 低：强制类型转换

低 `->` 高：自动类型转换 

**注意：**

1. 把高容量转换为低容量的时候，强制类型转换
2. 不能对布尔类型进行转换
3. 转换时存在内存溢出问题，或者是精度问题



## 变量

+ 如果不自行初始化，那么这个变量默认值为`0/0.0`(基本数据类型的变量)
+ 布尔值默认值是`false`
+ 除了基本类型，其余的默认值都是`null`(比如引用类型)
+ 修饰符不区分先后顺序

#### 变量的命名规范

+ 类成员变量：首字母小写+驼峰原则
+ 局部变量：首字母小写+驼峰原则
+ 常量：大写字母+下划线  MAX_VALUE
+ 类名：首字母大写+驼峰原则
+ 方法名：首字母小写+驼峰原则

## 位运算

A&B: 和运算

A|B: 或运算

A^B: 异或

~B: 非

**重点**

<<         *2

/>>      /2

## 包机制

为了更好的组织类，Java提供了包机制，用于区分类名的命名空间

```java
package pkg1[.pkg2[.pkg3...]];
```

- 如果一个源文件中没有使用包声明，那么其中的类，函数，枚举，注释等将被放在一个无名的包（unnamed package）中
- 一般公司域名倒置作为包名
- 当同时调用两个不同包中相同类名的类时，应该加上包名加以区别
- 如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略

**import 语句**

为了能够使用某一个包的成员，我们需要在Java程序中明确导入该包

```java
import package1[.package2…].(classname|*);
import java.util.Scanner;  // 导入Scanner类
import  static java.lang.Math.random;   // 导入一个类的静态字段和静态方法
import package.*;   //导入这个包下所有的类！
```

`import`语句应位于`package`语句之后，所有类定义之前

Java提供的重要的包：

```java
java.lang     // 包含所有的基本语言类(9,12章)
javax.swing   // 包含抽象窗口工具集中的图形、文本、窗口GUI类(11章)
Java.io       // 包含所有的输入输出类(10章)
java.util     // 包含实用类(9章)
java.sql      // 包含操作数据库的类(14章)
java.net      // 包含所有实现网络功能的类(13)
```

## JavDoc

javadoc命令是用来生成自己的API文档的

参数信息：

- `@author ` 作者名
- `@version`   版本号
- `@since `  指明需要最早使用的jdk版本
- `@param`  参数名
- `@return `  返回参数的情况
- `@throws`   异常抛出情况

#### 通过命令行生成`JavaDoc`文档

在`cmd`中当前java文件路径下输入`javadoc -encoding UTF-8 -charset UTF-8 文件名.java`

之后就能在目录下看到`index.html`文件

#### 通过IDEA生成`JavaDoc`文档

……（未完成）

## Scanner对象

```java
Scanner sc = new Scanner(System.in);
sc.next();   // 获取输入的数据
sc.nextLine();  // 获取一整行输入的数据
sc.hasNext();  // 判断是否还有输入的数据
sc.hasNextLine();  // 判断是否还有输入的数据
```

**注意**

`next()`不能读取有空格的字符串，会将后面的空格作为分隔符或是结束符

`nextLine()`可以读取有空格的分隔符，它返回的是**回车之前的**所有字符

`hasNext()`方法会判断接下来是否有非空字符.如果有,则返回`true`,否则返回`false`

``hasNextLine()` 方法会根据行匹配模式去判断接下来是否有一行(包括空行),如果有,则返回`true`,否则返回`false`

## 方法

**定义**

```java
修饰符 返回值类型 方法名(参数类型 参数名){
	'''
	方法体
	'''
	return 返回值；
}
```

`static`修饰符：跟类绑定，类存在时就存在，而普通方法是在类实例化对象之后才存在于对象中的

#### 方法重载

重载就是在一个类中，有相同的函数名称，但形参不同的函数

**重载规则**

- 方法名必须相同
- 参数列表必须不同(个数不同，或类型不同，参数排列顺序不同)
- 方法的返回类型可以相同也可以不同
- 仅仅返回类型不同不足以成为方法的重载

**实现理论**

方法名称相同时，编译器会根据调用方法的参数个数，参数类型等去逐个匹配，以选择对应的方法，如果匹配失败就会报错

#### 通过命令行传参

返回到Java文件的`src`目录下后，输入`java 包名.文件名 参数`，回车即可运行

#### 可变参数

```java
public static void function(double... number){  // number为形参数组名
    ...
}
```

一种方法只能指定一个可变参数，它必须是方法的最后一个参数，任何普通参数必须在他的声明之前.

## 数组

**声明**

```java
int[] nums;   //首选方案
int nums[];
```

**创建数组**

```java
nums = new int[10];
```

**连写**

```java
int[] nums = new int[10];
int[] nums = {1,2,3,4,5};
```

**数组的基本特性**

- 数组一旦被创建，其长度就不可更改
- 数组的元素类型必须一致
- 数组中的类型可以是任何数据类型，包括基本类型和引用类型
- 数组本身是在堆中的

#### **数组的使用**

For-Each 循环:`arrays.for`

```java
// JDK1.5, 没有下标
for(int array:arrays){    // 数组元素：数组名
    System.out.println(array);
}
```

数组作为方法参数、数组作为返回值

```java
pulic static int[] reverse(int[] arrays){
    int[] result = new int[arrays.length];
    for(int i=0,j=result.length-1;i<arrays.length;i++,j--){
        result[j] = arrays[i];
    }
    return result;
}
```

#### **多维数组**

```java
int[][] a[][] = new int[2][5];
int[][] a[][] = {{1,2},{3,4},{5,6},{7,8}};
```

遍历一个二位数组

```java
for (int[] ints : array1) {
    for (int anInt : ints) {
        System.out.print(anInt+"\t");
    }
    System.out.println();
}
```

#### Arrays类

> 由于数组对象本身并没有什么方法可以调用，但API中提供了一个工具类Arrays供使用，用于对数组对象进行一些基本操作

Arrays类中的方法都是ststic修饰的静态方法，可以直接使用类名进行调用

常用方法：

| 方法                          | 作用                           |
| ----------------------------- | ------------------------------ |
| `Arrays.fill`(数组，数组元素) | 给数组赋值                     |
| `Arrays.sort()`               | 对数组排序                     |
| `Arrays.equals()`             | 比较数组                       |
| `Arrays.binarySearch()`       | 查找数组元素                   |
| `Arrays.toString()`           | 打印数组元素(返回值是一个列表) |

#### 稀疏数组

当一个数组中大部分元素为0，或者为统一值的数组时，可以使用稀疏数组来保存该数组

稀疏数组的处理方式：

- 记录数组一共有几行几列，有多少个不同的值
- 把具体不同值的元素和行列及值记录在一个小规模数组中，从而缩小程序的规模

<img src="Grammar.assets/image-20211228003522319.png" alt="image-20211228003522319" style="zoom:50%;" />

左边是原始数组，右边是稀疏数组

```java
// 原始数组
int[][] array1 = new int[11][11];
array[1][2] = 1;
array[2][3] = 2;
// 创建一个稀疏数组
int[][] array2 = new int[sum+1][3];   //sum 为原始数组有效值的个数
array2[0][0] = 11;  
array2[0][1] = 11;   // 原始数组为11行11列
array2[0][2] = sum;
//遍历二维数组，将非0的值储存在稀疏数组中
int count = 0;
for(int i = 0;i < array1.length;i++) {
    for(int j = 0;j < array1[i].length;j++) {
        count++;
        array2[count][0] = i;
        array2[count][1] = j;
        array2[count][2] = array1[i][j];
    }
}
// 输出稀疏数组
for(int i = 0;i<array2.length;i++) {
    System.out.println(array2[i][0]+"\t" + array2[i][0]+"\t" + array2[i][0]+"\t");
}
// 还原稀疏数组
int[][] array3 = new int[array2[0][0]array2[0][1]];
for(int i=0;i<array2.length;i++) {
    array3[array2[i][0][array2[i][1]]] = array2[i][2];
}
```

输出结果

<img src="Grammar.assets/image-20211228005659540.png" alt="image-20211228005659540" style="zoom:50%;" />

## 冒泡排序

```java
public static int[] sort(int[] array) {      // 函数的返回类型是int[]
    int temp = 0;
    for(int i = 0;i<array.length-1;i++) {
        for(int j = 0;j<array.length-1-i;j++) {
            if (array[j+1]>array[j]) {
                temp = array[j];
                array[j] = array[j+1];
                array[j+1] = temp;
            }
        }
    }
}
```

---

## 面向对象`(OOP)`

### 构造方法

所有类默认存在一个构造方法(构造器)

规则：

- 构造方法必须和类的名字相同
- 必须没有返回类型，不能是`void`
- 一旦定义了有参构造，使用无参构造时，无参构造就必须显示定义

`alt + insert` 生成构造器

#### 实例变量和类变量

类中的成员变量分为实例变量和类变量

实例变量：不加`static`修饰的变量

类变量：加`static`修饰的变量(又称静态变量)

**两者区别**

- 不同对象的变量互不相同
- 所有对象共享类变量
- 通过类名直接访问类变量

#### 类方法和实例方法

类中的方法分为实例方法和类方法

实例方法：不加`static`修饰的方法

类方法：加`static`修饰的方法(又称静态方法)，`static`需放在方法类型的前面

**两者的区别**

- 对象调用实例方法

  - 只用当类实例化时，实例方法才会存在

- 类名调用类方法

  - 当类被加载到内存时，类方法即存在

  - 类方法不可以操作实例变量

#### `this`关键字

`this`关键字可以现在实例方法和构造方法中，但不可以出现在类方法中

`this`出现在构造方法中，代表用使用该构造方法创建的对象

`this`出现在实例方法中，代表调用当前方法的当前对象

### 访问权限

#### 私有类

用关键词`private`修饰的成员变量和方法称为私有变量和私有方法

```java
class Tom{
    private float weight;     //weight私有变量
    private float f(float a,float b) {    //私有方法
        return a+b;
    }
}
```

当在另一个类中用Tom创建了一个对象之后，该对象不能访问自己的私有变量和私有方法

```java
class Tom {
    void g() {
        Tom cat = new Tom();
        cat.weight = 23f;  // 非法
        float sum = cat.f(3,4);   // 非法
    }
}
```

####  公共类

用关键词`public`修饰的成员变量和方法称为公共变量和公共方法

类的对象能访问自己的`public`变量和方法

一个类中只能有只能有一个`public class`

#### 友好类

不用`private,public,protected`修饰的成员变量和方法被称为友好变量和友好方法

```java
class Tom {
    float weight;   // 友好变量
    float f(float a,float b) {   // 友好方法
        return a+b;
    }
}
```

当在另一个类中用类Tom创建了一个对象后，如果这个类与Tom类在**同一个包**中，那么该对象能够访问自己的友好变量和方法。在任何一个**与Tom同一包中**的类中，也可以通过Tom类的类名访问Tom类的类友好成员变量和友好方法

如果源文件使用`import`语句引入了另外一个包中的类，并用该类创建了一个对象，那么该类的对象不能访问自己的友好变量和方法

#### 受保护类

用关键词`protected`修饰的成员变量和方法称为受保护变量和受保护方法

`protected`用于继承关系，定义为`protected`的字段或方法可以被子类方法，以及子类的子类访问

```java
class Tom {
    protected float weight;   // 受保护变量
    protected float f(float a,float b) {   // 受保护方法
        return a+b;
    }
}
```

当在另一个类中用类Tom创建了一个对象后，如果这个类与Tom类在**同一个包**中，那么该对象能够访问自己的受保护变量和方法。在任何一个**与Tom同一包中**的类中，也可以通过Tom类的类名访问Tom类的类受保护成员变量和方法

注意：

- 不能用`protected`和`private`修饰类
- 修饰符按访问权限从高到低排序是:  `public,protected,友好类,private`

### **三大基本特性**

#### 继承

```java
class 子类名 extends 父类名 {
    ...
}
```

Java不支持单继承，即子类只能有一个父类

一个类的声明中没有使用`extends`关键字，则默认为时Object的子类.Object是`Java.lang`包中的类

快捷键：按**`ctrl + H`**查看继承关系

**子类和父类在一个包中的继承性**

如果子类与父类在一个包中，那么子类自然地继承了父类中不是`private`的成员和方法，继承的成员或方法访问权限保持不变

**子类和父类不在一个包中的继承性**

如果子类与父类不在一个包中，父类中的`private`和友好访问权限的成员变量不能被继承

**成员变量的隐藏**

在编写子类时，如果声明的成员变量与继承的变量相同(声明类型可以不同)，子类会隐藏掉继承的成员变量，即子类对象以及子类自己定义的方法操作与父类同名的变量是指子类自己重新声明这个成员变量

子类仍然可以调用从父类继承的方法操作隐藏成员变量

##### **super关键字**

如果想在子类中使用被隐藏的成员变量或方法就需要时用`super`关键字

**使用`super`调用父类的构造方法**

当用子类的构造方法创建一个对象时，**子类的构造方法总是默认的调用父类的某个构造方法**，如果子类没有明显的指明使用父类的那个构造方法，就调用父类的无参构造

由于子类不继承父类的构造方法，因此子类指明调用父类的构造方法时需要使用`super`关键字来调用父类的构造方法，而且`super`必须是子类构造方法的第一条语句，即如果子类的构造方法中，如果没有明显的写出`super`关键字来调用父类的某个构造方法，则默认的有：

```java
super();
```

`super`和`this`不能同时调用构造方法

##### final关键字

final类不能有子类，即不能被继承

final方法不允许子类重写

final修饰的成员变量或局部变量是常量

#### 封装

核心思想：“**高内聚，低耦合**”

高内聚：类的内部数据操作细节自己完成，不允许外部干涉

低耦合：仅暴露少量的方法给外部使用

封装的意义：

1. 提高程序的安全性，保护数据
2. 隐藏代码的实现细节
3. 统一接口
4. 增加系统可维护性

**信息隐藏**：通常，应禁止直接访问一个对象中数据的实际表示，而应通过操作接口来访问

面向对象提倡应该设置属性私有，调用方法来该改变自己的属性，类应当提供操作数据的方法，这样就可以规避一些错误，防止对系统的破坏

```java
public class Student{
    private int age;
    public void setAge(int age) {
        if(age >= 7&&age <= 28) {   // 对age进行规范
            this.age = age;
        }
    }
    public int getage() {
        return age;
    }
}
```

#### 多态

Java中的多态有两种：**重载**与**重写**

##### 方法重载

重载就是在一个类中，有相同的函数名称，但形参不同的函数

**重载规则**

- 方法名必须相同
- 参数列表必须不同(个数不同，或类型不同，参数排列顺序不同)
- 方法的返回类型可以相同也可以不同
- 仅仅返回类型不同不足以成为方法的重载

##### 方法重写

即方法覆盖，子类通过重写可以隐藏已继承的实例方法，通过方法重写子类可以把父类的行为和状态改变为自身的状态和行为

方法重写必须要有继承关系

快捷键：`Alt + Insert Override` 

**重写的语法规则**

- 重写的方法类型与父类的方法一致，方法名字，参数个数，参数类型和父类方法完全相同
- 重写父类方法时，不可以降低方法的访问权限，但可以提高访问权限
- 抛出的异常，范围可以缩小，但不能扩大

### 对象的上转型对象

```java
class A {
    ...
}
class B extends A {
    ...
} 
public class Main {
    public static void main(String[] args) {
        A a = new B();  // a是B的上转型对象
        // 对象能执行哪些方法主要看左边，跟右边关系不大
    }
}
```

上转型对象具有子类继承或隐藏的变量和继承或重写的方法，但不具有子类新增的变量或者方法.上转型对象操作子类继承或重写的方法，等价于子类对象去调用这些方法

可以将对象的上转型对象再强制转换到一个子类对象，则该对象又具备了子类所有的属性和功能

```java
(B) a;  // 从高 -> 低需要进行强制类型转换
```

- 把子类转换成父类，向上转型(自动转换)；
- 把父类转换成子类，向下转型(强制转换)；

注：如果子类重写了父类的静态方法，那么子类对象的上转型对象不能调用子类重写的静态方法，只能调用父类的静态方法

**`instanceof`运算符**

该运算符是一个二元运算符，左边的操作元是一个对象，右边是一个类.  当左边对象是右边的类或子类创建的对象时，该运算符运算的结果是`true`，否则是`false`

用`instanceof`关键字来判断两个类型是否相似，以便于进行强制转换

如果`x instanceof y`编译能通过，那么两者能够进行相互转换

```java
A instanceof B   // Object -> A -> B,返回true
A instanceof String  // 同级不能进行比较，编译报错   Object -> String
```

### 抽象类

用关键字`abstract`修饰的类称为`abstract`类，用`abstract`修饰的方法称为`abstract`方法

`abstract`类只关心操作，不关心实现操作的具体细节

*使用多态进行程序设置的核心技术之一就是使用上转型对象，即将`abstract`类声明对象作为其子类的上转型对象，那么这个对象就可以调用子类重写的方法* 

```java
abstract class A {   // abstract 类
    abstract int min(int n ,int m);   // abstract 方法
}
```

如果一个非抽象类是某个抽象类的子类，那么他必须实现父类中的抽象方法

注意：

- 与普通类相比，抽象类可以有抽象方法，也可以有抽象方法，也可以没有抽象方法，但抽象方法必须存在抽象类中
- `abstract`类不能用`new`运算符创建

- `abstract` 类的子类可以重写父类的抽象方法，也可以继承，但仍需要他的子类实现

### 接口

```java
interface A {   // 定义接口
    ...
}
class B implements A {  // 实现接口
    ...
}
```

接口不允许实现方法，接口中所有方法的定义都是`public abstract`，允许省略`public abstract`修饰符

接口中只能有常量不能有变量，即属性都是`public static final`

接口也可以被继承，通过`extends`关键字。由于接口中的常量和方法都是`public`的，子接口将继承父接口的全部方法和常量

接口只关心操作，不关心操作的具体实现

类可以实现接口，一个类如果继承了某个接口，就必须实现接口中的方法.如果一个类声明实现了一个接口，但没有实现接口中的全部方法，那么这个类必须是`abstract`类

接口可以是多继承，即一个类可以继承多个接口，`extends`关键字后面用逗号隔开

*当不希望某些类通过继承使得它们具有相同的方法时，可以考虑让他们是实现相同的接口而不是把他们声明为一个类的子类*

类重写的接口方法以及接口中的常量可以被类的对象调用，而且常量也可以用接口名直接调用

接口不能实例化，没有构造方法

#### 接口回调

接口回调是指可以把实现某一接口的类创建的对象的引用赋值给该接口声明的接口变量在中，那么该接口变量就可以调用被类实现的接口方法

```java
interface A {
    ...
}
class B implements A {  // B实现了A接口
    ...
}
A a;  // a即为接口变量
a = new B();   // 接口回调
```

与实现某一接口的类创建的对象相比，接口回调创建的接口变量能调用被类实现的接口方法，而不具有类原有的方法，类似于类的上转型变量

#### 接口与多态

接口产生的多态是指不同类在实现同一接口时可能具有不同的实现方式，那么接口变量在回调接口方法时就可能具有多种形态

```java
interface A {
    ...
}
class B implements A {
    ...
}
class C implements A {
    ...
}
A a;
a = new B();
a = new C();   // a中的方法发生改变
```

#### 接口变量做参数

如果一个方法的参数是接口类型，我们就可以将任何实现该接口的类的实例的引用传递给该接口参数，那么接口参数就可以回调类实现接口方法。

举个栗子：

```java
interface SpeakHello {
    void speakHello();
}
class Chinese implements SpeakHello {
    public void SpeakHello() {
        System.out.println("你好，吃饭了吗？")；
    }
}
class English implements SpeakHello {
    public void SpeakHello() {
        System.out.println("你好，天气怎样？")；
    }
}
class KindHello {
    public void lookHello(SpeakHello s) {  // 接口类型参数
        s.speakHello();    // 接口回调
    }
}
public class Mian {
    public static void main(String[] args) {
        KingHello kindHello = new KindHello();
        kingHello.lookHello(new Chinese());
        KingHello.lookHello(new English());
    }
}
```



**抽象类，接口的区别：**

1. 抽象类具体实现和规范都有，接口只有规范
2. 接口中只能有常量不能有变量，`abstract`类可以有常量也可以有变量
3. 接口中只能有`abstract`方法，`abstract`类中可以有非`abstract`方法
4. 如果某个问题需要子类继承父类的一些变量和非`abstract`方法，那么使用`abstract`类可以更好的解决

### 开闭原则

开闭原则就是让设计的系统应对拓展开放，对修改关闭。本质是指当系统中增加新的模块时，不需要修改现有的模块，使系统变得易于维护

---

## 内部类

内部类可以调用外部类的`private`属性和`private`方法

内部类的外嵌类的成员变量在内部类中依然有效，内部类中的方法也可以调用外嵌类中的方法

内部类的类体中不可以声明**类变量**和**类方法**，外嵌类的类体可以用内部类声明对象，作为外嵌类的成员

```java
public class A {
    B b;  // 用内部类声明对象
    class B{
        ...
    }
}
```

内部类仅供他的外嵌类使用，其他类不可以用某个类的内部类声明对象

静态内部类只能访问外部类的静态属性和方法，因为外部类的非静态方法在实例化时候才存在

局部内部类：在外部类的**方法中定义**的类

**匿名内部类**

匿名类就是一个子类，由于无名可用，所以不可能用匿名类声明对象，但却可以直接用匿名类创建一个对象

匿名类一定是内部类

```java
new Bank() {    // 表示用Bank类的匿名子类创建的对象
    匿名类类体
}；
```

**和接口有关的匿名类**

Java允许直接用接口名和一个类体创造一个匿名对象，此类体被认为是实现了接口的匿名类

```java
interface UserService {
    ...
}

(UserService userservice = )new UserService(){//new UserService()为实现了接口的匿名内部类
    @Override                                    
    实现了接口的匿名类的类体
}

void f(UserService x)   // 接口类型的参数函数,x是接口
f(new UserService() {   // 向f传递一个匿名对象
    实现了接口的匿名类类体
})
```

---

## 异常

Java将异常当作对象来处理，并定义了一个基类`java.lang.Throwable`作为所有异常的超类

Java API中已经定义了许多异常类，这些异常类分为两大类，错误`Error`和异常`Exception`

异常对象可以通过调用如下方法得到或输出有关异常的信息：

`public String getMessage();`

`public void printStackTrace();`

`public String toString();`

**`try-catch`语句**

使用`try-catch`语句捕获到错误后，程序可以继续往下执行而不是终止，并能在`catch`中处理异常

```java
try{
    包含可能发生异常的语句
}catch(ExceptionSubClass e){    // ExceptionSubClass想要捕获的异常类型，最高为Throwable
    System.out.println(e.getMessage());  // 输出异常信息
}catch(ExceptionSubClass e){
    ...
}finally{
    最终都要执行的语句
}
```

- 各个`catch`参数中的异常类都是`Exception`的某个子类，表明`try`部分可能发生的异常，这些子类之间不能有父子关系，否则保留一个含父类参数的`catch`即可
- 最高的异常类型应写在最后

- 如果在`try-catch`语句中执行了`return`语句，那么`finally`语句仍然会被执行
- `try-catch`语句中执行力程序退出代码，即执行`System.exit(0)`,则不会执行`finally`语句

`throw`**关键字**

主动抛出异常，一般用在方法中

```java
if(判断语句) {
    throw new ExceptionSubClass();
}
```

**自定义异常类**

编写程序时可以拓展`Exception`类来定义自己的异常类

```java
public class BankException extends Exception {
    ...
}
```

**`throws`关键字**

一个方法可以在声明时使用`throws`关键字声明要产生的若干个异常，并在方法体中使用`throw`关键字抛出该异常，导致该方法结束

```java
public void income(int in,int out) throws BankException,... {
    if (in<=0||out>=0||in + out<=0) {
        throw new BankException();
    }
}
```

## `Lambda`表达式

函数式接口：

任何接口，如果只包含唯一一个抽象方法，那么他就是一个函数式接口

```java
public interface Runnable {
    public abstract void run(int t);
}
```

对于函数式接口，我们可以用`lambda`表达式来创建函数式接口的对象

```java
runnable = (int t) -> {// 重写run方法};
```

注：`lambda`表达式只能创建函数式接口的对象。
