# mvp_hybride_framwork


## 文档有些写的不详细，大家最主要是看代码，看思想。这是我项目架构的初型，    不是很完善，完善的架构大家根据自己的想法和app去扩展的（文档打不开的发邮件我，我给原档）

## MVP说明
* 如果不是很了解mvp的可以先去看看mvp相关的文章。
* 一个v对应一个p，多个v对应多个p，但是这个多个p可以都是同一个p的基类继承而来。一个v也可以对应多个p，业务比较复杂的时候。 这方面的东西，还没写到架构中，但是大家可以自己去改，很简单，后期我会给出这方面的代码。
* 不要什么页面都用mvp来做。逻辑简单的变动不大的就不要用mvp了。例如：登录，设置，个人信息修改。。等等，这页面本来就很简单，就算所有业务逻辑代码在activity也没什么。
## 架构文档说明连接地址：
* App Android架构设计  https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145  文档在coding上面，需要注册一个账号
* Android Hybird的App架构设计  https://shimo.im/doc/DUbkBsq2KMYvA7sE   文档在 石墨文档 上面，需要注册一个账号



## 1.App的整个包的部署
* 整个app采用的是最新的Android api和现在最流行的第三方框架，以及谷歌提倡的MVP设计模式。
* 整个app分层是按照功能职责来划分的，ui层是安装功能模块来划分的，页面简单的直接用Activity来承载页面，复杂的页面用Activity+Fragment来做，另外采用的是MVP设计模式，将视图view和业务逻辑presernter隔离开来。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145
## 2.Api层（网络请求层）

 * 整个app网络请求是rxjava+retrofit+okttp 来完成网络请求的。这一层主要是对网络请求框架的一个封装方便我们使用。主要处理就是给所有请求添加共同的消息头，打印所有请求的log日志，请求结果的过滤和请求错误的通用处理。。。等等。

 * Api层的类图:

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

  *    Api主要是将 rxjava+retrofit+okttp 整合到一起，其中包含了网络的拦截器以及请求的通用处理。对网络接口返回的Response进行分割操作 对于jasn 解析错误以及返回的 响应实体为空的处理,对每个请求的调用线程和回调线程的设置。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


* Apiservi主要是负责网络请求接口的处理
* Apiwripper 是Api的辅助类这里主要是对Apiservi请求的统一处理。
* MyCallBack 是自定义的网络请求的接口。将网络请求结果统一处理之后，     在回调MycallBack的实现类 
图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


## 3.Base层
* 这一层包含了所有app的基类，类图：
图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


* BaseAcitity为所有Activity的基类，此类为抽象类，采用模板方法模式， 在init（）方法中定义一个骨架，而将一些步骤延迟到子类中。使得子类acitivity可以在不改变结构的情况下，实现定义抽象中的某些步骤，这样所有的Activity都是这样的一种模式，看着简单易懂。另外包含了一些Activity常用的方法。
图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

* BaseCommonPresenter 为presenter的基类，这里理做了一些presenter
初始化的事情，以及一些公用的方法，同时将persenter与view关联起来

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


* BaseFragment 和 BaseAcitity 一样采用了采用模板方法模式，另外也定义了一些公用方法（因为Fragment是依赖Activity而存在的所以这里面的公用方法实际是调用Acitity里面的公用方法），因为页面显示有两种情况一个是直接Activity来显示，另外一个是用Activity+Fragment来做，这样Fragment就从当了view层，于是这用泛型将fragment与presenter关联起来，同样Activity就从当了view层时BaseAcitity也是这样。

在LoginFragmentActivity 中，代码如下：

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

在LoginActivity 中，代码如下：

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


* BasePresent 和 BaseView 为view接口和preseter接口的父类。

* HaiBaoApplication 自定义的Application 这对第三方的库的一些初始化，还有我们自定义的一些东西的初始化，例如： 日志处理类，全局图片加载默认图片设置。。。等等，对于贯穿真个app的变量和对象也保存在这里方便，全局拿到，比如：用户信息对象（User）。。。等等。

* 对于app的列表展示全部采用谷歌最新的api，用RecylerView来做MyRecylerViewHolder和BaseRecylerAdapter 这两个基类处理了关于列表适配器的封装，BaseRecylerAdapter 在onCreateViewHolder（）中返回的是MyRecylerViewHolder这个对象，而不是针对不同的RecylerView返回不同的ViewHolder，MyRecylerViewHolder用SparseArray通过view的id来充当key来保存view对象的。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

* 由于RecylerView展示的都是列表数据，于是在BaseRecylerAdapter <T> 用泛型将每一个adaper（适配器）与与之对应的数据集合关联起来，构建一个基类adapter，将每个适配器处理的事情都在这里共同处理了。同时封装一些公用的方法，例如：更新列表，移除数据。。。等等对于RecylerView的item的点击事件，在BaseRecylerAdapter 基中定义一个借口将事件暴露出去。需要注意的是 RecylerView在设置点击事件的时候，要在RecylerView.setadapter 之前的

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

## 4.common层

* 这层里面放着一些作用于整个App的一些工具。建议将项目的一些共同的处理以及和业务逻辑不相关的代码封装成一个工具类。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

* ActivityPagerManager 为Activity的堆栈管理类，另外这里包含了对Activity资源的释放，采用的时递归的方法，从Activity的根view开始递归释放资源，包含了退出App的处理方法。
图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

* CrashHander 是app未捕捉异常的处理类，在调试阶段把错误日志存在手机的文件夹里面，方便测试出问题了，开发人员可以从手机中找到错误日志，如果发布的话，将错误日志联网发送到第三方错误统计。
* AnimationUtils 处理Acitiity和Fragment里面view的动画，能公用的公用，不能共用的单独在写个方法。把页面上的动画效果都放到这里来处理
* MobclickAgentUtils 处理统计友盟事件的工具类也是统计的管理类，这样有利于观察整个app统计了那些时间，同时也方便查到一个统计时间在哪里调用了。将页面所有的统计都用这个类来作为统一 接口来处理。

## 5.Hybrid 和 hybridModer层



* 这里为app与html5交互的 hybrid架构，详细说明见： [Android Hybird的App架构设计][18] 文档。有单独的说明。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

## 6.modle层

* modle层主要包含两种实体类型，一种是eventbus传递事件的事件实体，第二种是api发送请求后的响应实体

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


## 7.received层
* app内的广播层，包含自定义广播和第三方的广播等等，例如：极光推送的广播接受器。

## 8.RequestParamete 层
* RequestParamete 发送请求后的请求实体层。

## 9.service 层
图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145
* Service 层包含app的自定义service。对于播放音乐采用service来处理（Musicservice），离线下载服务等等

## 10.ui层

* ui层是安装功能模块来划分的，分为课堂模块，书架模块，发现模块，我的模块。对于页面的交互采用MVP的模式，例如：如下的登录页面。用了LoginActivity+LoginFragment来做，LoginActivity里面承载着LoginFragment，而LoginFragment承载着我们的主要页面view。loginPreseter处理登录页面的相关逻辑。

* Model:    负责数据的检索,持久化等操作
* View:     负责UI的绘制和用户的交互
* Presenter:  作为Model和View的中间协调部分,负责两者之间的业务逻辑处理



* View层
把View层针对控件操作抽象出来一些列的接口
在Activity或者Fragment里面实现该接口的控件操作,并且初始化Presenter,这是可以看到Activity里面没有逻辑处理,只是对UI的控件进行数据或者行为的操作,所有的动作都是有Presenter的接口来实现，这样在项目里面会极大得精简Activity的体积.


* Presenter层
在Presenter层里面,Presenter掌握着View和Model的所有接口,Presenter就可以根据不同的业务逻辑通过MV两层的接口来实现特定的功能,让M和V独立出来.

* 为了减少多的类处理这里没有对M层单独处理，直接交给Presenter层，present直接处理了M层数据模型的处理（数据的获取，例如：发送请求获得实体类），这样减少了M层的一层接口。


图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145

* 对于xxxContract是对 Preseter的接口以及view 的管理，在这里可以看到整个页面需要调用各层之间的接口。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145


## 11.utils层
* 该层是工具类层。例如：文件处理工具，图片处理。。。等等


## 12.view层
* 该层是自定义view层。同时也包含第三方的需要自己更改的view



## 13.widget层
* 该层是分两层，一层为dialog层为app对话框层，另外一层是popwindow为app popwindow层，每一层都一个管理者采用的门面模式和单利模式。

图片去看：https://coding.net/s/f9565dd3-1508-4471-bf01-c711828eb145
## 14.其他

* App运行在Andriod 4.1版本以上。



