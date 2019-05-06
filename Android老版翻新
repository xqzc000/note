## 升级到26才能在googleplay发布，老的版本会遇到以下问题。
### 1.网络方面

- httpclient如果想继续使用，则使用以下版本：

httpclient-4.3.6.jar（必须）
httpcore-4.3.3.jar（必须）
httpmime-4.0.1.jar（上传必须）

slf4j-android-1.5.8.jar （非必须，日志相关）
apache-mime4j-0.6345035.jar（非必须，日志相关）
commons-logging-1.1.3.jar（非必须，日志相关）

否则使用okhttp或者HttpUrlConnection


- Android P 使用HttpUrlConnection进行http请求会出现以下异常

 W/System.err: java.io.IOException: Cleartext HTTP traffic to **** not permitted
使用OKHttp请求则出现

java.net.UnknownServiceException: CLEARTEXT communication ** not permitted by network security policy

解决：
在res文件夹下创建一个xml文件夹，然后创建一个network_security_config.xml文件，文件内容如下：
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
接着，在AndroidManifest.xml文件下的application标签增加以下属性
 <uses-library android:name="org.apache.http.legacy" android:required="false" />


### 2.样式报错
style，按提示改。然后原始弹框会有问题。
注意androidmanifest 报错的话，点击下面mergedmanifest，看报错信息。

### 3.权限报错
```
activity中：

if(PermissionUtil.hasPermission(MainActivity.this,PermissionUtil.PERMISSIONS[3])) {
			//如果有权限，执行接下来方法。
}else{
            //如果没有权限，申请
		requestPermissions(new String[]{PermissionUtil.PERMISSIONS[3]},3);
          
	}

@Override
	public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
		
			for (int i = 0; i < PermissionUtil.PERMISSIONS.length; i++) {
				if (requestCode == i) {
					if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
						if (requestCode == 1) {
						   //执行方法
						}else if(requestCode == 2){
                                                 //执行方法
                                                }else ....


					} else {
						Toast.makeText(this.getApplicationContext(), PermissionUtil.PERMISSIONS[i] + "has not granted!", 1).show();
					}
					break;
				}
			}
	}
```
```

public class PermissionUtil {

    public static final String[] PERMISSIONS=new String[]{
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE,
            Manifest.permission.INTERNET,
            Manifest.permission.READ_PHONE_STATE
    };

    /**统一查询并申请所有权限
     *
     * @param activity
     * @return
     */
    public static boolean hasPermissionAndRequest(Activity activity){
        if(Build.VERSION.SDK_INT<23)
            return true;
        boolean bool=true;
        for (int i=0;i<PERMISSIONS.length;i++){
            if(activity.checkSelfPermission(PERMISSIONS[i])!=PERMISSION_GRANTED){
                bool=false;
                activity.requestPermissions(new String[]{PERMISSIONS[i]},i);
                break;
            }

        }
        return bool;
    }


    /**
     * 未找到权限也返回false
     * @param activity
     * @param permission
     * @return
     */
    public static boolean hasPermission(Activity activity ,String permission){
        if(Build.VERSION.SDK_INT<23)
            return true;
        for (int i=0;i<PERMISSIONS.length;i++){
            if(PERMISSIONS[i].equals(permission))
                return activity.checkSelfPermission(PERMISSIONS[i]) == PERMISSION_GRANTED;

        }
        return false;
    }

    public static int getPermissionRequestCode(String permission){
        for (int i=0;i<PERMISSIONS.length;i++){
            if(PERMISSIONS[i].equals(permission)){
                return i;
            }
        }
        return -1;
    }
}

```
### 4.文件报错
在AndroidManifest.xml文件下的application标签增加以下属性
```
<!-- 在这里定义共享信息 -->
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="包名.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/filepaths" />
        </provider>
```
  接着，res/xml下添加filepaths.xml，内容如下：
```
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path path="/xx" name="xx" />


    <!--<files-path> 分享app内部的存储；-->
    <!--<external-path> 分享外部的存储；-->
    <!--<cache-path> 分享内部缓存目录-->
</paths>
```
最后调用：
```
Uri uri = FileProvider.getUriForFile(this, this.getPackageName() +".fileprovider", new File(path));
```

### 5.第三方jar包有配置文件导致的报错。

apply plugin: 'com.android.application'
android {
//添加以下配置
packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
    }
}

### 其他api变化

