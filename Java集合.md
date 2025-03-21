# Java集合

Java平台提供了一个全新的集合框架（Collection Framework）。

Java 2集合框架包括集合接口、抽象类和实现类3部分。它们的类型主要有3种：集`Set`​、列表`List`​、映射`Map`​

* ​`Set`​：对象不按特定方式排序，不可重复。集有多种类的实现体。
* ​`List`​：主要将对象以线性方式储存，通常一个头一个结尾。
* ​`Map`​：`Map`​中的每一个项目都成对出现，表现为“键-值”关系，并且关键字具有唯一性。

集合是Java中比较基础的模块，所有的集合类都处于 `java.util`​包下，其中支持多线程的集合类位于 `java.util.concurrent`​包下

# 容器`Collection`​和迭代器`Iterator`​

## 容器/集合Collection

​`Collection`​是一个接口，它是一个高度抽象出来的接口，定义了集合的基本操作： 添加、删除、清空、遍历、是否为空、获取大小等方法。

​`Collection`​接口主要有 2 个子分支： `List`​和 `Set`​，并且定义了 `AbstractCollection`​抽象类让其他类继承，`AbstractCollection`​实现了 `Collection`​中的绝大部分方法；`AbstractList`​和 `AbstractSet`​都继承于 `AbstractCollection`​。

​`Collection`​中的元素必须是对象，不能是Java的基本数据类型，所以可将集合转化为任何其他的对象数组，但不能转化为基本类型的数组。

*注意：*​*​`Collection`​*​*的方法中不包括随机访问元素的*​*​`get()`​* ​*方法，所以访问*​*​`Collection`​*​*的元素必须使用迭代器。*

## 迭代器Iterator

​`Collection`​接口的`Iterator()`​方法用于返回一个迭代器`Iterator`​。

迭代器提供了一种遍历容器中元素的方式，也即是说：我们可以通过迭代器来遍历集合元素。`Iterator`​迭代器接口定义了迭代器所应该具有的功能。

迭代器 Iterator接口定义了迭代器应具备的功能，

* ​`boolean hasNext()`​判断是否存在可以访问的元素。
* ​`Object next()`​返回下一个元素，如果到了集合末尾，将抛出`NoSuchElementException`​异常。
* ​`void remove()`​删除上次访问的元素，必须紧跟访问之后执行，如果已被删除，就会抛出`IlleagalStateException`​异常。

其中 `hasNext()`​和 `next()`​方法由具体的容器来实现，迭代器只能通过容器本身得到，每个容器都通过内部类实现了自己的迭代器，因此迭代器的使用方式如下：

```java
@Test
    public void test(){
        List<Integer> list = new ArrayList<>(6);
        for (int i = 0; i < 6; i++) {
            list.add(i);
        }
        // 迭代器只能通过容器本身得到 （PS：可能有些容器会实现一些迭代器的子接口，诸如ListIterator，只是一些优化）
        Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {//iterator.hasNext() 用于检查是否有下一个元素。如果有，iterator.next() 将返回下一个元素，并将其打印到控制台。
            System.out.println(iterator.next());
        }
    				}
```

*迭代器有一个重要的特性即故障快速修复，* 当一个线程正在使用迭代器遍历集合时，如果另一个线程对该集合的结构进行了修改（例如添加、删除元素等操作），迭代器会立即检测到这种变化，并抛出 `ConcurrentModificationException`​ 异常，而不是在后续的某个不确定时间点产生不可预期的结果。 

# List

列表、链表，`List`​，Java 的 `List`​ 是非常常用的数据类型。`List`​ 是有序的 `Collection`​。Java `List`​ 一共三个实现类：分别是`ArrayList`​、`Vector`​ 和`LinkedList`​。

## List接口方法

List比Collection多了添加方法add和addAll查找方法**get,indexOf,set**等方法，**并且支持index下标操作**（`Collection`​是无序的，没有顺序的概念，不支持索引操作）。

## ListIterator

​`ListIterator`​继承了`Iterator`​接口，并没有对其进行扩展。ListIterato主要是为了支持对`List`​的访问， 它是一个双向迭代器。

## ArrayList

数组，这个也应该是用的最多的。最常见的。

* 支持快速随机访问

* 每个元素之间不能有间隔
* 在ArrayList中间位置插入或删除元素时，需要对数组进行复制、移动、代价比较高
* 覆盖了函数`clone()`​，他是可以被克隆的。
* 实现了`java.io.Serializable`​接口，支持序列化，能通过序列化进行传输。
* 适合随机**查找和遍历**，不适合**插入和删除**。

ArrayList是实现了基于动态数组的数据结构，因为地址连续，一旦数据存储好了，查询操作效率较高，同样因此，ArrayList要移动数据，所以插入和删除操作效率比较低。

```java
public ArrayList(int initialCapacity) {//指定大小的ArrayList
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
public ArrayList() {//默认大小
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

```

## LinkedList

​`LinkedList`​ 是用链表结构存储数据的，很适合数据的动态插入和删除，随机访问和遍历速度比较慢（`linkedList`​要移动指针）。

另外，他还提供了 List 接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作堆栈、队列和双向队列使用。

* LinkedList既是List接口的实现也是Queue的实现（Deque）。`LinkedLis`​t实现了*​`Serializable`​*​ *、*​*​`AbstractSequentialList`​*​ *、*​*​`Deque`​*​ *、*​*​`Cloneable`​*​。

LinkedList基于链表的数据结构，地址是任意的，所以开辟内存空间的时候不需要连续地址，对于新增和删除操作add和remove，LinkedList比较占优势。因为要移动指针。所以查询操作性能比较低。

## ~~Vector~~

与ArrayList相似，但是Vector是同步的。所以说Vector是线程安全的动态数组。它的操作与ArrayList几乎一样。

* **线程安全**：不同的是它<u>支持线程的同步</u>，即某一时刻只有一个线程能够写 Vector，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费。因此，访问它比访问 ArrayList 慢。

​`Vector`​和`Stack`​是历史遗留产物，在JDK的后续发展中都有对应的替代产物

​`Vector`​是线程安全的 `List`​，在实际的开发中我们可以使用 `CopyOnWriteArrayList`​来代替；`Stack`​提供了栈功能，我们可以使用 `LinkedList`​来代替。

# 集`Set`​

如果说List对集合加了有序性的化，那么Set就是对集合加上了唯一性。它代表的是**数学概念中的集合——不能有重复的元素**。

​`Set`​集合家庭中，供我们使用的主要有： `TreeSet`​、`HashSet`​以及 `LinkedHashSet`​这三个类。

* ​`TreeSet`​：有序的存放，线程不安全，可以对Set集合中的元素进行排序，由红黑树来实现排序。
* ​`HashSet`​：底层数据结构由HashMap的键来实现。不保证集合中元素的顺序，即不能保证迭代的顺序与插入的顺序一致。是线程不安全的。
* ​`LinkedHashSet`​：底层由链表实现，按照元素插入的顺序进行迭代，即迭代输出的顺序与插入的顺序保持一致。

## TreeSet

​`TreeSet()`​创建空的树集

```java
TreeSet<Integer> treeSet = new TreeSet<>();
```

​`TreeSet(Collection c)`​创建一个树集，并添加集合c的元素

​`TreeSet(Comparator c)`​创建树集，并制定特定的比较器进行排序

## HashSet

HashSet是Set集合最常用实现类，是其经典实现。HashSet是按照hash算法来存储元素的，因此具有很好的存取和查找性能。

HashSet具有如下特点：

* 不能保证元素的顺序。

* HashSet不是线程同步的，如果多线程操作HashSet集合，则应通过代码来保证其同步。

* 集合元素值可以是null。

通过源码可以得知，HashSet内部其实是一个HashMap。HashSet的add方法，插入的值会作为HashMap的key，所以是HashMap保证了不重复。

​`HashSet()`​创建一个空的哈希集

​`HashSet(Collection c)`​创建一个哈希集，并将c中的元素添加其中

​`HashSet(int initialCapacity,float loadFactor)`​创建具有一定容量和加载因子 *（加载因子是一个浮点数，它表示哈希表在其容量自动增加之前可以达到多满的一种尺度。在哈希表中，数据是存储在一个个桶（bucket）里的，当哈希表中的元素数量达到一定程度时，为了保证哈希表的性能，需要对其进行扩容操作。加载因子就是用来衡量这个 “一定程度” 的指标。）的空哈希集。*

## LinkedHashSet

用的也较少，其操作方法和HashSet完全一样，那么二者区别是什么呢？1.首先LinkedHashSet是HashSet的子类.2.LinkedHashSet中用于存储值的实现LinkedHashMap，而HashSet使用的是HashMap。

## 性能对比

HashSet的性能比TreeSet的性能好（特别是添加，查询元素时），因为TreeSet需要额外的红黑树算法维护元素的次序，如果需要一个保持排序的Set时才用TreeSet，否则应该使用HashSet。

LinkedHashSet是HashSet的子类，由于需要链表维护元素的顺序，所以插入和删除操作比HashSet要慢，但遍历比HashSet快。

EnumSet是所有Set实现类中性能最好的，但它只能 保存同一个枚举类的枚举值作为集合元素。

# 映射Map

​`Map`​是一种键值对的结构，就是常说的`Key-Value`​结构，一个`Map`​就是很多这样K-V键值对组成的，一个K-V结构我们将其称作`Entry`​，在Java里，`Map`​是用的非常多的一种数据结构。

Map接口本身就是一个顶层接口，由一堆Map自身接口方法和一个`Entry接`​口组成，`Entry`​接口定义了主要是关于`Key-Value`​自身的一些操作，`Map`​接口定义的是一些属性和关于属性查找修改的一些接口方法。

## 映射接口方法

添加、删除操作：

```java
Object put(Object key,Object value)//将相关的键值放入映射，如果键值已经存在，将修改原来键值对，返回关键字的旧值；如果关键字不存在，返回null
Object remove(Object key)//删除与键对应的映射
void putAll(Map t)//给该map田间t Map的所有元素
void clear()//删除所有元素
```

查询操作

```java
Object get(Object key)//返回，如果没有找到则返回null
boolean containsKey(Object key)//判断是否存在key
boolean containsValue(Object value)//判断是否存在value
int size()//返回映射的数量
boolean isEmpty()//判断映射是否为空
```

​`HashMap`​和`TreeMap`​：

* `HashMap`​是Java中最常用K-V容器，采用了哈希的方式进行实现，`HashMap`​对`Entry`​进行了扩展（称作`Node`​），使其成为链表或者树的结构使其存储在`HashMap`​的容器里（是一个数组）。

* 当用户频繁地在Map中插入插入、删除元素时，HashMap是比较适合的；如果只是按照自然顺序或者自定义遍历，TreeMap更好。

* ​`HashMap`​添加的键类必须重新写`hashCode()`​和`equals()`​方法，`TreeMap`​的键类必须实现`Comparable`​接口。

​`LinkedHashMap`​：为了提高速度，`LinkedHashMap`​对所有的东西都进行了哈希处理，（但是在遍历过程中也会按照插入顺序输出键值对）。

## **HashMap与Hashtable**

HashMap与Hashtable是Map接口的两个典型实现，它们之间的关系完全类似于ArrayList与Vertor。HashTable是一个古老的Map实现类，它提供的方法比较繁琐，目前基本不用了，HashMap与Hashtable主要存在以下两个典型区别：

* HashMap是线程不安全，HashTable是线程安全的。

* HashMap可以使用null值最为key或value；Hashtable不允许使用null值作为key和value，如果把null放进HashTable中，将会发生空指针异常。

为了成功的在HashMap和Hashtable中存储和获取对象，用作key的对象必须实现hashCode()方法和equals()方法。

### HashMap工作原理如下：

HashMap基于hashing原理，通过put()和get()方法存储和获取对象。当我们将键值对传递给put()方法时，它调用建对象的hashCode()方法来计算hashCode值，然后找到bucket位置来储存值对象。当获取对象时，通过建对象的equals()方法找到正确的键值对，然后返回对象。HashMap使用链表来解决碰撞问题，当发生碰撞了，对象将会存储在链表的下一个节点中。

## **LinkedHashMap实现类**

LinkedHashMap使用双向链表来维护key-value对的次序（其实只需要考虑key的次序即可），该链表负责维护Map的迭代顺序，与插入顺序一致，因此性能比HashMap低，但在迭代访问Map里的全部元素时有较好的性能。

## **Properties**

Properties类时Hashtable类的子类，它相当于一个key、value都是String类型的Map，主要用于读取配置文件。

## **TreeMap实现类**

* TreeMap是SortedMap的实现类，是一个红黑树的数据结构，每个key-value对作为红黑树的一个节点。TreeMap存储key-value对时，需要根据key对节点进行排序。TreeMap也有两种排序方式：

* 自然排序：TreeMap的所有key必须实现Comparable接口，而且所有的key应该是同一个类的对象，否则会抛出ClassCastException。

* 定制排序：创建TreeMap时，传入一个Comparator对象，该对象负责对TreeMap中的所有key进行排序。

## **各Map实现类的性能分析**

 HashMap通常比Hashtable（古老的线程安全的集合）要快

* TreeMap通常比HashMap、Hashtable要慢，因为TreeMap底层采用红黑树来管理key-value。

* LinkedHashMap比HashMap慢一点，因为它需要维护链表来爆出key-value的插入顺序。
