#纯vim实现实现servlet程序(新手入门学习java后端之不依赖idea)

不依赖idea实现，主要是为了了解java项目如何实现http请求，新手了解原理用的。
主要以*nix环境为例（正经后端程序员谁用windows开发捏）
。。。。。。。话又说回来，正经人谁干程序猿

##依赖
1 jdk 1.8.0_271

    *要注意配置配置环境变量JAVA_HOME,CLASSPATH和PATH,不然可能会影响tomcat启动运行，
    需要了结的基本的指令java和javac使用

2 tomcat 9.0.40 

    *注意给tomcat下的bin目录可执行权限
    默认原始配置，监听8080端口

3 macOs 10.15.7

##手动创建目录层级

```
├── README.md
├── src  // 源码目录
│   └── action
│       └── HelloServlet.java
└── web  // 用于部署项目的文件目录
    ├── WEB-INF
    │   ├── classes  // 源码编译后存放的字节码文件位置（由上文src编译生成的文件)
    │   │   └── action
    │   │       └── HelloServlet.class
    │   ├── lib    // 用于存放以来的库
    │   └── web.xml  // 接口映射配置文件
    └── index.jsp //根目录默认的首页模板
```

## 流程

  1.创建上述目录层级结构

```
├── src  // 源码目录
│   
└── web  // 用于部署项目的文件目录
    ├── WEB-INF
        ├── classes  // 源码编译后存放的字节码文件的目录（由上文src编译生成的文件)
        ├── lib    // 用于存放以来的库文件的目录
```

  2.在WEB-INF/目录下创建接口映射文件web.xml，
  <servlet>*</servlet>标签是key-value形式，以servlet-name和servlet-class标签把方法和名字绑定
   <servlet-mapping>*</servlet-mapping>标签是key-value形式，以servlet-name和url-pattern标签把监听的url和名字绑定
   这两对打标签用的同一个servlet-name实现了绑定
  
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>action.HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

  3.在src目录下，创建方法文件action/HelloServlet.java，其实就是开始实现url对应的接口，
  注意一下这个依赖包 import javax.servlet.http.HttpServlet,这个其实是tomcat里面的库文件，
  位置应该是${TOMCAT_ROOT_PATH}/lib/servlet-api.jar
  ```
    package action;
    
    import java.io.IOException;
    import java.util.Date;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    
    public class HelloServlet extends HttpServlet {
        public HelloServlet() {
        }
    
        public void doGet(HttpServletRequest var1, HttpServletResponse var2) {
            try {
                var2.getWriter().println("<h1>Hello Servlet!</h1>");
                var2.getWriter().println((new Date()).getTime());
            } catch (IOException var4) {
                var4.printStackTrace();
            }
    
        }
    }
  ```

  4.创建服务根目录下默认的静态文件（有么有无所谓），在web目录下创建index.jsp文件
  
  ```
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
      <head>
        <title>$Title$</title>
      </head>
      <body>
      $END$
      </body>
    </html>
  ```

到这步，已经完成率所有需要的资源了，接下来解释编译部署的问题了。

## 编译文件

    * cd 本项目的根目录下，执行
    javac -cp ${TOMCAT_ROOT_PATH}/lib/servlet-api.jar src/action/HelloServlet.java -d web/WEB-INF/classes
    
这个时候web目录就已经有了所有服务所需要的资源了，就到部署了

## 部署

部署有两种办法，先写第一种，比较简单

 1. 挂在目录型：找到tomcat的配置文件server.xml（位置：${TOMCAT_ROOT_PATH}/conf/server.xml)，
    定位到<Host></Host>标签之间，加上一行 
    ```<Context path="" docBase="此项目根目录路径/web" />```
 
 ```
<Host name="localhost"  appBase="webapps"
             unpackWARs="true" autoDeploy="true">
      <Context path="" docBase="/Users/dezhouxuan/work/java/servlet-by-vim/web" />
   <!-- SingleSignOn valve, share authentication between web applications
              Documentation at: /docs/config/valve.html -->
         <!--
         <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
         -->

         <!-- Access log processes all example.
              Documentation at: /docs/config/valve.html
              Note: The pattern used is equivalent to using pattern="common" -->
         <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                prefix="localhost_access_log" suffix=".txt"
                pattern="%h %l %u %t &quot;%r&quot; %s %b" />

       </Host>
```

##这个时候就可以访问了

[http://127.0.0.1:8080/](http://127.0.0.1:8080/)

![alt text](https://github.com/momodezhou/servlet-by-vim/blob/master/index.jpg "首页展示的详情")






[http://127.0.0.1:8080/hello](http://127.0.0.1:8080/hello)

<img src="https://github.com/momodezhou/servlet-by-vim/blob/master/hello.jpg" alt="接口显示" width="400px" height="200px" style="margin:0"> 









  





