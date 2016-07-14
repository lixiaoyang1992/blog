---
title: 在ART获取权限
date: 2016-05-30 08:41:01
tags: Android
category : Android
---

入手SamsungGalaxyS7edge两个月了，实际上手android6.0也2个月了，对于新的权限系统有了切实的体验，也领略了微博等国产应用的无耻。今天参照 [官方的指南](https://developer.android.com/training/permissions/index.html) 也写一遍如何在android6.0中获取权限。
<!-- more -->
## 声明权限  

第一步和之前一样，在 Manifest 里面把我们需要的权限声明。  

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="cn.lixiaoyang.permissions">

      <uses-permission android:name="android.permission.SEND_SMS"/>

      <application
          android:allowBackup="true"
          android:icon="@mipmap/ic_launcher"
          android:label="@string/app_name"
          android:supportsRtl="true"
          android:theme="@style/AppTheme">
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:theme="@style/AppTheme.NoActionBar">
          <intent-filter>
            <action android:name="android.intent.action.MAIN"/>

            <category android:name="android.intent.category.LAUNCHER"/>
          </intent-filter>
        </activity>
      </application>

    </manifest>

## 运行时获取权限  

在android6.0之后，用户在应用运行时给权限，而不是在安装的时候。这给了用户更大的自由来决定给应用什么权限。必入，用户可以给一个照相应用访问摄像头的权限，而不给获取位置信息的权限。  

### 检查权限  

首先要检查是否获得了权限

    int permissionCheck = ContextCompat.checkSelfPermission(thisActivity, Manifest.permission.SEND_SMS);

### 获取权限  

    if (ContextCompat.checkSelfPermission(thisActivity, Manifest.permission.SEND_SMS)
            != PackageManager.PERMISSION_GRANTED) {

          if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
              Manifest.permission.SEND_SMS)) {
            //用户不给权限的友好提示,引导用户去权限设置页
            alertNeedPermission();
          } else {

            // 获取权限

            ActivityCompat.requestPermissions(thisActivity,
                new String[] { Manifest.permission.SEND_SMS }, MY_PERMISSIONS_REQUEST_SEND_SMS);

            // 回调的requestCode
          }
        } else {
          sendSMS();
        }

### 回调

    @Override public void onRequestPermissionsResult(int requestCode, String permissions[],
          int[] grantResults) {
        switch (requestCode) {
          case MY_PERMISSIONS_REQUEST_SEND_SMS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

              sendSMS();
              // permission was granted, yay! Do the
              // contacts-related task you need to do.

            } else {

              alertNeedPermission();
              // permission denied, boo! Disable the
              // functionality that depends on this permission.
            }
            return;
          }

          // other 'case' lines to check for other
          // permissions this app might request
        }
      }

其他方法  

      public void alertNeedPermission() {
        new AlertDialog.Builder(MainActivity.this).setMessage("app需要开启权限才能使用此功能")
            .setPositiveButton("设置", new DialogInterface.OnClickListener() {
              @Override public void onClick(DialogInterface dialogInterface, int i) {
                Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
                intent.setData(Uri.parse("package:" + getPackageName()));
                startActivity(intent);
              }
            })
            .setNegativeButton("取消", null)
            .create()
            .show();
      }

      public void sendSMS() {
        Intent intent = new Intent(Intent.ACTION_SENDTO, Uri.parse("smsto:" + 10086));
        intent.putExtra("sms_body", "短信");
        startActivity(intent);
      }

## 最佳实践  

1. 只获取你需要的权限；
1. 告诉用户你为什么需要这个权限；
1. 不要一口气把所有需要的权限都申请下来，只在你需要的时候申请。

本文代码[Github](https://github.com/lixiaoyang1992/android_permission)
