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

```
      File dir=Environment.getDownloadCacheDirectory();// /data/cache/ （无权限）
      File dir=Environment.getDataDirectory();// /data/     （无权限）
      File dir=Environment.getRootDirectory();// /system/ （只读）
      File dir=Context.getFilesDir();// /data/user/0/包名/files/ (可读写)
      Context.getExternalFilesDir=/storage/emulated/0/Android/data/应用包名/files/
      
      
      getApplicationInfo().dataDir；//  /data/user/0/
      getDir()
```
3. 非沙盒中的路径文件读写。

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
#### 总结：无论是/data/还是/sdcard下，数据读写只能是在沙盒中，否则不能直接读写。



### 二.imei 
```
报错：
java.lang.SecurityException: getUniqueDeviceId: The user 10221 does not meet the requirements to access device identifiers.
```

目前可用appid代替
