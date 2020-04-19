#  I/O

使用 Java I/O 中的类，需要进行导入： `import java.io.*; `      

---

## Serializable 接口

在Java程序中，我们可以在内存中创建 Java 对象，但只有当 JVM 运行时，这些对象才能存在。但有时候我们需要在 JVM 停止运行之后我们能够持久化对象以便下次使用（保存在文件中），或者在多个JVM间共享对象（通过网络发送）。

在类中声明实现 Serializable 接口，即允许 Java 对象序列化：在保存对象时会把其成员变量保存为一组字节，在未来再将这些字节组装成对象。
对象序列化只保存的对象的成员变量（而不会关注类中的静态变量）。
    

```java
//person类的读入读出
//对于 class Person implements Serializable

        ObjectOutputStream oout = new ObjectOutputStream(new FileOutputStream(file));
        Person person = new Person("John", 101, Gender.MALE);
        oout.writeObject(person);
        oout.close();

        ObjectInputStream oin = new ObjectInputStream(new FileInputStream(file));
        Object newPerson = oin.readObject(); // 没有强制转换到Person类型
        oin.close();
```

1. **transient 字段**：默认序列化机制就会被忽略。
2. **private 字段**：序列化后不会被保护，任何JVM都可读取。

---

## 标准输入/输出                              

### 标准输入流 System.in

读取标准输入设备数据（如键盘），数据类型为 InputStream。【一次键盘输入以换行符结束】


```java
char c = (char)System.in.read();   // 读取输入字符，返回ASCII值(int)

byte[] b = new byte[20];
System.in.read(b);                 // 读取输入定长字符组，返回字符个数(int)
```


### 标准输出流 System.out

向标准输出设备输出数据，其数据类型为 PrintStream。

```java 
System.out.print("hello");                         // 输出数据
System.out.println("hello");                       // 输出数据并换行

System.out.format("The number is %+,9.3f%n", PI);  // 输出指定格式数据
```

符号|作用|示例|效果
-|-|-|-
+|为正数或者负数添加符号|("%+d",99)|+99
Num|位数（默认右对齐）|("%4d", 99)|__99
−|左对齐|("%-4d", 99)|99__
0|数字前补0|("%04d", 9999)|0099
,|以“,”对数字分组|("%,d", 9999)|9,999
.Num|小数点后精确位数|("%5.2f", 9.999)|_9.99


---

## 流输入输出

### 字节流

#### InputStream/OutputStream 类

以字节为单位进行读取的数据流。常用来处理二进制数据的输入输出，如键盘输入、网络通信。但字节流不能正确显示 Unicode 字符。


```java
InputStream in = new InputStream(socket.getIntputStream());        // 创建输入对象

int len = in.available();                                          // 读取输入对象长度

char c = (char)in.read();                                          // 读取输入字节

byte[] b = new byte[len];                                          // 连续读取输入字节
in.read(b);

in.close();                                                        // 关闭输入对象
```

```java
OutputStream out = new OutputStream(socket.getOutputStream());     // 创建输出对象

byte[] b = {1,2,3};                                                // 导入输出字节          
out.write(b);

out.flush();                                                       // 刷新输出对象，输出字节

out.close();                                                       // 关闭输出对象，输出字节
```



### 字符流

#### Reader/Writer 类

以字符为单位进行读取的数据流。只能用于处理文本数据。所有文本数据，即经过 Unicode 编码的数据都必须以字符流的形式呈现。

*我们在 Java 程序中处理数据往往需要用到字符流，但在通信中却需要使用字节流。这就需要进行数据格式转化：*

#### InputStreamReader 类

将字节流数据转换成字符流，常用于读取控制台输入或读取网络通信。可指定编码方式，否则使用 IDE 默认编码方式。

`InputStreamReader in = new InputStreamReader(System.in);`

`InputStreamReader in = new InputStreamReader(socket.getInputStream(), "UTF-8");`

#### OutputStreamWriter 类

将字符流数据转换成字节流，常用于发送网络通信。

`OutputStreamWriter out = new OutputStreamWriter(socket.getOutputStream());`


### 文件流

#### File 类

用于文件或者目录的描述信息，默认加载当前目录。

`File f1 = new File("FileTest.txt");`

#### FileInputStream/FileReader 类

读取文件信息。

```java
public class TestFileReader {
    public static void ReadFile(String textName) {
        int c = 0;
        try {
            //连接文件
            FileReader fr = new FileReader("D:\\Workspaces" + textName);
            //逐个读取字符并输出
            while ((c = fr.read()) != -1) {
                System.out.print((char)c);
            }
            //关闭文件
            fr.close();
        } catch (FileNotFoundException e) {
            System.out.println("找不到指定文件");
        } catch (IOException e) {
            System.out.println("文件读取错误");
        }
    }
}
```

如果使用 FileInputStream 类读取文本文件，Unicode 字符将为乱码。必须通过 InputStreamReader 类转化为字节流：

`InputStreamReader fr = new InputStreamReader(new FileInputStream("D:\\Workspaces" + textName));`

#### FileOutputStream/FileWriter 类

写入文件信息。

```java
public class TestFileWriter {
    public static void ReadFile(String textName) {
        int c = 0;
        try {
            FileWriter fw = new FileWriter(textName);                
            fw.write("Hello world！欢迎来到 java 世界\n");
            fw.append("我是下一行");                              
            fw.flush();                                       
            System.out.println("文件的默认编码为" + fw.getEncoding());
            fw.close();                    
        } catch (FileNotFoundException e) {
            System.out.println("找不到指定文件");
        } catch (IOException e) {
            System.out.println("文件写入错误");
        }
    }
}
```

写入文本(write/append)默认将文本信息追加到原有信息后（追加模式），如果想覆盖原有信息：

`FileWriter fileWriter = new FileWriter(“data.txt", false);`         


### 缓冲流

#### BufferedReader/BufferedWriter 类

输入输出数据将会暂存到缓冲区，只有在缓冲区空/满之后才会一次性输入输出，减少了对 CPU 的频繁请求。

BufferedReader 类了提供一个 readLine 方法可以方便地读取一行，返回字符串格式(String)。

```java
class TestBuffer{
    public static void bufferUse() throws IOException {
        // 通过缓冲区读取键盘输入
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        // 通过缓冲区输出到文件
        BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"));
        String line = null;
        while((line = br.readLine()) != null){
            if("over".equals(line)){
                break;
            }
            bw.write(line);
            bw.newLine();
            bw.flush();
        }
        bw.close();
        br.close();
    }
}
```

*如果需要字节流缓冲，使用 BufferedInputStream/BufferedOutputStream 类。*

---

## 扫描器

### Scanner 类

包装输入并自动分割数据，调用 next 方法捕获，可以自动转换数据类型。

使用 Scanner 类，需要进行导入：`import java.util.Scanner;`

```java
Scanner sc = new Scanner(System.in);                             // 读取键盘输入，返回 String 数据类型                  
Scanner sc = new Scanner(new FileInputStream("example.txt");     // 读取文件信息，返回 String 数据类型

int n = sc.nextInt();                                            // 截取数据并自动转化数据类型
String str = sc.nextLine();                                      // 取出全部数据

sc.close();                                                      // 关闭 Scanner 类
```


