# JEE Course Notes

## 01 课程标准 网络架构 搭建

1. 客户端浏览器，浏览器内核，兼容性<br>
2. 通过复杂的网络路由<br>
3. request到服务端<br>
4. 服务端response经过路由到客户端<br>
5. 服务端总是被动的<br>
6. HTTP保证其中传递的稳定可靠<br>

1. Tomcat<br>
2. Eclipse EE<br>
3. 在Eclipse当中改变服务器部署的位置<br>

接下来课程内容：<br>
JSP<br>
Servlet<br>
JavaBean<br>
JDBC<br>
MVC<br>
Struts2<br>

作业：<br>
读书报告，读HTTP协议，写收获，内容不限，2周完成。<br>

## 03 JSP 5大组成部分elements
### 一、JSP指令Directives

Page &lt;%@page XXX %&gt;<br>
1. 编程语言java<br>

2. 文本解析方式text/html<br>
   字符编码集 浏览器运行的编码<br>
   浏览器本身内置了解析方式，还能解析PDF、ms/word<br>

3. 网页编码集<br>
   文件存储的编码<br>
   ISO西文编码<br>
   UTF中文<br>

meta和page是冗余设置，有一个就行<br>

### 二、JSP脚本段script&lt;% %&gt;

提取出JAVA代码，按照某种方式生成JAVA类来放在服务器上编译运行<br>
	1. 在网页端运行某些JAVA代码<br>
	  变量的定义、赋值、简单运算<br>
	  控制台输出<br>
	  带包使用类 java.util.Date<br>
	  也可以在page里导入包&lt;%@page import="*"%&gt;<br>
	  类的创建<br>
	2. 内置对象out 把结果输到网页上<br>
	    out.println()<br>
Tomcat对JAVA脚本遵循单例模式，一个对象来服务所有人，但是有很多线程高并发。<br>
	3. 用脚本段输出HTML标签<br>
	    out.print("&lt;br&gt;");<br>
	4. 用脚本段分拆，囊括HTML段落<br>
	    &lt;%for(int i=0;i&lt;5;i++){%&gt;<br>
	    &lt;%}%&gt;<br>
	

JSP文件，每次被提交（网页刷新）的时候，先查看文件有没有更新，无更新则用旧缓存，有更新则重新让服务器编译运行。<br>

### 三、JSP表达式expression&lt;%=   %&gt;
向网页输出表达式内容<br>
  &lt;%int x=1,y=2;%&gt;<br>
  &lt;%=x+y%&gt;<br>

### 四、JSP注释comments&lt;%--  --%&gt;
不会被发送到浏览器端<br>
  HTML注释里可以插JSP脚本<br>
  JSP注释里插进去没用<br>

### 五、JSP声明declaration&lt;%! %&gt;
相当于在java类的成员变量的部分，不能执行过程<br>
也可以声明成员方法、内部类<br>
  机器会自动创建对象，哪怕不是静态方法也能调用。<br>
  也可以用this.方法()<br>

### Homework:
做一个九九乘法表页面<br>
看看<br>
  Tomcat是怎么实现这五大部分<br>
  单例模式<br>
  多线程高并发<br>
  线程安全性<br>

## 04 JSP工作原理 多页面web系

### jsp的类

设计模式 工厂模式 factory<br>
基于某种领域，为多家厂商提供服务，可以建立很多工厂<br>
把技术的细节屏蔽掉，只关注使用产品<br>

内部方法jspServer<br>
下划线开头的一般是只限于内部使用<br>
HttpServlet<br>
pageContext页面环境，页面的所有内容<br>
session页面间的会话<br>
ServletContext application服务器内几乎所有servlet的内容，权限很高<br>
JspWriter out<br>

jsp类，会把所有静态的标签内容用字符串形式保存和输出<br>
out.write<br>
jsp类，会把jsp脚本直接放在类里变成运行代码<br>
declare是成员变量，脚本是方法内变量<br>

只声明无调用，可能会优化掉，不更新<br>



### 多页面
数据传递问题 http协议是无状态的<br>

jsp 9大内置对象（其中重要的5个）<br>
引擎运行，就能用这些对象<br>
request<br>
response 所有回馈的信息<br>
session 依赖于特定浏览器内核的容器，实现多页面数据分享<br>
application 依赖于服务器的容器对象，全域、所有用户共享<br>
out <br>

一个form表单跳转到另一个页面<br>
在另一个页面用request.getParameter("填input的名")获取<br>
这是公开的数据<br>

request.getRealPath<br>

还可以用request封装数据<br>
request.setAttribute(arg0, arg1);<br>
request.getAttribute()<br>
但是就会得到null，因为跨越两个jsp了<br>

要用session来发数据<br>

多页面，要么超级链接，要么表单<br>

## 05 （续）多页面web系统

### 请求转发：
“最多跑一次”<br>
自己做不来，转给会做的人，做完再发回来<br>

#### 第一种

jsp1<br>
request.setAttribute("null", 10);<br>
request.getRequestDispatcher("2.jsp").forward(request,response);<br>

jsp2<br>
int num1=(int)request.getAttribute("num1");<br>
System.out.print("num1 "+num1);<br>
request.setAttribute("num2",100);<br>
request.getRequestDispatcher("3.jsp").forward(request,response);<br>

jsp3<br>
int num1=取出来<br>
int num2=取出来<br>
int res=num1+num2;<br>
显示在页面上<br>

访问之后，URL是jsp1，页面内容是jsp3<br>

#### 第二种

用session存数据，然后超链接跳转<br>
不能用request<br>

### 重定向：

“我不会做，你去找某某某”<br>

index.jsp:<br>
session.setAttribute<br>
response.sendRedirect("show.jsp");<br>

shou.jsp:<br>
session.getAttribute<br>

## 07 Servlet单页面

### 内容该要：
开发几个符合servlet规范的、特殊的java类，实现servlet的接口、所有抽象方法。<br>
配置、映射。<br>

### 接口和类的关系：

Servlet是顶层接口interface<br>
为了方便，还继承了一个抽象类abstract class：GenericServlet。<br>
为了更方便，还继承了一个抽象类：HttpServlet。<br>
	写我的servlet，可以继承3种。<br>

### 项目建立:
新建项目时，勾选生成web.xml配置文件。<br>
双击server可以改部署路径。<br>
可选择新建java类时，选继承servlet。也可以选择新建servlet类，选要实现的接口。<br>
必须要实现init，service，destroy三个方法。<br>
HttpServlet可以用doGet和doPost。<br>

### 项目部署：
​	servlet文件部署：<br>
​		机器真正运行的是.class，在服务器项目路径下的“/WEB-INF/classes/包路径/”里，可以自己编译放进去。<br>
​	虚拟路径配置（URI）：<br>
​		发布到网上去访问的地址。<br>
​		原生xml配置方式：<br>
​			web.xml<br>
​			&lt;servlet&gt;<br>
​				&lt;servlet-name&gt;test&lt;/servlet-name&gt;访问的名字（虚拟的）<br>
​				&lt;servlet-class&gt;servlets.xxx.xxx.MyServlet&lt;servlet-class&gt;带包写对应的servlet类<br>
​			&lt;/servlet&gt;<br>
​			&lt;servlet-mapping&gt;<br>
​				&lt;servlet-name&gt;test&lt;servlet-name&gt;上面写的访问名字<br>
​				&lt;url-pattern&gt;/hello&lt;url-pattern&gt;转换成URI的方式（用“/”开头）<br>
​			&lt;/servlet-mapping&gt;<br>
​			访问 XXXXXX/项目名/hello 就能用了。<br>
​			可能会遇到冲突问题，web.xml配置不成功。先注释掉下面那种注解配置代码，重启配置web.xml，再把注释恢复，再重启。<br>
​		注解代码配置方式：<br>
​			在类代码的前面一行“@WebServlet("/MyServlet")”。<br>

### 抽象方法解析：
​	init：<br>
​		只调用一次<br>
​		参数ServletConfig config实际上就是web.xml<br>
​		可以把连接数据库等操作放在这里<br>
​	destroy：<br>
​		销毁时调用，只一次<br>
​	service：<br>
​		每次提供服务时调用<br>
​		参数request（请求数据）和response（处理结果和目的地）<br>
​	doGet：<br>
​		get被调用<br>
​	doPost<br>
​		post被调用<br>
​	理论上，get或post只要写一个就行，因为剩下那个可以去调用它。<br>

### 生命周期：
客户 request到 容器<br>
容器 寻找Servlet类<br>
容器 new Servlet()<br>
容器 调用init()<br>
容器 调用service()<br>
servlet实例 输出响应消息，返回给 容器<br>
容器 response到 客户<br>
（空闲一段时间后）容器 调用destroy()<br>

### 页面输出：
​	PrintWriter out = resp.getWriter();<br>
​	out.print("hello");<br>
​	out.print("&lt;h1&gt;hello&lt;/h1&gt;");//只是这一句标签片段，不是完整的网页文档<br>

### 扯淡的背景故事：
​	先有了Servlet，再有了Jsp。<br>
​	Servlet是Java嵌入HTML。<br>
​	Jsp是HTML嵌入Java。<br>
​	Java环境下对网页的标签不会检查错误。所以写前端还是需要Jsp。<br>
​	在MVC结构里面，Jsp是视图，Servlet是控制器。<br>

### 任务：
读一读关于ServletConfig的文档，设置应用程序的状态。<br>
多个Servlet传递数据。<br>

## 08 Servlet规范

### 接口

ServletConfig<br>
	来自于web.xml。<br>
	可以由Servlet.getServletConfig()得到。<br>
	然后config.getInitParameter("encoding");就可以得到web.xml里面设置的encoding键值。<br>

ServletContext 在多个servlet之间传递数据<br>
	可以由Servlet.getServletContext()得到。<br>
	setAttribute()<br>
	getAttribute()<br>

和web.xml之间联动<br>
web.xml<br>
&lt;context-param&gt;环境参数，更高层级的键值对<br>
	&lt;param-name&gt;xxx&lt;/param-name&gt;<br>
	&lt;param-value&gt;ooo&lt;/param-value&gt;<br>
&lt;/context-param&gt;<br>
然后用context.getInitParameter();就能取到这键值对。<br>

### 任务：
写个流输入、流输出的案例<br>

## 09 Servlet收尾－实用案例

ServletConfig<br>
ServletContext<br>
request/response<br>

### 中文乱码
​	方案1：<br>
​		response.setCharacterEncoding("utf-8");<br>
​		response.setHeader("Content-type","text/html;charset=utf-8");<br>
​	方案2：超简单的方法<br>
​		response.setContentType("text/html;charset=utf-8");<br>

### 时钟－不断更新时间

​	传统情况下：接收完服务端的数据之后，网页其实是离线的。<br>
​	解决方法：<br>
		1. 自动刷新<br>
			response.setHeader("Refresh",1);<br>
			response.getWriter().print(new java.util.Date());<br>
		2. 重定向<br>
			response.sendRedirect();<br>
		3. 无刷－异步通信模式(阿贾克斯)<br>
			以后的课再说。<br>

### Request的底层－涉及到计算机网络和协议
​	获取HTTP请求头的名和值<br>
​			getHeaderNames然后从迭代器取出名字，从getHeader取出对应的值。<br>

### Response的底层－涉及到计算机网络和协议
​	对HTTP响应头操作<br>
​		HTTP的响应头的格式，是有国际规范的。<br>
​		response.setHeader();<br>
​		response.getHeader();<br>
​		response.addHeader();<br>
​	获取HTTP返回的状态码<br>
​		getStatus<br>
​		404 200<br>
​	获取其他乱七八糟的东西<br>
​		getMethod<br>
​		getURI Identify标识<br>
​			跟应用相关，跟硬件服务器不相关。<br>
​			例如“/lesson09/RequestServlet”<br>
​		getURL Location定位<br>
​			跟部署有关，找到硬件服务器，再找到应用。<br>
​			例如“http://localhost:8080/lesson09/RequestServlet”<br>
​		getRemoteAddress<br>
​			例如“0:0:0:0:0:0:0:1”<br>
​	网页字节流IO<br>
​		OutputStream<br>

### 多Servlet系统

​	重定向<br>
​	请求转发<br>
​		request.getRequestDispatcher("login.html").forward(request,response);<br>
​	请求包含<br>

​		request.getRequestDispatcher("login.html").include(request,response);<br>

### 任务：

1. getHeader获得默认的HTTP头，尝试阅读。<br>

