## 一、前言

这个项目是一个综合的项目，网络api 和客户端都是自己进行设计的，用到的技术基本上都来自郭霖大神写的第一行代码第三版，本人大三下，参加过去年的冬令营，这一年来的安卓学习由王少聪学长进行指导的，从刚接触安卓的单机设计到这个项目使用自己开发的网络 api ，受益匪浅，这个项目由于申请软著了和上架应用商店，所以这个github项目没有源代码，只有介绍文档，当然也希望加入西二这个大家庭，不过可能太老了，不收也没关系。

## 二、项目概述

### 1. 语言方面

全部采取Kotlin进行编写，运用的比较多的是Kotlin的函数型编程和Kotlin 扩展函数封装的好用的函数，web项目也是全部采用Kotlin进行编写的，由于有过一点点Java基础，语言方面基本上没有障碍

### 2.项目的结构截图

- 客户端结构截图

  ![1](https://res.dreamstudio.online/img/project/1.jpg)

  

- 服务端结构截图

  ![2](https://res.dreamstudio.online/img/project/2.jpg)

  

### 3.项目代码量截图

由于之前花了快半个月完成整个项目的重构，所以整体代码并不是很多

- 客户端

  ![3](https://res.dreamstudio.online/img/project/3.jpg)

- 服务端

  ![4](https://res.dreamstudio.online/img/project/4.jpg)

### 4.项目用到的技术

- 客户端
  - UI方面，图标基本上就是阿里巴巴的开源图标库找的，动画方面采用开源的Lottie 动画，然后各种显示由于实在没什么设计天赋，索性全部采取Google的cardView进行展示各种文字方面的内容，布局用的比较多的是constraint layout，还有些线性布局，整体的导航设计采取主页加底部导航进行展示
  - 网络请求方面用到的就是郭霖大神在第一行代码里面推荐的方式，retrofit进行请求，然后viewModel里面采用livedata进行监听，这部分并没有进行很多改动，可能项目需求在网络端解决的缘故，之前碰到不同域名的请求，采取反射机制进行解决，后面由于自己初步掌握服务端的开发，最后统一了域名，解决方案在这篇博客[retrofit解决不同域名请求的解决方案](https://dreamstudio.blog.csdn.net/article/details/119843286)
  - 本地的架构是推荐的MVVM架构
- 服务端
  - 服务端开发采用的是springboot这一开箱即用的工具进行开发
  - 处理数据库方面采取 mybatis-plus 这一工具进行数据库之间的查询
  - 请求方面，比较简单粗暴，毕竟不是专业的后端开发者，采用restful 风格的get和post请求



## 三、项目架构

主要介绍客户端架构，这边的架构参考郭霖大神公众号的一篇文章进行重构的，不过重构还没完全结束，有些技术仍然在学习中，例如rxjava，kotlin 的flow流等，参考文章[优秀架构](https://mp.weixin.qq.com/s/mFDb_7D6lZxK1rtHcg2RVg)

- 第一个是base方面的

  这部分主要针对旧项目的kotlin扩展插件的废弃进行优化，在activity和fragment中都针对viewBinding进行了一个封装，后面方便后面的项目的对于布局中控件的调用进行简化

  完成对于base Adapter的设计和封装，这部分当时封装的时候遇到一定的困难，内部类的viewHolder不可被继承，之后采用外部类解决此问题，并且对于adapter 的写法进一步封装，考虑到了可扩展性等，下面为创建adapter的例子

  ```kotlin
  class MyAdapter(private val myList: List<MyItem>): BaseAdapter(MyList,R.layout.my_item){
      override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BaseViewHolder {
          super.onCreateViewHolder(parent, viewType)
          return BaseViewHolder(view)
      }
      override fun onBindViewHolder(holder: BaseViewHolder, position: Int) {
      }
  }
  ```

- 第二个是针对常用的函数进行扩展函数的封装

  这部分的重构节省了相当多的重复代码，使得重构前后的代码量至少减少1/4

- 第三个是针对变量命名方面的重构

  这部分主要解决之前第一次写代码命名不规范的问题，同时针对bean方面进行了相关的命名优化和修改，严格区分正常bean、本地持久化和网络响应相关的bean

  

这部分项目重构主要用到函数型编程、扩展函数、泛型和对于项目基类的设计，同时汲取优秀架构的建议，使得分包更加合理

## 四、项目构成

项目的主要内容就是模拟基金的买卖，最开始只有查询的功能，即完成和郭霖大神最后实战的app差不多，后面添加了用户交互的模块，导致需要自行开发api，所以就有了以上的重构，从最开始的2个api到目前的17个api

### 1.登录系统的设计

这部分主要是集成了三种方式的登录，

- 第三方登录(QQ等)
- 验证登录(邮箱等)
- 统一账号登录

详情请看这篇博客[Android 登录系统的设计](https://dreamstudio.blog.csdn.net/article/details/123406495)

### 2.基金搜索

这方面主要是调用开放的api，然后服务端稍微处理成合适的数据返回给客户端，可以添加到持仓，这部分实现难度不大，比较头疼的是UI展示部分，做的还不够好，输入基金代码即可完成搜索，列表展示相应的数据，具体效果可以谷歌搜索VFund下载进行查看

### 3.基金持仓

这部分利用数据库进行查询，返回相应用户的持仓情况，点击item可以查看详情，并且模拟买卖基金，主要操作在服务端完成，客户端只需要发送api请求即可完成

### 4.聊天部分

这部分最初是想集成腾讯的即时IM系统进行的，后面由于时间耽搁了，目前的方案是采用数据库的读取和写入实现，在用户不多的情况下，这个开发很高效，只有简单的发送聊天文字和获取聊天记录两种功能

### 5.个人主页

这部分主要用于设置昵称邮箱等，功能不是很多，除此之外有相关的隐私协议的地址和第三方sdk共享信息的网页(这部分主要是为上架app商店准备的)，还有一个退出登录的按钮

## 五、个人博客主页

大概在CSDN博客上坚持发文4个月左右了，知识转化成文章进行分享也是一件挺快乐的事情

这是我目前在CSDN的[博客主页](https://dreamstudio.blog.csdn.net/)

个人网站的[博客主页](https://www.dreamstudio.online/)

## 六、上架谷歌商店

谷歌开发者在去年的12月份的时候申请成功，这个还是比较坎坷的，一个国际visa卡就卡了好久，最终注册成为谷歌开发者，前几天app完善之后就申请上架了，审核了三天左右，最终完成上架，直接搜索VFund 就可以搜索到，或者点击以下链接进行下载,备用下载链接是自己服务器上的apk资源

[谷歌商店的链接](https://play.google.com/store/apps/details?id=online.dreamstudio.fundtest)

[备用下载链接](https://res.dreamstudio.online/apk/VFund_release.apk)

## 七、总结

这个项目的最终开发完成就在前两周，对于个人而言这是第一次开发网络交互相关的app，很多方面是初学者，这个app准备持续维护（可能写在简历里），同时还有很多未知的领域等待探索和学习，最后引用第一行代码书籍背面以做结尾，“我挥舞着键盘和本子，发誓要把世界写的明明白白”。
