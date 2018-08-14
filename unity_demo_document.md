# ASCSDK（Unity）文档(ASCSDK Unity Document)

------
###简介(Introduction)
欢迎使用ASCSSDK，ASCSSDK是一款广告集成SDk，方便您快速的集成广告
Welcome to ASCSSDK. ASCSSDK is an advertising integration SDk that allows you to quickly integrate advertising
###  目录               (directory)
> * ASCSDK接入       (ASCSDK access)
> * ASCSDK工作流程   (ASCSDK The working process)
> * 广告示例         (AD sample)
> * 支付及其他示例   (Payment and other examples)
> * 统计功能         (statistics)
> * 打包             (Build)
### ASCSDK接入(ASCSDK Access)
   首先下载最新的Unity SDK。
   First download the latest Unity SDK
   
   下载完毕后，新建一个Unity项目或者直接双击导入ASCSDK资源。
   After downloading, create a new Unity project or simply double-click to import the ASCSDK resources
> * 开发环境需求(Development environment requirements)：Unity5.5+。Unity  java

###  广告示例(AD sample)

> * 无需多次加载的广告(No need to load multiple ads)
##### 横幅（Banner）

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
    }
    
    //展示或者隐藏横幅广告（广告是自动加载，在需要隐藏的时候调用HideBanner，需要展示的时候调用ShowBanner）
    //Display or Hide banner
    void OnBannerClick(bool isOn)
    {
       if (isOn)
           ASCSDKInterface.Instance.ShowBanner();  //展示广告（show ads）
       else
           ASCSDKInterface.Instance.HideBanner();//隐藏广告(Hide ads)
    }
    

> * 需要多次加载的广告 (Ads that require multiple loads: screenshots)
##### 插屏（Inters ）

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
    }
    
    //展示广告
    //Display Inters
    void OnIntersClick(string level)
    {
        //该广告已经可以播放
        //The advertisement is already available
        if (ASCSDKInterface.Instance.GetIntersFlag())
        {
            //传入当前关卡若无则传入"0"
            //Pass in the current level and if not, pass in "0"
            ASCSDKInterface.Instance.ShowInters(level);
        }
    }
    


> * 需要多次加载且有回调的广告 (Ads that require multiple loads and callbacks)
##### 视频（Video ）

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
        //回调事件
         ASCSDKInterface.Instance.OnVideoCallBack = delegate(CallBackStatus status)
         {
              if (status==CallBackStatus.SUCCEED)
              {
                  //视频播放成功(Succeed)
              }else
              {
                  //视频播放失败(Defeated)
              }
         }；
    }
    
    //展示广告
    //Display Video
    void OnVideoClick()
    {
        if (ASCSDKInterface.Instance.GetVideoFlag())
        {
            ASCSDKInterface.Instance.ShowVideo();
        } 
    }

 
> * PS:根据游戏项目的需求手动设置横竖屏：Assect->Plugins->Android->AndroidManifest.xml文件里
PS: set the horizontal screen manually according to the requirements of the game project: Assect-> plugins-> android-> androidmanifest.xml file

     android:screenOrientation="portrait"

### 支付及其他示例(Payment and other examples)
> * 登入、登出、上传数据(Login, logout, upload data)
##### 登入（Login）、登出（LogOut）、上传数据（upload data）

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
        //登入回调事件
         ASCSDKInterface.Instance.OnLoginSuc = delegate (ASCLoginResult result)
        {
            OnLoginSuc(result);
        };
        //登出回调 
        ASCSDKInterface.Instance.OnLogout = delegate ()
        {
            OnLogout();
        };
        //上传游戏数据（补单用，必须）
        //Upload game data
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
    }
    
    //上传数据
    //upload data
    void SubmitGameDataAfferent(int type)
    {
        ASCExtraGameData AfferentData = new ASCExtraGameData();
        //调用时机，type对应的值详情查看ASCExtraGameData的注释
        AfferentData.dataType = type;
        //角色ID
        AfferentData.roleID = "role01";
        //角色名称
        AfferentData.roleName = "role01";
        //角色等级
        AfferentData.roleLevel = "10";
        //服务器ID
        AfferentData.serverID = 1;
        //服务器名称
        AfferentData.serverName = "asc";
        //当前角色生成拥有的虚拟币数量
        AfferentData.moneyNum = 1;
        //当前角色生成拥有的虚拟币数量
        AfferentData.roleCreateTime = DateTime.Now.Second.ToString();
        //角色创建时间，从1970年到现在的时间，单位秒
        AfferentData.roleLevelUpTime = DateTime.Now.Second.ToString();
        //上传游戏数据
        ASCSDKInterface.Instance.SubmitGameData(AfferentData);
    }
    
    void Update()
    {
        //退出（*按下弹起时调用）
        //exit（*Call when pressed）
        if (Input.GetKeyUp(KeyCode.Escape))
        {
            //调用SDK的退出确认框,返回false，说明SDK不支持退出框，游戏需要使用自己的退出框
            ASCSDKInterface.Instance.SDKExit();
            //退出前上传数据（必须）
            SubmitGameDataAfferent(ASCExtraGameData.TYPE_EXIT_GAME);
        }
    }

    //登陆回调
    void OnLoginSuc(ASCLoginResult result)
    {
        if (!result.isSuc)
        {
            //"登录失败(login failure)";//login failure
            return;
        }
        if (result.isSwitchAccount)
        {
            //"切换帐号成功(Switch account successful):" + result.token;//Switch account successful
        }
        else
        {
            //"登录成功(login successfully):" + result.token;//login successfully
        }
    }
    
    //登出回调
    //Logout callback
    void OnLogout()
    {
         //已登出(Have to log out)
    }

> * 支付（pay）
##### 支付（pay）

    int [] DefaultPrice=new [3]{6,10,20};//示例：默认所有道具价格（Default all prop prices）
    int [] DefaultQuantity=new [3]{60,100,200};//示例：默认道具对应的数量（Default all props price corresponding to the number）
    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
        //支付回调
        ASCSDKInterface.Instance.OnPayCallBack = delegate (CallBackStatus status,int productId)
        {
            OnLPaySuc(status，productId);
        };
        //上传游戏数据（补单用，必须）
        //Upload game data
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
        //示例：获取后台价格及数据,如果没有,则使用默认价格.
        //Get background price and data,If not, use the default price
        if (ASCSDKInterface.Instance.PointConfig != null && ASCSDKInterface.Instance.PointConfig.Count > 0)
		{
			for (int i = 0; i < 3; i++)
			{
				DefaultPrice[i] = ASCSDKInterface.Instance.PointConfig[i].Price;
				DefaultQuantity[i] = ASCSDKInterface.Instance.PointConfig[i].Quantity;
			}
		}
    }
    
    //支付回调(pay callback)
    void OnLPaySuc(CallBackStatus status,int productId)
    {
        if(status==CallBackStatus.SUCCEED)
        {
            //支付成功(pay success)
        }
        else
        {
            //支付失败(Payment Failed)
        }
    }
    
    //支付
    //pay
    void OnPayClick(int price,int productId)
    {
        ASCPayParams data = new ASCPayParams();
        //游戏中商品ID（必须(Need)）
        //Product ID  in the game (Need)
        data.productId = productId.ToString();
        //游戏中商品名称，比如元宝，钻石...（必须(Need)）
        //The names of products in the game, such as yuan bao, diamonds...(Need)
        data.productName = "元宝";
        //游戏中商品描述（必须(Need)）
        //Product description in the game (Need)
        data.productDesc = "购买100元宝，赠送20元宝";
        //价格，单位为元
        //Price, in yuan
        data.price = price;
        //购买数量,定值为1（必须(Need)）
        // purchase quantity, set as 1 (Need))
        data.buyNum = 1;
        //当前玩家身上剩余的虚拟币数量
        //The amount of virtual currency left over from the current player
        data.coinNum = 300;
        //当前角色所在的服务器ID
        //The server ID where the current role is located
        data.serverId = "10";
        //当前角色所在的服务器名称
        //The name of the server where the current role is located
        data.serverName = "地狱之恋";
        //当前角色ID
        //Current role ID
        data.roleId = "asc_24532452";
        //当前角色名称
        //Current role name
        data.roleName = "麻利麻利吼";
        //当前角色等级
        //Current role level
        data.roleLevel = 15;
        //游戏服的支付回调地址，用于接收支付回调通知
        //The payment callback address of the game server is used to receive payment callback notification
        data.payNotifyUrl = "";
        //当前角色的vip等级
        //The current role's VIP rating
        data.vip = "v15";
        //扩展数据， 支付成功回调通知游戏服务器的时候，会原封不动返回这个值
        //Extending the data, when a successful callback is paid to notify the game server, the value is returned intact
        data.extension="";
        ASCSDKInterface.Instance.Pay(data);
    }
> * 分享、评价、礼包兑换 (Share and evaluate and Gift bag for)
##### 分享（Share）、评价（Evaluate）、礼包兑换(Gift bag for)

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface.Instance.Init();
        //上传游戏数据（补单用，必须）
        //Upload game data
        SubmitGameDataAfferent(ASCExtraGameData.TYPE_ENTER_GAME);
        //礼包回调
        //Gift bag for callBack
        ASCSDKInterface.Instance.getGiftInfo = OnGetGiftInfo;
    }
    
    //分享
    //Share
    void Share()
    {
        ShareParams SP = new ShareParams();
        //分享的标题，最大30个字符
        //Share the title, up to 30 characters
        SP.title = "分享标题";
        //标题链接
        //The title link
        SP.titleUrl = "http://www.uustory.com/";
        //分享此内容显示的出处名称
        //Share the source name that this content shows
        SP.sourceName = "ASCSDK出处名称";
        //出处链接
        //The source link
        SP.sourceUrl = "http://www.uustory.com/";
        //内容，最大130个字符
        //Content, up to 130 characters
        SP.content = "分享内容";
        //链接，微信分享的时候会用到
        //Link, WeChat will be used when sharing
        SP.url = "http://www.uustory.com/";
        //图片地址
        //Picture address
        SP.imgUrl = "http://www.uustory.com/";
        //是否全屏还是对话框
        //Full screen or dialog box
        SP.dialogMode = true;
        //Notification的图标
        //The Notification icon
        SP.notifyIcon = 1;
        //notification的文字
        //Notification of the text
        SP.notifyIconText = "文字";
        //内容的评论，人人网分享必须参数，不能为空
        //Content comments, which must be Shared by renren, cannot be empty
        SP.comment = "评论，人人网分享必须参数，不能为空";
        ASCSDKInterface.Instance.Share(SP);
    }
    
    //评价
    //evaluate
    void OnEvaluationClick()
    {
        ASCSDKInterface.Instance.Evaluation();
    }
    
    //兑换礼包(若cdkey为0，则调用sdk的输入界面)
    //Cash gift bag，If cdkey is 0, the input interface of the SDK is called
    void OnGetGiftInfoClick(string cdkey)
    {
        //cdkey is Exchange codes for gift bags,It's usually handed out in the background。
        ASCSDKInterface.Instance.ExchangeGift(cdkey);
    }
    
    //礼包兑换回调实现
    //Gift bag for
    void OnGetGiftInfo(int propNumber, string msg, GiftBagType type)
    {
        //propNumber为下发的道具数量，msg为状态信息（若为0，则道具下发失败）,type 是道具类型（金币，钻石，其他）
        //PropNumber is the number of props sent down, MSG is the status information (if it is 0, the prop sent down fails), and type is the prop type (gold coin, diamond, others).
    }

### 注意事项(Matters needing attention)
如果您使用了第三方插件，请确认您的项目文件夹：Assect->Plugins->Android->AndroidManifest.xml文件里
application 下包含 android:name="com.asc.sdk.ASCApplication">

If you are using a third-party plug-in, make sure your project folder is in the Assect-> plugins-> androidb2 androidmanifest.xml file
Under the application contains the android: name = "com. Asc. SDK. ASCApplication" >

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" 
        android:name="com.asc.sdk.ASCApplication">


###  统计功能(Statistics)

     //开始关卡
    //Starting level
    ASCSDKInterface.Instance.StartLevel(string num);

    //胜利关卡
    //Victory gate
    ASCSDKInterface.Instance.FinishLevel(string num);

    //失败关卡
    //Failure levels
    ASCSDKInterface.Instance.FailLevel(string num);

    //道具使用统计 item:道具名字，num：消耗数量，price：需要的金币
    //Item: property name, num: consumption amount, price: needed gold COINS
    ASCSDKInterface.Instance.UserUseBoosterInfo(string item, int num, double price);

    //道具购买统计 item:道具名字，num：购买数量，price：需要的金币
    //Item: item name, num: number of items purchased, price: needed gold COINS
    ASCSDKInterface.Instance.UserBuyBoosterInfo(string item, int num, double price);

    //自定义事件 eventName：自定义事件名字（由另一方实现）
    //Custom event eventName: custom eventName (implemented by the other party)
    ASCSDKInterface.Instance.UserCustomEvent(string eventName);
    
    //上传用户游戏参数，level->玩家当前通关最高关卡,buff->玩家当前道具总量,coin->玩家当前剩余金币数量
    //Upload user game parameters, level-> players currently pass the highest level of clearance, the total number of buff > players' current props, and the number of coin-> players' current remaining gold COINS
	ASCSDKInterface.Instance.UploadUserInfo(string level, string buff, string coin);

###  打包(Build)
打包Android应用，在build settings中switch到Android平台，build即可。打包成功后接入渠道资源前调用视频广告、支付回调、退出、礼包兑换都会有弹窗提示，用于测试。
Package Android applications, switch to Android platform in build Settings, build。After successful packaging, video advertising, payment callback, exit and gift exchange will be called before access to channel resources for testing.


------



火星人互娱有限公司 [@huoxingren]     
2018 年 08月 9日    


