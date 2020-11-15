# JEE Course Notes

## 01 课程标准 网络架构 搭建

1. 客户端浏览器，浏览器内核，兼容性
2. 通过复杂的网络路由
3. request到服务端
4. 服务端response经过路由到客户端
5. 服务端总是被动的
6. HTTP保证其中传递的稳定可靠

1. Tomcat
2. Eclipse EE
3. 在Eclipse当中改变服务器部署的位置

接下来课程内容：
JSP
Servlet
JavaBean
JDBC
MVC
Struts2

作业：
读书报告，读HTTP协议，写收获，内容不限，2周完成。

## 03 JSP 5大组成部分elements
### 一、JSP指令Directives

Page &lt;%@page XXX %&gt;
1. 编程语言java

2. 文本解析方式text/html
   字符编码集 浏览器运行的编码
   浏览器本身内置了解析方式，还能解析PDF、ms/word

3. 网页编码集
   文件存储的编码
   ISO西文编码
   UTF中文

meta和page是冗余设置，有一个就行

### 二、JSP脚本段script&lt;% %&gt;

提取出JAVA代码，按照某种方式生成JAVA类来放在服务器上编译运行
	1. 在网页端运行某些JAVA代码
	  变量的定义、赋值、简单运算
	  控制台输出
	  带包使用类 java.util.Date
	  也可以在page里导入包&lt;%@page import="*"%&gt;
	  类的创建
	2. 内置对象out 把结果输到网页上
	    out.println()
Tomcat对JAVA脚本遵循单例模式，一个对象来服务所有人，但是有很多线程高并发。
	3. 用脚本段输出HTML标签
	    out.print("&lt;br&gt;");
	4. 用脚本段分拆，囊括HTML段落
	    &lt;%for(int i=0;i&lt;5;i++){%&gt;
	    &lt;%}%&gt;
	

JSP文件，每次被提交（网页刷新）的时候，先查看文件有没有更新，无更新则用旧缓存，有更新则重新让服务器编译运行。

### 三、JSP表达式expression&lt;%=   %&gt;
向网页输出表达式内容
  &lt;%int x=1,y=2;%&gt;
  &lt;%=x+y%&gt;

### 四、JSP注释comments&lt;%--  --%&gt;
不会被发送到浏览器端
  HTML注释里可以插JSP脚本
  JSP注释里插进去没用

### 五、JSP声明declaration&lt;%! %&gt;
相当于在java类的成员变量的部分，不能执行过程
也可以声明成员方法、内部类
  机器会自动创建对象，哪怕不是静态方法也能调用。
  也可以用this.方法()

### Homework:
做一个九九乘法表页面
看看
  Tomcat是怎么实现这五大部分
  单例模式
  多线程高并发
  线程安全性

## 04 JSP工作原理 多页面web系

### jsp的类

设计模式 工厂模式 factory
基于某种领域，为多家厂商提供服务，可以建立很多工厂
把技术的细节屏蔽掉，只关注使用产品

内部方法jspServer
下划线开头的一般是只限于内部使用
HttpServlet
pageContext页面环境，页面的所有内容
session页面间的会话
ServletContext application服务器内几乎所有servlet的内容，权限很高
JspWriter out

jsp类，会把所有静态的标签内容用字符串形式保存和输出
out.write
jsp类，会把jsp脚本直接放在类里变成运行代码
declare是成员变量，脚本是方法内变量

只声明无调用，可能会优化掉，不更新



### 多页面
数据传递问题 http协议是无状态的

jsp 9大内置对象（其中重要的5个）
引擎运行，就能用这些对象
request
response 所有回馈的信息
session 依赖于特定浏览器内核的容器，实现多页面数据分享
application 依赖于服务器的容器对象，全域、所有用户共享
out 

一个form表单跳转到另一个页面
在另一个页面用request.getParameter("填input的名")获取
这是公开的数据

request.getRealPath

还可以用request封装数据
request.setAttribute(arg0, arg1);
request.getAttribute()
但是就会得到null，因为跨越两个jsp了

要用session来发数据

多页面，要么超级链接，要么表单

## 05 （续）多页面web系统

### 请求转发：
“最多跑一次”
自己做不来，转给会做的人，做完再发回来

#### 第一种

jsp1
request.setAttribute("null", 10);
request.getRequestDispatcher("2.jsp").forward(request,response);

jsp2
int num1=(int)request.getAttribute("num1");
System.out.print("num1 "+num1);
request.setAttribute("num2",100);
request.getRequestDispatcher("3.jsp").forward(request,response);

jsp3
int num1=取出来
int num2=取出来
int res=num1+num2;
显示在页面上

访问之后，URL是jsp1，页面内容是jsp3

#### 第二种

用session存数据，然后超链接跳转
不能用request

### 重定向：

“我不会做，你去找某某某”

index.jsp:
session.setAttribute
response.sendRedirect("show.jsp");

shou.jsp:
session.getAttribute

## 07 Servlet单页面

### 内容该要：
开发几个符合servlet规范的、特殊的java类，实现servlet的接口、所有抽象方法。
配置、映射。

### 接口和类的关系：

Servlet是顶层接口interface
为了方便，还继承了一个抽象类abstract class：GenericServlet。
为了更方便，还继承了一个抽象类：HttpServlet。
	写我的servlet，可以继承3种。

### 项目建立:
新建项目时，勾选生成web.xml配置文件。
双击server可以改部署路径。
可选择新建java类时，选继承servlet。也可以选择新建servlet类，选要实现的接口。
必须要实现init，service，destroy三个方法。
HttpServlet可以用doGet和doPost。

### 项目部署：
​	servlet文件部署：
​		机器真正运行的是.class，在服务器项目路径下的“/WEB-INF/classes/包路径/”里，可以自己编译放进去。
​	虚拟路径配置（URI）：
​		发布到网上去访问的地址。
​		原生xml配置方式：
​			web.xml
​			&lt;servlet&gt;
​				&lt;servlet-name&gt;test&lt;/servlet-name&gt;访问的名字（虚拟的）
​				&lt;servlet-class&gt;servlets.xxx.xxx.MyServlet&lt;servlet-class&gt;带包写对应的servlet类
​			&lt;/servlet&gt;
​			&lt;servlet-mapping&gt;
​				&lt;servlet-name&gt;test&lt;servlet-name&gt;上面写的访问名字
​				&lt;url-pattern&gt;/hello&lt;url-pattern&gt;转换成URI的方式（用“/”开头）
​			&lt;/servlet-mapping&gt;
​			访问 XXXXXX/项目名/hello 就能用了。
​			可能会遇到冲突问题，web.xml配置不成功。先注释掉下面那种注解配置代码，重启配置web.xml，再把注释恢复，再重启。
​		注解代码配置方式：
​			在类代码的前面一行“@WebServlet("/MyServlet")”。

### 抽象方法解析：
​	init：
​		只调用一次
​		参数ServletConfig config实际上就是web.xml
​		可以把连接数据库等操作放在这里
​	destroy：
​		销毁时调用，只一次
​	service：
​		每次提供服务时调用
​		参数request（请求数据）和response（处理结果和目的地）
​	doGet：
​		get被调用
​	doPost
​		post被调用
​	理论上，get或post只要写一个就行，因为剩下那个可以去调用它。

### 生命周期：
客户 request到 容器
容器 寻找Servlet类
容器 new Servlet()
容器 调用init()
容器 调用service()
servlet实例 输出响应消息，返回给 容器
容器 response到 客户
（空闲一段时间后）容器 调用destroy()

### 页面输出：
​	PrintWriter out = resp.getWriter();
​	out.print("hello");
​	out.print("&lt;h1&gt;hello&lt;/h1&gt;");//只是这一句标签片段，不是完整的网页文档

### 扯淡的背景故事：
​	先有了Servlet，再有了Jsp。
​	Servlet是Java嵌入HTML。
​	Jsp是HTML嵌入Java。
​	Java环境下对网页的标签不会检查错误。所以写前端还是需要Jsp。
​	在MVC结构里面，Jsp是视图，Servlet是控制器。

### 任务：
读一读关于ServletConfig的文档，设置应用程序的状态。
多个Servlet传递数据。

## 08 Servlet规范

### 接口

ServletConfig
	来自于web.xml。
	可以由Servlet.getServletConfig()得到。
	然后config.getInitParameter("encoding");就可以得到web.xml里面设置的encoding键值。

ServletContext 在多个servlet之间传递数据
	可以由Servlet.getServletContext()得到。
	setAttribute()
	getAttribute()

和web.xml之间联动
web.xml
&lt;context-param&gt;环境参数，更高层级的键值对
	&lt;param-name&gt;xxx&lt;/param-name&gt;
	&lt;param-value&gt;ooo&lt;/param-value&gt;
&lt;/context-param&gt;
然后用context.getInitParameter();就能取到这键值对。

### 任务：
写个流输入、流输出的案例

## 09 Servlet收尾－实用案例

ServletConfig
ServletContext
request/response

### 中文乱码
​	方案1：
​		response.setCharacterEncoding("utf-8");
​		response.setHeader("Content-type","text/html;charset=utf-8");
​	方案2：超简单的方法
​		response.setContentType("text/html;charset=utf-8");

### 时钟－不断更新时间

​	传统情况下：接收完服务端的数据之后，网页其实是离线的。
​	解决方法：
​		1. 自动刷新
​			response.setHeader("Refresh",1);
​			response.getWriter().print(new java.util.Date());
​		2. 重定向
​			response.sendRedirect();
​		3. 无刷－异步通信模式(阿贾克斯)
​			以后的课再说。

### Request的底层－涉及到计算机网络和协议
​	获取HTTP请求头的名和值
​			getHeaderNames然后从迭代器取出名字，从getHeader取出对应的值。

### Response的底层－涉及到计算机网络和协议
​	对HTTP响应头操作
​		HTTP的响应头的格式，是有国际规范的。
​		response.setHeader();
​		response.getHeader();
​		response.addHeader();
​	获取HTTP返回的状态码
​		getStatus
​		404 200
​	获取其他乱七八糟的东西
​		getMethod
​		getURI Identify标识
​			跟应用相关，跟硬件服务器不相关。
​			例如“/lesson09/RequestServlet”
​		getURL Location定位
​			跟部署有关，找到硬件服务器，再找到应用。
​			例如“http://localhost:8080/lesson09/RequestServlet”
​		getRemoteAddress
​			例如“0:0:0:0:0:0:0:1”
​	网页字节流IO
​		OutputStream

### 多Servlet系统

​	重定向
​	请求转发
​		request.getRequestDispatcher("login.html").forward(request,response);
​	请求包含

​		request.getRequestDispatcher("login.html").include(request,response);

### 任务：

	1. getHeader获得默认的HTTP头，尝试阅读。

