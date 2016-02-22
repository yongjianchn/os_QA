# 操作系统课需要的各种软件的使用方便

参考文献：[Building and Running an edX Course](http://edx-partner-course-staff.readthedocs.org/en/latest/creating_content/index.html)

## 学堂在线平台的课程维护

### 学堂在线平台上新加视频课程的方法
先上传字幕，再上传视频文件，经过转码后，以上可以看到有字幕的视频了。下面具体操作步骤。

#### 助教上传字幕
 1. 登录studio.xuetangx.com网站，进入操作系统课
 1. 点击“内容”菜单，选择“文件上传及管理”，打开"文件上传及管理"页面
 1. 点击右上角的“上传新文件”按钮，选择要上传的字幕srt文件（可多选）。
  * 必须保证srt文件是UTF-8编码的，如果不是，可使用notepad++、geany等工具转换为UTF-8编码。
 1. 上传后应记住字幕文件的URL，如/c4x/TsinghuaX/30240243X/asset/lab0-1.srt等。

#### 助教上传视频
 1. 点击“内容”菜单，选择“大纲”，进入"课程大纲"页面
 1. 展开需要添加视频的章节，点击“添加新单元”
 1. 将“显示名称”文本框中的内容改为“视频”（或其它便于学生理解的文字）
 1. 在添加新组件提示的下方选择“视频”按钮，并在出现的菜单中选择“默认”，几秒钟后页面加载出视频播放器；
 1. 在播放器的右上方点击“编辑”按钮，在弹出的对话框中右上角点击“设置”按钮，这时会出现四个文本框，分别为“显示名称”、“视频地址”、“视频字幕（中文）”、“视频字幕（英文）”
 1. 在“视频地址”右侧点击上传视频按钮，浏览器会打开一个新的“上传视频”页面；
 1. 在“上传视频”页面中点击“浏览”按钮，选择要上传的视频文件
 1. 然后点击“上传”按钮，等待页面中部的进度条从灰色的0%慢慢变成绿色的100%，这里上传速度比较慢，100M的视频大约需要15～20分钟；
 1. 当上传进度到100%时，关闭“上传视频”页面，回到原页面，可以发现“视频地址”文本框已经自动填写了视频的URL；
 1. 在“视频字幕（中文）”填写上一步中记下的字幕的URL，点击“保存”按钮。此时可以看到页面中黑色的视频播放器（或显示“正在转码”）。注意，这一步中的“保存”操作可以在上传进展没有达到100%前进行，并还会影响上传过程的继续进行。
 1. 在右上方“单元设置”栏的“是否公开”下拉框中选择“公开”。
 2. 在学生（或工作人员）界面中等待和检查网站转码是否正常完成（约1～2分钟）。出现过转码几个小时也不结束的情况，这时需要重新上传视频文件。
 3. 在学生（或工作人员）界面中检查视频与字幕是否正常播放。至此，整个视频添加完毕。

#### 由学堂在线技术人员上传视频和字幕文件的方法
 1. 制作方完成视频文件、字幕文件和文件的MD5校验和生成，然后通知任课教师；
 1. 任课教师确认视频文件和字幕合格后，共享相应的360云盘目录；
  * 如： http://yunpan.cn/cmNj7HmZYvHmf （提取码：59c5）
 1. 学堂在线技术人员从360云盘下载所有文件到学堂在线后台存储，确认MD5是正确的，并把所有文件的CCID信息回复给任课教师；
  * 如： {{{C4ABEF86BB881D099C33DC5901307461         操作系统-第5周-lecture09(7-1)V2.0.mp4}}}
 1. 任课教师安排助教更新课程页面的视频和字幕显示，并确认播放无异常。

#### 视频上传问题：我在页面中看到的是，100％完成上传。用户如何能知道上传不成功？

这个目前还没有好的办法。视频转码一般需要30min-1个小时，如果同时上传的视频很多，可能会再慢一些，要是等了4、5个小时还没有转码完成，估计就是视频没有传上去了。要后台可以查看视频的source id，然后到CC的视频列表里查询id是否有对应视频。目前在进行转码前能判断还不能判断是否成功。

### 上传和设置练习题的方法
 1. 上传练习题
 2. 设置练习题分数
  * 每个小题一分；有多少个题，这次练习就是多少分；课堂思考题也一样处理。最后学期结束时，所有练习的分数加到一起，加权成10分。
 3. 设置练习题截止时间
  * 建议前几周作业的截止日期宽限一些，给后加入课程的人机会。所有练习的完成时间是公开后一周，实验报告是公开后10天。前4周的截止时间不早于开课后30天。
 
### 已公开练习题的修改方法
 1. 进入学堂在线系统后台的“课程大纲”页面
 2. 找到需要修改的“练习”单元，点击“练习题”进入单元“单元”页面
 3. 点击页面右侧的“编辑草稿”，进入“编辑草稿”页面
 4. 点击需要修改的单元题的“编辑”后，进入“编辑”页面
 5. 进行修改，完成后点击“保存”后，回到“编辑草稿”页面
 6. 点击页面右侧的“使用本草稿进行替换”就完成修改了### 已公开练习题的修改方法

### piazza讨论区以及互评题的添加(相关文档已上传至wiki附件)
 1. [wiki上传附件地址](http://os.cs.tsinghua.edu.cn/oscourse/OsMooc2014#head-03e8ce3bdd8cdcc4a45e0366dad6a0ee43f3ade0)
  * piazza讨论区的添加方法
  * 互评区相关操作帮助
 
## 学堂在线平台的使用

## 清华网络学堂（新版）

### 视频上传
 1. 对于每一讲首先添加标题：点击红色的“维护课程结构”，鼠标悬停至“MOOC视频”，点击右边绿色加号，填写本讲标题等信息后确定，并关闭“维护课程结构”窗口。
 2. **此时界面会变空白，刷新之后出现新添加的一讲标题。**
 3. 对于每一个视频，点击所属第几讲下面的红色“新增文件”，资源类型“教学录像”，标题对应填写，最下面选择上传文件。
 4. **等待上传文件完成后，**点击确定关闭窗口。
 5. （“课程管理”->“学生界面浏览”可以确认效果。上传完成后学生不会立即看到视频，网络学堂需要几分钟进行转码。）

## 不需要VPN访问IBM超能云上“操作系统在线练习和实验环境”的方法

背景：由于此前用于访问IBM超能云（supervessel）的VPN很不稳定，在IBM中国研究院的大力支持下，我们完成了这个可较依赖VPN访问IBM超能云上操作系统课实验docker的方法，以方便学习操作系统课的同学们在IBM超能云上“操作系统在线练习和实验环境”。在这个环境中，可在线完成操作系统课的练习、用docker做ucore实验、浏览和编辑ucore代码，以及搜索piazza讨论区的历史记录。

### 1. 注册学堂在线，并选修操作系统课；
 1. 学堂在线注册链接 http://www.xuetangx.com/
 1. 注册成功的标志是，页面右上角第二行的图标是你自己的图标。
 1. 如果你是通过QQ等第三方登录学堂在线，请先绑定邮箱，并设置自己的学堂在线用户名(否则在内部平台的登录名将为随机字符串)。建议用户名请勿包含特殊字符。
 1. 查找“操作系统(2016春)”课程，并选修该课程。
 1. 选修成功的标志是，访问链接 http://www.xuetangx.com/courses/course-v1:TsinghuaX+30240243X+2016_T1/courseware/14def9edc58e4936abd418333f899836/ 可以看到第一行的“TsinghuaX: 30240243X 操作系统 (2016春)”。

### 2. 注册或关联操作系统课的piazza讨论区
 1. 访问下面链接 http://www.xuetangx.com/courses/course-v1:TsinghuaX+30240243X+2016_T1/courseware/14def9edc58e4936abd418333f899836/a99db084082345c1875ca1d963c42f38/ 并点击”在新窗口中查看资源”即可访问此课程在piazza中的交流平台。
 2. 第一次使用的用户需要填写邮箱（要求使用学堂在线注册的邮箱，不要求一定是清华大学的邮箱）及密码（已注册过Piazza的用户不用输入密码）进行注册。建议设置用户名为实名加学号，例如“张三2010011234”。在此过程中需要输入操作系统课号“30240243x”和年份编号“2015”。
 3. 注册或关联成功的标志是，访问链接 https://piazza.com/tsinghua.edu.cn/spring2015/30240243x/home 可以看到第二行的“Tsinghua University - Spring 2015”和第三行的“30240243X: Principles of Operating Systems”，并且右上角是你自己的用户名。

### 3. 访问学堂在线网站，完成IBM超能云上访问“操作系统在线练习和实验环境”需要的用户注册。

 1. 在学堂在线注册学习操作系统课后，就可以访问链接 http://www.xuetangx.com/courses/course-v1:TsinghuaX+30240243X+2016_T1/courseware/14def9edc58e4936abd418333f899836/37afa0e088164343aab65b4d077fb372/2 获取进行IBM超能云上的用户注册工作。
 2. “实验账号初始化”操作会随网络系统负载情况，等待几十秒到几分钟不等。操作完成后会看到类似如下显示的信息。“supernova account”是在IBM超能云上的注册邮箱，在获取VPN账号时会用到，这个邮箱必须是你在学堂在线上的注册邮箱；在IBM超能云上的注册过程中包括一个邮件激活的操作，可能出现没有收到激活邮件等问题，可以不理它，这不会影响openedx和docker的访问。在“shibboleth account”是访问IBM超能云上的openedx、gitlab和docker的用户名；“password”是访问IBM超能云上所有服务所需要的密码。请妥善保存这三个信息。

IBM supernova account:

xxxxxx@qq.com

shibboleth account(for edx and gitlab):

ea23cbdac0xxxxxxxxxx

password:

18IFxxxx


### 4. 初始化IBM超能云上“操作系统在线练习和实验环境”设置，并登录并访问IBM超能云上的openedx系统

 1. 访问IBM超能云上的“内部实验平台测试课程”链接 http://crl.ptopenlab.com:8811/courses/edX/DemoX/Demo_Course/about ，并点击“登录”后会跳转到如下shibboleth登录界面 https://crl.ptopenlab.com:8800/idp/Authn/UserPassword 。这一步有些鬼异，目的是绕到shibboleth的登录界面，对“操作系统在线练习和实验环境”中的openedx进行初始化。输入你在前面步骤中得到的“shibboleth account”和“password”。
 2. 登录openedx系统并初始化成功的标志是，登录后的页面右上角有你的“shibboleth account”信息。这时你能链接 http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/ ，并看到“Tsinghua: CS101 操作系统内核实验”中的操作系统课实验和练习页面。
 3. 登录之后需要补充用户名(请与给出的shibboleth账号保持一致)。

### 5. 配置访问IBM超能云内部网络的VPN，并初始化gitlab上的ucore基准代码
 1. 访问链接 http://www.xuetangx.com/courses/course-v1:TsinghuaX+30240243X+2016_T1/courseware/14def9edc58e4936abd418333f899836/37afa0e088164343aab65b4d077fb372/1 ，获取配置VPN所需要需要的账号和密码信息。
 1. 参见帮助 https://services.ptopenlab.com/mediawiki/index.php/VPN%E7%9A%84%E4%BD%BF%E7%94%A8 设置访问IBM超能云内部网络的VPN。
 1. VPN连接成功的标志是，命令“ping 172.16.13.236”确认能连gitlab服务器。
 1. 访问gitlab服务器的登录页面 http://172.16.13.236/users/sign_in ，点击“Shibboleth”按钮，页面会跳转到shibboleth的登录页面，输入你在前面步骤中得到的“shibboleth account”和“password”。
 1. gitlab初始化成功的标志是，浏览器页面自动到你的gitlab主页，页面左下角有你的用户名。

### 6.在IBM超能云上的openedx系统中创建自己的操作系统课实验docker

访问下面链接，并阅读关于docker和gitlab的使用帮助。

http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/7be9a21ca62e4f5d8325d27b66a0c9bf/1

然后访问下面链接，点击“Create Docker”创建自己的操作系统课实验docker。

http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/7be9a21ca62e4f5d8325d27b66a0c9bf/2

创建成功后，你就会在“DockersList”列表中看到自己的docker信息。

由于docker创建和初始化的过程有些慢或多个用户同时进行docker创建，可能出现“看不到创建的docker”或“较长时间没有反应”的情况。这个过程通常会在3-4分钟的时间。4分钟以上可能会失去响应，这时可手动刷新浏览器页面或再次点击“Create Docker”。

刷新docker状态，不会导致重复创建。重复点击创建只会刷新页面，而且只是读状态，响应应该比刷新页面快。

### 7.访问自己的操作系统课实验docker

点击docker创建页面上的“Webshell”，跳转到类似如下链接的docker的web shell页面。

https://crl.ptopenlab.com:8800/webshell/vzuFSEpyxxxxxxxx/

输入自己的“shibboleth account”和“password”，即可进入字符界面的实验环境。

注意：每个docker只有创建者和管理员可以访问。

至此，所有注册和初始化工作全部完成。

### 8. 确认所有注册和初始化工作完成

成功标志是，可以直接在浏览器中访问下面链接来访问IBM超能云上“操作系统在线练习和实验环境”中的各种功能。
 1. 创建和访问docker： http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/7be9a21ca62e4f5d8325d27b66a0c9bf/2
 2. ucore实验代码浏览和编辑： http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/400f7c812c254b799e66194d24b297ae/
 3. 搜索piazza上的历史记录： http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/896b56b6156047869eecd8b519852558/
 4. 完成在线练习题： http://crl.ptopenlab.com:8811/courses/Tsinghua/CS101/2015_T1/courseware/65a2e6de0e7f4ec8a261df82683a2fc3/73a7712ec5084f7598c97c129ba9ff52/1
