一、  功能测试：
首先显示的是该系统为学生提供的功能，主要就是查看兼职信息、申请兼职：

	首页

![shouye][1]
[1]:http://fmn.rrimg.com/fmn061/20130624/0000/large_DTz9_7e170000034f125d.jpg

	选择Student-part-time-job，显示学生可以申请Part-time jobs

![jobs][2]
[2]:http://fmn.rrimg.com/fmn065/20130624/0000/large_MrIt_217d000001af125f.jpg

	点击family-education显示它的详细信息

![family][3]
[3]:http://fmn.rrimg.com/fmn065/20130624/0000/large_tHoQ_1294000002121260.jpg
 
	填写自己详细信息，申请该part-time-job

![jianli][4]
[4]:http://fmn.rrimg.com/fmn065/20130624/0000/large_w3N9_081f000003b2118c.jpg
 
然后是管理员的功能，充当企业的角色，进行兼职信息的发布：

	管理员该登录系统

![adlogin][5]
[5]:http://fmn.rrfmn.com/fmn058/20130624/0000/large_vsFS_767b0000bc441190.jpg
 
	进入管理员首页面

![adshouye][6]
[6]:http://fmn.rrimg.com/fmn062/20130624/0000/large_SFoQ_796b00004092118f.jpg
 
	管理员查看part-time-job信息

![adjobs][7]
[7]:http://fmn.rrimg.com/fmn061/20130624/0000/large_xxqH_7a2b00004082118f.jpg
 
	管理员给系统添加新功能

![adadd][8]
[8]:http://fmn.rrimg.com/fmn064/20130624/0000/large_JRE6_24f2000003ba1191.jpg
 
	管理员新添加 一个part-time-job

![adaddjob][9]
[9]:http://fmn.rrfmn.com/fmn059/20130624/0000/large_3TUO_188a00002f55118e.jpg
 
	管理员可以修改自己的密码

![gamima][10]
[10]:http://fmn.rrimg.com/fmn061/20130624/0000/large_DgT4_76560000bc5b1190.jpg
 
	管理员可以修改某一职位的信息

![][11]
[11]:http://fmn.xnpic.com/fmn057/20130624/0000/large_lE3H_7e1700000350125d.jpg
 

结果总结：

链接测试，页面之间切换速度比较快，而且能够较好的指导用户的进一行为；

交互测试，页面之间的交互快而且满足用户的需求；

数据校验，数据的校验速度快、而且效率比较高；

Cookies测试，Cookies对用户信息和用户在应用系统的操作的存储正确性比较高；

数据库测试，数据库访问量不宜过高，否则存在崩溃的风险，不过就目前我们的测试而言，还没有出现这种问题；

动态效果测试，我们在flash这一块没有做过多努力，所以动态效果有待提高。

二、	内容测试：

	目标：

	找出基于文本的文档、图形展示和其它媒体中的语法错误，例如，打字错误、语法错误；

	找出当导航发生时所展现的任何内容对象中的语义错误；

	找出展示给最终用户的内容的组织或结构方面的错误。

	结果：

	对用户界面层进行测试，结果显示为对每一个用户查询都正确地构造了Web页面脚本，并且正确地传输给了服务器端；

	对服务器端的Web应用层进行测试，结果显示为能够从Web页面脚本中正确地抽取出用户数据，并且正确地传输给服务器端的数据转换层；

	对数据转换功能进行测试，结果显示为创建了正确的SQL，并且传给合适的数据管理组件。

三．Web页面测试

概述：用于核实用户与软件之间的交互是否正常。

目标：核实一下内容：

-确保各种浏览及各种访问方法（鼠标移动、快捷键等）都使用正常

-确保窗口对象及其特征（菜单、大小、位置、状态和中心）都符合标准等

1.web页面测试内容

检查项	测试人员评价

窗口切换、移动、改变大小时正常吗？	正常

各种界面元素的文字正确吗？（如标题、提示等）	正确

各种界面元素的状态正确吗？（如有效、无效、选中等状态）	正确

各种界面元素支持键盘操作吗？	支持

各种界面元素支持鼠标操作吗？	支持

对话框中的缺省焦点正确吗？	正确

数据项能正确回显吗？	能

对于常用的功能，用户能否不必阅读手册就能使用？	能

执行有风险的操作时，有“确认”、“放弃”等提示吗？	没有

操作顺序合理吗？	比较合理

按钮排列合理吗？	合理

导航帮助明确吗？	比较明确

提示信息规范吗？	基本规范

2.导航测试

测试标准	评分	评论

a.定向	6.0	基本满足需求

能从首页中看出产品和服务范围	5.0	部分内容看不到

主导航机制的功能一目了然	6.0	主导航机制有缺失

在每一页都显示了在网站中的位置	6.0	显示不太清晰

全局导航在网站中的展示是一致的	7.0	比较一致

b.导航	6.1	管理员部分比较到位，用户有缺失

从首页能访问网站的所有主要部分	5.0	首页功能较简

重要的内容位于网站结构的高层	7.0	比较容易访问到

从首页经过三次点击就能到达内容	6.0	基本可以

有补充的导航机制可用	4.0	机制不完善

每一页都有退出点	5.0	管理员登录可退出

除全局导航外，每一页上都有进一步的导航建议	7.0	导航建议不完整

相关信息被链接到一起	7.0	基本满足

导航链接的行为是一致的，可预测的	8.0	基本一致，可预测

c.标签系统	8.3	标签比较完善

链接的标签是精确的，其用语是彼此互斥的	9.0	精确且互斥

所用语言简单，并能被网站访客理解	9.0	较简单，易理解

导航选项的意义是明确、一致、可用的	7.0	基本满足

导航链接的目的地是可预测的	9.5	可预测

没有使用缩略语，或者使用的缩略语对目标受众而言意义明确	9.5	缩略语意义明确

每个页面的浏览器标题都与导航和页内标题相协调	9.0	协调

每个页面都有一个与周围其他标签相关的、明确的页内标题	9.0	有明确页内标题

如网站支持多种语言，其导航对各种语言版本是灵活的	5.0	只支持英语，浏览器可翻译为中文

d.视觉设计	7.0	视觉设计简单大方

导航选项是明确而可见的	6.0	基本明确

导航选项是易读的、能够快速扫视的	8.0	易读

每个页面上的选项、标签和标题有明显的视觉层次	7.0	视觉层次较明确，易分辨

导航机制令人愉悦、有吸引力	7.0	简洁大方

布局明确，有足够的留白空间	8.0	明确，有留白

有效地运用颜色来设置导航优先级和组织导航	6.0	颜色较简



3.图形测试

(1)	图形有明确用途，图片尺寸较小，图片意义有些模糊，但旁边有注释，且可链接到具体页面。

(2)	所有页面字体风格一致。

(3)	背景颜色与字体颜色和前景颜色基本搭配。

(4)	图片采用jpg格式，大小均小于30k。

(5)	文字回绕正确，如字指向左边的图片，图片即出现在左边。没有排列古怪的窗口、段落或孤行。

4.内容测试

目的：用来检验Web应用系统提供信息的正确性、准确性和相关性。

正确性：系统提供信息正确。

准确性：信息基本没有语法或拼写错误。

相关性：在当前页面可以找到与当前浏览信息相关的信息列表或入口。

5.表格测试

表格设置正确，不需滚动页面即可看见完整表格，表格布局比较合理，每一栏长宽合适，表格里的文字可以折行，不会因为某一格的内容太多而将整行内容拉长。

6.整体界面测试

整个web应用系统的页面结构设计比较简单清晰。当用户浏览web应用时感觉比较舒适，基本可以凭直觉就知道要找的信息在什么地方，整个web应用系统的设计风格一致，用户使用起来比较容易，但是整体界面都是英文的，需要有英语基础的用户才能使用，联网后，通过浏览器自动翻译，一般用户也可使用。

四．接口测试

我们校园兼职平台项目的接口测试是一个自下而上的发展过程，在这里主要测试这些系统对外部提供的接口，验证其正确性和稳定性。即：我传给你的数据，你能不能经过处理，传递我正常的结果。

接口测试工具

Junit：  java 语言事实上的标准测试框架，是接口测试技术中最基本的利器

1 服务器接口

首先需要测试的接口是浏览器与服务器的接口。即通过提交事务，然后查看服务器记录，可以在浏览器上看到的正好是服务器上发生的事务。

学生身份的事务：以游客身份来进入系统，通过注册成为用户，填写信息来申请兼职、同时浏览各个企业发布的最新招聘信息

管理员身份的事务：登录系统查看信息并且为系统增添新功能、同时可以查看到某一职务的申请状况

2 外部接口

对于所做的校园兼职平台系统，系统对于外部服务器返回的所有可能的消息，可以及时响应处理，不过网络因素的影响对于处理的效率有一定影响。

3 错误处理

最容易被测试人员忽略的地方是接口错误处理。

3.1 当管理员密码丢失系统安全性出现危机时，系统为管理员提供密保关联找回并重置密码的解决方法；

3.2 管理员发布信息后发现平台出现不良不利信息，可以及时修改并且中断用户正在进行事务

3.3当平台服务中，用户方网络中断，在订单已保存而用户没有返回网站确认的时候，需要由客户代表致电用户进行订单确认。

3.4对于操作中的顺序紊乱，比如某企业已经录用某用户，但是用户方面显示信息未同步更新，却又无法继续申请其他招聘。这一问题需要通过数据库技术加强来改善解决。                 



五．配置测试

配置测试这一部分，用于测试和验证软件，在不同的软件和硬件配置中进行运行。对于校园兼职平台系统在不同的软件和硬件配置中的运行情况作以分析。

配置测试常用技术的使用
配置测试常用的技术主要包括：多次单因素实验法、正交实验和均匀设计法。

硬件环境配置测试

1.	对主机要求

Windows系统


2.	对于浏览器及服务器的要求

校园兼职平台系统在开发过程中，是使用谷歌浏览器来开发。后面经测试，在IE、360浏览器中系统都可运行；

对于这个动态网站的运营，使用XAMPP Control Panel Application即可方便支持系统运行。


六．性能测试

1.测试网页打开速度

我对网站进行打开速度测试的时候采取的两个方法交叉进行，1是邀请10名同学在不同的电脑上打开网站，然后记录打开时间，将平均打开速度记录下来。2是利用ping工具与网页速度诊断工具在三个小时内测试六次，然后将他们的数值记录下来，最后网页连接速度小于1秒，我们认为此网页打开比较良好。

2.测试网页色彩性

整体的色彩较单一，背景为白色，清爽直观；标题字体为红色，底色为灰色，一目了然；选择按钮的颜色配置和标题的一样，醒目明白；在每个工作前有它代表的图片，更丰富了网页的直观性。整体来说这个网页的色彩搭配虽然单调，但清晰明了，不给用户繁重琐碎之感，简洁的风格会给用户留下好的印象，让用户使用的更加轻松。

3.测试网页布局性

这个网站经测试，在360、IE、遨游、Google Chrome、搜狗的浏览器上都有完整的页面，不会靠左靠右或打开不完全，影响用户的体验。网站的布局合理，重点突出，没有很多空白或无内容的地方。文字或图片链接也都有效，点击之后内容迅速打开完全，满足用户的使用。

4.网页注册可删性

用户认为繁多的注册内容浪费时间、影响个人隐私，做网页的人认为简单的注册内容会造成网站多广告、不能知晓用户信息，这就是一对矛盾，我们的网页主注册时只需提供用户名、地址、联系方式就可以供用户使用，既满足了用户使用方便的要求，又可以满足信息的获取。



七．安全测试

1.登录


我们的网页使用登录或者注册后使用的方式，因此，必须对用户名和匹配的密码进行校验，以阻止非法用户登录。对这个页面在进行管理员登陆测试，输入的密码是对大小写敏感的，输入并没有长度和条件限制，最多可以尝试多少次登录也没有限制，在登录之后可以进行查看主页面、添加一个新的目录、添加一个新的兼职工作和更改密码的操作。

2.数据加密

某些数据需要进行信息加密和过滤后才能进行数据传输，例如用户信用卡信息、用户登陆密码信息等。此时需要进行相应的其他操作，如存储到数据库、解密发送要用户电子邮箱或者客户浏览器。我们的网页的数据加密的过程时可逆的，也就是说能进行加密，同时也可以进行解密。

3.SSL测试

所有的输入点有明确的入口点，并且有清晰的信任边界，有完整的web页输入验证，而且可以从数据库中检索数据。不依赖于客户端的验证，采用服务器的同步验证。应用容易收到SQL和XSS的攻击。

4.防火墙测试

网站部署的服务器有完善的安全通信，部署拓扑结构包含了内部防火墙，也包括了远程应用服务器，但安全性的需求限制过低。 






