### 一.外部存储

1.getExternalStorageDirectory 用getExternalFilesDir（），MediaStore 或者Intent#ACTION_OPEN_DOCUMENT代替

```
//目录如下：
getExternalFilesDir=/storage/emulated/0/Android/data/应用包名/files/

    * @deprecated To improve user privacy, direct access to shared/external
     *             storage devices is deprecated. When an app targets
     *             {@link android.os.Build.VERSION_CODES#Q}, the path returned
     *             from this method is no longer directly accessible to apps.
     *             Apps can continue to access content stored on shared/external
     *             storage by migrating to alternatives such as
     *             {@link Context#getExternalFilesDir(String)},
     *             {@link MediaStore}, or {@link Intent#ACTION_OPEN_DOCUMENT}.
     */
    @Deprecated
    public static File getExternalStorageDirectory() {
        throwIfUserRequired();
        return sCurrentUser.getExternalDirs()[0];
    }
```  
2.  存储路径及访问情况。

#### 总结：无论是/data/还是/sdcard下，数据读写只能是在沙盒中，否则不能直接读写。

```
      File dir=Environment.getDownloadCacheDirectory();// /data/cache/ （无权限）
      File dir=Environment.getDataDirectory();// /data/     （无权限）
      File dir=Environment.getRootDirectory();// /system/ （只读）
      File dir=Context.getFilesDir();// /data/user/0/包名/files/ (可读写)
      Context.getExternalFilesDir("xx")=/storage/emulated/0/Android/data/应用包名/files/xx/(可读写)
      Context.getApplicationInfo().dataDir；//  /data/user/0/包名  (可读写)
      Context.getDir(“xx”,MODE_PRIVATE) // /data/user/0/包名/app_xx  (可读写)
      Context.getCacheDir()// /data/user/0/包名/cache/ (可读写)
      注：若报错(open failed: ENOENT)，可能是文件或文件夹不存在。
```
3. 非沙盒中的路径文件读写。
##### 注意：所有非沙盒文件，可通过ContentProvider查询文件uri，然后通过文件Uri获取文件内容，不能直接通过路径读取。
#####    如果是媒体文件，不同应用可通过mediastore接口读，不可直接修改其他应用生成的文件

参考文档： https://developer.huawei.com/consumer/cn/doc/app/50127

通过uri读取代码如下：
```
public static Bitmap getBitmapFromUri(Context context, Uri uri) throws IOException {
    ParcelFileDescriptor parcelFileDescriptor =
            context.getContentResolver().openFileDescriptor(uri, "r");
    FileDescriptor fileDescriptor = parcelFileDescriptor.getFileDescriptor();
    Bitmap image = BitmapFactory.decodeFileDescriptor(fileDescriptor);
    parcelFileDescriptor.close();
    return image;
}
```
#### 总结如下：非沙盒可以通过saf和置默认系统应用，contentprovider查找的uri去读取媒体文件，mediastore接口操作媒体文件

1)saf,可以读写所有公共目录文件（两种方式）
   a)类似系统文件浏览器功能，ACTION_OPEN_DOCUMENT_TREE
   Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT_TREE);
   startActivityForResult(intent, REQUEST_CODE_OPEN_DIRECTORY);
   通过getContentResolver().takePersistableUriPermission(uri, takeFlags)判断是否有权限。
   授权目录可以保存在shareparence中。
   
   b）通过ACTION_CREATE_DOCUMENT或ACTION_OPEN_DOCUMENT
   Intent intent = new Intent(Intent.ACTION_CREATE_DOCUMENT);
   Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT);
   
2）如果是媒体文件（图片，视频，音乐），可设置默认系统应用

3）如果是媒体文件，可用mediastore接口（插入）


4.扩展：webview相关缓存
```
webview缓存构成
/data/data/package_name/cache/
/data/data/package_name/database/webview.db
/data/data/package_name/database/webviewCache.db
h5缓存
1、缓存构成
根据setAppCachePath(String appCachePath)提供的路径，在H5使用缓存过程中生成的缓存文件。
2、缓存模式
无模式选择，通过setAppCacheEnabled(boolean flag)设置是否打开。默认关闭，即，H5的缓存无法使用。
3、清除缓存
找到调用setAppCachePath(String appCachePath)设置缓存的路径，把它下面的文件全部删除就OK了
4、控制大小
通过setAppCacheMaxSize(long appCacheMaxSize)设置缓存最大容量，默认为Max Integer。
同时，可能通过覆盖WebChromeClient.onReachedMaxAppCacheSize(long requiredStorage, long quota, WebStorage.QuotaUpdater quotaUpdater)来设置缓存超过先前设置的最大容量时的策略。
```




### 二.imei ，mac地址
```
报错：
java.lang.SecurityException: getUniqueDeviceId: The user 10221 does not meet the requirements to access device identifiers.
```

目前可用appid代替(注：安卓q之后，mac地址随机会变)

### 三.不允许调用后台的activity,除非以下情况：
1.直接点应用小图标
2.多进程状态
3.widget
4.通知页面调起
5.前台activity调起后台应用

###  四.定位
1.新增权限ACCESS_BACKGROUND_LOCATION
老的权限：ACCESS_FINE_LOCATION和ACCESS_COARSE_LOCATION 只会前台使用
如果 targetsdk< Q  ACCESS_FINE_LOCATION和ACCESS_COARSE_LOCATION会自动转ACCESS_BACKGROUND_LOCATION

2.如果用户选择仅前台使用允许，应用的页面退后台，通过启动前台服务让应用处于前台状态，必须把前台服务标为：foregroundServiceType=“location”，才能获取位置信息。

3.最佳实现：建议应用升级TargetSdkVersion到Q，只申请老的位置权限，不要申请ACCESS_BACKGROUND_LOCATION权限。这样系统弹框只有允许前台使用的选项：

### 五.安装

用file provider代替file:\\url

### 六.相机信息权限

如果没有CAMERA权限，获取到的cameraCharacteristics.get(CameraCharacteristics.LENS_POSE_ROTATION)值为null

### 七、Wi-Fi

1.启用和禁用Wi-Fi的限制

WifiManager.setWifiEnabled()始终是false，需要交互的方式设置
```
Intent panelIntent = new Intent(Settings.Panel.ACTION_INTERNET_CONNECTIVITY);

startActivityForResult(panelIntent, 100);
```
2.Wi-Fi网络列表的手动配置限制

### 八.电话，Wi-Fi，蓝牙API所需的精确位置权限

1、应用的targetSdkVersion>=Q：除非应用具有ACCESS_FINE_LOCATION权限，否则在Android Q上运行时，应用无法在Wi-Fi，Wi-Fi Aware或蓝牙API中使用多种方法。

2、应用的targetSdkVersion < Q：不受影响，只需要申请ACCESS_COARSE_LOCATION或者ACCESS_FINE_LOCATION即可

### 九.应用无法后台访问剪切板数据

### 十.USB序列号增加新的敏感权限,非系统应用无法获取USB序列号

### 十一.权限

1.TargetSdkVersion<=22，则用户首次在Android Q上运行您的应用时会看到权限页面
2.TargetSdkVersion>=Q ，新增敏感权限：ACTIVITY_RECOGNITION，Android Q为需要检测用户移动的应用程序（例如步行，骑自行车或车辆）

### 十二. 非SDK接口管控

1.https://developer.android.com/reference/packages，谷歌这个网站能查到的接口都是SDK接口；

2.查看管控接口方法

a)通过日志测试:greylist 灰度列表

```
Accessing hidden field Landroid / os / Message; - > flags: I(light greylist, JNI)
```
b）通过命令测试。安卓q中强制非sdk管控策略
```
adb shell settings put global hidden_api_policy  1

说明：您可以将API强制策略中的整数设置为以下值之一：

0：禁用所有非SDK接口检测。使用此设置会禁用非SDK接口使用的所有日志消息，并阻止您使用StrictMode API测试应用程序。建议不要使用此设置。

1：允许访问所有非SDK接口，但打印日志消息，并显示任何非SDK接口使用情况的警告。使用此设置还允许您使用StrictMode API测试您的应用程序。

2：禁止使用属于黑名单的非SDK接口或目标API级别的受限制灰名单。

建议开发者设置成1，再通过自动化，或者是人工测试遍历应用每一个界面和所有功能，然后抓日志分析调用的所有非SDK接口。
```
c) 静态扫描，通过谷歌提供的veridex扫描工具扫描获取

d)给谷歌申请灰名单
申请链接：https://partnerissuetracker.corp.google.com/issues/new?component=328403&template=1027267

### 十三.并发录音

安卓Q录音会被抢占。

###  十四.TargetSdkVersion等级限制（2019年上架谷歌Play商店对应用的TargetSdkVersion要求）
  1.基本要求
（1）新开发的应用：2019-8-1之后，上架谷歌Play商店要求应用的TargetSdkVersion>=28；

（2）更新的应用：2019-11-1之后，上架谷歌Play商店要求应用的TargetSdkVersion>=28。


 2.最小TargetSdkVersion要求：当用户首次运行 API 低级低于 23 (Android Marshmallow) 的应用时，会受到来自 Android Q 的警告信息。
 
### 十五.内联方法不允许跨dex限制

### 十六.前台服务增加权限

针对 Android 9 或更高版本并使用前台服务的应用必须请求 FOREGROUND_SERVICE 权限。这是普通权限，因此，系统会自动为请求权限的应用授予此权限

### 十七.TargetSdkVersion < 28 针对不同版本有影响

谷歌适配指导链接：https://developer.android.google.cn/distribute/best-practices/develop/target-sdk.html

重点关注以下特性：

（1）6.0：

（a）运行时权限：https://developer.android.google.cn/training/permissions/requesting.html

（2）7.0：

（a）低电耗模式和应用待机模式：https://developer.android.google.cn/training/monitoring-device-state/doze-standby.html

（b）文件共享需要使用FileProvider：https://developer.android.google.cn/training/secure-file-sharing/setup-sharing.html

（c）系统禁止链接到非 NDK 库：https://developer.android.google.cn/about/versions/nougat/android-7.0-changes.html#ndk

（3）8.0：

（a）后台执行限制：https://developer.android.google.cn/about/versions/oreo/background.html

（b）通知渠道：https://developer.android.google.cn/about/versions/oreo/android-8.0.html#notifications

（c）为每个应用签名密钥指定 ANDROID_ID：https://developer.android.google.cn/about/versions/oreo/android-8.0-changes.html#privacy-all

### 十八.动态库需要64位支持。

参考文档：https://developer.huawei.com/consumer/cn/doc/app/50127

（1）Google Q版本系统镜像下载：https://developer.android.com/preview/download.html

（2）刷机指导：https://source.android.com/source/running#unlocking-the-bootloader

（3）Google android code下载方法：https://source.android.com/source/downloading.html

（4）Android 10隐私安全变更：https://developer.android.com/preview/privacy

（5）对所有应用有影响的行为变更：https://developer.android.com/preview/behavior-changes-all

（6）对以Q为目标的应用有影响的行为变更：https://developer.android.com/preview/behavior-changes-q

（7）Q版本适配流程：https://developer.android.com/preview/overview

