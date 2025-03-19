# Java IO系统

IO 即 `Input/Output`​，输入和输出。数据输入到计算机内存的过程即输入，反之输出到外部存储（比如数据库，文件，远程主机）的过程即输出。数据传输过程类似于水流，因此称为 IO 流。IO 流在 Java 中分为输入流（InputStream）和输出流（OutputStream），这两种数据流是两个抽象类。键盘只是一个产生输入流的工具，而屏幕只是一个输出流的体现工具。

​`InputStream`​/`Reader`​: 所有的输入流的超类，前者是字节输入流，后者是字符输入流。

​`OutputStream`​/`Writer`​: 所有输出流的超类，前者是字节输出流，后者是字符输出流。

# 字节流`InputStream`​、`OutputStream`​

## InputStream（字节输入流）

​`InputStream`​用于从源头（通常是文件）读取数据（字节信息）到内存中

常用方法 ：

* ​`read()`​ ：返回输入流中下一个字节的数据。返回的值介于 0 到 255 之间。如果未读取任何字节，则代码返回 `-1`​ ，表示文件结束。
* ​`read(byte b[ ])`​ : 从输入流中读取一些字节存储到数组 `b`​ 中。如果数组 `b`​ 的长度为零，则不读取。如果没有可用字节读取，返回 `-1`​。如果有可用字节读取，则最多读取的字节数最多等于 `b.length`​ ， 返回读取的字节数。这个方法等价于 `read(b, 0, b.length)`​。
* ​`read(byte b[], int off, int len)`​ ：在`read(byte b[ ])`​ 方法的基础上增加了 `off`​ 参数（偏移量）和 `len`​ 参数（要读取的最大字节数）。
* ​`skip(long n)`​ ：忽略输入流中的 n 个字节 ,返回实际忽略的字节数。
* ​`available()`​ ：返回输入流中可以读取的字节数。
* ​`close()`​ ：关闭输入流释放相关的系统资源。

从 Java 9 开始，`InputStream`​ 新增加了多个实用的方法：

* ​`readAllBytes()`​ ：读取输入流中的所有字节，返回字节数组。
* ​`transferTo(OutputStream out)`​ ： 将所有字节从一个输入流传递到一个输出流。

​`FileInputStream`​ 是一个比较常用的字节输入流对象，可直接指定文件路径，可以直接读取单字节数据，也可以读取至字节数组中。

```java
try (InputStream fis = new FileInputStream("input.txt")) {
    System.out.println("Number of remaining bytes:"+ fis.available());
    int content;
    long skip = fis.skip(2);//使用 skip() 方法跳过文件中的前两个字节。skip(2) 表示跳过两个字节，参数中的 2 指的是要跳过的字节数。同时，将实际跳过的字节数存储在 skip 变量中
    System.out.println("The actual number of bytes skipped:" + skip);
    System.out.print("The content read from file:"); 
    while ((content = fis.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

*不过，一般我们是不会直接单独使用* *​`FileInputStream`​*​  *，通常会配合* *​`BufferedInputStream`​*​ *（字节缓冲输入流）来使用。*

下面这段代码在项目中就比较常见，我们通过 `readAllBytes()`​ 读取输入流所有字节并将其直接赋值给一个 `String`​ 对象。

```java
// 新建一个 BufferedInputStream 对象
try (BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("file1.txt"));){
		// 读取文件的内容并复制到 String 对象中
		String result = new String(bufferedInputStream.readAllBytes());
		System.out.println(result);
	} 
	catch (IOException e) 
	{
		e.printStackTrace();
	}
```

## OutputStream（字节输出流）

​`OutputStream`​用于将数据（字节信息）写入到目的地（通常是文件），`java.io.OutputStream`​抽象类是所有字节输出流的父类。

​`OutputStream`​ 常用方法 ：

* ​`write(int b)`​ ：将特定字节写入输出流。
* ​`write(byte b[ ])`​ : 将数组`b`​ 写入到输出流，等价于 `write(b, 0, b.length)`​ 。
* ​`write(byte[] b, int off, int len)`​ : 在`write(byte b[ ])`​ 方法的基础上增加了 `off`​ 参数（偏移量）和 `len`​ 参数（要读取的最大字节数）。
* ​`flush()`​ ：刷新此输出流并强制写出所有缓冲的输出字节。
* ​`close()`​ ：关闭输出流释放相关的系统资源。

​`FileOutputStream`​ 是最常用的字节输出流对象，可直接指定文件路径，可以直接输出单字节数据，也可以输出指定的字节数组。

```java
try (FileOutputStream output = new FileOutputStream("output.txt")) {
    byte[] array = "OutputStreamDemo111".getBytes();
    output.write(array);
} catch (IOException e) {
    e.printStackTrace();
}
```

*类似于* *​`FileInputStream`​*​ *，*​*​`FileOutputStream`​*​ *通常也会配合* *​`BufferedOutputStream`​*​ *（字节缓冲输出流）来使用。*

## 字节缓冲流

IO 操作是很消耗性能的，为了减少对磁盘的访问，缓冲流将数据加载至缓冲区，读入文件时不是以一个字节作为读取、写入单位，而是读入/写入一定长度的数据，从而避免频繁的 IO 操作，提高流的传输效率。

字节缓冲流这里采用了装饰器模式来增强 `InputStream`​ 和`OutputStream`​子类对象的功能。

```java
InputStream fis = new FileInputStream("input.txt")
OutputStream fos = new FileOutputStream("output.txt") 
```

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("input.txt"));
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("output.txt")
```

缓冲区的大小默认为 **8192** 字节，也可以通过 `BufferedInputStream(InputStream in, int size)`​ 这个构造方法来指定缓冲区的大小。

## 字节数据输入、输出流

​`DataInputStream`​ 、`DataOutputStream`​用于读取/写入指定类型数据，不能单独使用，必须结合 `FileInputStream`​、`FileOutputStream`​ 。

```java
DataInputStream dataInputStream = new DataInputStream(fileInputStream);
//可以读取任意具体的类型数据
dataInputStream.readBoolean();
dataInputStream.readInt();
dataInputStream.readUTF();
```

```java
DataOutputStream dataOutputStream = new DataOutputStream(fileOutputStream);
// 输出任意数据类型
dataOutputStream.writeBoolean(true);
dataOutputStream.writeByte(1);
```

## 字节对象输入、输出流

Java中的 数据以对象的形式保存与内存中，所以希望数据也能以对象的形式保存于硬盘文件，下次运行程序时可以读取硬盘文件中的数据文件，并还原为对象。

### 序列化（Serialization）

* **定义**：将对象转换为字节序列的过程。
* **目的**：将对象的状态保存到文件、数据库，或通过网络传输到其他系统。
* **例子**：将一个 `Person`​ 对象转换为字节流，保存到文件中。

### 反序列化（Deserialization）

* **定义**：将字节序列恢复为对象的过程。
* **目的**：从文件、数据库或网络接收的数据中重新构建对象。
* **例子**：从文件中读取字节流，恢复为一个 `Person`​ 对象。

​`ObjectInputStream`​ 用于从输入流中读取 Java 对象（反序列化），`ObjectOutputStream`​ 用于将对象写入到输出流(序列化)。

用于序列化和反序列化的类必须实现 `Serializable`​ 接口，对象中如果有属性不想被序列化，使用 `transient`​ 修饰。

```java
	class MyClass implements Serializable{
    
}
	ObjectInputStream input = new ObjectInputStream(new FileInputStream("object.data"));
	MyClass object = (MyClass) input.readObject();
	input.close();
```

## 字符流

如果我们不知道编码类型就很容易出现乱码问题。I/O 流提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。

### Reader（字符输入流）

​`Reader`​用于从源头（通常是文件）读取数据（字符信息）到内存中，`java.io.Reader`​抽象类是所有字符输入流的父类。

​`Reader`​ 用于读取文本， `InputStream`​ 用于读取原始字节。

​`Reader`​ 常用方法 ：

* ​`read()`​ : 从输入流读取一个字符。
* ​`read(char[] cbuf)`​ : 从输入流中读取一些字符，并将它们存储到字符数组 `cbuf`​中，等价于 `read(cbuf, 0, cbuf.length)`​ 。
* ​`read(char[] cbuf, int off, int len)`​ ：在`read(char[] cbuf)`​ 方法的基础上增加了 `off`​ 参数（偏移量）和 `len`​ 参数（要读取的最大字节数）。
* ​`skip(long n)`​ ：忽略输入流中的 n 个字符 ,返回实际忽略的字符数。
* ​`close()`​ : 关闭输入流并释放相关的系统资源。

​`InputStreamReader`​ 是字节流转换为字符流的桥梁，其子类 `FileReader`​ 是基于该基础上的封装，可以直接操作字符文件。

```java
// 字节流转换为字符流的桥梁
public class InputStreamReader extends Reader {
}
// 用于读取字符文件
public class FileReader extends InputStreamReader {
}
```

​`FileReader`​ 代码示例：

```java
try (FileReader fileReader = new FileReader("input.txt");) {
    int content;
    long skip = fileReader.skip(3);
    System.out.println("The actual number of bytes skipped:" + skip);
    System.out.print("The content read from file:");
    while ((content = fileReader.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### Writer（字符输出流）

​`Writer`​用于将数据（字符信息）写入到目的地（通常是文件），`java.io.Writer`​抽象类是所有字节输出流的父类。

​`Writer`​ 常用方法 ：

* ​`write(int c)`​ : 写入单个字符。
* ​`write(char[] cbuf)`​ ：写入字符数组 `cbuf`​，等价于`write(cbuf, 0, cbuf.length)`​。
* ​`write(char[] cbuf, int off, int len)`​ ：在`write(char[] cbuf)`​ 方法的基础上增加了 `off`​ 参数（偏移量）和 `len`​ 参数（要读取的最大字节数）。
* ​`write(String str)`​ ：写入字符串，等价于 `write(str, 0, str.length())`​ 。
* ​`write(String str, int off, int len)`​ ：在`write(String str)`​ 方法的基础上增加了 `off`​ 参数（偏移量）和 `len`​ 参数（要读取的最大字节数）。
* ​`append(CharSequence csq)`​ ：将指定的字符序列附加到指定的 `Writer`​ 对象并返回该 `Writer`​ 对象。
* ​`append(char c)`​ ：将指定的字符附加到指定的 `Writer`​ 对象并返回该 `Writer`​ 对象。
* ​`flush()`​ ：刷新此输出流并强制写出所有缓冲的输出字符。
* ​`close()`​:关闭输出流释放相关的系统资源。

​`OutputStreamWriter`​ 是字符流转换为字节流的桥梁，其子类 `FileWriter`​ 是基于该基础上的封装，可以直接将字符写入到文件。

```java
// 字符流转换为字节流的桥梁
public class InputStreamReader extends Reader {
}
// 用于写入字符到文件
public class FileWriter extends OutputStreamWriter {
}
```

​`FileWriter`​ 代码示例：

```java
try (Writer output = new FileWriter("output.txt")) {
    output.write("WRITER111");
} catch (IOException e) {
    e.printStackTrace();
}
```

## 打印流

```java
System.out.print("Hello World！");
System.out.println("Helle World！");
```

‍

​`System.out`​ 实际是用于获取一个 `PrintStream`​ 对象，`print`​方法实际调用的是 `PrintStream`​ 对象的 `write`​ 方法。

​`PrintStream`​ 属于字节打印流，与之对应的是 `PrintWriter`​ （字符打印流）。`PrintStream`​ 是 `OutputStream`​ 的子类，`PrintWriter`​ 是 `Writer`​ 的子类。

```java
try (FileWriter fw = new FileWriter("output.txt");
             PrintWriter pw = new PrintWriter(fw)) {
            // 使用 PrintWriter 输出字符数据
            pw.println("Hello, PrintWriter!");
            pw.println("This is a character stream.");
            pw.printf("Formatted value: %.2f%n", 123.456);
        } catch (IOException e) {
            e.printStackTrace();
        }
```

## 随机访问流

​`RandomAccessFile`​

​`RandomAccessFile`​ 的构造方法如下，我们可以指定 `mode`​（读写模式）。

```java
// openAndDelete 参数默认为 false 表示打开文件并且这个文件不会被删除
public RandomAccessFile(File file, String mode)
    throws FileNotFoundException {
    this(file, mode, false);
}
// 私有方法
private RandomAccessFile(File file, String mode, boolean openAndDelete)  throws FileNotFoundException{
  // 省略大部分代码
}
```

读写模式主要有下面四种：

* ​`r`​ : 只读模式。
* ​`rw`​: 读写模式
* ​`rws`​: 相对于 `rw`​，`rws`​ 同步更新对“文件的内容”或“元数据”的修改到外部存储设备。
* ​`rwd`​ : 相对于 `rw`​，`rwd`​ 同步更新对“文件的内容”的修改到外部存储设备。

*文件内容指的是文件中实际保存的数据，元数据则是用来描述文件属性比如文件的大小信息、创建和修改时间。*

​`RandomAccessFile`​ 中有一个**文件指针**用来表示下一个将要被写入或者读取的字节所处的位置。我们可以通过 `RandomAccessFile`​ 的 `seek(long pos)`​ 方法来设置文件指针的偏移量（距文件开头 `pos`​ 个字节处）。也就是说，<u>可以使用</u>​`seek()`​<u>来在文件中来回移动，并修改其中的值。</u>如果想要获取文件指针当前的位置的话，可以使用 `getFilePointer()`​ 方法。

如果使用了`RandomAccessFile`​，就必须知道文件的布局，以实现对文件的正确操作;`RandomAccessFile`​含有专门用于读写基本类型和UTF-8字符串的方法。

​`RandomAccessFile`​ 比较常见的一个应用就是实现大文件的 **断点续传** 。分片（先将文件切分成多个文件分片）上传是断点续传的基础，而`RandomAccessFile`​可以帮助我们合并文件分片

​`RandomAccessFile`​ 的实现依赖于 `FileDescriptor`​ (文件描述符) 和 `FileChannel`​ （内存映射文件）。*可以考虑用nio的内存映射文件来取代*​*​`RandomAccessFile`​*​ *。*

### 新I/O系统

Java 1.4的java.nio.* 包中引入的nio（“新”I/O库），它的目标只有一个：速度。

速度的提升来自于其使用的结构：**通道**（Channel）和**缓冲区**（buffer）。

大部分情况下，并不需要用到这一层的I/O，只有在遇到性能问题（比如说要用到内存映射文件的时候）或者要实现自定义的I/O库时，才有必要理解nio。（On Java进阶卷7.3）

‍
