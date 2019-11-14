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

```
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

```

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




### 二.imei 
```
报错：
java.lang.SecurityException: getUniqueDeviceId: The user 10221 does not meet the requirements to access device identifiers.
```

目前可用appid代替
