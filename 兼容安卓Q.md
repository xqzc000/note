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
```
#### 总结：无论是/data/还是/sdcard下，数据读写只能是在沙盒中，否则不能直接读写。

二.imei 
```
报错：
java.lang.SecurityException: getUniqueDeviceId: The user 10221 does not meet the requirements to access device identifiers.
```

目前可用appid代替
