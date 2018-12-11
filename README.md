# ZADDemo
演示版是 targetSdkVersion 26 开发的。

```
当 targetSdkVersion >=24 需要添加如下路径
  <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="${applicationId}.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/zad_file_path" />
        </provider>
        ```
