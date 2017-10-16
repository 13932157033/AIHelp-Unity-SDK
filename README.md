[查看Android中文接入文档](https://github.com/AI-HELP/AIhelp-Unity-SDK/blob/master/README_CN.md)

## AIHelp SDK Integration Guide
### 1. Download The AIHelp Cocos2d-x Android SDK：
Click "Clone or download" to download Android SDK in github page.

The folder **aihelp-plugin-cocos2dx** contains:

| subfolder name | description |
|:------------- |:---------------|
| **android-libs**    | AIHelp Android SDK dependencies|
| **elvachatservice**    | AIHelp Android SDK core files|
| **v3.x/Classes**      | containing the cpp files for Cocos2dx version 3.x|
| **v2.x/Classes**      | containing the cpp files for Cocos2dx version 2.x| 

### 2. Add AIHelp to your Cocos2dx project:
**a.** copy folder *elvachatservice* to the the root directory of your project.

**b.** copy cpp files under folder *Classes* into the Classes folder of your Cocos2dx project, choose files to copy based on the version of your Cocos2dx.

**c.** Import dependencies under *android-libs* to your Cocos2dx Project：

If your project has already imported some of the dependencies, just import those you don't have. If you use Gradle，all you need to do is add below dependencies in your _build.gradle_:

    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.android.support:design:23.4.0'
    compile 'com.android.support:recyclerview-v7:23.4.0'
    compile 'com.android.support:cardview-v7:23.4.0'
    # add this if using appindexing：
    compile 'com.google.android.gms:play-services-appindexing:8.1.0'

If you use __Eclipse__ that doesn't use Gradle, you need to import each of the dependencies into your project as a library. You also need to explicitly add dependency relationship between AIHelp SDK and the libraries:  
__elvachatservice__ depends __appcompat__, which depends __design__, __recyclerview__ and __cardview__.
 
### 3. Configure your Android Manifest
  In the AndroidManifest.xml of your project, add below information：     
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
**c. Add meta data (if usig appindexing)**      

    <meta-data
       android:name="com.google.android.gms.version"
       android:value="@integer/google_play_services_version" />

### 4. Initialize AIHelp SDK in your Project

```
Note：
You must use ECServiceCocos2dx::init(...) in your App's Initialization Code, otherwise you can't use AIHelp service properly. 

```

```
ECServiceCocos2dx::init(
			String appKey,
			String domain,
			String appId);
```
	


**About Parameters：**

| Parameters | Description |
|:------------- |:---------------|
| appKey    | Your unique developer API Key|
| domain     | Your AIHelp domain name. For example: foo.AIHELP.NET|
| appId     | A unique ID assigned to your app.| 

Note: Please log in [AIHelp Web Console](https://aihelp.net/elva) with your registration email account to find __appKey__, __domain__ and __appId__ In the _Application_ page of the _Settings_ Menu. 
If your company doesn't have an account, you need to register an account in [AIHelp Website](http://aihelp.net/index.html)


**Coding Example：**

```
// Must be called during application/game initialization, otherwise you can't use AIHelp properly

ECServiceCocos2dx::init(
			"YOUR_API_KEY",
			"YOUR_DOMAIN_NAME",
			"YOUR_APP_ID");
```

---

### 5. Start using AIHelp

#### 1. API summary

| Method Name| Purpose |Prerequisites|
|:------------- |:---------------|:---------------|
| **[showElva](#showElva)**      | Launch AI Conversation Interface| 
| **[showElvaOP](#showElvaOP)** | Launch Operation Interface| Need to configure Operation sections|
| **[showFAQs](#showFAQs)** | Show all FAQs by sections|Need to configure FAQs|
|**[showConversation](#showConversation)**|Launch VIP conversation interface| Need to setUserName and setUserId |
| **[showSingleFAQ](#showSingleFAQ)** | Show single FAQ|Need to configure FAQ|
| **[setName](#setName)** | Set APP/Game Name|Use it after initialization|
| **[setUserName](#UserName)** | Set User in-app name|
| **[setUserId](#UserId)** | Set unique user ID|
| **[setSDKLanguage](#setSDKLanguage)** | Set SDK language|


**Note：It's not necessary for you to use all the APIs, especially when you only have one user interface for the customer service in your application. Some APIs already contains entry to other APIs, see below in detail：**

#### <h4 id="showElva">2. Launch the AI Conversation Interface，Use`showElva`</h4>


	ECServiceCocos2dx::showElva(
				string playerName,
				string playerUid,
				int serverId,
				string playerParseId,
				string showConversationFlag);
			
or

	ECServiceCocos2dx::showElva(
				string playerName,
				string playerUid,
				int serverId,
				string playerParseId,
				string showConversationFlag,
				cocos2d::ValueMap& config);

**Coding Example：**

	// Presenting AI Help Converation with your customers
	void GameSettingsScene::menuHelpCallback(CCObject* pSender)
	{						
		ECServiceCocos2dx::showElva (
				"USER_NAME",
				"USER_ID",
				123, 
				"",
				"1",
				{ 
					hs-custom-metadata＝｛
					hs-tags＝'vip，pay1',
					VersionCode＝'3'
					｝
				});
	}

**About Parameters：**

- __playerName__: In-app User Name
- __playerUid__: In-app Unique User ID
- __serverId__: The Server ID
- __playerParseId__: Can be empty string, can NOT be NULL
- __showConversationFlag__: Should be "0" or "1". If set "1", the VIP conversation entry will be displayed in the upper right of the AI conversation interface.
- __config__: Optional param for custom ValueMap information. You can pass specific Tag information using vector hs-tags, see above coding example. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
	
![showElva](https://github.com/AIHELP-NET/Pictures/blob/master/showElva-EN-Android.png "showElva")
	
**Best Practice：**

> 1. Use this method to launch your APP's customer service. Configure specific welcome text and AI story line in the AIHelp Web Console to better customer support experiences.
> 2. Enable VIP Conversation Entry to allow user to chat with your human support with parameter "__showConversationFlag__" setting to "__1__", you may use this method for any user or as a privilege for some users only.

#### <h4 id="showElvaOP">3. Launch The Operation Interface, use`showElvaOP`</h4>

The operation module is useful when you want to present update, news, articles or any background information about your APP/Game to users. The AI Help

	ECServiceCocos2dx::showElvaOP(
				string playerName,
				string playerUid,
				int serverId,
				string playerParseId,
				string showConversationFlag,
				cocos2d::ValueMap& config);

or

	ECServiceCocos2dx::showElvaOP(
				string playerName,
				string playerUid,
				int serverId,
				string playerParseId,
				string showConversationFlag,
				cocos2d::ValueMap& config,
				int defaultTabIndex);

**Coding Example：**

	// Presenting Operation Info to your customers
	void GameSettingsScene::menuOperationCallback(CCObject* pSender)
	{						
		ECServiceCocos2dx::showElvaOP (
					"USER_NAME",
					"USER_ID",
					123, 
					"",
					"1",
					{ 
						hs-custom-metadata＝｛
						hs-tags＝'vip,pay1',
						VersionCode＝'3'
						｝
					});
	}

**About Parameters：**

- __playerName__: User Name in Game/APP
- __playerUid__: Unique User ID
- __serverId__: The Server ID
- __playerParseId__: Can be empty string, can NOT be NULL
- __showConversationFlag__: Should be "0" or "1". If set "1", the VIP conversation entry will be shown in the top right corner of the AI conversation interface.
- __config__: Custom ValueMap information. You can pass specific Tag information using vector hs-tags, see above coding example. Please note you also need to configure the same tag information in the Web console so that each conversation can be correctly tagged.
- __defaultTabIndex__: Optional. The index of the first tab to be shown when entering the operation interface. Default value is the left-most tab，if you would like to show the AI conversation interface(the right-most，set it to 999.
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showElvaOP_Android.png "showElvaOP")

**Best Practice：**
> 1. Use this API to present news, announcements, articles or any useful information to users/players. Configure and publish the information in AIHelp web console. 

#### <h4 id="showFAQs">4. Display FAQs, use`showFAQs `</h4>

	ECServiceCocos2dx::showFAQs();

or

	ECServiceCocos2dx::showFAQs (cocos2d::ValueMap& config)

**Coding Example：**

	// Presenting FAQs to your customers
	void GameSettingsScene::menuFAQCallback(CCObject* pSender)
	{						
		ECServiceCocos2dx::showFAQs (
					{ 
						hs-custom-metadata＝｛
						hs-tags＝'vip,pay1',
						VersionCode＝'3'
						｝
					});
	}

**About Parameters：**

- __config__: Custom ValueMap information. You can pass specific Tag information using vector hs-tags, see above coding example. Please note you also need to configure the same tag information in the Web console to make each conversation be correctly tagged.
	
![showElva](https://github.com/AI-HELP/Docs-Screenshots/blob/master/showFAQs-EN-Android.png "showFAQs")

**Best Practice：**
> 1. Use this method to show FQAs about your APP/Game properly. Configure FAQs in AIHelp Web Console. Each FAQ can be categroized into a section. If the FAQs are many, you can also add Parent Sections to categorize sections to make things clear and organized. 

#### <h4 id="showSingleFAQ">5. Show A Specific FAQ，use`showSingleFAQ`
</h4>

	ECServiceCocos2dx::showSingleFAQ(string faqId);

or

	ECServiceCocos2dx::showSingleFAQ(
				string faqId,
				cocos2d::ValueMap& config);

**Coding Example：**

	// Presenting FAQs to your customers
	void GameSettingsScene::menuFAQCallback(CCObject* pSender)
	{						
		ECServiceCocos2dx::showSingleFAQ (
					"20",
					{ 
						hs-custom-metadata＝｛
						hs-tags＝'vip,pay1',
						VersionCode＝'3'
						｝
					});
	}

**About Parameters：**

- __faqId__: The PublishID of the FAQ item, you can check it in [AIHelp Web Console](https://aihelp.net/elva): Find the FAQ in the FQA menu and copy its PublishID.
- __config__: Custom ValueMap information. You can pass specific Tag information using vector hs-tags, see above coding example. Please note you also need to configure the same tag information in the Web console to make each conversation be correctly tagged.
	
![showSingleFAQ](https://github.com/AIHELP-NET/Pictures/blob/master/showSingleFAQ-EN-Android.png "showSingleFAQ")

<h4 id="selfservice"></h4>

**Self-Service：**

If you configure the "self-service" link in FAQ's configuration, and set [UserId](#UserId), [UserName](#UserName), [ServerId](#ServerId) in SDK，then the FAQ shows with functional menu in the right-top cornor.


**Best Practice：**
> 1. Use this method when you want to show a specific FAQ in a proper location of your APP/Game.

#### <h4 id="setName">6. Set Your App's name for AIHelp SDK to display，use`setName`</h4>

	ECServiceCocos2dx::setName(string game_name);

**Coding Example：**

	ECServiceCocos2dx::setName("Your Game");

**About Parameters：**

- __game_name__: APP/Game name

**Best Practice：**
> 1. Use this method after SDK initialization.The App's name will display in the title bar of the customer service interface.

#### <h4 id="UserId">7. Set the Unique User ID, use `setUserId`</h4>


	ECServiceCocos2dx::setUserId(string playerUid);

**Coding Example：**

	ECServiceCocos2dx::setUserId("123ABC567DEF");

**About Parameters：**

- __playerUid__:Unique user ID

**Best Practice：**
> 1. Normally you don't need to use this method if you have passed user ID in other method. However, if you want to use FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="UserName">8. Set User Name，use`setUserName`</h4>

	ECServiceCocos2dx::setUserName (string playerName);

**Coding Example：**

	ECServiceCocos2dx::setUserName ("PLAYER_NAME");

**About Parameters：**

- __playerName:__ User/Player Name

**Best Practice：**
> 1. Use this method to set the user name, which will be shown in the web console's conversation page for the user. You can address the user with this name during the chat.
> 2. Normally you don't need to use this method if you have passed user name in other method. However, if you want to use FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="ServerId">9. Set Unique Server ID，use`setServerId`
</h4>

	ECServiceCocos2dx::setServerId(int serverId);

**Coding Example：**

	ECServiceCocos2dx::setServerId("SERVER_ID");

**About Parameters：**

- __serverId:__ The unique server ID

**Best Practice：**
> 1. Normally you don't need to use this method if you have passed server ID in other method. However, if you want to use FAQ's [Self-Service](#selfservice), then you must set the User Id first.

#### <h4 id="showConversation">10. Launch VIP chat console, use`showConversation`(need to set [UserName](#UserName))</h4>

	ECServiceCocos2dx::showConversation(
					string playerUid,
					int serverId);

or

	ECServiceCocos2dx::showConversation(
					string playerUid,
					int serverId,
					cocos2d::ValueMap& config);
	
**Coding Example：**

	ECServiceCocos2dx::setUserName ("PLAYER_NAME");
	ECServiceCocos2dx::showConversation(
					"PLAYER_ID",
					123,
					{ 
						hs-custom-metadata＝｛
						hs-tags＝'vip,pay1',
						VersionCode＝'3'
						｝
					});

**About Parameters：**

- __playerUid__:Unique user ID
- __serverId:__ The unique server ID
- __config__: Custom ValueMap information. You can pass specific Tag information using vector hs-tags, see above coding example. Please note you also need to configure the same tag information in the Web console to make each conversation be correctly tagged.

**Best Practice：**
> 1. Normally you don't need to use this method unless you intend to allow users to enter VIP conversation without engaging with the AI chat. You may use this method as a privilege for some users.

![showConversation](https://github.com/AIHELP-NET/Pictures/blob/master/showConversation-EN-Android.png "showConversation")


#### <h4 id="setSDKLanguage">11. Set SDK Lanague，use`setSDKLanguage`
</h4>
Set SDK Language will change the FAQs, Operational information, AI Chat and SDK display language. 

	ECServiceCocos2dx::setSDKLanguage(string language);
	
**Coding Example：**

	ECServiceCocos2dx::setSDKLanguage("en");

**About Parameters：**

- __language:__ Standard Language Alias. For example: en is for English, zh_CN is for Simplified Chinese。More language label to check the AIHelp Web Console:"Settings"-->"Language"->Alias.

![language](https://github.com/AI-HELP/Docs-Screenshots/blob/master/Language-alias.png "Language Alias")

**Best Practice：**
> 1. Normally AIHelp will use the mobile's lanaguge configuration by default. If you intend to make a different language setting, you need to use this method right after SDK initialization.
> 2. If your allow users to change APP language, then you need to call this method to make AIHelp the same lanague with your APP.

#### 12. Set a different greeting story line.

If your APP provides multiple entries to AIHelp, and you intend to introduce different AI welcome text and story line to users from different entries, you can set config parameter in [showElva](#showElva) or [showElvaOP](#showElvaOP)： 

	map.put("anotherWelcomeText","usersay");

**Coding Example：**

	ArrayList<String> tags = new ArrayList();
	tags.add("vip");
	tags.add("pay1");
	HashMap<String,Object> map = new HashMap();
	map.put("hs-tags",tags);
	
	// note：anotherWelcomeText is key，should be unchanged.
	// you need to change usersay according to the "User Say" in your new 
	// story line
	map.put("anotherWelcomeText","usersay");
	HashMap<String,Object> config = new HashMap();
	config.put("hs-custom-metadata",map);
	

	ECServiceCocos2dx::showElva(
				"elvaTestName",
				"12349303258",
				1, 
				"",
				"1",
				config);
Or

	ECServiceCocos2dx::showElvaOP(
				"elvaTestName",
				"12349303258",
				1, 
				"",
				"1",
				config);


**Best Practice：**
> 1. Introduce different story lines to users from different source.




showConversationFlag