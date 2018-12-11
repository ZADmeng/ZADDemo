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
# 权限动态申请
```
  private void initView() {
        main = (LinearLayout) findViewById(R.id.main);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
            checkAndRequestPermission();
        else
            handler.sendEmptyMessage(2001);
    }

    private void init() {

        new ZADSplash(this, main, "zm_ad_1_1000", new ZADSplashListener() {
            @Override
            public void onNoAD(AdError error) {
                Log.e("错误" + error.getErrorCode(), error.getErrorMsg());
//                main.post(new Runnable() {
//                    @Override
//                    public void run() {
//                        handler.sendEmptyMessage(2001);
//                    }
//                });
//                if(error.getErrorCode()==2001){
//                    Intent intent = new Intent(LoadingActivity.this, MainActivity.class);
//                    startActivity(intent);
//                    finish();
//                }
            }

            @Override
            public void onADDismissed() {
                Intent intent = new Intent(LoadingActivity.this, MainActivity.class);
                startActivity(intent);
                finish();
            }

            @Override
            public void onADPresent() {

            }

            @Override
            public void onADClicked() {

            }

            @Override
            public void onADExposure() {

            }
        });
    }

    // 如果targetSDKVersion >= 23，那么必须要申请到所需要的权限，再调用SDK，否则SDK不会工作。
    @TargetApi(Build.VERSION_CODES.M)
    private void checkAndRequestPermission() {
        List<String> lackedPermission = new ArrayList<String>();
        if (!(checkSelfPermission(Manifest.permission.READ_PHONE_STATE) == PackageManager.PERMISSION_GRANTED)) {
            lackedPermission.add(Manifest.permission.READ_PHONE_STATE);
        }

        if (!(checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED)) {
            lackedPermission.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
        }

        if (!(checkSelfPermission(Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED)) {
            lackedPermission.add(Manifest.permission.ACCESS_FINE_LOCATION);
        }

        // 权限都已经有了，那么直接调用SDK
        if (lackedPermission.size() == 0) {
            handler.sendEmptyMessage(2001);
        } else {
            // 请求所缺少的权限，在onRequestPermissionsResult中再看是否获得权限，如果获得权限就可以调用SDK，否则不要调用SDK。
            String[] requestPermissions = new String[lackedPermission.size()];
            lackedPermission.toArray(requestPermissions);
            requestPermissions(requestPermissions, 1024);
        }
    }

    private boolean hasAllPermissionsGranted(int[] grantResults) {
        for (int grantResult : grantResults) {
            if (grantResult == PackageManager.PERMISSION_DENIED) {
                return false;
            }
        }
        return true;
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == 1024 && hasAllPermissionsGranted(grantResults)) {
            //得到权限开始开屏
        } else {
            // 如果用户没有授权，那么应该说明意图，引导用户去设置里面授权。
            Toast.makeText(this, "应用缺少必要的权限！请点击\"权限\"，打开所需要的权限。", Toast.LENGTH_LONG).show();
            Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
            intent.setData(Uri.parse("package:" + getPackageName()));
            startActivity(intent);
            finish();
        }
    }
```
