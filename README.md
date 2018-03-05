[查看Unity SDK中文接入文档](https://github.com/AI-HELP/AIHelp-Unity-SDK/blob/master/README_CN.md)

[AIHelp Unity Example Project ](https://github.com/AI-HELP/Unity_Demo)

## AIHelp SDK Integration Guide
### 1. Download The AIHelp Unity SDK：
Select "Clone or download" to download the SDK file in the github page, unzip the compressed file and get **AIHelp-plugin-unity.unitypackage**

| File name | Description |
|:------------- |:---------------|
| **AIHelp-plugin-unity.unitypackage**    | AIHelp Unity SDK |
| **AndroidManifest.xml**    | Example AndroidManifest file|


### 2. Add AIHelp to your Unity project:

In your Unity project，select menu Assets->Import Package->Custom Package，find **AIHelp-plugin-unity.unitypackage** in the finder window.

Or find AIHelp-plugin-unity.unitypackage and double-click to open it, 
import the AIHelp unity sdk into your project


	Note：After you import the sdk, don not change the files location under 
	Plugins/Android and Plugins/iOS, unless you have a valid reason.
	
	Changing file location may result in an integration failure.



![Import AIHelp Package](https://github.com/AI-HELP/Docs-Screenshots/blob/master/import-AIHelp-unity-package.png "Import AIHelp Package")

 
### 3. Configure your Android Manifest（Only for Android)
  In the AndroidManifest.xml of your project, add the below information：     
**a. Add Required Permissions**

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
**b. Add AIHelp Activities:**

    <activity
       android:name="com.ljoy.chatbot.ChatMainActivity"
       android:configChanges="orientation|screenSize|locale"
       android:screenOrientation="portrait">
    </activity>
    <activity
       android:name="com.ljoy.chatbot.FAQActivity"
       android:configChanges="orientation|screenSize|locale"
       android:screenOrientation="portrait"
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
            android:screenOrientation="portrait"
            android:theme="@style/Theme.AppCompat.Light.NoActionBar"
            >
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
**c. Add Meta Data (if using 'appindexing')**      

    <meta-data
       android:name="com.google.android.gms.version"
       android:value="@integer/google_play_services_version" />

### 4. Initialize AIHelp SDK in your Project

#### Android: 

Instantiate Class ElvaChatServiceSDKAndroid with appKey, domain, appId:


```
//ElvaChatServiceSDKAndroid Constructor:

	public ElvaChatServiceSDKAndroid(string appKey, string domain, string appId)
	{
		AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
		currentActivity = jc.GetStatic<AndroidJavaObject>("currentActivity");
		sdk = new AndroidJavaClass("com.ljoy.chatbot.sdk.ELvaChatServiceSdk");

		// pass the appkey, domain and appId of your own app, respectively
		init(appKey, domain, appId);
	}

```

#### iOS:

Instantiate Class ElvaChatServiceSDKIOS with appKey, domain, appId:


```
//ElvaChatServiceSDKIOS  Constructor:

	public ElvaChatServiceSDKIOS(string appKey, string domain, string appId)
	{
		// pass the appkey, domain and appId of your own app, respectively
		init(appKey, domain, appId);
	}

```

__Add Required Permissions:__

Please note that, you should add the below content into the Info.plist file of your Unity-iPhone Xcode project:

	<key>NSCameraUsageDescription</key>
	<string>The customer service of this App may require your permission to access camera</string>
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>The customer service of this App may need to access your location data</string>
	<key>NSMicrophoneUsageDescription</key>
	<string>This App may require your permission to access audio speaker for ingame customer service</string>
	<key>NSPhotoLibraryUsageDescription</key>
	<string>This App may require your permission to access photo library for ingame customer service</string>
	

**About Parameters：**

| Parameters | Description |
|:------------- |:---------------|
| appKey    | Your unique developer API Key|
| domain     | Your AIHelp domain name. For example: foo.AIHelp.NET|
| appId     | A unique ID assigned to your app.| 

Note: Please log into [AIHelp Web Console](https://console.AIHelp.net/elva) with your registration email account to find the __appKey__, __domain__ and __appId__ In the _Application_ page of the _Settings_ Menu. 
If your company does not have an account, you can register an account at [AIHelp Website](http://AIHelp.net/index.html)


**Coding Example：**

```
// Must be called during application/game initialization, otherwise you can't use AIHelp properly


private void initAIHelpSDK()
{
    AIHelpService = new AIHelpServiceExample();
}
public AIHelpServiceExample()
{
	#if UNITY_ANDROID
	if(Application.platform == RuntimePlatform.Android)
	sdk = new ElvaChatServiceSDKAndroid(
		"TRYELVA_app_5a6b4540bbee4d7280fda431700ed71a", 
		"TryElva.AIHelp.NET", 
		"TryElva_platform_14970be5-d3bf-4f91-8c70-c2065cc65e9a");
	#endif
	#if UNITY_IOS
	if(Application.platform == RuntimePlatform.IPhonePlayer)
	sdk = new ElvaChatServiceSDKIOS(
		"TRYELVA_app_5a6b4540bbee4d7280fda431700ed71a",
		"TryElva.AIHelp.NET", 
		"TryElva_pf_ec28eb91dd7d463bb359fc53d43dcfd6");
	#endif
}
```

---

### 5. Start Using AIHelp

#### 1. API Summary

| Method Name| Purpose |Prerequisites|
|:------------- |:---------------|:---------------|
| **[showElva](#showElva)**      | Launch AI Conversation Interface| 
| **[showElvaOP](#showElvaOP)** | Launch Operation Interface| Need to configure Operation sections|
| **[showFAQs](#showFAQs)** | Show all FAQs by Sections|Need to configure FAQs|
|**[showConversation](#showConversation)**|Launch VIP Conversation Interface| Need to setUserName and setUserId |
| **[showSingleFAQ](#showSingleFAQ)** | Show Single FAQ|Need to configure FAQ|
| **[setName](#setName)** | Set APP/Game Name|Use it after initialization|
| **[setUserName](#UserName)** | Set User In-App Name|
| **[setUserId](#UserId)** | Set Unique User ID|
| **[setSDKLanguage](#setSDKLanguage)** | Set SDK Language|


**Note：It is not necessary for you to use all the APIs, especially when you only have one user interface for the customer service in your application. Some APIs already contains entry to other APIs, See below for details：**

#### <h4 id="showElva">2. Launch the AI Conversation Interface，use `showElva`</h4>

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

**Coding Example：**


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

**About Parameters：**

- __playerName__: In-App User Name
- __playerUid__: In-App Unique User ID
- __serverId__: The Server ID
- __showConversationFlag__: Should be "0" or "1". If set at "1", the VIP conversation entry will be displayed in the upper right hand side of the AI conversation interface.
- __config__: Optional parameters for customized information. You can pass specific Tag information using the sting list "elva-tags", see the above coding example. Please note that you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
	
![showElva](https://github.com/AIHelp-NET/Pictures/blob/master/showElva-EN-Android.png "showElva")
	
**Best Practice：**

> 1. Use this method to launch your APP's customer service. Configure specific welcome text and AI story line in the AIHelp Web Console to better the customer support experiences.
> 2. Enable VIP Conversation Entry to allow users' to chat with your human support team with parameter "__showConversationFlag__" setting to "__1__", you may use this method for any user or as a privilege for some users only.

#### <h4 id="showElvaOP">3. Launch The Operation Interface, use `showElvaOP`</h4>

The operation module is useful when you want to present updates, news, articles or any background information about your APP/Game to users. The AI Help

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag);
OR

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag,
				Dictionary<string,object> config);
OR

	void showElvaOP(
				string playerName,
				string playerUid,
				string serverId,
				string showConversationFlag,
				Dictionary<string,object> config,
				int defaultTabIndex);

**Coding Example：**

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
		     "", 
		     "1", 
		     config);
	}

**About Parameters：**

- __playerName__: User Name in Game/APP
- __playerUid__: Unique User ID
- __serverId__: The Server ID
- __showConversationFlag__: Should be "0" or "1". If set at "1", the VIP conversation entry will be shown in the top right hand side corner of the AI conversation interface.
- __config__: Optional parameters for customized information. You can pass specific Tag information using sting list "elva-tags", see above coding examples. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
- __defaultTabIndex__: Optional. The index of the first tab will be shown when entering the operation interface. Default value is the left-most tab，if you would like to show the AI conversation interface(the right-most side) set to 999.
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showElvaOP_Android.png "showElvaOP")

**Best Practice：**
> 1. Use this API to present news, announcements, articles or any useful information to users/players. Configure and publish the information in AIHelp web console. 

#### <h4 id="showFAQs">4. Display FAQs, use `showFAQs `</h4>

	void showFAQs();

OR

	void showFAQs(Dictionary<string,object> config);

**Coding Example：**

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
			config.Add("showConversationFlag", "1"); // 显示可以从FAQ进入人工客服
			sdk.showFAQs(config);
        }
    }

**About Parameters：**

- __config__: Optional parameters for customized information. You can pass specific Tag information using sting list "elva-tags", see the above coding examples. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showFAQs-EN-Android.png "showFAQs")

**Best Practice：**
> 1. Use this method to show FAQs about your APP/Game properly. Configure FAQs in AIHelp Web Console. Each FAQ can be categroized into a section. If the FAQs are great in number, you can also add Parent Sections to categorize sections to make things clear and organized. 

#### <h4 id="showSingleFAQ">5. Show A Specific FAQ，use `showSingleFAQ`
</h4>

	void showSingleFAQ (string faqId);
	void showSingleFAQ (string faqId, Dictionary<string,object> config);

**Coding Example：**

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

**About Parameters：**

- __faqId__: The PublishID of the FAQ item, you can check it at in [AIHelp Web Console](https://AIHelp.net/elva): Find the FAQ in the FAQ menu and copy its PublishID.
- __config__: Optional parameters for customized information. You can pass specific Tag information using sting list "elva-tags", see the above coding example. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
	
![showSingleFAQ](https://github.com/AIHelp-NET/Pictures/blob/master/showSingleFAQ-EN-Android.png "showSingleFAQ")

<h4 id="selfservice"></h4>

**Self-Service：**

If you configure the "self-service" link in the FAQ's configuration, and set [UserId](#UserId), [UserName](#UserName), [ServerId](#ServerId) in SDK，then the FAQ shows a functional menu in the right-top cornor.


**Best Practice：**
> 1. Use this method when you want to show a specific FAQ in a proper location of your APP/Game.

#### <h4 id="setName">6. Set Your App's name for AIHelp SDK to display，use `setName`</h4>

	void setName(string game_name);

**Coding Example：**

	sdk.setName("Your Game");

**About Parameters：**

- __game_name__: APP/Game Name

**Best Practice：**
> 1. Use this method after SDK initialization.The App's name will display in the title bar of the customer service interface.

#### <h4 id="UserId">7. Set the Unique User ID, use `setUserId`</h4>


	void setUserId(string userId);

**Coding Example：**

	sdk.setUserId("123ABC567DEF");

**About Parameters：**

- __playerUid__:Unique User ID

**Best Practice：**
> 1. Normally you don not need to use this method if you have passed the user ID in another method. However, if you want to use the FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="UserName">8. Set User Name，use `setUserName`</h4>

	void setUserName(string userName);

**Coding Example：**

	sdk.setUserName ("PLAYER_NAME");

**About Parameters：**

- __playerName:__ User/Player Name

**Best Practice：**
> 1. Use this method to set the user name, which will be shown in the web console's conversation page for the user. You can address the user with this name during the chat.
> 2. Normally you do not need to use this method if you have passed the user name in another method. However, if you want to use FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="ServerId">9. Set Unique Server ID，use `setServerId`
</h4>

	void setServerId(int serverId);

**Coding Example：**

	sdk.seterverId("SERVER_ID");

**About Parameters：**

- __serverId:__ The Unique Server ID

**Best Practice：**
> 1. Normally you do not need to use this method if you have passed the server ID in another method. However, if you want to use the FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="showConversation">10. Launch VIP Chat Console, use `showConversation`(need to set [UserName](#UserName))</h4>

	void showConversation(
				string playerUid,
				string serverId);

or

	void showConversation(
				string playerUid,
				string serverId,
				Dictionary<string,object> config);
	
**Coding Example：**

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
			sdk.setUserName("PLAYER_NAME");
			sdk.showConversation(faqId, config);
        }
    }

**About Parameters：**

- __playerUid__:Unique User ID
- __serverId:__ The Unique Server ID
- __config__: Optional parameters for customized information. You can pass specific Tag information using vector elva-tags, see the above coding example. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.

**Best Practice：**
> 1. Normally you do not need to use this method unless you intend to allow users to enter VIP conversation without engaging with the AI chat. You may use this method as a privilege for some users.

![showConversation](https://github.com/AIHelp-NET/Pictures/blob/master/showConversation-EN-Android.png "showConversation")


#### <h4 id="setSDKLanguage">11. Set SDK Lanague，use `setSDKLanguage`
</h4>
Setting the SDK Language will change the FAQs, Operational information, AI Chat and SDK display language. 

	void setSDKLanguage (string language);
	
**Coding Example：**

	sdk.setSDKLanguage("en");

**About Parameters：**

- __language:__ Standard Language Alias. For example: en is for English, zh_CN is for Simplified Chinese。More language labels can be selected through AIHelp Web Console:"Settings"-->"Language"->Alias.

![language](https://github.com/AI-HELP/Docs-Screenshots/blob/master/Language-alias.png "Language Alias")

**Best Practice：**
> 1. Normally AIHelp will use the mobile's lanaguge configuration by default. If you intend to make a different language setting, you need to use this method right after SDK initialization.
> 2. If your allow users to change the APP language, then you need to use this method to make AIHelp the same lanague with your APP.

#### 12. Set a Different Greeting Story Line.

If your APP provides multiple entries to AIHelp, and you intend to introduce different AI welcome texts and story lines to users from different entries, you can set config parameters within [showElva](#showElva) or [showElvaOP](#showElvaOP)： 

**Coding Example：**
	
	Dictionary<string, object> welcomeText = new Dictionary<string, object> ();
	// note：anotherWelcomeText is key，should be unchanged.
	// you need to change usersay according to the "User Say" in your new 
	// story line
	welcomeText.Add("anotherWelcomeText","usersay");
	Dictionary<string, object> config = new Dictionary<string, object> ();
	config.Add ("elva-custom-metadata", welcomeText);
	

	sdk.showElva(
			"TEST_PLAYER_NAME", 
			"TEST_UID_123", 
			"TEST_SRV_ID_123", 
			"", 
			"1",
			config);
Or

	sdk.showElvaOP(
			"TEST_PLAYER_NAME", 
			"TEST_UID_123", 
			"TEST_SRV_ID_123", 
			"", 
			"1",
			config);


**Best Practice：**
> 1. Introduce different story lines to users from different sources.

