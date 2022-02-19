## 集合

Java的`java.util`包主要提供了以下三种类型的集合：

- `List`：一种有序列表的集合
- `Set`：一种保证没有重复元素的集合
- `Map`：一种通过键值`key-value`查找的映射表集合

Java访问集合总是通过统一的方式——迭代器`Iterator`来实现，它最明显的好处在于无需知道集合内部元素是按什么方式存储的。

### `List`

`List<E>`接口主要的接口方法：

| 方法                            | 作用                           |
| ------------------------------- | ------------------------------ |
| `boolean add(E e)`              | 在末尾添加一个元素             |
| `boolean add(int index, E e)`   | 在指定索引添加一个元素         |
| `list.remove(int index)`        | 删除指定索引的元素             |
| `boolean list.remove(Object e)` | 删除某个元素                   |
| `list.get(int index)`           | 获取指定索引的元素             |
| `int size()`                    | 获取链表大小（包含元素的个数） |
| `boolean contains(Object o)`    | 判断是否包含指定元素           |
| `int indexOf(Object o)`         | 返回某个元素的索引             |

`List`允许添加重复元素，并且允许添加`null`

**创建`list`**

```java
List<String> list = new ArrayList<>();
List<Integer> list = List.of(1,2,5);  // 方式二,不支持传入null 
```

**遍历`list`**

```java
// 不推荐，代码复杂，如果换成LinkedList效率低
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("a","b","c");
        for (int i=0; i<list.size(); i++) {
            String s = List.get(i);
            System.out.println(s);
        }
    }
}

// 使用迭代器Iterator访问List 推荐
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("a","b","c");
        for (Iterator<String> it = list.iterator(); it.hasNext();) {  // 判断是否有下一个元素
            String s = it.next();  // 返回下一个元素
            System.out.println(s);
        }
    }
}
```

**查找元素**

`List`内部不是通过`==`来判断两个元素是否相等，而是使用`equals()`方法判断两个元素是否相等

`== `是判断两个变量或实例是否指向同一个内存空间

`equals()`是判断两个变量或实例所指的内存空间的值是不是相同

```java
public class Main {
    public static void main(String[] args) {
        List<Person> list = List.of(
            new Person("Xiao Ming"),
            new Person("Xiao Hong"),
            new Person("Bob")
        );
        System.out.println(list.contains(new Person("Bob"))); // false
    }
}

class Person {
    String name;
    public Person(String name) {
        this.name = name;
    }
}
```

因此，要正确使用`List`的`contains()`、`indexOf()`这些方法，放入的实例必须正确覆写`equals()`方法，否则，放进去的实例，查找不到。

Java标准库提供的`String`、`Integer`等已经覆写了`equals()`方法

```java
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("A", "B", "C");
        System.out.println(list.contains(new String("C"))); // true
        System.out.println(list.indexOf(new String("C"))); // 2
    }
}
```

### `Map`

`Map<K, V>`是一种键-值映射表，作用是能高效通过`key`快速查找`value`

用`Map`来实现根据`name`查询某个`Student`的代码如下：

```java
public class Main {
    public static void main(String[] args) {
        Student s = new Student("Xiao Ming", 99);
        Map<String, Student> map = new HashMap<>();
        map.put("Xiao Ming", s); // 将实例映射并关联
        Student target = map.get("Xiao Ming"); // 通过key查找并value
        System.out.println(target == s); // true，同一个实例
        System.out.println(target.score); // 99
        Student another = map.get("Bob"); // 通过另一个key查找
        System.out.println(another); // 未找到返回null
    }
}

class Student {
    public String name;
    public int score;
    public Student(String name, int score) {
        this.name = name;
        this.score = score;
    }
}
```

当调用`put(K key, V value)`方法时，就把`key`和`value`做了映射并放入`Map`。和`List`类似，`Map`也是一个接口，最常用的实现类是`HashMap`.

如果只是想查询某个`key`是否存在，可以调用`boolean containsKey(K key)`方法

`map.get(key)`返回`value`

一个`key`只能关联一个`value`,如果放入的`key`已经存在，`put()`方法会返回被删除的旧的`value`。

此外，在一个`Map`中，虽然**`key`不能重复，但`value`是可以重复的**

注意：`Map`不保证顺序，而`List`是有序的

**创建`Map`**

```java
Map<Key,Value> map = new HashMap<>();
```

**遍历`Map`**

```java
public class Main {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 123);
        map.put("pear", 456);
        map.put("banana", 789);
        // 使用keySet()
        for(String key : map.keySet()) {
            Integer value = map.get(key);
            System.out.println(key + "=" + value);
        }
        // 使用entrySet(),同时遍历key和value
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String key = entry.getKey();
            Integer value = entry.getValue();
            System.out.println(key + " = " + value);
        }
    }
}
```

**问题发现：**我们放入`Map`的`key`是字符串`"a"`，但是，当我们获取`Map`的`value`时，传入的变量不一定就是放入的那个`key`对象。

```java
public class Main {
    public static void main(String[] args) {
        String key1 = "a";
        Map<String, Integer> map = new HashMap<>();
        map.put(key1, 123);

        String key2 = new String("a");
        map.get(key2); // 123

        System.out.println(key1 == key2); // false
        System.out.println(key1.equals(key2)); // true
    }
}

```

因为在`Map`的内部，对`key`做比较是通过`equals()`实现的，这一点和`List`查找元素需要正确覆写`equals()`是一样的，即正确使用`Map`必须保证：作为`key`的对象必须正确覆写`equals()`方法。

我们经常使用`String`作为`key`，因为`String`已经正确覆写了`equals()`方法。但如果我们放入的`key`是一个自己写的类，就必须保证正确覆写了`equals()`方法。

**`HashCode()`**

通过`key`计算索引的方式就是调用`key`对象的`hashCode()`方法，它返回一个`int`整数。`HashMap`正是通过这个方法直接定位`key`对应的`value`的索引，继而直接返回`value`。

**`TreeMap`**

`SortedMap`会在内部对`Key`进行排序, `SortedMap`是一种接口，实现类为`TreeMap`

作为`SortedMap`的Key必须实现`Comparable`接口。

如果作为Key的class没有实现`Comparable`接口，那么必须传入一个`Comparable`对象：

```java
public class Main {
    public static void main(String[] args) {
        Map<Person, Integer> map = new TreeMap<>(new Comparator<Person>() {
            public int compare(Person p1, Person p2) {
                return p1.name.compareTo(p2.name);
            }
        });
        map.put(new Person("Tom"), 1);
        map.put(new Person("Bob"), 2);
        map.put(new Person("Lily"), 3);
        for (Person key : map.keySet()) {
            System.out.println(key);
        }
        // {Person: Bob}, {Person: Lily}, {Person: Tom}
        System.out.println(map.get(new Person("Bob"))); // 2
    }
}

class Person {
    public String name;
    Person(String name) {
        this.name = name;
    }
    public String toString() {
        return "{Person: " + name + "}";
    }
}
```



`String`、`Integer`这些类已经实现了`Comparable`接口，因此可以直接作为Key使用。

### `Set`

`Set`用于储存不重复的元素集合,  最常用的`Set`实现类是`HashSet`

我们经常用`Set`去除重复元素

主要方法：

| 方法                         | 作用                |
| ---------------------------- | ------------------- |
| `boolean add(E e)`           | 将元素添加进`Set`   |
| `boolean remove(Object e)`   | 将元素从`Set`中删除 |
| `boolean contains(object e)` | 判断是否包含元素    |
| `int set.size()`             | 返回`Set`的大小     |

- `HashSet`是无序的
- `TreeSet`是有序的，因为它实现了`SortedSet`接口。和使用`TreeMap`的要求一样，添加的元素必须正确实现`Comparable`接口

### `Queue`

队列（`Queue`）是一种经常使用的集合。`Queue`实际上是实现了一个先进先出的有序表。它和`List`的区别在于，`List`可以在任意位置添加和删除元素，而`Queue`只有两个操作：

- 把元素添加到队列末尾；
- 从队列头部取出元素。

主要方法：

| 方法                                | 作用                           |
| ----------------------------------- | ------------------------------ |
| `int size()`                        | 获取队列长度                   |
| `boolean add(E)`/`boolean offer(E)` | 添加元素到队尾                 |
| `E remove()`/`E poll()`             | 获取队首元素并从队列中删除     |
| `E element()`/`E peek()`            | 获取队首元素但并不从队列中删除 |

注意到添加、删除和获取队列元素总是有两个方法，这是因为在添加或获取元素失败时，这两个方法的行为是不同的。

|                    | throw Exception | 返回false或null      |
| :----------------- | :-------------- | :------------------- |
| 添加元素到队尾     | `add(E e)`      | `boolean offer(E e)` |
| 取队首元素并删除   | `E remove()`    | `E poll()`           |
| 取队首元素但不删除 | `E element()`   | `E peek()`           |

### `Deque`

双端队列，允许两头都进，两头都出

|                    | **Deque**                         |
| :----------------- | :-------------------------------- |
| 添加元素到队尾     | `addLast(E e) / offerLast(E e)`   |
| 取队首元素并删除   | `E removeFirst() / E pollFirst()` |
| 取队首元素但不删除 | `E getFirst() / E peekFirst()`    |
| 添加元素到队首     | `addFirst(E e) / offerFirst(E e)` |
| 取队尾元素并删除   | `E removeLast() / E pollLast(`    |
| 取队尾元素但不删除 | `E getLast() / E peekLast()`      |

### `Stack`

栈（Stack）是一种后进先出的数据结构，即最后进`Stack`的元素一定最早出`Stack`。

`Stack`只有入栈和出栈的操作：

- 把元素压栈：`push(E)`
- 把栈顶的元素“弹出”：`pop()`
- 取栈顶元素但不弹出：`peek()`