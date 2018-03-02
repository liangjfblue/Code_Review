## upsource

从零搭建代码审查平台，需要的不仅是把代码审查的工具搭起来，还要结合公司情况制定一系列的代码审查规范。下面是对选择的upsource web端代码审查工具的安装及介绍。详细的请看这篇文档。


### 1.安装方法
- 1、**下载地址** `http://www.jetbrains.com/upsource/`
- 2、**如果是Linux 修改 /etc/security/limits.conf 文件，增加以下命令：**
```memlock unlimited```
```nofile 100000```
```nproc 32768```
```as unlimited```
- 3、**配置UpSource**
	- ```windows：  <upsource_home>\bin\upsource.bat configure --listen-port 1111```
	- ```Linux：    <upsource_home>/bin/upsource.sh configure --listen-port 1111```
- 4、**启动UpSource**
	- ```windows：  <upsource_home>\bin\upsource.bat start```
	- ```Linux：    <upsource_home>/bin/upsource.sh start```
- 5、**停止UpSource**
	- ```windows：  <upsource_home>\bin\upsource.bat stop```
	- ```Linux：    <upsource_home>/bin/upsource.sh stop```
- 6、**第一个登录的是管理员账号注册**


### 2.启动配置
其实在公司中，基本是用Linux作为服务器的，下面的内容全都以Linux(我这里是ubuntu)为例子。

**重设端口：**
```./upsource.sh configure --listen-port 8000```

**重设upsource访问ip地址：**
```./upsource.sh configure --base-url http://xx.xx.xx.xx:8000```


### 3.创建 code review 工程
- 1.使用管理员帐户登录到Upsource（非管理员用户帐户没有资格创建新项目），点击创建一个项目。

- 2.General / Code intelligence / Integration / Advanced选择一种项目模板
- 3.以 General  项目创建为例子：
    Project name: 项目名称（自行设定）
    Project ID: （自动根据Project name设定，不用管）
    Code review ID pattern: （一般默认）
    VCS: Git / Subversion / Perforce / Mervurial
    Repository URL: 源码的URL

- 4.Branches:
    +:trunk
    +:branches/*
- 5.Check interval:反映Upsource检查仓库中新修订的频率(默认情况下每300秒一次)。
 - （如果提交到您的存储库并不是经常这样做，请考虑将检查间隔设置为更大的值）

- 6.Users: 创建用户组:让Upsource为您创建以下用户组:
 - 1.代码的浏览者
 - 2.开发者
 - 3.项目管理员
 - (注意或稍后自己创建它们。组是用户的集合，其中每个用户具有相同的权限或角色(权限集))

- 6.点击 Create Project

至此，一个code review项目创建完成。这里是写的比较简单的，详细的请看这篇文档，此文档是对官网用户手册的中文翻译，当然英语好的可以去官网查看。

### 4.过程遇到的问题
##### 4.1 服务器IP地址切换后，upsource连接不上
可以通过以下命令来修改upsource的访问ip地址和port端口号
- ```./upsource.sh configure --listen-port port --base-url http://ip:port```

##### 4.2 公司SVN服务器使用https访问，需要验证，创建不了SVN code review工程
自己测试的时候是svn://访问方式的，本以为可以了，但是用公司svn路径一测，出现这个错误：
```svn: E230001: Server SSL certificate verification failed: certificate issued```

**出现这个原因**：是因为我司svn访问方式是https方式，通过小乌龟svn客户端是可以访问的，还记得当时会弹出一个窗口询问是否接受信任认证。所以通过小乌龟访问是可以自动验证的。但是upsource是web端访问，虽然是自动带https等协议认证的，但是我不能自动安装，然后网上查找了很多，有在svn服务器那边修改的(这个肯定行不通)，有在浏览器上点击导出证书，然后一系列的shell命令配置等。以上办法都不能解决我的问题。

**解决办法**：其实解决办法很简单，不要忽略一个重要的前提，upsource创建svn仓库工程时是连接我们的svn库。因此可以通过先在机器本地svn checkout这个svn仓库出来，在这个过程中会询问是否接受信任认证，选择(p)ermanently 【永久接受信任】,然后输出本机 ubuntu 当前用户密码,回车, svn 用户,回车, svn 用户密码。 svn checkout 之后再去 upsource 就可以创建 svn https 的工程。

##### 4.3 创建SVN code review工程时，+:trunk   +:branches/* 填写不对造成创建失败
在创建svn code review 仓库工程时，填写Branches。乍一看，很多人会填写错误。
```Use rules like +:<pattern> or -:<pattern> to include or exclude branches.
Branch paths are relative to the repository URL specified above.
Pattern may contain one * to match any number of characters in directory name.```

这里是要求填写你的svn库是否有trunk和branch，

### 5.小结
此次，负责搭建公司的代码审查平台，从0到1掌握了代码审查的知识，但代码审查之路仍然还有很漫长的路要走，代码审查的习惯并非一朝一日培养好的，需要花时间来沉淀坚持。
