# Java 网络通信

---
## TCP/IP网络架构

1. **应 用 层(Application)**：实现各种终端服务
2. **传 输 层(Transport)** ：实现端对端连接（服务器端与客户端之间）
3. **网 际 层(Network)** ：实现路由选择
4. **网络接口层(Link)** ：实现点对点连接（相邻节点之间）

---

## WEB 服务

### 基本概念

#### HTTP协议

应用层通信协议，用于浏览器对于 web 内容访问的一次性连接。

下层使用TCP协议，默认端口号1024。

#### URL

web 内容访问标识符，用于浏览器识别互联网资源。 <font size=2 color=brown>URI：网络资源标识符( URL 属于 URI )</font>

通用格式为： 协议://IP地址或域名:端口号/文件路径?参数
如 https://baike.baidu.com/item/URL/10056474?fr=aladdin


#### Session/Cookie

HTTP连接无状态，客户端每次访问服务器都是独立的。session存储在服务端，而cookie存储在客户端：用于记录对方的身份，在重新连接时帮助双方相互识别。

1. 当用户第一次访问 Servlet 时，服务器端会给用户创建一个独立的 Session 。 response 会携带储存 SessionID 的 Set-cookie 项,发送给浏览器保存。

2. 当用户重新访问 Servlet 时， request 会携带储存 SessionID 的 cookie 项，服务器根据 SessionID 去查看是否有对应的 Session 对象，以识别客户端。

/.....浏览器存储分为localstorage 永久存储 和 sessionstorage 会话存储，显然cookie存在 会话存储中。


#### 请求类型

**GET**：请求服务器数据，请求参数直接附在URL上（不够安全，仅适用于简单公开数据的请求和提交）

**POST**：向服务器提交数据，数据以表单形式提交（内容长度无上限，且不会被浏览器记录）

**PUT**：修改服务器数据（慎用）

**DELETE**：删除服务器数据（慎用）

**OPTION**：在正式通信前会增加一次HTTP查询请求，称为"预检"。浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。

### HTTP通信

相关类保存在net包下，使用时需进行导入。

`import java.net.*; `   

### URL类
用于定向资源所在位置（可以由URLConnection类读取）

### URLConnection类
用于读取和写入URL类定向的资源（常用HttpURLConnection子类）

1. 由URL创建连接对象。
2. 设置连接参数和请求属性。
3. 建立连接，输出流发送请求。 
4. 输入流读取返回内容。
5. 关闭连接。

**创建连接对象**

`URL myUrl = new URL("https://www.baidu.com");`

创建URL对象，url格式错误则抛出`IOException`

`HttpURLConnection myCon = (HttpURLConnection)myUrl.openConnetcion();` 

创建URLConnection对象

**设置连接参数**

`myCon.setRequestMethod("POST");` 

设置连接方法（默认使用GET）

`myCon.setDoInput(true);`

允许进行字符流输入: `myCon.getInputStream().read();`

`myCon.setDoOutput(true);`

允许进行字符流输出: `myCon.getOutputStream().write();`

`myCon.setRequestMethod(1000);` 

设置最长建立连接时间，若超时则抛出`SocketTimeoutException`

`myCon.setRequestMethod(1000);` 

设置最长数据读取时间，若超时则抛出`SocketTimeoutException`

**设置请求属性**

`myCon.setRequestProperty("version", "1.2.3");`

设置版本

`myCon.setRequestProperty("user-agent", "Mozilla/5.0 (compatible;  Windows NT 5.1;SV1)");`

设置浏览器类型（用于爬虫伪装）

`myCon.setRequestProperty("Content-Type", "application/json;charset=utf-8");`

设置发送文本类型

**连接并发送请求**

`myCon.connect();`

建立连接并发送请求

`myCon.getOutputStream();`

关闭输出流时自动建立连接并发送输出流请求
```java
OutputStreamWriter out = new OutputStreamWriter(myCon.getOutputStream());
out.write(str);                  
out.close();
```


**获取响应数据**

`myCon.getResponseCode();`

获取响应码（200为连接成功，404为未找到）

`myCon.getHeaderField();`

获取响应头字段

`myCon.getContent (https://my.oschina.net/zhanghc/blog/134591);`

获取响应内容

`myCon.getInputStream();`

输入流获取响应内容,响应表明发送了错误则抛出`IOException`
```java
BufferedReader in = new BufferedReader(new InputStreamReader(myCon.getInputStream()));
while ((str = in.readLine()) != null) {
    System.out.println(str);
}
in.close();
```

**关闭连接**

`myCon.disconnect();`



```java
//示例：网络爬虫

public class WebCrawler{
    public static void getHttpJson(int i) {
        try {
            //配置URL
            URL myUrl = new URL("https://static-data.eol.cn/www/school/"+ i + "/info.json");
            //配置连接
            HttpURLConnection myCon = (HttpURLConnection) myUrl.openConnection();
            myCon.setRequestProperty("user-agent", "Mozilla/5.0 (compatible; MSIE 6.0; Windows NT 5.1;SV1)");
            myCon.setConnectTimeout(10000);
            myCon.setReadTimeout(1000);
            //建立连接
            myCon.connect();
            //如果连接成功
            if (myCon.getResponseCode() == 200) {
                System.out.print("ID" + i + "的数据读取成功，数据内容：");
                //读取返回数据
                InputStream in = myCon.getInputStream();
                int cnt = in.available();
                byte[] b = new byte[cnt];
                in.read(b);
                String str = new String(b);
                //输出返回数据
                System.out.println(str);
            }else{
                System.out.println("ID" + i + "的数据读取失败，代码：" + myCon.getResponseCode());
            }
        } catch (MalformedURLException e) {
            System.out.println("URL错误，无法查找到资源。");
        } catch(SocketTimeoutException e){
            System.out.println("ID" + i + "的数据访问连接超时。");
        } catch (IOException e) {
            System.out.println("发送的请求存在错误，资源拒绝访问。");
        }
        return;
    }
}
```

---

## 传输层

**TCP协议**

传输层通信协议。建立虚电路传输数据（三次握手四次挥手），为服务器端和客户端之间提供可靠连接。

**UDP协议**

传输层通信协议。直接传输数据而不处理数据包的乱序和丢失，为服务器端和客户端之间提供不可靠连接。

**套接字(socket)**

传输层识别标志。由IP地址(address)和端口号(port)组成。

如 192.168.0.1:8080


https://www.cnblogs.com/yiwangzhibujian/p/7107785.html#q2.1
https://www.cnblogs.com/linkenpark/p/11289018.html


### TCP通信

#### Socket类

Socket在建立网络连接时使用。连接成功时应用程序两端都会产生一个Socket实例指向对方，完成所需的会话。


**服务器端**

1. 创建ServerSocket对象，绑定监听端口监听客户端请求。
2. 接收客户端请求，创建Socket与该客户建立专线连接（多线程）。
3. 双方通过输入输出流进行对话。
4. 关闭流和套接字，继续等待新的连接。

**客户端**

1. 创建Socket对象，指明需要连接的服务器地址和端口号。
2. 连接建立后，通过输出流向服务器发送请求信息。
3. 双方通过输入输出流进行对话。
4. 关闭流和套接字。

**创建Socket对象**

`ServerSocket server = new ServerSocket(55533);`

创建ServerSocket对象，并指定服务器端接口

`Socket socket = new Socket(127.0.0.1, 55533);`

创建Socket对象，并指明对方主机和接口

**监听端口**

`Socket socket = server.accept();`

ServerSocket接收请求，并且返回一个客户端的Socket对象实例。

Accept方法用于产生“阻塞”（由循环产生），使程序运行停在这个地方，直到一个会话产生。

**进行对话**

```java
//打开输出流
OutputStream out = socket.getOutputStream();
//输出信息（字节流）
String message = "Hello";
out.write(message.getBytes("UTF-8"));
out.write("end");
//关闭输出流
out.close();
```

输出流输出信息，另一端输入流将得到输入。失败则抛出`IOException`


```java
//打开输入流（转化为字节流被缓冲流读取）
BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream(),"UTF-8"));
//输入信息(接收到end字符则结束)
StringBuilder message = new StringBuilder();
while ((str = in.readLine()) != null && "end".equals(str)) {
  message.append(str);
}
System.out.println("get message: " + message);
//关闭输入流
in.close(); //socket.shutdownOutput();
```

输入流输入信息，得到另一端输出流信息。失败则抛出`IOException`



```java
//客户端

public class SocketClient {
  public static void main(String args[]) throws Exception {
    //与本地服务器端建立连接
    String host = "127.0.0.1"; 
    int port = 55533;
    Socket socket = new Socket(host, port);

    //控制台输入并向服务器端输出
    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in, "UTF-8"));      
    BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));         
    while (str != "end") {
      String str = bufferedReader.readLine();
      bufferedWriter.write(str + "\n");
      bufferedWriter.flush();
    }

    //关闭连接
    outputStream.close();
    socket.close();
  }
}
```


```java
//服务器端

public class SocketServer {
  public static void main(String[] args) throws Exception {
    // 监听指定的端口
    int port = 55533;
    ServerSocket server = new ServerSocket(port);
    
    //循环等待请求
    while(true){
      //建立连接
      Socket socket = server.accept();
      //从socket中获取输入流并读取
      BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream(), "UTF-8"));
      String str;，
      while ((str = bufferedReader.readLine()) != null) {
        System.out.println("get message from client:" + str);
      }
      //关闭连接
      inputStream.close();
      socket.close();
    }
  }
}
```
**1> 多线程**

每有一个Socket请求的时候，就创建一个线程来处理它。（通常交给线程池管理，保证线程的复用）

能够循环处理多个Socket请求，否则一个请求的处理耗时，后面的请求将被阻塞。


```java
//服务器端（线程池管理）

public class SocketServer {
    public static void main(String[] args) throws IOException {
        // 监听指定的端口
        int port = 55533;
        ServerSocket server = new ServerSocket(port);
        //创建一个线程池
        ExecutorService executorService = Executors.newFixedThreadPool(100);
        //循环等待请求
        while (true) {
            //建立连接
            Socket socket = serverSocket.accept();
            //分配线程
            Runnable runnable = () -> {
                BufferedReader bufferedReader = null;
                //从socket中获取输入流并读取
                try {
                    bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream(), "UTF-8"));
                    String str;
                    while ((str = bufferedReader.readLine()) != null) {
                        System.out.println("get message from client:" + str);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            };
            executorService.submit(runnable);
        }
    }
}
```

**2> 规范流输入输出长度**

在实际应用中，socket发送的数据并不是按行发送。我们就不会每次发送数据，都用“\n”标识区分是否发送完毕。

在实际应用中，我们通过是采用数据长度+类型+数据的方式来告知一次流输入完成，方便进行后续操作。



https://blog.csdn.net/qq_33865313/article/details/79363640


https://www.cnblogs.com/huanzi-qch/p/9889521.html

Socket的消息推送机制中，用的都是 Ajax 轮询。在特定的时间间隔由客户端自动发出请求，将服务器的消息主动拉回来（服务器启动一个线程去监听与此客户端的通信），这种方式是非常消耗资源的，因为它本质还是http请求，而且显得非常笨拙：服务端不能主动向客户端推送数据。

####WebSocket类

`import javax.websocket.*;`

WebSocket 在浏览器和服务器完成一个握手的动作，在建立连接之后，服务器可以主动传送数据给客户端，客户端也可以随时向服务器发送数据。多客户端、涉及有界面的聊天建议使用websocket（嵌入到了浏览器的内核中）。

https://www.cnblogs.com/interdrp/p/7903736.html




**Spring框架**

服务端：

 1、添加Jar包依赖：

 2、创建一个WebSocket服务端类，并添加@ServerEndpoint(value = "/websocket")注解
表示将 WebSocket 服务端运行在 ws://[Server 端 IP 或域名]:[Server 端口]/项目名/websocket 的访问端点

 3、实现onOpen、onClose、onMessage、onError等方法

客户端

 1、添加Jar包依赖：

 2、创建WebSocket客户端类并继承WebSocketClient

 3、实现构造器，重写onOpen、onClose、onMessage、onError等方法

 **Maven依赖**
```xml
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
    <version>1.1</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>7.0</version>
    <scope>provided</scope>
</dependency>
```


**服务器定义WebSocket类**

```java
@ServerEndpoint("/websocket/{username}")  
public class WebSocket {  
  
    private static int onlineCount = 0;  
    private static Map<String, WebSocket> clients = new ConcurrentHashMap<String, WebSocket>();  
    private Session session;  
    private String username;  
      
    @OnOpen  
    public void onOpen(@PathParam("username") String username, Session session) throws IOException {  
  
        this.username = username;  
        this.session = session;  
          
        addOnlineCount();  
        clients.put(username, this);  
        System.out.println("已连接");  
    }  
  
    @OnClose  
    public void onClose() throws IOException {  
        clients.remove(username);  
        subOnlineCount();  
    }  
  
    @OnMessage  
    public void onMessage(String message) throws IOException {  
  
        JSONObject jsonTo = JSONObject.fromObject(message);  
          
        if (!jsonTo.get("To").equals("All")){  
            sendMessageTo("给一个人", jsonTo.get("To").toString());  
        }else{  
            sendMessageAll("给所有人");  
        }  
    }  
  
    @OnError  
    public void onError(Session session, Throwable error) {  
        error.printStackTrace();  
    }  
  
    public void sendMessageTo(String message, String To) throws IOException {  
        // session.getBasicRemote().sendText(message);  
        //session.getAsyncRemote().sendText(message);  
        for (WebSocket item : clients.values()) {  
            if (item.username.equals(To) )  
                item.session.getAsyncRemote().sendText(message);  
        }  
    }  
      
    public void sendMessageAll(String message) throws IOException {  
        for (WebSocket item : clients.values()) {  
            item.session.getAsyncRemote().sendText(message);  
        }  
    }  
          
  
    public static synchronized int getOnlineCount() {  
        return onlineCount;  
    }  
  
    public static synchronized void addOnlineCount() {  
        WebSocket.onlineCount++;  
    }  
  
    public static synchronized void subOnlineCount() {  
        WebSocket.onlineCount--;  
    }  
  
    public static synchronized Map<String, WebSocket> getClients() {  
        return clients;  
    }  
}  

```

客户端在JavaScript中调用即可

```javascript
var websocket = null;  
var username = localStorage.getItem("name");  
  
//判断当前浏览器是否支持WebSocket  
if ('WebSocket' in window) {  
    websocket = new WebSocket("ws://" + document.location.host + "/WebChat/websocket/" + username + "/"+ _img);  
} else {  
    alert('当前浏览器 Not support websocket')  
}  
  
//连接发生错误的回调方法  
websocket.onerror = function() {  
    setMessageInnerHTML("WebSocket连接发生错误");  
};  
  
//连接成功建立的回调方法  
websocket.onopen = function() {  
    setMessageInnerHTML("WebSocket连接成功");  
}  
  
//接收到消息的回调方法  
websocket.onmessage = function(event) {  
    setMessageInnerHTML(event.data);  
}  
  
//连接关闭的回调方法  
websocket.onclose = function() {  
    setMessageInnerHTML("WebSocket连接关闭");  
}  
  
//监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。  
window.onbeforeunload = function() {  
    closeWebSocket();  
}  
  
//关闭WebSocket连接  
function closeWebSocket() {  
    websocket.close();  
}  
```

发送消息只需要使用websocket.send("发送消息")，就可以触发服务端的onMessage()方法，当连接时，触发服务器端onOpen()方法，此时也可以调用发送消息的方法去发送消息。关闭websocket时，触发服务器端onclose()方法，此时也可以发送消息。





```java

WebSocket ws = new WebSocket();

JSONObject jo = new JSONObject();
jo.put("message", "这是后台返回的消息！");
jo.put("To",invIO.getIoEmployeeUid());
ws.onMessage(jo.toString());
```

```java
public class MyTest{

　　public static void main(String[] arg0){
　　　　MyWebSocketClient myClient = new MyWebSocketClient("此处为websocket服务端URI");
　　　　// 往websocket服务端发送数据
　　　　myClient.send("此为要发送的数据内容");
　　}

}
```

---

###UDP通信 

使用 DatagramPacket, DatagramSocket 和 MulticastSocket(广播)类。