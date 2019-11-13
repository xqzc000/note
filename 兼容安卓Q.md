1.外部存储

getExternalStorageDirectory 用getExternalFilesDir（），MediaStore 或者Intent#ACTION_OPEN_DOCUMENT代替

```
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
    

2.imei 
```
报错：
java.lang.SecurityException: getUniqueDeviceId: The user 10221 does not meet the requirements to access device identifiers.
```

目前可用appid代替
