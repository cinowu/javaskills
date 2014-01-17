#Java网络通信基本的示例
网络通信基本步骤：


1. 服务器端启动，占用一个端口
1. 客户端创建连接监听服务器端地址和端口
1. 组装数据，发送到服务器端
1. 服务器端接收客户端发送过来的数据，解析使用，组装响应数据，发往客户端
1. 客户端接收到服务器端响应的数据，解析使用



##服务器端代码
```java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {

    private int port = 8001;  
    private int server_num = 3;  
    private static ServerSocket serverSocket;  
      
    public Server() throws IOException {  
        serverSocket = new ServerSocket(port, server_num);  
        System.out.println("The server is starting");  
          
    }  
    public void service() {
        int i = 0;
        while(true) {  
            i++;
            Socket socket = null;  
            try {  
              //服务器接收到客户端的数据后，创建与此客户端对话的Socket
              socket = serverSocket.accept();  //accept方法侦听并接受到此套接字的连接
              System.out.println("new connection is completed " + socket.getInetAddress() + ":" + socket.getPort());  
                
              //用于接收客户端发来的数据的输入流
              DataInputStream dis = new DataInputStream(socket.getInputStream());
              System.out.println("服务器接收到客户端的连接请求：" + dis.readUTF());
              
              
              //做一些耗时的操作，比如查询数据库，逻辑处理等......
              Thread.sleep(5000);
              
              //用于向客户端发送数据的输出流
              DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
              //服务器向客户端发送连接成功确认信息
              dos.writeUTF("我是服务器端"+i+"(本消息来自服务器端)");
            } catch(IOException e) {  
                e.printStackTrace();  
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {  
                if(socket != null) {  
                    try {  
                        //不需要继续使用此连接时，关闭连接，关闭此套接字
                        socket.close();  
                    } catch (IOException e) {  
                        e.printStackTrace();  
                    }  
                }  
            }
        }  
    }  
      
    public static void main(String args[]) throws IOException, InterruptedException {  
        Server server = new Server();  
        server.service();  
    }  
}
```


##客户端代码
```java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;
import java.net.UnknownHostException;

public class Client {
    public static void main(String args[]) throws UnknownHostException, IOException, InterruptedException {  
        String host = "localhost";  
        int port = 8001;  
        int i = 0;
        while(true) {  
            Socket socket = new Socket(host, port);  
            System.out.println("the " + (i+1) + "connection is successful"); 
            
            //获取输出流，用于客户端向服务器端发送数据
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
            
            //做一些耗时的操作，比如组装数据等......
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            //客户端向服务器端发送数据
            dos.writeUTF("我是客户端"+(i+1)+"(本消息来自客户端)!");
            
            
            //向服务器端发送完成数据，就等着服务器端返回数据啦......
            //获取输入流，用于接收服务器端发送来的数据
            DataInputStream dis = new DataInputStream(socket.getInputStream());
            //打印出从服务器端接收到的数据
            System.out.println("客户端接收来自服务器端的消息："+dis.readUTF());
            
            //不需要继续使用此连接时，记得关闭哦
            socket.close();  
            i++;
        }  
    }  
}
```
