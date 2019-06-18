
## 接入说明
### 1. 下载AIhelp Unity SDK：
点击页面右上角的“Clone or download”按钮下载, 下载完成后解压文件得到：
aihelp-plugin-unity.unitypackage

| 文件 | 说明 |
|:------------- |:---------------|
| **aihelp-plugin-unity.unitypackage**    | AIhelp Unity3D SDK|
| **AndroidManifest.xml**    | 示例AndroidManifest.xml|

### 2. 把AIhelp SDK 放入你的Unity3D 工程：
打开Unity工程，在菜单栏选择Assets->Import Package->Custom Package，选择**aihelp-plugin-unity.unitypackage**

或者直接双击aihelp-plugin-unity.unitypackage打开。

在弹出的窗口中选择import把aihelp unity sdk 导入到您的工程。

```
注意：导入SDK后，不要擅自改动Plugins/Android 或者Plugins/iOS有关AIhelp sdk的文件位置，除非你特别清楚这么做的理由。否则会导致接入不成功。
```
	
![Import AIHelp Package](https://github.com/AI-HELP/Docs-Screenshots/blob/master/import-aihelp-unity-package.png "Import AIHelp Package")

### 3. 接入工程配置（仅用于Android）
  在你的Android工程的AndroidManifest.xml，增加需要的配置：     
**a. 增加需要的权限**

	  <uses-permission android:name="android.permission.INTERNET" />
	  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  
**b. 增加activity:**

    <activity
       android:name="com.ljoy.chatbot.ChatMainActivity"
       android:configChanges="orientation|screenSize|locale"
       android:screenOrientation="sensor">
    </activity>
    <activity
       android:name="com.ljoy.chatbot.FAQActivity"
       android:configChanges="orientation|screenSize|locale"
       android:screenOrientation="sensor"
       android:theme="@android:style/Theme.Holo.Light.DarkActionBar">
       <intent-filter android:label="@string/app_name">
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="https"
                android:host="cs30.net"
                android:pathPrefix="/elvaFAQ" />
       </intent-filter>
    </activity>
    <activity
       android:name="com.ljoy.chatbot.OPActivity"
       android:configChanges="orientation|screenSize|locale"
       android:screenOrientation="sensor"
       android:theme="@style/Theme.AppCompat.Light.NoActionBar">
    </activity>
    <activity 
       android:name="com.ljoy.chatbot.WebViewActivity"
       android:screenOrientation="sensor"
       android:configChanges="orientation|screenSize|locale"
       android:theme="@android:style/Theme.Holo.Light.DarkActionBar">
       <intent-filter android:label="@string/app_name">
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
       </intent-filter>
    </activity>
    <activity
            android:name="com.ljoy.chatbot.QAWebActivity"
            android:configChanges="orientation|screenSize|locale" >
    </activity>

如果你的Unity工程还没有AndroidManifest.xml, 可以参照SDK下载文件包含的中的AndroidManifest.xml创建一个。
### 4. 在你的Unity工程中初始化AIHelp SDK

#### Android: 
```
在你的游戏初始化时实例化ElvaChatServiceSDKAndroid, 然后调用init接口传入appKey, domain, appId:

init(string appKey,string domain,string appId);
```

```
ElvaChatServiceSDKAndroid的构造函数:

public ElvaChatServiceSDKAndroid()
{
	AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
	currentActivity = jc.GetStatic<AndroidJavaObject>("currentActivity");
	sdk = new AndroidJavaClass("com.ljoy.chatbot.sdk.ELvaChatServiceSdk");
}

初始化init接口:

public void init(string appKey,string domain,string appId){
	sdk.CallStatic("init",currentActivity,appKey,domain,appId);
}
```

#### iOS:
```

在你的游戏初始化时实例化ElvaChatServiceSDKIOS 并且调用传入appKey, domain, appId:
（甲方有义务按照乙方接入文档说明的正常接入方式和调用方式使用乙方服务，如甲方通过技术手段影响乙方计费，乙方有权在通知甲方的同时立即单方面终止服务，并要求甲方承担责任。）

init(string appKey,string domain,string appId);
```

```
ElvaChatServiceSDKIOS可以用默认构造函数

public ElvaChatServiceSDKIOS()
{

}

初始化init接口

public void init(string appKey,string domain,string appId){
	elvaInit(appKey,domain,appId);
}

```
__增加需要的权限:__

请注意，在Unity生成的Unity-iPhone Xcode工程里, 在Info.plist文件增加如下内容：

	<key>NSCameraUsageDescription</key>
	<string>The customer service of this App may require your permission to access camera</string>
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>The customer service of this App may need to access your location data</string>
	<key>NSMicrophoneUsageDescription</key>
	<string>This App may require your permission to access audio speaker for ingame customer service</string>
	<key>NSPhotoLibraryUsageDescription</key>
	<string>This App may require your permission to access photo library for ingame customer service</string>
	<key>NSPhotoLibraryAddUsageDescription</key>
	<string>This App may require your permission to access photo library for ingame customer service</string>


* 参数说明：

| 参数 | 说明 |
|:------------- |:---------------|
| appKey    | app唯一密钥，从AIHelp Web管理系统获取|
| domain     | 您的AIHelp域名，从Web管理系统获取,例如foo.AIHELP.NET|
| appId     | app唯一标识，从Web管理系统获取| 

注：请使用注册邮箱登录 [AIHelp 后台](http://console.aihelp.net/elva)。在“设置”菜单的“应用”页面查看。初次使用，需登录[AIHelp 官网](http://www.aihelp.net/index.html)自助注册。


**初始化代码示例：**

```
// 一定要在应用启动时进行初始化操作，不然会无法进入AIHelp智能客服系统。

private static AIhelpService _instance;

public static AIhelpService Instance
{
    get{
		if (_instance == null)
		{
			//Debug.LogError ("AIHelp service is not initialized!");

			_instance = new AIhelpService ();
		}
		return _instance;
    }
}

public void Initialize(string appkey, string domain, string appId)
{
	if (sdk != null)
	{
		sdk.init(appkey, domain, appId);
		postInitSetting ();
	}
}

public AIhelpService()
{
    #if UNITY_ANDROID
      if(Application.platform == RuntimePlatform.Android)
		sdk = new ElvaChatServiceSDKAndroid();
    #endif
	#if UNITY_IOS
		if(Application.platform == RuntimePlatform.IPhonePlayer)
		sdk = new ElvaChatServiceSDKIOS();
	#endif
}
```

**注册初始化完成回调（可选）：**
### <h4 id="registerInitializationCallback"> 初始化完成回调 `registerInitializationCallback`</h4>

	void registerInitializationCallback(string gameObject);
		
**代码示例：**

	public void RegisterInitializationCallback(string gameObject)
	{
		if(sdk != null)
		{
			sdk.registerInitializationCallback(gameObject);
		}
	}
	

**参数说明：**

- gameObject: Unity 中gameObject的名字。可以通过获取transform.name动态传入。例如：

```
// 注册初始化回调
AIhelpService.Instance.RegisterInitializationCallback (transform.name);

// 初始化
AIhelpService.Instance.Initialize ("TRYELVA_app_5a6b4540bbee4d7280fda431700ed71a", "TryElva.AIHELP.NET", appid);

```	

成功使用条件：

- 必须在调用init接口之前调用
- 必须在gameObject的脚本类中，实现回调方法`OnAIHelpInitialized，方法名字不可更改`，调用内容可自定义：

```
// 初始化回调函数实现
public void OnAIHelpInitialized(string str)
{	
    // 以下内容可自定义
	isAIHelpInited = true;
	Debug.Log ("AIhelpInitCallback is called str:" + str);
}
```


---

### 5. 使用AIHelp 接口


#### 1. 接口说明

| 接口名 | 作用 |备注|
|:------------- |:---------------|:---------------|
| **[showElva](#showElva)**      | 启动机器人客服聊天界面| 
| **[showElvaOP](#showElvaOP)** | 启动运营界面| 需配置运营模块|
| **[showFAQs](#showFAQs)** | 展示全部FAQ菜单|需配置FAQ,需调用[setUserName](#setUserName) 和[setUserId](#setUserId)|
|**[showConversation](#showConversation)**|启动人工客服聊天界面|需配置FAQ,需调用[setUserName](#setUserName) 和[setUserId](#setUserId)|
| **[showSingleFAQ](#showSingleFAQ)** | 展示单条FAQ|需配置FAQ,需调用[setUserName](#setUserName) 和[setUserId](#setUserId)|
| **[setName](#setName)** | 设置游戏名称|在初始化之后调用|
| **[setUserName](#UserName)** | 设置玩家(用户)名称| 如果拿不到username，就传入空字符串""，会使用默认昵称"anonymous"
| **[setUserId](#UserId)** | 设置玩家(用户)ID| 如果拿不到userid，就传入空字符串""，系统会生成一个唯一设备id 
| **[setSDKLanguage](#setSDKLanguage)** | 设置SDK语言|


**注：您并不需要调用以上所有接口，尤其当您的游戏/应用只设置一个客服入口时, 有的接口所展示的界面包含了其他接口，详情见下：**

#### <h4 id="showElva">2. 智能客服主界面启动，调用`showElva`接口，启动机器人客服聊天界面</h4>

	void showElva(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag);
			


	void showElva(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag,
				Dictionary<string,object> config);

**代码示例：**

	public void AIHelpChat()
	{
		Dictionary<string, object> tags = new Dictionary<string, object> ();
		List<string> tag = new List<string>();
		tag.Add ("server1");
		tag.Add ("pay3");
		tags.Add ("elva-tags", tag);
		Dictionary<string, object> config = new Dictionary<string, object> ();
		config.Add ("elva-custom-metadata", tags);

		sdk.showElva(
				"TEST_PLAYER_NAME", 
				"TEST_UID_123", 
				"TEST_SRV_ID_123", 
				"1",
				config);
	}

	
**参数说明：**

- playerName:游戏中用户名称。 
- playerUid:用户在游戏里的唯一标识。 
- serverId:用户所在的服务器编号。 
- showConversationFlag(0或1):是否开启人工入口。此处为1时，将在机器人的聊天界面右上角，提供人工聊天的入口。如下图。
- config:可选，自定义用户信息。可以在此处设置特定的Tag信息。说明：elva-tags对应的值为个字符串列表，此处传入自定义一个或多个tag值。这些tag需要在Web管理配置同名称的标签才能生效。
	

![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showElva-CN-Android.png "showElva")

	
**最佳实践：**

> 1. 在您应用的客服主入口触发这个接口的调用。
在AIHelp 配置个性化的机器人欢迎语，以及更多机器人对话故事线，引导用户反馈并得到回答。
> 2. 打开人工客服入口，用户可以在机器人客服界面右上角进入人工客服进行聊天, 你也可以设置条件只让一部分用户看到这个入口

#### <h4 id="showElvaOP">3. 智能客服运营模块主界面启动，调用`showElvaOP`方法，启动运营模块界面</h4>

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag);
或

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag,
				Dictionary<string,object> config);
或

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag,
				Dictionary<string,object> config,
				int defaultTabIndex);

**代码示例：**

	// Presenting Operation Info to your customers
	public void OperationModule()
	{
		Dictionary<string, object> tags = new Dictionary<string, object> ();
		List<string> tag = new List<string>();
		tag.Add ("server1");
		tag.Add ("pay3");
		tags.Add ("elva-tags", tag);
		Dictionary<string, object> config = new Dictionary<string, object> ();
		config.Add ("elva-custom-metadata", tags);
		sdk.showElvaOP (
		     "TEST_PLAYER_NAME", 
		     "TEST_UID_123", 
		     "TEST_SRV_ID_123", 
		     "1", 
		     config);
	}

**参数说明：**

- playerName:游戏中用户名称。 
- playerUid:用户在游戏里的唯一标识。 
- serverId:用户所在的服务器编号。 
- showConversationFlag(0或1):是否开启人工入口。此处为1时，将在机器人的聊天界面右上角，提供人工聊天的入口。如下图。
- config:可选，自定义用户信息。可以在此处设置特定的Tag信息。说明：elva-tags对应的值为个字符串列表，此处传入自定义一个或多个tag值。这些tag需要在Web管理配置同名称的标签才能生效。
- defaultTabIndex: 可选，首次进入运营界面时候展示的tab的编号，默认为第一个tab，若需默认展示客服界面tab，设置值为999
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showElvaOP_Android.png "showElvaOP")

**最佳实践：**
> 1. 在您应用的运营入口触发这个接口的调用。
在AIHelp 后台配置运营分页（tab)并且发布跟应用相关的运营公告内容。就通过AIHelp展示这些内容给用户。运营界面的最后一个分页总是机器人客服聊天界面。
> 2. 在tab页面，用户可以在页面右上角进入FAQ页面查看；在机器人客服页面（Help页面），用户可以在页面右上角进入人工客服，此人工客服入口可以通过参数设置条件，根据条件打开或关闭，只让一部分用户看到这个入口。

#### <h4 id="showFAQs">4. 展示FAQ列表, 调用`showFAQs `方法</h4>

	void showFAQs();

或

	void showFAQs(Dictionary<string,object> config);

**代码示例：**

    public void ShowFAQs()
    {
        if(sdk != null)
        {
			Dictionary<string, object> tags = new Dictionary<string, object> ();
			List<string> tag = new List<string>();
			tag.Add ("server1");
			tag.Add ("pay3");
			tags.Add ("elva-tags", tag);
			Dictionary<string, object> config = new Dictionary<string, object> ();
			config.Add ("elva-custom-metadata", tags);
			config.Add ("showContactButtonFlag", "1"); // 显示可以从FAQ列表右上角进入机器人客服
			config.Add("showConversationFlag", "1"); // 显示可以从FAQ进入人工客服

			sdk.showFAQs(config);
        }
    }

**参数说明：**

- config:可选，自定义用户信息。可以在此处设置特定的Tag信息。说明：elva-tags对应的值为个字符串列表，此处传入自定义一个或多个tag值。这些tag需要在Web管理配置同名称的标签才能生效。
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showFAQs-CN-Android.png "showFAQs")

**最佳实践：**
> 1. 在您应用的FAQ主入口触发这个接口的调用。在AIHelp 后台页面配置并分类FAQ，如果您的FAQ较多，可以增加一个父级分类。

#### <h4 id="showSingleFAQ">5. 展示单条FAQ，调用`showSingleFAQ`方法
</h4>

	void showSingleFAQ (string faqId);
	void showSingleFAQ (string faqId, Dictionary<string,object> config);

**代码示例：**

	// Presenting single FAQ to your customers
    public void ShowSingleFAQ(faqId)
    {
        if(sdk != null)
        {
			Dictionary<string, object> tags = new Dictionary<string, object> ();
			List<string> tag = new List<string>();
			tag.Add ("server1");
			tag.Add ("pay3");
			tags.Add ("elva-tags", tag);
			Dictionary<string, object> config = new Dictionary<string, object> ();
			config.Add ("elva-custom-metadata", tags);
			config.Add("showConversationFlag", "1"); // 显示可以从FAQ进入人工客服
			sdk.showSingleFAQ(faqId, config);
        }
    }
    
**参数说明：**

- faqId:FAQ的PublishID,可以在[AIHelp 后台](https://aihelp.net/elva)中，从FAQs菜单下找到指定FAQ，查看PublishID。
- config:可选，自定义用户信息。可以在此处设置特定的Tag信息。说明：elva-tags对应的值为个字符串列表，此处传入自定义一个或多个tag值。这些tag需要在Web管理配置同名称的标签才能生效。
	
![showSingleFAQ](https://github.com/CS30-NET/Pictures/blob/master/showSingleFAQ-CN-Android.png "showSingleFAQ")

<h4 id="selfservice"></h4>

自助服务：
如果在web管理后台配置了FAQ的自助服务链接，并且SDK设置了[UserId](#UserId), [UserName](#UserName), [ServerId](#ServerId)，将在显示FAQ的同时，右上角提供功能菜单，可以对相关的自助服务进行调用。


**最佳实践：**
> 1. 在您应用的特定功能入口触发这个接口的调用，可以方便用户了解具体功能相关的FAQ。


#### <h4 id="setName">6. 设置游戏名称信息，调用`setName`方法(建议游戏刚进入，调用init之后就默认调用)</h4>

	void setName(string game_name);

**代码示例：**

	sdk.setName("Your Game");

**参数说明：**

- game_name:游戏名称

**最佳实践：**
> 1. 在初始化后调用该接口设置游戏名称，将显示在AIHelp相关界面标题栏。

#### <h4 id="UserId">7. 设置用户唯一ID信息，调用`setUserId`方法</h4>

	void setUserId(string userId); 如果拿不到userid，就传入空字符串""，系统会生成一个唯一设备id 

**代码示例：**

	sdk.setUserId("123ABC567DEF"); 如果拿不到userid，就传入空字符串""，系统会生成一个唯一设备id 

**参数说明：**

- playerUid:用户唯一ID

**最佳实践：**
> 1. 通常你可以用在其他接口传入用户Id，无需调用该接口，但是若要使用[自助服务](#selfservice)，则必须调用。

#### <h4 id="UserName">8. 设置用户名称信息，调用`setUserName`方法</h4>

	void setUserName(string userName); 如果拿不到username，就传入空字符串""，会使用默认昵称"anonymous"

**代码示例：**

	sdk.setUserName ("PLAYER_NAME"); 如果拿不到username，就传入空字符串""，会使用默认昵称"anonymous"

**参数说明：**

- playerName:用户名称

**最佳实践：**
> 1. 传入你的App的用户名称，这样在后台客户服务页面会展示用户的应用内名称，便于客服在服务用户时个性化称呼对方。
> 2. 通常你无需调用该接口，可以用其他接口传入用户名称，但是若要使用[自助服务](#selfservice)，则必须调用。

#### <h4 id="ServerId">9. 设置服务器唯一ID信息，调用`setServerId`方法
</h4>

	void setServerId(int serverId);

**代码示例：**

	sdk.seterverId("SERVER_ID");

**参数说明：**

- serverId:服务器ID

**最佳实践：**
> 1. 通常你无需调用该接口，可以用其他接口传入服务器ID，但是若要使用[自助服务](#selfservice)，则必须调用。


#### <h4 id="showConversation">10. 直接进入人工客服聊天，调用`showConversation`方法(要求设置[UserName](#UserName))</h4>

	void showConversation(
				string playerUid,
				string serverId);
或

	void showConversation(
				string playerUid,
				string serverId,
				Dictionary<string,object> config);
	
**代码示例：**

    public void ShowVIPChat()
    {
        if(sdk != null)
        {
			Dictionary<string, object> tags = new Dictionary<string, object> ();
			List<string> tag = new List<string>();
			tag.Add ("server1");
			tag.Add ("pay3");
			tags.Add ("elva-tags", tag);
			Dictionary<string, object> config = new Dictionary<string, object> ();
			config.Add ("elva-custom-metadata", tags);
			sdk.setUserName("PLAYER_NAME"); 如果拿不到username，就传入空字符串""，会使用默认昵称"anonymous"
			sdk.showConversation(faqId, config);
        }
    }

**参数说明：**

- playerUid:用户在游戏里的唯一标识
- serverId:用户所在的服务器编号
- config:可选，自定义用户信息。可以在此处设置特定的Tag信息。说明：elva-tags对应的值为个字符串列表，此处传入自定义一个或多个tag值。这些tag需要在Web管理配置同名称的标签才能生效。

**最佳实践：**
> 1. 通常你不需要调用这个接口，除非你想在应用里设置触发点，让用户有机会直接进入人工客服聊天界面。

![showConversation](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showConversation-CN-Android.png "showConversation")


#### <h4 id="setSDKLanguage">11. 设置语言，调用`setSDKLanguage`方法
</h4>

	void setSDKLanguage (string language);
	
**代码示例：**

	sdk.setSDKLanguage("en");

**参数说明：**

- language:语言名称。如英语为en,简体中文为zh_CN。更多语言简称参见AIHelp后台，"设置"-->"语言"的Alias列。

![language](https://github.com/AI-HELP/Docs-Screenshots/blob/master/Language-alias.png "语言Alias列")

**最佳实践：**
> 1. 通常SDK会使用手机的默认语言设置，如果你的应用使用跟手机设置不一样的语言，那么你需要在AIHelp SDK初始化之后调用此接口修改默认语言。
> 2. 如果你的应用允许用户更改语言，那么每次更改语言之后，也需要调用此接口重新设置SDK的语言。


#### 12. 设置另一个欢迎语。

如果你设置了进入AI客服的不同入口，希望用户从不同的入口进入AI客服时显示不同的欢迎语，进入不同故事线，可以通过设置config参数来实现： 
	
**代码示例：**

	Dictionary<string, object> welcomeText = new Dictionary<string, object> ();
	
	//调用不同故事线功能，使用指定的提示语句，调出相应的机器人欢迎语
	//注：anotherWelcomeText是key，不能改变。
	//需要改变的是usersay，保持和故事线中配置的User Say内容一样
	welcomeText.Add("anotherWelcomeText","usersay");
	Dictionary<string, object> config = new Dictionary<string, object> ();
	config.Add ("elva-custom-metadata", welcomeText);
	
	//如果是在智能客服主界面中	
	sdk.showElva(
			"TEST_PLAYER_NAME", 
			"TEST_UID_123", 
			"TEST_SRV_ID_123", 
			"", 
			"1",
			config);
或

	//如果是在智能客服运营主界面中
	sdk.showElvaOP(
			"TEST_PLAYER_NAME", 
			"TEST_UID_123", 
			"TEST_SRV_ID_123", 
			"", 
			"1",
			config);


**最佳实践：**
> 1. 引导玩家从不同入口看到不同的服务
