# 前后台数据交换

# 服务器
	* 软件架构模式
		bs = browser浏览器 server服务器
		* 凡是用浏览器打开的 不管是在电脑还是手机 全都是bs架构
		小程序 --> bs
			* 微信作为小程序的浏览器入口

		cs = client客户端 server服务器
		* 凡是需要安装客户端 不管是手机还是电脑 都是cs

	* 服务器的概念：
		硬件：就是一台电脑
		软件：需要在这台电脑上安装服务器的软件
		*** 如果一台电脑上安装了服务器的软件的话，那么这台电脑就可以称为WEB服务器

	* 访问：
		IP:端口号(服务器软件运行的端口0-65535)
		如果是域名解析成IP地址www.baiwu.com  
		默认端口号是80/443：可以不写

	你们公司有多少台服务器?
		* 具体有多少台我也不知道,部署用到了测试的服务器和线上服务器
		* 我们公司是租用的阿里云/百度云服务器
####http协议
	超文本传输协议,用于定义web浏览器(客户端)和web服务器之间数据交换过程

	* 请求
		一个请求行
			* 请求方式(request method) 请求路径 协议版本
			GET
				* 是用于获取服务器上的数据资源,是最常用的请求方式,传递的数据放在地址栏上,不安全,并且传递的数据量有限,根据浏览器不同1-8kb
			POST
				* 用于向服务器发送数据,请求发送的数据在请求的实体内容中,相对比较安全,传递数据大小在修改了服务器支持大数据量传递的情况下是没有限制的
		若干请求头(request header头)
			* Accept 告诉服务器当前浏览器可以接受的数据类型
			* Accept-Encoding 告诉服务器当前浏览器能够接受的数据压缩格式
			* Cookie 给服务器发送的缓存数据
			* Content内容-Type类型: 告诉服务器 当前浏览器给服务器发送的数据类型
			
		一个空行
		请求的实体内容
			* 只有post方式 请求实体内容才会有数据 ---> Form表单 data
	* 响应
		一个响应行
			* 协议版本 响应状态码Status Code
			200 服务器做出响应
			302/307 重定向
			304 使用缓存数据
			404 资源不存在
			500 服务器内部错误


		若干响应头(response响应 header)
			* Connection: keep-alive 服务器告诉浏览器保持连接
			* Content-Encoding: gzip 服务器告诉浏览器数据压缩格式gzip
			* Content-Type: text/html;charset=utf-8 服务器告诉浏览器响应数据的类型
			* Set-Cookie 服务器告诉浏览器需要缓存下来的内容
			
		一个空行
		响应的实体内容
			* html xml json txt png vedio......


# 搭建后台测试环境
	1.服务器--linux系统电脑--centos虚拟机
		* xshell:windowns上命令行的形式操作linux
		* xftp:windowns与linux之间文件传输

	2.linux上安装并配置java环境
		* 把jdk-8u171-linux-i586.tar.gz上传到linux根目录下
		* 根目录下执行命令解压到/usr/local目录下
			tar -xvf jdk-8u171-linux-i586.tar.gz -C /usr/local
		* 配置环境变量
			vim /etc/profile
				JAVA_HOME=/usr/local/jdk1.8.0_171/
				CLASSPATH=$JAVA_HOME/lib/
				PATH=$PATH:$JAVA_HOME/bin
				export PATH JAVA_HOME CLASSPATH
		* 刷新环境变量
			source /etc/profile
		* 验证
			java
			java -version
			javac


	3.linux安装tomcat服务器软件,并导入后台项目
		tomcat是apache开源组织开发的,开源免费的服务器软件

		* 把apache-tomcat-7.0.47.tar.gz传到根目录下
		* 根目录下执行指令进行解压
			tar -xvf apache-tomcat-7.0.47.tar.gz -C /usr/local
		tomcat目录结构分析
			* bin(binary) 二进制可执行文件
				.sh是linux的可执行二进制文件 .bat是windowns二进制可执行文件

				startup.sh 启动服务器
				shutdown.sh 关闭服务器
			* conf(config配置) --- 配置服务器的目录
			* lib(library库) tomcat运行需要的库文件
			* logs---存放的是服务器运行的日志
			* webapps(web applications应用) ---> 放后台项目/对外提供的资源用的
				.war--后台项目 --> 服务器能够自动解压war压缩包

		* 把后台项目war包放到webapps目录下
		* 启动服务器
			tomcat的bin目录下执行./startup.sh
		* 把linux防火墙关闭
			service iptables stop
			chkconfig iptables off ---> 只有在重启服务器之后才会起作用
		* 访问服务器:
			ip:端口号 ,tomcat默认端口号是8080

	4.linux上安装mysql数据库,并且导入数据
		* 查看linux上是否安装了xx软件
			rpm -qa|grep mysql
		* 卸载已经安装的mysql相关软件
			rpm -ev xxx --nodeps
		* 把mysql-community-release-el6-5.noarch.rpm上传到linux根目录下,在根目录下执行指令在线安装mysql
			yum localinstall mysql-community-release-el6-5.noarch.rpm
			* local本地 install安装
		* 在线安装mysql服务
			yum install mysql-server
		* 启动mysql服务
			service mysqld start
		* 设置mysql root用户密码(root)
			/usr/bin/mysqladmin -u root password 'root'
		* 登录mysql
			mysql -uroot -proot
		* 命令行创建商城需要的数据库shop
			create database shop;
		* navicat(数据库连接工具)中导入sql文件
			1.开启linux上mysql的远程授权(允许windowns上的navicat工具进行连接)
				* mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
				* flush privileges; 刷新授权
			2.navicat连接并运行sql导入
				* shop.sql
				
	5.打开商城网站
		ip:端口号/shop/index

	重启服务器之后 只需要:
		* bin-->./startup.sh启动服务器
		* 关闭防火墙

###服务器软件
	* tomcat apache开源组织,开源免费
	* WebLogic是BEA公司的产品，是目前应用最广泛的Web服务器
	* IBM公司的WebSphere

###http协议版本
	http1.1
		* 与服务器建立一次连接,可以发送多次请求,获取多个数据资源 
	http1.0
		* 建立一次连接 只会发送一次请求,获取一个数据资源

###前后台联调测试
	

#软件测试基础
	* 软件测试概念
		使用人工或者自动手段，来运行或者测试某个系统的过程。其目的在于检测它是否满足规定的需求或者弄清预期结果与实际结果之间的差别
	* 测试内容
		软件
		硬件
		软硬件
	* 测试环境(测试用到的东西)
		测试环境=硬件+软件+网络	

###测试流程(0-1)
	* 需求评审
		产品需求文档 产品原型图 UI效果图(页面测试) ---> 梳理功能模块和测试点
	* 测试计划制定
		输出测试计划文档
		测试用例编写
	* 测试计划执行
		* 单元测试
		* 集成测试 ---> 接口
		* 系统测试
			功能
			性能
		* 验收
			发布与测试报告总结
###测试分类
	按照阶段:
		* 单元
		* 集成
		* 系统
		* 验收
	按照是否查看源代码:
		* 白盒
			静态测试
			动态测试
		* 黑盒
			功能
				* 逻辑功能
				* 界面
				* 安全性
				* 易用性
				* 兼容性
				* 安装卸载...
			性能
				* 时间
				* 空间
				* 一般性能
				* 稳定性
				* 压力
				* 负载
	其他:
		* 回归测试
			版本之间的回归
			版本内回归
		* 冒烟测试
			
		* 随机测试

####测试案例
	* 如何测试一支笔?
	* 如何测试一把椅子?
	* 如何测试一个杯子?
	* 如何测试电梯?

	思路:
		* 功能
		* 界面
		* 安全性
		* 易用性
		* 性能
		* 兼容性
####测试时间占比
	功能60%
	接口20%
	性能20%
####软件测试原则
	所有的测试都应该追溯到用户的需求
	应当尽早、不断的测试
	测试发现的错误很可能起源于20%的模块中
	设计测试用例时，应该考虑各种情况
	对测试出的错误结果一定要有一个确认的过程(描述缺陷报告)
	制定严格的测试计划
	完全测试是不可能的，测试需要终止
	妥善保管一切测试资料

###软件模型
	* V模型
	* W模型
		提测===提交测试
###敏捷开发_测试
	


###部门结构
	技术部:
		* 开发
			前端 android ios 后台 架构师
		* 测试
			
		* UI
	QualityAssurance测试 RD开发 ProductManager产品经理 项目经理ProjectManager TestManager测试经理
	case/TestCase/CheckList测试用例

	开发:测试=1:1 / 3:1 /4:1 /5:1
	android--->3
	ios-->2
	后台-->6
	前端-->3

	测试:5
###软件测试与SQA
	
#web测试方法
	* 逻辑功能测试
	* 页面测试
	* 安全性
	* 性能
	* 时间
	* 兼容性
		操作系统
			* windowns
			* mac
			* linux(centos ubuntu)
		浏览器
			* chrome fireFox oprea ie safari

		浏览器版本:
			* ietester专门用于测试ie版本兼容性

			1.明确兼容浏览器的版本范围
			2.明确浏览器缩放比例范围80%-120%

#测试计划
	* 需求评审
		产品需求文档(word) 产品原型图(html) UI效果图
	* 测试计划制定
		测试计划
		编写测试用例
	* 执行
		单元
		集成
		系统
		验收
			* 验收与测试报告总结

	测试计划
		* 测试背景
			历史版本
			历史版本遗留缺陷
			需求文档及设计文档参考

		* 测试目标
			测试用例覆盖率达到100%?
				严格按照标准的测试流程进行测试工作,首先需求分析,分析各个需求点进行覆盖;按模块进行测试用例的编写,编写过程中使用测试用例常见各种设计方法;编写完成之后我们公司需要经过严格的测试用例评审,评审之后需要添加或者修改的在进行修改
		* 测试范围
		* 测试输出文档
		* 测试工具
		* 测试规模以及工作量划分 ---------->
			30--->22工作日-->3x5 1x7
		* 测试进程
		* 测试进度及时间安排 -------------->
		* 测试轮次
		* 测试方法

		v=version版本

	-------->周四

####测试用例
	概念:
		* 测试用例是为达到最佳的测试效果或高效的揭露隐藏的错误而精心设计的少量测试数据，包括测试输入、执行条件和预期的结果;测试用例是执行的最小实体
	测试用例元素:
		1.测试用例编号 
		2.测试模块
		3.测试用例名称(测试注册用例) 
		4.操作步骤(打开网站,输入信息,点击搜索…等
		5.输入数据 
		6.预期结果 
		7.测试结果 
		测试用例设计者 
		软件版本号 
		测试目的 
		参考信息  
		测试环境 

	设计方法
		* 等价类划分法
			有效等价类
				* 1-100 整数
			无效等价类
				* 所有小数1-100 <1 >100
				* <1 >100整数
				* 字母 汉字 特殊字符 空格
		* 边界值法
			边界 次边界
			0 1 2 
			99 100 101

		* 正交表法
			正交表能够在因素变化范围内均衡抽样,实现通过少数的实验次数达到均衡的测试效果
			
			pict.exe
			allParis.exe

			举例 项目中正交表用处

		* 因果图
		* 判定表
			------------------->自动售货机
		* 场景法
			ATM取款案例
		* 错误推断法

		正向用例 ---> 通过性
		反向用例 ---> 异常--容错性处理


	作业:宝淘商城20条bug的测试用例

####测试用例评审
	会议
		* 测试组内部
		* 项目组内部

	内容
		* 1)用例设计的结构安排是否清晰、合理，是否利于高效对需求进行覆盖。 
		* 2)优先极安排是否合理。 
		* 3)是否覆盖测试需求上的所有功能点。 
		* 4)用例是否具有很好可执行性。例如用例的前提条件、执行步骤、输入数据和期待结果是否清晰、正确期待结果是否有明显的验证方法。
		* 5)是否已经删除了冗余的用例。
			12+42 13+43

####bug分类
	* 按照严重程度划分
		系统崩溃 严重 次要 一般 建议

		首页-->搜索页面-->列表-->详情-->购物车-->订单生成页面-->支付-->待支付
	* 优先级
		高 中 低
	* 按照解决方案划分
		有效:
			* fixed 已解决
			* external 外部原因
			* postponed 延期
			* wont fix 不予解决
		无效
			* by design 设计如此
			* dumplicate 重复
			* not repro 无法重现

###bug生命周期
	新建(测试)，确认(程序员)，解决，重新验证，关闭，重新打开

	听写:
		* 软件模型V W
		* 测试用例元素:
			1.测试用例编号 
			2.测试模块
			3.测试用例名称(测试注册用例) 
			4.操作步骤(打开网站,输入信息,点击搜索…等
			5.输入数据 
			6.预期结果 
			7.测试结果 
		* 测试用例设计方法
		* bug分类
			按照严重程度
			按照解决方案
		* bug生命周期

#web控件/页面测试
	app ---> web官网
	* 应用市场
		百度应用 豌豆荚 应用宝 360应用市场 各个手机市场 安智市场...
		app store--->ios应用市场
	* app:
		android ---> .apk
		ios --> .ipa

	类型:
		* 视频类
			视频播放 (优酷 爱奇艺)
			直播类型(快手 抖音)
		* 旅游类(途牛 携程)
		* 电商类(京东 淘宝 拼多多)
		* 教育类型
		* 金融类
		
		app(android/ios) web端 小程序

	1.输入框测试
	2.按钮测试
		* 前后台数据校验
	3.下拉框测试
	4.列表测试

#web项目分析
	* 视频类
		视频播放 (优酷 爱奇艺)
		直播类型(快手 抖音)
	* 旅游类(途牛 携程)
	* 电商类(京东 淘宝 拼多多)
	* 教育类型
	* 金融类

	管理系统 :OA(办公自动化) CRM(客户管理系统) ERP(进销存管理系统)

	* 测试流程
		需求评审
			* 产品需求文档 产品原型图 UI效果图 ---> xmind列出功能模块以及测试点
		测试计划制定
			根据xmind列出功能模块以及测试点进行下面文档编写
			* 1.编写测试计划
			* 2.编写测试用例

	xmind可以写测试点 也可以写测试用例!!!
		* 测试点结果不明确 是否
		* 测试用例结果明确

		xmind补全
			* 京东广告 搜索 轮播图 登录 ---> 测试用例 ---->周四

#云产品
	类型:
		* 基础设施即服务（IaaS）
		* 平台即服务（PaaS）
		* 软件即服务（SaaS）

	云OA系统
		* 任务提醒
		* 1.日程安排 提醒
		* 即时通讯
		* 2.安排任务
		* 3.审批
			请假 出差 ....
		* 4.日报

		----------1.流程
		----------2.测试点
	电商类型:
		* P2P
		* O2O
		* B2C
		* B2B
		* C2C
		* P2C

		https://www.cnblogs.com/sap-ronny/p/8149960.html
###支付测试
	* 第三方支付原理
	* 第三方支付测试点

	如何操作?
		* 1.真实金额 -- >报销
		* 2.使用0.01支付,分配测试账号,只有测试账号才能看到某些测试的商品


# 测试管理工具
	概念:
		* 测试管理工具是在指在软件开发过程中，对测试需求、计划、用例和实施过程进行管理、对软件缺陷进行跟踪处理的工具
	
	禅道,Jira 管理工具

	禅道:
		* 是一款国产的，开源免费的项目管理软件
		* 使用PHP(后台开发语言) + MySQL(数据存储)+apache(服务软件tomcat)开发
		* 开发者：青岛易软天创网络科技有限公司
		* B/S Broswer/Server

	搭建禅道后台环境:
		* linux
			* 1.把压缩文件传到centos上 然后解压到opt目录下
		        tar -zxvf ZenTaoPMS.9.4.zbox_32.tar.gz -C /opt
		    * 2.更改mysql和acpache端口
		        cd /opt/zbox
		        ./zbox -mp 3307 更改mysql端口 (mp=mysql port)
		        ./zbox -ap 90 更改apache端口(ap=apache port)
		    * 3.启动mysql和Apache
		        /opt/zbox/zbox start #命令开启Apache和Mysql。
		        /opt/zbox/zbox stop #命令停止Apache和Mysql。
		        /opt/zbox/zbox restart #命令重启Apache和Mysql
		    * 4.关闭防火墙
		
		    * 5.访问禅道 ip:端口号
		        http://192.168.209.128:90/

			下次在开机:
				* 关闭防火墙
				* /opt/zbox/zbox start

		* windowns

	使用禅道:
		* 管理员
			维护公司 部门结构
			添加用户 分配权限
		* 产品经理
			创建产品
			维护模块
			计划
			需求
		* 项目经理
			创建项目
			维护团队
			关联产品
			关联需求
			分解任务
		* 开发
		* 测试
			提交bug
			验证bug
			关闭

			编写用例
			用例转bug

			导出缺陷报告
			生成报表


		提一个bug包含哪些内容?/如何提交一条高质量的bug?
			* 所属产品 模块 项目 影响版本
			* 指派人员 bug类型 bug标题 严重程度 优先级
			* 重现步骤 相关需求 相关任务

		你们公司缺陷报告有哪些内容????

# windowns搭建后台测试环境
	* 1.服务器 -- 电脑
	* 2.java环境变量安装配置
		jdk = java develop开发 kit工具包  ---> 所有java编写的软件要运行必须有jdk环境

		1.双击运行jdk-8u171-windows-x64.exe安装,选择文件夹(不允许有中文 特殊字符)
		2.配置环境变量
			* 新建JAVA_HOME G:\software\java12a
			* PATH中添加三个变量
				%JAVA_HOME%
				%JAVA_HOME%\bin
				%JAVA_HOME%\jre\bin

				;%JAVA_HOME%;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

			* 验证
				java /java -version /javac

	* 3.tomcat服务器软件安装
		解压软件(360压缩 好压 快压)解压apache-tomcat-7.0.52.zip到没有中文的目录下

		把shop.war放到webapps目录下

		启动tomcat ---> startup.bat
			* 启动窗口一闪而过 ---> 就是java环境变量配置有问题

	* 4.安装mysql数据库 并且导入数据 --> 本地电脑
		navicat连接localhost的数据库

		cmd创建数据库shop
		
		http://localhost:8080/shop/index

# 手机端测试
	特性:
		* 手机软件测试网络多样化
		* 手机软件测试支持系统多样化
		* 手机界面分辨率类型多样化
			2560x1920 1920x1080
			5.5 5.7 

	概念:
		*  手机软件测试：他是符合多种网络，不同系统不同分辨率下发现软件缺陷，保证并提高软件质量的过程。

	手机端测试策略:
		* 安装/卸载测试
		* 安全性测试-权限
		* 版本更新/覆盖安装测试
		* 逻辑功能测试
		* UI界面测试
		* 前后台切换测试
		* 中断/交互测试
		* 网络测试
		* 消息推送测试
		* 兼容性测试
			rom --> android内置系统 
			ram -->运行内存
		* 性能测试 --- app软件运行cpu 内存 电量 流量 卡顿 启动时间
		* 随机压力测试

####安装/卸载测试
	手动覆盖:
		* 用高版本--> 低版本 
		* apk文件:调试debug版本的apk 签名发布版本的正式apk
		* 高debug--->低debug版本 高签名-->低签名(签名必须一致)

		android与ios运行机制
####版本更新
	* 版本更新原理
	* 版本更新测试点
####前后台切换

###消息推送
	消息推送原理
	消息推送测试点

###App本身性能测试
	android介绍:
		* Android 是google公司主导的一个开放的手机操作系统
		* 底层以Linux内核工作为基础，由C语言开发
	Android四层架构
		* applications 应用层
		* application framework 应用程序架构层
		* libraries android运行库层
		* linux kenerl --linux内核
	android版本特性:
		* 5.0 增加界面元素的新特性,显示风格更加美观
		* 6.0 增加运行时权限
			权限
				* 安装权限
				* 运行时权限
		* 7.0 分屏效果
	屏幕:
		* 全面屏
		* 曲面屏
		* 刘海屏
		* 水滴屏


	app性能测试:
		 cpu 内存 时间 流量 电量 卡顿(帧率)
		* 1.adb指令获取性能数据
		* 2.程序员代码中集成第三方SDK
		* 3.手机安装第三方应用apk,获取性能数据

# adb
	adb = android debug调试 bridge桥 ,作用连接手机和电脑的桥梁

	配置adb的环境变量
		* 1.电脑上配置java环境变量
		* 2.电脑上配置adb环境
			解压AndroidSdk.rar到没有中文及特殊字符的目录下
			
			新建ANDROID_HOME G:\software\adt-bundle-windows-x86_64_20140101\sdk
			path中添加三个变量:
				* %ANDROID_HOME%
				* %ANDROID_HOME%\platform-tools
				* %ANDROID_HOME%\tools

				;%ANDROID_HOME%;%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools;

	常见的adb指令:
		* adb devices 查看与电脑连接的设备(手机 模拟器 平板 电视盒子 手环 眼镜 ......)
			127.0.0.1:54001(设备名称) device(已连接 offline掉线)

		* adb install apk文件路径 安装应用到手机
		* adb install -r apk文件路径 覆盖安装

		* adb uninstall 包名(包名是一个应用的唯一标识) ---- 根据包名卸载应用

		* adb shell pm list packages 列出手机所有应用的包名(包括系统应用,也包括第三方应用)
		* adb shell pm list packages -s(system) 列出所有系统应用包名
		* adb shell pm list packages -3 列出第三方应用包名
		* adb shell pm list packages XXX 根据关键词搜索包名


		* adb shell pm clear 包名 --根据包名清空某个应用的数据和缓存

		* adb start-server启动adb服务
		* adb kill-server 杀死adb服务

	adb查看手机运行日志:
		* adb logcat 动态查看手机运行日志

		* adb logcat -v time process | findstr 包名 > 文件路径 ---根据包名查看某个应用运行的日志
			-v=verbosity详细程度
			time process 输出日志里面带时间
			findstr查找字符串 

			实际测试的时候,打开cmd命令行运行上面的指令,操作app收集日志

			日志等级:
				* V
				* D=debug 程序员的调试信息
				* I=infomation 信息
				* W=warning 警告信息
				* E=error错误信息

		* adb logcat *:等级 ----根据等级进行日志过滤

	日志分析:
		* 实际测试的时候,打开cmd命令行运行上面的指令,操作app收集日志
		* 搜CRASH(崩溃)/Exception(异常)

#####adb获取性能数据
	adb =android debug bridge
	adb shell 进入手机的linux模式

	1.cpu
		* adb shell top -m 数字 -s cpu 获取前x个cpu消耗的进程
		* adb shell dumpsys cpuinfo | findstr 包名 ---根据包名获取某个应用的cpu消耗
	2.内存
		* adb shell dumpsys meminfo 包名 --- 根据包名查看某个应用内存消耗信息
			mem=memory内存


	3.启动时间
		冷启动 热启动
		* 1.获取要开启的页面
			adb shell dumpsys activity | findstr mFocusedActivity
			* activity页面
			* mFocused获取到焦点的Activity

			  mFocusedActivity: ActivityRecord{5299c63c u0 com.jingdong.app.mall/.MainFrameActivity t12}
		* 2.启动这个页面查看启动时间
			adb shell am start -W 页面(com.jingdong.app.mall/.MainFrameActivity)

	4.电量
		* 电量测试场景
		* 测试方法
			1.手机自带电量查看
			2.adb查看电池相关信息
				* adb shell dumpsys battery 查看电池信息
					 AC powered: true 交流电
					 USB powered: false 
					 Wireless powered: false无线充电
					 status: 2 电池状态2
					 health: 1 电池健康程度2
					 present: true 有无安装电池
					 level: 48 电量
					 scale: 100 总电量
					 voltage: 10000 电压
					 temperature: 0 温度
					 technology: Unknown 电池技术(Li)
				* adb shell dumpsys batterystats 包名 > 文件,,,根据包名查看应用耗电信息
					wifi gps 蓝牙 电话 锁屏-->亮屏

	5.卡顿(帧率)

	6.随机压力测试
		monkey是android的是一个随机压力 自动化测试小工具
		主要用于测试android app在随机情况下是否会发生崩溃CRASH
		操作的对象是包(应用程序),不能够操作单个页面

		* adb shell monkey 次数 --- 让monkey随机执行多少次
			Events事件 injected注入: 1000  ---> 50
			time=13166ms 执行的时间
		* adb shell monkey -p 包名 次数
			根据包名指定某个应用执行monkey测试
		* adb shell monkey -p 包名 -v -v -v 次数 > 路径
			指定日志详细程度执行monkey测试
			-v 
			-v -v
			-v -v -v


			// Event percentages比例:
			//   0: 15.0%	点击		70%
			//   1: 10.0%	滑动		20%
			//   2: 2.0%			
			//   3: 15.0%			
			//   4: -0.0%			
			//   5: 25.0%			
			//   6: 15.0%			
			//   7: 2.0%			
			//   8: 2.0%			
			//   9: 1.0%			
			//   10: 13.0%
		* adb shell monkey -p 包名 --pct-touch 比例 --pct-motion 比例 -v -v -v 次数 > 文件,,,分配点击和滑动事件的比例
			--pct-touch 点击 70
			--pct-motion 滑动 20

		* adb shell monkey -p 包名 --pct-touch 比例 --pct-motion 比例 --ignore-crashes --ignore-timeouts -v -v -v 次数 > 文件
			--ignore忽略-crashes崩溃 ,默认monkey遇到崩溃就停止
			--ignore-timeouts超时 ,ANR=application应用程序 not没有 response响应(应用程序无响应) ---> 引起超时

		* adb shell monkey -p 包名 --pct-touch 比例 --pct-motion 比例 --ignore-crashes --ignore-timeouts --throttle 延时毫秒 -v -v -v 次数 > 文件
			--throttle 延时毫秒   指定monkey两次事件之间的延时 100ms

	完整monkey指令:
		* adb shell monkey -p 包名 --pct-touch 比例 --pct-motion 比例 --ignore-crashes --ignore-timeouts --throttle 延时毫秒 -v -v -v -s 随机数字字符串 次数 > 文件
			-s=seed种子 因为monkey是随机的,两次执行动作肯定不一样,为了保证两次动作一样可以添加种子
			复现bug--->monkey事件执行要与上一次一致

	常问问题:
		* 1.monkey执行多久 ?多少次?
			30万次起 --- 100万
			1000 --- 100ms ----47s
			30万 --- 100ms -----3小时左右
			100万---------------9-10小时
		* 2.monkey测试完成之后如何分析结果?
			monkey最后执行完成之后只有monkey日志的输出,先搜索CRASH或者Exception关键词,查看是否又崩溃或者异常
		* 3.ios可不可以执行monkey测试?
			不可以,monkey是android内置的随机压力测试小工具;得需要开发写代码进行随机压力测试

	monkey日志分析:
		* 搜CRASH
		* 搜Exception

	当执行monkey测试的时候,建议大家开一个cmd手机logcat日志;一个cmd执行monkey收集monkey日志

####app性能测试
	* 1.adb指令获取
	* 2.开发人员代码中集成第三方SDK用于获取性能数据
		滴滴--->哆啦A梦

		app基本信息
			* 目标系统版本号22(5.0)/最低系统版本号19(4.4)
		查看崩溃日志
		logcat日志查看
		帧率---监测app有误卡顿
			* 一秒钟能够展示多少张图片
			* 24帧/秒
			* 网络上流畅的视频30帧/秒 60帧清晰度特别高
		cpu
		mem
		跳转耗时
	* 3.手机上安装第三方apk用于获取自己app的性能数据
		ali--->solopi.apk
		3.1真机adb调试
			* 开启开发者模式	
			* 开发人员选项中 打开usb调试		
			* adb devices	
			在电脑上下载安装对应的手机助手 打开手机助手连接手机,在电脑上安装驱动

		3.2安装使用solopi
	
#手机云测兼容性
	* 兼容性 ---> Top300机型
	* 崩溃率标准
		android---千分之2-千分之4
		ios---千分之3-千分之8
	
	腾讯WeTest Testin

	兼容性测试报告:
		* 手动做兼容性:
			1.运行monkey
			2.logcat+monkey日志
		

	机型列表 -- 如何找手机
		* 50台
		* android + ios
		* ios:6 7 8 X 11 ----->10台
		* android--->40台
			http://top.zol.com.cn/compositor/57/hit_wave.html




# 第三方登录
	* 原理
	* 测试点

# 直播流程
	* 原理
	* 测试点

# 直播间需求文档
	* 项目流程分析 ---> 拿一个功能模块 说一下流程
	

	1.编写测试用例/测试点
	2.web端测试 手机端测试话术 
	
	功能测试 接口测试 性能测试 自动化测试

#接口测试 = api(接口) test
	1.复习前后台数据交换格式
		
	2.http协议
		* 概念:超文本传输协议 用于定义web浏览器和web服务器之间数据交换过程

		请求
			* 一个请求行
				请求方式 请求路径 协议版本
				* get 用于从服务器获取数据 是最常见的请求方式,请求的参数放在地址栏,不安全的,并且传递的数据量有限,根据浏览器不同1-8kb
				* post 用于向服务器发送数据 请求参数放在请求实体内容中,相对安全,在修改了服务器支持post大数据传递的情况下,post发送数据大小不受限制,可以用于文件上传
			* 若干请求头
				Content-Type 告诉服务器请求数据的类型(请求参数的类型)
				Cookie 浏览器向服务器发送的cookie缓存
			* 一个空行
			* 请求的实体内容
				只有post方式有
		响应
			* 一个响应行
				响应状态码 协议版本
				200 ---服务器做出响应/响应成功(200只能说明服务器做出了响应,不能够说明服务器响应内容的正确性---json数据里面的错误码)
				302/307 重定向
				304 使用本地缓存
				404 路径找不到/资源不存在
				500 服务器异常
			* 若干响应头
				Content-Type 告诉浏览器 服务器响应的数据类型:html json png ....
				Set-Cookie 服务器传递给浏览器需要让浏览器缓存到本地的数据
	
			* 一个空行
			* 响应的实体内容

	3.接口概念
		* 程序内部接口
			函数与函数之间的调用
		* 系统对外接口
			简单来说就是通过请求url路径以及传递响应参数可以获取服务器数据,这个路径系统对外接口

	4.接口类型
		* webservice
			用soap协议 请求和响应的数据都是xml格式
			soap协议=http协议+ xml数据传递

			xml数据格式是重量级的,同样的数据传递要比json大好几倍
		* http api
			通过路径来区分调用的方法，请求报文都是key(键)-value(值)形式的，返回报文一般都是json串，有get和post等请求方式
			* 报文=参数

	5.什么是接口测试
		* 所谓接口测试就是通过测试不同情况下的入参与之相应的出参信息来判断接口是否符合或满足相应的功能性、安全性要求
		
	6.为什么做接口测试
		* 通过接口测试可以知道是否进行了前后台数据校验
		* 做功能或者界面测试的时候 不知道页面上数据展示是否正确(可以发现页面操作发现不了的bug) 可以借助于接口测试
		* 检查系统的异常处理能力
		* 检查系统的安全性(请求及响应的关键敏感数据是否进行了加密)、稳定性(通过接口可以测试服务器的性能)
		* 
	7.如何做接口测试
		* 分析接口文档 ---> 编写接口测试用例 ---> 放在工具(postman)中执行测试	

		* 一个接口有哪些内容:
			接口名称
			请求路径
			请求方式
			请求头参数
			请求参数
			请求示例
			
			返回结果说明
		* 编写接口测试用例
			接口用例和功能测试用例有什么区别? ---> 回答一下接口用例和功能用例包含哪些元素/栏位即可
			* 功能测试用例
				用例编号 标题 所属模块 操作步骤 输入数据 预期结果 实际结果
			* 接口用例
				id	所属模块	接口名称	url	请求方式	header	是否依赖其他case	依赖的字断 请求参数	预期结果	实际结果

			如何编写接口用例:
				* 通过性 --->正确的 先保证给接口正确的请求 接口能够正确返回
				* 异常性 ---> 测试服务器的容错性


				不同情况下的入参与之相应的出参信息来判断接口 ---> 编写测试用例 实际上就是不停的更改请求参数

	作业:
		* 登录接口
		* 二级分页接口

####Postman ---> 专门做接口测试的工具
	* 下载地址:https://www.getpostman.com/downloads/
	* 双击Postman-win64-7.5.0-Setup.exe安装postman

	1.get方式的请求
		不带参数
		带参数
	2.postman界面内容解析
		
	3.post方式请求 以及post报文(参数)格式
		* post报文格式
			form-data 可以上传文本数据和文件数据,通常可以用于图文混传
			x-www-form-urlencoded 自动添加请求头Content-Type 只能上传文本格式的键值对数据
			raw 可以上传任意格式的文本 text(普通字符串) json(字典格式字符串) html(网页字符串) xml ....
			binary 只能上传单个二进制文件

			用哪一个????服务器上的接口要哪种格式的数据就要传哪种格式的数据 --->后台开发人员写接口的时候确定了报文格式

		* 发送post请求
	
	4.创建项目 添加断言 批量执行
		* response body contsains string ---> 通过判断响应内容中是否包含某个字符串来判断是否通过

		postman能不能对服务器做压力测试?
			* 能 可以设置循环迭代次数给服务器产生压力 但是获取不到服务器的性能数据,所以一般postman都是做接口测试 性能测试用jmeter 


		作业:
			* 宝淘里面登录 商品信息接口 购物车 ---> 创建项目 添加断言 批量执行
			* 明天晚上检查话术 李鑫组 手机端


	5.postman自动添加请求头cookie
		* 公司中大部分登录接口用post ---> 如果发现你们公司登录用get方式 提bug
		
		* postman具有记录cookie头信息的功能,cookie头信息里面保存了登录的状态

	6.参数化
		a = 10

		print(a)
		print(a+5)

		* 1.全局globals变量 ---> 每个接口里面都可能使用到的参数
			主机路径
				* 好处????
					测试接口的时候 线上接口要测试 线下接口也要测试,而线上和线下主机路径是不一样的
	
		* 2.环境environment变量
			登录:普通 vip svip ...... 100种角色--->每种角色的用户都要测试一下 

			如果不使用环境变量 需要添加100个登录请求

		variable变量 value值

		什么参数可以进行参数化?---->所有的参数都可以

	7.接口依赖/接口联调
		* 第一个接口--提供数据  ---> 获取验证码
			在tests位置 获取需要提供的数据,设置成环境变量

			//1.把响应实体内容转成json对象
			var json = JSON.parse(responseBody);
			
			//取出需要的数据 并且设置成环境变量 json.status json.msg json.data.code
			pm.environment.set("ccc", json.data.code);


		* 第二个接口--需要数据 ----> 注册接口
			在需要参数值的位置 {{}}引用变量即可

	8.文件上传
		* SeleniumDemo.war放到tomcat的webapps目录下,启动tomcat服务器

	9.测试webservice接口
		* webservice使用soap协议=http协议+xml数据格式(请求报文 响应报文)
		xml数据格式重量级 所以很多webservice接口废弃;个别公司依然使用webservice

	10.测试resetful风格的接口
		* restful风格的接口是http api接口的一种
		* 普通的http api接口都是键值对形式 并且使用的时候键和值必须都要传递
		* restful风格接口 比较特殊 只需要按照参数的位置进行传值,不会暴露键(参数名称),相对比较安全 并且简洁

		http://www.jingdong.com/login?username=zhangsan&password=123456

		http://www.jingdong.com/login/zhangsan/123456

		把stest.war放到webapps目录下
		* http://127.0.0.1:8081/stest/product/{color}/{type}/{minPrice}/{maxPrice}

		特点:
			用四种请求方式表示对数据库的增删改查

			get 查询
			post 增加
			put 修改
			delete 删除

	11.模拟服务器mock server
		* 前提需要注册并登录上postman

####宝淘接口

	There was an error有一个错误 in evaluating the test script测试脚本:  ReferenceError: reaponseBody is not没有 defined定义

	在线格式化json工具:
		* http://json.la/

####接口中常见bug
	面试经常问题:你测出过来哪些印象深刻的bug?
		* 功能 界面 兼容性 弱网 前后台切换 ......
		* 接口
		* 性能

	1.注册接口的时候 正常注册没有问题,减少了用户名等参数在执行注册发现能够注册成功,是个bug,说明后台sql并没有做非空限制,,,也没有做唯一性校验
	2.注册接口邮箱写非法格式,发现注册成功,说明没有对邮箱格式进行校验
	3.查看商品分类数据的时候,分类id存在但是页码page给一个比较大的值,返回结果是id没有查询到,应该返回page过大,没有更信息
	4.查询分页数据,page字段表示页码,是应该有默认值的,但是不传会报错500服务器内部异常,说明后台程序员没有对page做一个判断
	5.我们公司有些接口必传字段不传递,没有任何容错性的提示,而是直接报500异常

	6.接口中操作加入购物车 在web页面上进行刷新 发现没有任何商品数据--->发现程序员把购物车数据加载缓存里面,并没有购物车sql表
	7.添加购物车的时候 商品数量在前台页面限制为200,但是接口中添加的时候可以任意数量 甚至还可以是复数

####项目测试策略
	* 需求评审
		产品需求文档 产品原型图 UI效果图
	* 测试计划制定
		测试计划
		测试用例
	* 测试计划执行	
		单元 集成(测后台接口) 系统(功能 性能) 验收


	1.后台
		* 1.1数据库
			增删改查 ---> 数据是否正确
			----- 后台管理系统web网站
		* 1.2后台接口
			如何知道接口返回数据的正确性? ---> 通过对比数据库中查询出来的数据来判断
		* 1.3性能测试

		通过后台测试 使用接口返回的数据是没有问题了 可以给前台使用了

	2.前台
		1.功能
		2.非功能:
			* 界面
			* 兼容性
			* 权限-安全性
			* 弱网
			* 前后台
			* 中断
			* 性能
			* 随机压力
			* 推送
			* 版本更新
			* 安装卸载
			* ......

		前台测试的时候 判断展示数据是否正确 需要请求接口进行对比

	3.前后台联调 --- 与后台服务器的联调测试

	作业:
		* 宝淘所有接口 ---> 练熟postman
		* 常见bug
		* 接口测试话术 ---周四晚上
		
		
####数据/会话保存技术
	* 默认http连接无状态 

	cookie可以保存数据
		* 原理

	session会话

	token令牌 ---> app
		* session还是cookie都会借助于cookie,而cookie是浏览器保存数据的技术,app是一个客户端,所以尽量不使用,,,有自己保存/认证状态的技术token

	* 问题:cookie session 和token区别
		1）cookie数据存放在客户的浏览器上，session数据放在服务器上；
		2）cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗,考虑到安全应当使用session；
		3）session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能,考虑到减轻服务器性能方面，应当使用cookie；
		4）单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie,最多300个站点。
		所以个人建议：
		将登陆信息等重要信息存放为session；
		其他信息如果需要保留，可以放在cookie中
		* Token就是令牌，比如你授权（登录）一个程序时，他就是个依据，判断你是否已经授权该软件,Token的状态是存储在客户端

####物流平台接口文档
	* 1.接口加密/签名的方式
		为什么进行签名?
			* 为了保证接口数据的安全性,前台/接口传过来的签名数据跟后台签名数据作对比,如果不匹配提示签名错误,没有权限
			
	* 2.公司接口文档中错误码及含义
		1	操作成功	
		2	操作失败	
		33	参数解析失败	一般指json格式错误
		44	订单重复提交	(会返回提交成功时的承运信息)
		401	身份安全验证失败	
		500	系统错误	
		999	需要登录	

	* 作业:
		1.cookie session token原理图
		2.总结记住错误及含义
		3.了解下签名信息

		练熟postman

####switchhosts工具使用
	* switch选择hosts主机 
	
	为什么使用switchhosts工具?
		* 环境分类
			开发环境
			测试环境
			线上/发布/生产环境
			
			区别:
				* 搭建环境方式一样
				* 数据的真实性不一样
				* 主机路径不一样 ---> 服务器ip地址不同
		* 各个环境都需要测试

		线上:123.57.29.238 www.baotao.com
		线下:192.168.0.183 www.test.baotao.com

		域名 
			* 真实公司里面用的都是域名 而不是直接使用ip地址
			* 所以需要域名跟ip地址一一对应

	switchhosts工具可以实现域名和服务器的ip地址一一对应

			
	* 使用域名访问服务器过程
		先进行域名解析---> ip
		1.先在本地hosts文件中查找是否有baidu.com对应的ip地址
			* C:\Windows\System32\drivers\etc

		如果有 则使用本地hosts文件里面的ip地址
		如果没有 则去公网解析域名

	-----------各种环境-----------
	开发环境
		* 169.254.1.123 www.dev.baotao.com
	测试环境
		* 192.168.0.183 www.test.baotao.com
	线上环境
		* 123.57.29.238 www.baotao.com

		完成了域名和ip地址一一对应 在测试的时候 你就可以使用域名 而不是输入ip地址


	switchhosts可以代替手动打开hosts文件去修改,然后选择环境
		* 1.修改hosts文件的权限
			在hosts文件上面右键属性 ---> 安全 -->编辑 --> 每个用户都允许 --应用--确定
		* 2.退出360/腾讯...电脑管家
		* 3.右键SwitchHosts!.exe管理员身份运行

#抓包测试
	概念:
		* 抓包就是将网络传输发送与接收的数据包进行截获、重发、编辑、转存等操作，也用来检查网络安全
		注意:一个上线的app如果被抓取到数据 本身就是不安全的 就是大bug

	为什么抓包:
		1:有时候公司中接口文档某几个信息可能不详细，要测试这些接口的请求参数与响应结果，以及数据传输是否安全，那么抓包测试
			* 如果没有接口文档你能不能做接口测试???
        2:通过抓取数据请求，可以放到Jmeter中对服务器做压力或者接口测试
        3:通过抓包工具，可以分析 接口的请求参数，响应结果，以及数据传输是否安全
        4:通过抓包工具，可以编辑请求或者响应，达到测试效果

	抓包原理:
		
####charles工具使用
	* 双击charles-proxy-4.1.4-win64.msi安装charles
	* 注册破解:默认charles半小时退出
		help ---> register .... 输入账号和认证
		 https://zhile.io
         48891cf209c6d32bf4

	1.抓取http(请求 和 响应)
		* 1.设置charles允许抓取http请求
			proxy-->proxy setting ---> 勾选enable proxying http
		* 2.保证电脑防火墙关闭,保证电脑和手机在同一个网络内
		* 3.设置手机上网经过代理服务器
			wifi上 修改网络-->高级选项-->手动代理-->电脑ip+charles运行的端口号(一个端口只能运行一个应用)
			* 当你的电脑网络换了 需要重新设置ip!!!!
		* 4.操作手机浏览器 charles上将会弹出允许/拒绝的询问框 点击allow


	proxy代理 proxy setting代理设置

	2.抓取内容解析
		

		搜索接口:
			* 关键词word
			* 哪一页数据page
			* 一页展示多少条数据pageSize


	* 作业:
		switchhosts工具
		charles安装 抓取儿歌多多 ---> 模拟器

	3.抓取https请求 ---- 瓜子二手车
		* https与http的区别
			安全套接字层超文本传输协议HTTPS，为了数据传输的安全，HTTPS在HTTP的基础上加入了SSL协议
			1、https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
		　　2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
		　　3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
		　　4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全
	
		* 抓取https原理
			
		* 安装charles之后的完整设置 --> http和https
			1.设置charles允许抓取http
				proxy-->proxy setting ---> 勾选enable proxying http
			2.保证电脑防火墙关闭,保证电脑和手机在同一个网络内
			3.设置手机上网经过代理服务器
				wifi上 修改网络-->高级选项-->手动代理-->电脑ip+charles运行的端口号(一个端口只能运行一个应用)
			* 当你的电脑网络换了 需要重新设置ip!!!!
			4.操作手机浏览器 charles上将会弹出允许/拒绝的询问框 点击allow


			5.电脑上安装charles证书并且信任证书
				* 安装到电脑
					help --> ssl(https) proxing --> 点击install(安装) charles root certificate(ca,证书)
				* 电脑上信任charles证书
					ie浏览器位置:C:\Program Files\Internet Explorer

					设置-->Internet选项-->内容-->证书
						* 需要把charles证书放到受信任里面
						* 如果在受信任里面 不需要做任何操作
						* 如果不在受信任,在中级证书里面,需要导出,然后到受信任里面进行导入
			6.手机上安装证书
				help---ssl proxying---点击install charles certificate on a mobile device.....
				打开手机浏览器 输入chls.pro/ssl进行安装证书
			7.charles进一步设置抓取https
				* proxy-->ssl proxying setting-->勾选enable ssl proxing(允许https待柳)-->点击add-->*:* 

	4.断点操作
		可以查看app有没有网络重连机制,一个网络健壮的app是应该有网络重连机制的
			* 瓜子二手车没有网络重连机制
			* 儿哥多多是有网络重连机制的 ----> 一般网络重连次数3次,具体几次由公司前台程序员确定

		* 断请求
			修改请求方式 请求参数 请求头参数等来达到接口测试的效果
			
			proxy--> breakpoint setting --> 勾选enable breakpoint(允许断点)-->添加需要断点操作的路径
			
		* 断响应
			通过断响应 修改响应返回的数据 然后在返回给前台app,查看app展示内容是否对应改变

			* 我们在做前后台联调测试的时候,需要修改后台数据库中的数据,来查看前台页面上数据是否对应的改变,但是很多情况下我们没有修改数据库的权限,可以借助于断响应的操作来模拟的修改后台数据


			* 1.把响应的数据 全部删除 再给app返回 .,...判断app有没有做非空校验,如果没有做app将会崩溃
			* 2.改变某一个字段的值 来查看页面上的数据是否会动态改变


		注意 不做断点操作的时候 直接关闭断点

	5.模拟弱网
		* 1.第三方wifi工具可以进行限速 ---> 整个手机的网速
		* 2.使用charles/fiddler来模拟弱网

		charles模拟弱网的原理是限制带宽;fiddler模拟弱网是延长时间
		charles可以只对某个主机路径进行限速;而fiddler是对所有的路径进行限速

		2G:15-20kb/s 
			* 一般设置10kb/s的速度

		注意:不使用的时候 一定关闭限速

	6.模拟本地数据map local
		* 操作看录屏
		* 
		* 不使用的时候 关闭map local : tools-->map local-->取消勾选enable map local
		* 
		为什么时候map local模拟本地数据的功能?
			* 我们在做前后台联调测试的时候,需要修改后台数据库中的数据,来查看前台页面是否会	对应改变,但是没有数据库修改权限,可以借助map local的功能,通过修改本地数据来模拟的修改后台数据库数据

	7.抓包能抓哪些?
		* app(android/ios)
		* pc客户端
		* web网站 ---> F12也可以
		* 小程序

		-------------->经过上面那个完整的设置 不需要做多余的设置就可以抓取

	8.抓包工具能干什么?
		* 分析接口响应时间及响应速度是否满足要求(2-5-8原则),接口响应时间一般1s内
			举例:其他页面打开都会正常显示,但是其中一个页面需要等待5s左右才显示?
				1.接口响应时间长 2.前台程序员接受到数据之后,解析渲染到页面的时间长
		* 分析接口中关键的数据(请求+响应)有没有进行加密传递 --- 安全性
		* 抓包工具还可以进行断点操作,可以判断app有没网络重连机制
		* 断请求可以修改请求方式 请求参数等 来达到接口测试效果
		* 断响应可以通过修改响应的数据来模拟修改后台服务器的数据,来查看前台页面是否对应改变
		* map local可以通过修改本地数据,来模拟修改后台服务器数据,来查看前台页面是否对应改变
			前后台联调测试的时候
		* 可以模拟弱网来进行弱网测试
		* 可以定位bug是在前台还是后台????
			举例:进入页面展示空白,其他页面都没有问题,bug属于前台还是后台?
			* 定位bug过程::::::!!!!!!
				1.抓取请求,分析请求参数 路径 方式等,跟接口文档中的接口进行对比,如果有问题,说明前台程序员发送的请求是有问题的,bug在前台
				2.查看响应内容,如果有问题说明后台返回数据有问题,bug属于后台
				3.如果后台返回数据也正常,说明前台程序员在接受到数据之后的解析展示有问题,bug属于前台
			
	9.是不是所有的app都能被抓取到数据?
		* 如果一个上线的app能够被抓取到数据 本身就是不安全的
		* 一个上线的app是不应该被抓取到数据的
		避免被抓包的方式:
			1.可以判断是否使用了代理服务器,如果使用代理服务器不继续网络请求
			2.前台程序员还可以判断证书的正确性,如果是假证书不继续发送请求,也抓取不到
			3.让抓 但是抓取出来的请求 和 响应全都是加密的乱码数据,不是明文

		app抓取不到数据的表现:
			* 不开代理服务器charles,app正常能够使用
			* 开了代理服务器之后,发现app总是提示网络异常////提示证书错误
		

		到公司之后 按照步骤设置手机 charles等,先抓取瓜子二手车,如果抓不到说明设置有问题;;;如果抓到了,但是你们公司的app抓不到,说明你们公司的app不让抓;需要程序员给你打包一个可以抓包的apk文件

	* 作业:
		练熟charle所有操作
		找一个新的应用https,登录 搜索页面(多个) 首页(多个) -----> 对app里面的数据
		
#Fiddler
	双击FiddlerSetup.exe安装,不需要破解

	1.抓取http
		* 设置fiddler允许抓取http
			tools(工具) --options(选项)--connection(连接)---勾选allow remote computer to connect(允许远程设备连接到fiddler)
		* 设置手机
			手机与电脑在同一个网络内,手机设置手动代理ip(电脑)+fiddler端口号

	2.抓取内容分析

	3.抓取https
		* fiddler完整设置
			* 设置fiddler允许抓取http
				tools(工具) --options(选项)--connection(连接)---勾选allow remote computer to connect(允许远程设备连接到fiddler)
			* 设置手机
				手机与电脑在同一个网络内,手机设置手动代理ip(电脑)+fiddler端口号
			* 允许抓取https,并且安装证书到电脑
				tools-->options-->https	--勾选detry https traffic(允许https)
				1.自动弹出信任证书的询问框,点击yes进行证书的安装
				2.不自动弹出,actions--->reset all certificate(重置所有证书),查看是否弹出信任证书的提示框,弹出点击yes进行安装
				3.如果还不弹出询问框,actions-->点击trust root certificate(手动信任证书)

				勾选ignore server certificate errors(忽略服务器证书错误)
			* 证书安装到手机上
				手机浏览器上输入ip:fiddler端口号
	4.断点操作
		charles可以对某一个路径进行断点 fiddler是对所有进行断点

		rules(规则)--automatic breakpoints(自动断点)--disable(不允许断点) before request(断请求) after response(断响应)

		* 断请求
			可以编辑请求方式 请求参数等 来达到接口测试的效果(接口测试就是不断修改请求参数来查看响应数据是否满足功能性以及安全性的要求)
		* 断响应
			在做前后台联调测试的时候 需要修改数据库中的数据来查看前台页面是否对应的改变,但是很多情况下没有修改数据库的权限,可以借助于断响应来模拟的修改数据库的数据,查看前台页面是否对应的改变

		注意:不用的时候disable

	5.模拟弱网
		* charles模拟弱网的原理是限制带宽,降低网速(宽带100M---带宽100/8=12.5M/s),并且charles限速可以只对某个主机路径(单个app)进行限速
		* fiddler原理是延长请求和响应的时间,并且fiddler限速是对整个手机

		1.自定义限速的规则
			rules(规则)--customize rules(自定义规则)--搜索m_Simulate
		2.开启规则 进行限速
			rules-->performace-->点击simulate modem speed进行开启限速规则

		注意:不使用之后 rules-->performace-->取消勾选simulate modem speed

	6.模拟本地数据
		* 在做前后台联调测试的时候 需要修改后台数据库中的数据来查看前台页面是否对应的改变,很多时候没有修改数据库的权限,可以借助于模拟本地文件数据,来模拟的修改后台数据

		auto responder(自动响应) --勾选enable rules(允许接口数据来自本地文件)--点击add rules进行规则添加-->选择find a file

		注意:不使用的时候 取消勾选enable rules

	7.添加过滤器
		filters(过滤器)---勾选use filters-->选择show only the following hosts(只展示下面输入的主机路径)-->添加主机路径

		注意:不使用的时候 取消勾选use filter

####抓包测试中常见的bug
	* 1.抓取数据过程中,发现其他接口响应时间正常,有一个接口返回数据量不是特别大,但是响应时间已经超过了1S
	* 在抓我们公司app的时候,发现线上的版本也能抓取数据,并且是明文传递 --- 登录接口(12306)
	* 在抓包过程中,断请求或者响应发现我们app刚开始没有网络重连
	* 通过断响应把响应的数据删除或者删除了部分数据,破坏了原来后台返回的json内容,前台发生崩溃,说明前台程序员没有对后台返回的数据进行判断
	* 通过断响应修改某个字段的内容,例如商品的名称/价格,发现app页面上没有动态改变,原因前台程序员把商品名称/价格在前台写的是固定的
	* 在抓包工具模拟弱网的时候 数据返回延时 程序崩溃等


##抓包_接口测试练习
	* 找一个项目(https)抓包 形成接口文档
	* 首页上所有的接口 登录接口 搜索及搜索(热门搜索 + 搜索接口)页面上接口
	
	-----------目的:你拿一个你们项目中的接口 给我说明一下 里面有哪些参数 什么含义?返回数据里面有什么内容?你如何做的接口测试?

	1.不是所有的app都能抓取数据 
	2.如果手机设置了代理服务器 但是代理服务器没有开启 手机上不能够上网的

###接口 抓包测试面试题
	* 接口测试测什么?
		通过性 异常性 安全性 性能测试


	作业:
		* 1.形成接口文档 至少8个接口
		* 2.接口测试话术 ---- 抓包测试话术

# python复习
	* 测试人员会代码重要性?
		技术水平 ----> 钱
		职位高低 

	* python代码可以干什么?
		接口自动化测试 -python
		功能/UI自动化测试 -python
		性能自动化测试----jmeter loadrunner

	* 复习
		while for循环
		字符串
		列表
		字典
		文件
		异常
		单元测试
		生成html报告
# 接口自动化
	自动化:
		* 单元测试
		* 接口自动化 
		* UI/功能自动化

	requests
		* 发送get请求
		* 发送post请求
		* 添加超时
		* 添加请求头
		* 接口依赖
		* 文件上传

	requests安装失败:
		* 1.从谷歌浏览器去关闭电脑代理,重启一下pycharm

		* 2.能搜到 但是安装失败,,,找到pycharm的helpers目录,复制替换packaging_tools.py

	requests宝淘接口练习

#HttpRunner接口自动化
	介绍:
		* HttpRunner 是一款面向 HTTP(S) 协议的通用测试框架，只需编写维护一份JSON脚本，即可实现自动化接口测试。
	特性:
		* 	继承 Requests 的全部特性
		* json文件表示了接口的测试用例
		* 支持参数化和数据驱动机制

	环境配置:
		* 安装httpRunner
			File--->Settings--->Project Interpreter--->+ 搜索httprunner选择安装
		* 配置httprunner的环境变量
			搜索hrun.exe文件,把该文件所在的目录添加到环境变量path里面去
		* 验证是否配置成功
			cmd--->hrun -V 查看版本号

	json表示了接口测试用例---json编写测试用例的格式:
		* config配置
			base_url 基准/主机路径
			name模块名称
			variables变量---参数化
		* teststeps测试步骤---列表
			一个步骤表示一条测试用例 ---> 表示一条接口请求
			name接口名称
			request接口请求
				* method请求方式
				* url请求路径
				* headers请求头
				* params ---get方式的请求参数
				* data --post方式的请求参数
				* json ---json格式的请求参数
			validate预期结果 ----列表 ---预期结果可以有多个
				* eq=equals是否一致
				"eq":["status_code",200]   ---- 根据requests模块里面,status_code表示http响应状态码
				"eq":["content.msg","success"] ---content表示响应的内容

				* contains 实际结果是否包含预期结果

	1.发送get请求(不带参数/带参数)
	2.发送post方式请求
	3.上传json格式的报文
		* tomcat服务器启动
	4.预期结果
		* eq
		* contains
	5.参数化
		* base_url
		* variables
			$变量名称 ---->使用参数化
		* debugtalk.py的形式
			${函数名()} ---调用函数 获取返回值

	6.接口依赖 添加请求头
		* 第一个接口
			extract --- 导出
	7.测试报告解析
	8.上传文件
		"files":{
          "pic":["文件名称","文件对象","文件类型"]
        }
	9.生成httprunner接口自动化测试项目
		* 可以批量执行json接口用例,生成一份测试报告
		* 执行hrun --startproject 项目名称 ---->生成httprunner项目
		* 用pycharm打开项目


		项目文件夹:
			* reports 看报告用的
			* testcases 放json格式的测试用例用的
			* testsuites 测试套件 包含/引用测试用例文件的
		顺序:
			* 在testcases里面写json文件
			* 到testsuites里面去运行 ----> 会把所有的json文件 按照顺序执行 生成一份html测试报告

	10.宝淘接口流程

	11.参数化补充
		* base_url
		* variables
		* debugtalk.py
		* extract ----> 导出 ---- 只能在同一个json文件里面

		举例:postman测试登录 ----参数化 写一个接口请求

		httprunner如何实现写一个登录请求,csv表格里面写了多个数据 ---测试多次?????
			* 在testsuites里面编写csv文件
			* 在testsuites里面读取csv文件
		

#Selenium-web功能/UI自动化测试
	* 介绍
		在设计完测试用例 并通过了评审之后
		前提:
			* 需求变动不频繁
			* 项目周期足够长
			* 自动化测试脚本可重复使用
		分类:
			* 单元测试
			* 接口自动化测试
			* UI/功能自动化测试
		Selenium是一个用于Web应用程序测试的工具，支持多平台，多浏览器，多语言去实现自动化测试，目前在Web自动化领域应用越来越广泛

	* 环境搭建
		1.python环境 并且配置了环境变量
		2.pycharm 编写代码工具
		3.在pycharm上安装Selenium的库文件
			* file-->setting-->Project-->project intercepter-->+号搜索selenium-->左下角安装
		4.安装谷歌浏览器
		5.搜索对应版本的谷歌浏览器驱动 并放到python安装根目录
			* 查看谷歌浏览器版本-chrome://version/
			* 查看对应的驱动版本进行下载
				http://npm.taobao.org/mirrors/chromedriver/
			* 解压压缩文件之后 把chromedriver.exe复制粘贴到python安装的根目录下

		验证:
			webdriver.Chrome()

	* 代码分析
	* 1.selenium定位/查找元素
		元素:就是页面上展示的内容
			* img button <a> input select checkbox .....
		使用本地的网页进行元素定位:
			* SeleniumDemo.war放到webapps目录下
			* 启动tomcat
			* http://localhost:8080/SeleniumDemo/elements.html


		1.id
		2.name
		3.class
		4.tagName标签名
		5.css
		6.xpath
		7.link_text
		8.partial_link_text部分连接文本

			* find_element_by_xxxx('值')
	
		所有的操作 都需要借助于driver驱动对象完成
			* get()打开一个网页
			* find_element_by_xxxx('值')查找元素
			* clear()清空输入框
			* send_keys()输入
			* click()点击

	* 2.浏览器操作
		get()打开网页
		back()后退
		forword()前进
		current_url当前页面路径
		title标题
		page_source源代码
		get_screenshot_as_file()截图

	* 3.鼠标事件
		点击 悬浮 移动 拖动 双击 右击....
		ActionChains(driver) 动作链
		move_to_element()移动到元素上
		perform()完成
	* 4.键盘事件
		send_keys()
	* 5.单选框 多选框操作
		使用name定位一组元素

		get_attribute()获取元素的属性 ----标签里面的键值对
		text获取标签体内容 ----开始标签和结束标签之间的内容
	* 6.宝淘登录练习
	* 7.下拉选择框
		先找到选择框元素
		再借助于Select类进行操作
			* select_by_index(0) # 通过选项的下标来选中
			* select_by_visible_text('天津') #通过展示出来的文本进行选中
			* select_by_value('chongqing') #通过value属性值进行选中
	* 8.文件上传
		修改tomcat端口号8081,重启tomcat
	* 9.对话框操作
		alert
		switch_to切换到
		accept()确定
		dismiss()取消
		send_keys()输入
	* 10.cookie操作
		get_cookies()获取所有的cookie信息
		get_cookie(name) 根据名称获取cookie信息
		add_cookie()添加
		delete_cookie(name)根据名字进行删除
		delete_all_cookies()删除所有cookie
	* 11.宝淘登录练习
		打开一次浏览器 登录十次 登录失败进行截图

	* 12.元素等待
		implicity_wait(30) 隐式等待,等待页面元素的加载:整个页面加载完成开始查找元素,最多等30s,如果1s找到了元素,不再继续等待;如果超过30s还没找到 会报错(元素找不到)NoSuchElementException

		显式等待 ---不关心整个页面的加载,而是只关心某个元素,只要元素加载出来就进行操作
		WebDriverWait(driver=self.driver,timeout=10,poll_frequency=0.5).until(presence_of_element_located(e))

		* 

	* 13.frameset框架
		switch_to
		frame()
		default_content()默认内容
	* 14.iframe
		switch_to
		frame() ...根据id切换
	* 15.多窗口定位
		switch_to
		windown()
		driver.windonw_handles获取打开的所有窗口

	* 16.练习 --云OA
		进入登录页面:
			1.进入首页 点击登录连接
			2.直接输入登录地址 ---提高脚本效率

###Selenium封装
	为什么进行封装?封装思想?
		* 1.get()打开页面经常会用到页面路径,url路径最好与我们的代码相分离,单独写一个文件记录所有的路径,好处是当需要修改的时候 只需要到这个文件中进行修改即可,而不需要到每个代码中进行修改
		* 2.在做自动化测试过程中 会用到一些数据 例如登录操作,需要登录不同的用户,也应该做到逻辑代码和数据相分离的操作,好处数据和代码单独维护...csv表格数据---ddt数据驱动的模式
		* 3.单元测试类setUp和tearDown函数都是固定操作,可以抽取一个单元测试的基类,以后再写单元测试的时候 不需要继承unittest.TestCase类,只需要继承自己写的基类
		* 4.获取驱动对象也是每个自动化页面必须要有的,只需获取一次 其他地方进行引用
		* 5.每次打开页面之后 例如:查找元素 截图等都是公共的操作,可以抽取一个所有页面的基类PageObject,以后不管打开什么样的页面都需要继承PageObject即可

	步骤:
		* 1.分包--分文件夹:文件夹的名称 文件的名称不允许出现汉字 数字等等
		* 2.common(公共文件夹)
			* 获取驱动对象的函数
			* myunit单元测试基类封装
		* 3.所有页面的父类PageObject
			查找元素
			截图
			读取csv数据
		* 4.编写每个页面的操作,例如登录页面 购物车页面....
			先找元素
			逻辑操作
			检查逻辑操作是否成功
		* 5.创建data文件夹
			urlPath.py记录所有需要的路径
		* 6.编写每个页面测试用例
		* 7.执行测试用例 生成html测试报告

# Appium
	* selenium ---web端功能/UI自动化测试框架
	* appium ---手机端功能/UI自动化测试框架
	* requests/HttpRunner ---接口自动化测试框架

	1.appium介绍
		* Appium是一个开源测试自动化框架
		* 可用于原生(native app)，混合(html)和移动Web应用程序()测试
			原生app---原生代码java swift
			混合开发式app ---原生 + html页面(前端)--->H5   
			移动web应用程序 ----所有的页面全都是html页面
		* 它使用WebDriver协议驱动iOS，Android和Windows应用程序

	模拟器/手机 取消手动代理!!!!

	2.优势:
		•	可以跨平台同时支持android、ios
		•	支持多种语言，java、python、php、Ruby等等
		•	不用为复杂的环境发愁
		•	如果你有selenium经验，直接上手 ---- appium里面所有的操作跟selenium一样

	3.appium原理

	4.环境搭建:
		* python环境 pycharm编写代码工具
		* 安装appium的库文件
			file--setting--project--project intercepter--点击+号搜索appium
		* JDK=java develop kit环境
			见Day26下
		* AndroidSDK环境
			见Day27下
		* appium服务的安装
			安装之前把360等电脑管家退出
			右键管理员身份运行appium-desktop-Setup-1.5.0-ia32.exe

			1.安装完成之后会自动打开,提示更新选择intsall later(稍后再说)
			2.搜索appium.exe 找到之后发送桌面快捷方式

	5.启动appium服务的三种方式
		* simple简单
			host指的是appium运行的主机路径 host指的是appium运行端口号
		* advanced高级 --->保存到preset预置
		* presets预置

		get raw logs---可以把appium服务运行的日志保存到本地文件
		stop server停止服务

	6.获取驱动对象
		* ip和端口号
		* dc字典格式的数据--capibility参数
			
	7.查找元素
		* id
		* xpath

#手机端测试复习
	1.app测试策略:
		* 安装卸载测试
		* 版本更新测试
		* 功能测试
		* UI界面测试
		* 前后台切换测试
		* 中断测试
		* 兼容性测试
			真机兼容 --- monkey
			云测兼容性---腾讯云测 testin 
			* 兼容性测试报告
				通过率标准:2%o-4%o
		* 消息推送测试
			极光/友盟推送
		* 弱网测试
			2G 15kb-20kb/s
			* 360wifi限速
			* 抓包工具限速
		* 性能测试
		* 随机压力monkey测试

	2.adb指令
		* 查看设备
		* 安装
		* 卸载
		* 查看包名
		* 清空数据及缓存
		* 查看日志
		* cpu
		* 内存
		* 电量
		* 启动时间
		* monkey测试

	3.app性能测试
		* adb指令获取性能数据
		* app里面集成第三方工具 进行性能数据的获取 ---滴滴的哆啦A梦
			网络上在线视频一般是30帧/s
		* 第三方apk工具获取性能数据 阿里solopi.apk
			自己装上solopi到手机上 查看某个应用数据

	4.产品原型---xmind测试点
		功能测试
		非功能
			* 安装卸载测试
			* 版本更新测试
			* 功能测试
			* UI界面测试
			* 前后台切换测试
			* 中断测试
			* 兼容性测试
				真机兼容 --- monkey
				云测兼容性---腾讯云测 testin 
				* 兼容性测试报告
					通过率标准:2%o-4%o
			* 消息推送测试
				极光/友盟推送
			* 弱网测试
				2G 15kb-20kb/s
				* 360wifi限速
				* 抓包工具限速
			* 性能测试
			* 随机压力monkey测试

		分组完成房产测试点
			* 功能
			* 非功能
		如果没有需求文档怎么进行测试?

	5.selenium面试题
		* 自动化用在没有需求改变的模块上...每次版本迭代进行回归测试的时候


	* 作业
		1.敲代码 --- selenium/appium群里发截图
		2.分组写房产测试点
		3.adb指令
			
#安全测试
	* 攻防 ---- <<web安全与攻防指南>>
	* 
	安全性:
		* 数据机密 ---- 抓包(是否加密)
		* 数据库不允许被攻击
		* 服务器需要7x24不宕机
	遵循安全标准
		* 国际标准
		* 国家标准
		* 企业标准		
			企业在开发项目之初就需要把安全考虑进去 
				* 1.产品经理不懂
				* 2.项目经理不管
				* 3.程序员嫌麻烦

	常见安全问题:
		* 注入
			sql注入 xpath注入
			演示
				* 1.在本地数据库创建库并插入数据
				* 2.把SQLInjection资料放到tomcat的webapps目录下 启动tomcat
					http://localhost:8081/SQLInjection/login.jsp
			避免sql注入:
				* 1.sql语句采用预编译的形式
				* 2.把传递到数据库的数据 进行编码转义
				* 3.验证白名单(只有张三 王五能操作数据库)
				* 4.最小权限(某测试只有查看的权限)

		* xss跨站脚本攻击
			浏览器---cookie
			* cookie加密处理，避免存放关键信息  
			* httponly=true 安全

		* 敏感信息泄露
			加密
				* 设计合理
				* 程序员代码正确实现
				*  正确的加密
				*  传输加密机制
				*  存储加密机制

			加密方式:
				* MD5加密 号称不可逆
					可以采用多次加密 还可以加盐再加密
					* lisi ---> lisi_abc / l1i2s3i4
				* 支付宝--RSA非对称加密
					公钥<---私钥
				* AES对称加密
					一把秘钥
		* 跨站请求伪造CSRF
			cookie关键认证数据
			session每次在后台都要动态生成
#定位功能测试
	定位方式
		* wifi定位
		* 基站定位
		* GPS定位

	测试:
		* 单独测试某个方式的时候 其他关闭 看是否定位准确
		* GPS权限验证:关闭定位失败 打开定位成功
		* 定位超时 失败是否提示/或者展示上次定位位置
		* 检查三种定位方式定位是否能成功,定位精度准确且无报错;
		* 三种定位正交组合,检查定位功能和定位精度;
		* 程序有打开GPS权限时,关闭网络和GPS,检查能否定位和定位精度;
		* 定位失败的两种处理情况(失败提示和是否返回上一次位置);
		* 定位过程中与来电、短彩信、蓝牙的冲突;
		* 定位过程中与功能键交互;
		* 不同网络环境(移动、联通、电信中3G/2G)下的定位速度和精度;
		* 不同环境(室内、室外、电梯)的定位和精度;
		* 本地测试外地定位---模拟地位:神行者app :--->有的app是不能用 


###AppScan使用
	* 测试web端的安全性,可以测试常见的安全问题
	安装及破解:
		* 1.双击AppScan_Std_9.0.3.6_Eval_Win.exe进行安装
		* 2.进入安装的根目录下 复制LicenseProvider.dll到安装的根目录下

	* 使用
		


# SVN和GIT
	* 版本管理工具,管理代码的工具
	* 测试人员主要用版本管理工具管理测试相关的文档(需求文档 用例 计划 报告 bug缺陷报告...)
	* 
	SVN是Subversion的简称，是一个开放源代码的版本控制系统,说得简单一点SVN就是用于多个人共同开发同一个项目，共用资源的目的

		使用原理图

		如何使用?
			* 某台电脑上进行svn服务VisualSVN-Server-4.1.3-x64.msi的安装,傻瓜式安装
			
			repository仓库
			
			* 创建用户表,分配账号密码
			* 在仓库中创建一个项目,创建过程中需要分配权限
				nobody has access任何人没有访问的权限
					* Error: Access访问 to到 '/svn/cangku_1909a' forbidden拒绝  

				all user have read/write 所有的用户都可以对这个项目进行读写的操作
				custome自定义访问权限
			* 创建完成项目之后会得到该项目在服务上的地址
				https://Dash/svn/python_1809A/

			* 傻瓜式安装客户端TortoiseSVN-1.9.7.27907-x64-svn-1.9.7.msi,:
				所有操作这个项目代码的人都需要安装一个客户端

			* 使用右键checkout把服务项目下载本地,此时本地代码的文件夹绿色对号,表示跟服务上代码一致
				?表示是否添加到提交
				add这个文件之后,会变成加号,表示添加到提交
				文件夹变成红色感叹号,表示代码跟服务上发生了改变
				先右键update更新一下,再右键commit提交本地的代码到服务上
			* 冲突的解决
				conflicted冲突
			* 查看提交日志


		------->练习
		* svn 四人一组,分配账号,添加文件 提交文件 修改文件 更新 冲突解决 查看日志	
		* 
	* GIT
		svn与git的区别
			* git分布式，svn是单机，都是版本管理工具 


		* 傻瓜式安装:Git64_2.13.2.1.exe

		-------------------------->
			* github注册
		
		* 配置用户信息 用户名和邮箱
			git config --global user.name "DashShi"
			git config --global user.email 805256908@qq.com
		* 创建版本库 进入需要托管代码的文件夹 右键git bash here
			git init进行仓库的初始化
			此时该文件夹下面会生成一个隐藏的.git文件夹
		* 添加文件到版本库(其实是到版本库的缓存)
			git add . 把这个文件夹下面所有的文件都添加到库
			git add abc.txt 把某一个文件添加到库中
			git status -s 可以查看添加的状态
				* ?? 意思就是询问是否添加
				* A 表示已经添加到库 added
		* 提交添加到缓存的文件到真实的仓库 m=message信息
		 git commit -m "提交的信息说明"
		* 当修改了提交到仓库中的文件之后,再次查看状态
			M abc.txt (modified被修改)
			如果需要查看修改的具体内容,使用git diff指令(diff=diffrent)
			如果修改之后想要提交,再执行add 和commit指令即可
		* 查看提交的日志/记录
			git log
			git log --pretty=oneline 简略信息查看日志
		
		
		git的远程仓库
			* 先注册一个github账号
				GaoXiang1909 gaoxiang+qq号
			
			* 创建一个远程的仓库,会得到仓库在github上的地址
				例如:https://github.com/DashShi/testgitdemo.git
			* 要把本地的代码推送到github远程仓库
				先检查当前远程仓库是否存在:git remote add origin https://github.com/DashShi/testgitdemo.git
				远程仓库存在,把本地的代码推送到远程需要执行
					* git push -u origin master
					* 第一次执行的时候会弹出输入github账号的用户名和密码
			* 如果想把github远程仓库的代码拿到本地
				git clone "url"


	* 作业
		1.appscan运行生成报告
		2.svn练熟
		3.手机端话术 + 下一个selenium自动化话术

# Jmeter性能测试
	性能测试的目的:
		* 1.测出后台服务器系统最大处理能力
		* 2.测试后台系统最大支持的并发数

	1.性能测试概念
		* RT(response time)响应时间 
			用户标准2-5-10
			行业标准:500ms以下---互联网企业
		* 系统处理能力 --- 吞吐量TPS
			HPS=hit per second(服务器每秒被点击的次数)
			QPS=query per second(服务器每秒查询的次数)
			TPS（Transaction per Second）：系统每秒处理事务数，单位是笔/秒。

			行业标准:
				* 互联网中型网站：100TPS~500TPS
		* 并发用户数VU=virtual user(虚拟用户)
			并发用户数指在同一时刻内，打开系统并进行业务操作的用户数量

			测试后台性能需要虚拟用户数,标准???用多少??
				* 一般情况下测试性能 目的是测试最大处理能力
				* 而测试最大处理能力是需要并发用户的 但是不完全受并发用户数影响
			
			并发用户数=根据当前在线用户的峰值的8%-12%

		* 错误率
			稳定性较好的系统，其错误率应该由超时引起，即为超时率 设置超时时间是10s

			* 标准:成功率不低于99.4% 错误率不高于千分之六
		* CPU
			性能测试还要关注服务器的cpu消耗
			标准:CPU 利用率要低于业界警戒值范围之内，即小于或者等于75%
		* 内存memory
			内存利用率100%并不代表内存有瓶颈,因为目前很多电脑采用了缓存/虚拟内存swap技术

			SWAP交换空间利用率要低于70%

		* 磁盘吞吐量disk throughput
			磁盘吞吐量是指在无磁盘故障的情况下单位时间内通过磁盘的数据量
			
			一般情况下，磁盘繁忙率要低于70%,,,考虑更换磁盘 来提高性能

		* 网络吞吐量Network Throughput
			当网络吞吐量指标接近网络设备或链路最大传输能力时，则需要考虑升级网络设备;例如带宽100M ---> 12.5M/s --->检测发现一秒网络传输10M
			* 一般情况下不能超过设备或链路最大传输能力的70%。

			做性能测试的时候 考虑带宽的影响 尽量在局域网环境下;不得不外网的话 尽量企业级的百兆/千兆的网络

	2.并发用户数 与 TPS
		* TPS=虚拟用户数量/平均时间 ----> tps=VU/RT --->VU=TPS x RT

		并发用户数/虚拟用户数计算方式:
			* 第一个公式:根据在线用户的峰值计算并发量 8%-12% ,一般取10%
			* 第二个公式:根据pv数量，计算并发量
				PV(访问量)：即Page View, 即页面浏览量或点击量，用户每次刷新即被计算一次 
					* pv可以问后台开发要/或者运维要
				平均并发用户数
					* 
				峰值并发用户数
