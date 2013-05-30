 基于Web的校园兼职平台
                            	------应用建模
用例图：
Actor：学生、用户、管理员、企业
Usecase：浏览兼职信息、注册、登陆、查看个人信息、修改个人信息、论坛交流、申请兼职、更改兼职、撤销兼职、兼职评价、恶意信息删除、恶意用户删除、用户查询、查看企业信息、修改企业信息、兼职信息发布、兼职信息撤销、查看求职者信息
学生、管理员都属于泛化的用户类
兼职管理包含：申请兼职、更改兼职、撤销兼职、兼职评价
兼职信息管理包含：兼职信息发布、兼职信息撤销、查看求职者信息
![yonglitu][1]
[1]:http://fmn.rrimg.com/fmn060/20130530/2305/original_A8rs_5da30000229d1190.jpg 
![yonglitu][2]
[2]:http://fmn.xnpic.com/fmn057/20130530/2305/original_N4JN_0ee0000022fd118c.jpg
 
类图：
共6个类：用户user、学生student、管理员administrator、企业company、职位position、评价evaluate。
![leitu][3]
[3]:http://fmn.rrfmn.com/fmn059/20130530/2305/original_Xzha_720500001f01125d.jpg

学生注册、兼职评价为例：
学生注册：
（1）学生进入该系统的注册界面；
（2）系统显示空白注册表；
（3）客户填写注册信息；
（4）系统核对输入；
（5）系统添加注册表；
（6）显示注册信息；
（7）注册成功后返回信息给学生。

兼职评价：
用户登录兼职平台；
用户查看自己的申请兼职列表；
显示用户兼职列表；
进入某个公司的公司主页；
进行兼职评价；
该公司运行评价，评价成功，反之，评价失败；
返回个人主页。




3.1活动图：
学生注册：
![huodongtu][4]
[4]:http://fmn.rrfmn.com/fmn058/20130530/2305/original_HXqX_71b500001f30125d.jpg
兼职评价：
![huodongtu][5]
[5]:http://fmn.xnpic.com/fmn056/20130530/2305/original_Mx9W_727500001f2f125d.jpg
3.2序列图：
学生注册：
![xulietu][6]
[6]:http://fmn.rrimg.com/fmn064/20130530/2305/original_A8kH_44030000231b1191.jpg
兼职评价：
![xulietu][7]
[7]:http://fmn.rrimg.com/fmn062/20130530/2305/original_h7gw_4454000022f51191.jpg
3.3协作图：
学生注册：
![xiezuotu][8]
[8]:http://fmn.xnpic.com/fmn057/20130530/2305/original_0mDb_44940000230c1191.jpg

兼职评价：
![xiezuotu][9]
[9]:http://fmn.rrfmn.com/fmn059/20130530/2305/original_sjAl_5dd3000022f71190.jpg 
