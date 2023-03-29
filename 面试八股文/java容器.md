### 1.Java 容器都有哪些？

Java 容器分为 Collection 和 Map 两大类，其下又有很多子类，如下所示：

• Collection

• List
			ArrayList

​			LinkedList

​			Vector

​			Stack
• Set
​			HashSet

​			LinkedHashSet

​			TreeSet
• Map
​			HashMap

​			LinkedHashMap

​			TreeMap

​			ConcurrentHashMap

​			Hashtable

### 2.Collection 和 Collections 有什么区别？

• Collection 是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如 List、Set 等。

• Collections 是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法： Collections. sort(list)。

### 3.List、Set、Map 之间的区别是什么？

List、Set、Map 的区别主要体现在两个方面：元素是否有序、是否允许元素重复。
三者之间的区别，如下表：

|      |             | 元素有序           | 允许元素重复               |
| ---- | ----------- | ------------------ | -------------------------- |
| List |             | 是                 | 是                         |
| Set  | AbstractSet | 否                 | 否                         |
| Set  | HashSet     | 否                 | 否                         |
| Set  | TreeSet     | 是（用二叉树排序） | 否                         |
| Map  | AbstractMap | 否                 | Key值必须唯一，value可重复 |
| Map  | HashMap     | 否                 | Key值必须唯一，value可重复 |
| Map  | TreeMap     | 是（用二叉树排序） | Key值必须唯一，value可重复 |



### 4.HashMap 和 Hashtable 有什么区别？

• 存储：HashMap 运行 key 和 value 为 null，而 Hashtable 不允许。

• 线程安全：Hashtable 是线程安全的，而 HashMap 是非线程安全的。

• 推荐使用：在 Hashtable 的类注释可以看到，Hashtable 是保留类不建议使用，推荐 在 单 线 程 环 境 下 使 用 HashMap 替 代 ， 如 果 需 要 多 线 程 使 用 则 用ConcurrentHashMap 替代。

### 5.如何决定使用 HashMap 还是 TreeMap？

对于在 Map 中插入、删除、定位一个元素这类操作，HashMap 是最好的选择，因为相对而言 HashMap 的插入会更快，但如果你要对一个 key 集合进行有序的遍历，那 TreeMap 是更好的选择。

### 6.说一下 HashMap 的实现原理？

HashMap 基于 Hash 算法实现的，我们通过 put(key,value)存储，get(key)来获取。当传入 key 时，HashMap 会根据 key. hashCode() 计算出 hash 值，根据 hash 值将 value 保存在 bucket 里。当计算出的 hash 值相同时，我们称之为 hash 冲突，HashMap 的做法是用链表和红黑树存储相同 hash 值的 value。当 hash 冲突的个数比较少时，使用链表否则使用红黑树。

### 7.说一下 HashSet 的实现原理？

HashSet 是基于 HashMap 实现的，HashSet 底层使用 HashMap 来保存所有元素，因此HashSet 的实现比较简单，相关 HashSet 的操作，基本上都是直接调用底层 HashMap 的相关方法来完成，HashSet 不允许重复的值。

### 8.ArrayList 和 LinkedList 的区别是什么？

• 数据结构实现：ArrayList 是动态数组的数据结构实现，而 LinkedList 是双向链表的数据结构实现。

• 随机访问效率：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为LinkedList 是线性的数据存储方式，所以需要移动指针从前往后依次查找。

• 增加和删除效率：在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为 ArrayList 增删操作要影响数组内的其他数据的下标。

综合来说，在需要频繁读取集合中的元素时，更推荐使用 ArrayList，而在插入和删除操作较多时，更推荐使用 LinkedList。

### 9.如何实现数组和 List 之间的转换？

• 数组转 List：使用 Arrays. asList(array) 进行转换。

• List 转数组：使用 List 自带的 toArray() 方法。

代码示例：

```java
// list to array
List<String> list = new ArrayList<String>();
list. add("王磊");
list. add("的博客");
list. toArray();
// array to list
String[] array = new String[]{"王磊","的博客"};
Arrays. asList(array);
```

### 10.ArrayList 和 Vector 的区别是什么？

• 线程安全：Vector 使用了 Synchronized 来实现线程同步，是线程安全的，而ArrayList 是非线程安全的。

• 性能：ArrayList 在性能方面要优于 Vector。

• 扩容：ArrayList 和 Vector 都会根据实际的需要动态的调整容量，只不过在 Vector 扩容每次会增加 1 倍，而 ArrayList 只会增加 50%。

### 11.Array 和 ArrayList 有何区别？

• Array 可以存储基本数据类型和对象，ArrayList 只能存储对象。

• Array 是指定固定大小的，而 ArrayList 大小是自动扩展的。

• Array 内置方法没有 ArrayList 多，比如 addAll、removeAll、iteration 等方法只有 ArrayList 有。

### 12.在 Queue 中 poll()和 remove()有什么区别？

• 相同点：都是返回第一个元素，并在队列中删除返回的对象。

• 不 同 点 ： 如 果 没 有 元 素 poll() 会 返 回 null ， 而 remove() 会 直 接 抛 出NoSuchElementException 异常。

代码示例：

```java
Queue<String> queue = new LinkedList<String>();
queue.offer("string"); // add
System.out.println(queue.poll());
System.out.println(queue.remove());
System.out.println(queue.size());
```

### 13.哪些集合类是线程安全的？

Vector、Hashtable、Stack 都是线程安全的，而像 HashMap 则是非线程安全的，不过在JDK 1.5 之后随着 Java. util. concurrent 并发包的出现，它们也有了自己对应的线程安全类，比如 HashMap 对应的线程安全类就是 ConcurrentHashMap。

### 14.迭代器 Iterator 是什么？

Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中的 Enumeration，迭代器允许调用者在迭代过程中移除元素。

### 15.Iterator 怎么使用？有什么特点？

Iterator 使用代码如下：

```java
List<String> list = new ArrayList<>();
Iterator<String> it = list. iterator();
while(it. hasNext()){
     String obj = it. next();
     System. out. println(obj);
}
```

Iterator 的特点是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，就会抛出 ConcurrentModificationException 异常。

### 16.Iterator 和 ListIterator 有什么区别？

• Iterator 可以遍历 Set 和 List 集合，而 ListIterator 只能遍历 List。

• Iterator 只能单向遍历，而 ListIterator 可以双向遍历（向前/后遍历）。

• ListIterator 从 Iterator 接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

### 17.怎么确保一个集合不能被修改？

• 可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个只 读 集 合 ， 这 样 改 变 集 合 的 任 何 操 作 都 会 抛 出 Java. lang. UnsupportedOperationException 异常。

• 示例代码如下：

```java
List<String> list = new ArrayList<>();
list.add("x");
Collection<String> clist = Collections. 
unmodifiableCollection(list);
clist.add("y"); // 运行时此行报错
System.out.println(list. size());
```

