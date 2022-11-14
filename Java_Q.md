## 流问题
### 网络通讯socket 输入输出流问题
**q1、读取本地文件**
使用字节流读取文件, 最后一行出现 原文件test.txt中未解析的字符
```java
        InputStream inputStream = new FileInputStream("test.txt");
        byte[] buf = new byte[1024];

        int len; // 实际有效字符
        while (( len = inputStream.read(buf)) != -1) {
            // 根据实际有效字符构造字符串
            String infox = new String(buf, 0, len);
            System.out.println(infox);
        }
```
**q2、读取socket文件**
获取socket的输入输出流， 使用包装流读取， 流中最后的一行数据读取不到
```java
      String info;
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        while ((info = reader.readLine()) != null) {
            System.out.println(info);
        }
```

对于q1、q2，read() 和readLine()方法都是阻塞函数， read方法默认读取到文件尾部结束阻塞， readLine方法默认遇到\n结束阻塞
### 读取键盘
使用scanner.nextXxx()或者 scanner.next()读取键盘后，如果再使用scanner.nextLine()读取，则会先读取到一个空格过着回车，所以在正式使用nextLine之前，先使用一次消耗掉空格