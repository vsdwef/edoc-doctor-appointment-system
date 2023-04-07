## SOURCECODESTER DOCTOR'S APPOINTMENT SYSTEM前台登录处存在SQL 注入

 

### 项目来源

来源网站：https://www.sourcecodester.com/hashenudara/simple-doctors-appointment-project.html

Github地址：https://github.com/HashenUdara/edoc-doctor-appointment-system



 

### 漏洞复现

环境搭建成功，主页URL为 http://10.100.7.35/edoc-doctor-appointment-system-main/  

![1](export.assets/1.png)

 右上角存在登录和注册操作，我们选择登录功能点进行测试

![2](export.assets/2.png)

 通过对源码分析，当获取前端传入的 email 后拼接SQL进行查询操作，当查询到一条结果时，根据查询结果的角色信息utype 的值来判断进入哪一个if分支，也就是选择那个角色来进行登录操作

![3](export.assets/3.png)

所以当我们构造万能密码时，需要考虑只能控制SQL查询出一条语句，所以我们可以构造 test@qq.com' or 1=1 LIMIT 0,1# 为登录用户名，这样可以成功查询出一条语句。由于我们控制 LIMIT 0,1查出来的是admin@edoc.com，[所以就进入admin@edoc.com](mailto:所以就进入admin@edoc.com)密码判断程序

![4](export.assets/4.png)

所以，代码将进入下面if 分支

![5](export.assets/5.png)

当用户名为 test@qq.com' or 1=1 LIMIT 0,1# 传入这个分支的SQL语句时，也同样可以查询出一条结果![6](export.assets/6.png)

所以我们可以构造万能密码 test@qq.com' or 1=1 LIMIT 0,1# 来登录系统。

下面我们将抓包后的用户名修改为 万能密码进行系统登录

前端存在检测特殊符号的操作，我们抓包绕过，先输入一个合法的用户名

![7](export.assets/7.png)

然后抓包，将useremail 的值修改为  [test@qq.com'+or+1=1+LIMIT+0,1#](mailto:test@qq.com'+or+1=1+LIMIT+0,1#)

注意：在burpsuite  +号代表空格。

![8](export.assets/8.png)

 

修改后放包，成功登录Administrator 后台![9](export.assets/9.png)

这里证明存在SQL即可，就不再进一步进行SQL 注入数据的操作