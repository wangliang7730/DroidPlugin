Droid Plugin
======

纵览了前面的所有插件化技术，你会发现，它们都是基于单进程的。这就是说，插件更新只能等到App重新启动才能生效。

但是我们的用户大都是不懂得如何重启App的。这就导致了插件升级后的更新率并不高，两周时间也就50%的升级率，然后App就又发大版本了。

对于这个问题，360推出了DroidPlugin的插件化技术，它是基于多进程的思想。比如说一个App中有吃喝玩乐4个插件，如果“吃”这个插件有升级，DroidPlugin就可以把正在运行的“吃”的旧版本的这个进程杀掉，然后运行新的插件版本。

目前看起来，对于电商、O2O、OTA这样多业务线、并偏重于闭环的App而言，DroidPlugin是一个终极解决方案

[中文文档](readme_cn.md "中文文档")

DroidPlugin is a new **Plugin Framework** developed and maintained by the Android app-store team at Qihoo 360 (NYSE:QIHU).
It enables the host app run any third-party apk without installation, modification and repackage, which benefit a lot for collaborative development on Android.

-------



##Problems to be solved:
    
 1. Unable to send `Notification` with custom Resources，eg：
 
     a.  Notification with custom RemoteLayout, which means `Notification`'s `contentView`，`tickerView`，
     `bigContentView` and `headsUpContentView` must be null.

     b.  Notification with icon customized by R.drawable.XXX. The framework will transform it to Bitmap instead.

 2. Unable to define specified `Intent Filter` for the plugged app's `Service`、`Activity`、`BroadcastReceiver`
 and `ContentProvider`. So the plugged app is invisible for the outside system and app.

 3. Lack of `Hook` to the `Native` layer, thus apk (e.g. a majority of game apps) with `native` code cannot be loaded as plugin.
    
##Features：
  1. Compatible to Android 2.3 and later versions
  2. Given its .apk file, the plugged app could be run either independently or as plugin of the host, **NO** source code needed.
  3. Unnecessary to register the plugged app's `Service`、`Activity`、`BroadcastReceiver`、`ContentProvider` in the host.
  4. The plugged app are recognized as *Installed* by the host and other plugged apps
  5. Very low level of code invasion, in deed just one line code to integrate DroidPlugin into the host app.
  6. Complete code level separation between host and plugged apps, only system level message passing method provide by Android allowed.
  7. All system API supported
  8. Resources management are also completely separated between host and plugged apps.
  9. Process management for plugged apps, idle processed of the plugged app will be timely recycled to guarantee minimum memory usage.
  10. Static broadcast of plugged app will be treated as dynamic, thus the static broadcasting will never be trigger if
  the plugged app are not activated.
    
##Usage：

####Integrate with the host apps

It is very simple integrate Droid Plugin to your proejct：

1. Import Droid Plugin project to your project as a lib.

2. Include following attributes in host's `AndroidManifest.xml`：
	
		<application android:name="com.morgoo.droidplugin.PluginApplication" 
			android:label="@string/app_name"
			android:icon="@drawable/ic_launcher" >

           
3. Or, if you use customized `Application`，add following code in the methods `onCreate` and `attachBaseContext`:
    
		@Override
		public void onCreate() {
			super.onCreate();
			PluginHelper.getInstance().applicationOnCreate(getBaseContext()); //must behind super.onCreate()
		}
        
		@Override
		protected void attachBaseContext(Context base) {
			PluginHelper.getInstance().applicationAttachBaseContext(base);
            super.attachBaseContext(base);
		}

4.  **All**  `provider`'s `authorities` value in DroidPlugin's `Libraries\DroidPlugin\AndroidManifest.xml`
 default to be `com.morgoo.droidplugin_stub_P00`, e.g. :

		<provider
				android:name="com.morgoo.droidplugin.stub.ContentProviderStub$StubP00"
				android:authorities="com.morgoo.droidplugin_stub_P00"
				android:exported="false"
				android:label="@string/stub_name_povider" />

	You'd better change it to avoid conflict with other instances, e.g.:
		
		<provider
				android:name="com.morgoo.droidplugin.stub.ContentProviderStub$StubP00"
				android:authorities="com.example.droidplugin_stub_P00"
				android:exported="false"
				android:label="@string/stub_name_povider" />
    and change ```PluginManager.STUB_AUTHORITY_NAME``` to your value:

		PluginManager.STUB_AUTHORITY_NAME="com.example.droidplugin_stub"


####Install、Uninstall or Upgrade the plugged app：

1. **Install/Upgrade**, use this method：
 
		int PluginManager.getInstance().installPackage(String filepath, int flags);
   
	For installation, `filepath` set to path of the .apk file, and `flags` set to 0.

	For upgrade, `filepath` set to path of the .apk file, and  `flags` set to `PackageManagerCompat.INSTALL_REPLACE_EXISTING`.
        
    
2. **Uninstall**, use this method：

		int PluginManager.getInstance().deletePackage(String packageName,int flags);

	`packageName` is package name of the plugged app，`flags = 0`。

3. **Activate**

    Just use android's API, same for communication between components.

## Remark：

Please feel free to [report bugs](https://github.com/Qihoo360/DroidPlugin/issues) or ask for help via email.

##Who is using Droid Plugin?
	
 [360 App Store](http://sj.360.cn "360 App Store")

    
### Thanks：
    
    Translated by Ming Song（gnosoir@hotmail.com）    
