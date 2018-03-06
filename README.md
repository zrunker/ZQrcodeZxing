# ZQrcodeZxing
利用ZXing库实现二维码扫描和生成二维码功能。

>作者：邹峰立，微博：zrunker，邮箱：zrunker@yahoo.com，微信公众号：书客创作，个人平台：[www.ibooker.cc](http://www.ibooker.cc)。

>本文选自[书客创作](http://www.ibooker.cc)平台第40篇文章。[阅读原文](http://www.ibooker.cc/article/40/detail) 。

![书客创作](http://upload-images.jianshu.io/upload_images/3480018-83bc916b2d71be04..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

二维码最早是由日本的一家公司定义的一种编码，它包括四角定位和中央编码等多部分组成。随着智能手机的普及，二维码也越来越受各种App的喜爱。而二维码的生成和扫描分析是一个非常复杂的过程，ZXing是谷歌公司的一个开源工程，非常方便的解决二维码的生成和扫描。

### ZXing使用

先看一下最终效果图：

![最终效果图](http://upload-images.jianshu.io/upload_images/3480018-2d424538334b03fb..gif?imageMogr2/auto-orient/strip)
#### 一、下载ZXing源码

[ZXing的github地址](https://github.com/zxing/zxing)，Github上的源码太大，我这边提供一份，只征对于Android二维码扫描和生产的[ZXing库3.1](https://github.com/zrunker/libzxing)。

下载ZXing3.1库，是一个libzxing的Module文件。  

#### 二、ZXing库导入工程  

下载下来的ZXing库文件，实际上是一个Module，这里以Android Studio2.2.3（简称AS）为例，说明如何导入工程。  

1、AS导入ZXing库Module  

点击File选择New选择Import Module...  

![导入ZXing库Module1](http://upload-images.jianshu.io/upload_images/3480018-e224bcc9ccb56095..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
找到下载到本地的Zxing库，导入。  

![导入本地的Zxing库2](http://upload-images.jianshu.io/upload_images/3480018-3fc25db4770c7595..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
导入成功工程变化，工程结构图。  

![工程结构图](http://upload-images.jianshu.io/upload_images/3480018-3861fb0977c8689b..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
2、自己的工程引入Zxing库Module。  

![第一步](http://upload-images.jianshu.io/upload_images/3480018-fbda022f866f312e..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![第二步](http://upload-images.jianshu.io/upload_images/3480018-4e19ebda8189f79c..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![第三步](http://upload-images.jianshu.io/upload_images/3480018-65c43199a205d80e..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
这时候引入可能会出现下面的错误情况。  

![错误情况1](http://upload-images.jianshu.io/upload_images/3480018-c6dade7ec5d9308f..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
解决办法：这种情况是指目前你的Android Studio Tools版本不支持libzxing中的版本，直接点击下载即可。  

![错误情况2](http://upload-images.jianshu.io/upload_images/3480018-834bdd9ce90bbdd3..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
解决办法：这种错误是指libzxing中最小版本为16，而在主工程中最小版本为15，所以在修改主工程build.gradle最小版本为16就好。  

![错误情况3](http://upload-images.jianshu.io/upload_images/3480018-9527bfdab64e5d1e..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
解决办法：  
首先对比对比以下两张图，图一和图二。  

![libzxing清单文件](http://upload-images.jianshu.io/upload_images/3480018-4b08efae09daaa6a..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![自己工程清单文件](http://upload-images.jianshu.io/upload_images/3480018-118590518aeddfa4..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

导入过程中可能会出现引入失败的情况。下载好的Zxing库版本与自己新建工程版本要一致，如果不一致可以手动进入自己的工程或者Zxing库工程AndroidManifest.xml修改版本。Application的设置也要一致，如果出现不一致也会引入Zxing失败，同样自己去修改清单文件AndroidManifest.xml。  

#### 三、代码实现  

**1、布局XML**  
```
<?xml version="1.0" encoding="utf-8"?>  
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:background="#FFF"  
    android:scrollbars="none">  

    <LinearLayout  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:orientation="vertical">  

        <TextView  
            android:id="@+id/tv_scan_result"  
            android:layout_width="wrap_content"  
            android:layout_height="wrap_content"  
            android:padding="15dp"  
            android:text="显示扫描结果字符串" />  

        <Button  
            android:id="@+id/btn_scan_barcode"  
            android:layout_width="wrap_content"  
            android:layout_height="wrap_content"  
            android:layout_gravity="center_horizontal"  
            android:layout_margin="10dp"  
            android:text="扫描二维码" />  

        <EditText  
            android:id="@+id/et_qr_string"  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"  
            android:inputType="text"  
            android:padding="15dp" />  

        <ImageView  
            android:id="@+id/iv_qr_image"  
            android:layout_width="wrap_content"  
            android:layout_height="wrap_content"  
            android:layout_gravity="center"  
            android:contentDescription="@null"  
            android:padding="10dp"  
            android:src="@mipmap/ic_launcher" />  

        <CheckBox  
            android:id="@+id/logo"  
            android:layout_width="wrap_content"  
            android:layout_height="wrap_content"  
            android:layout_margin="15dp"  
            android:text="是否显示logo(图片)" />  

        <Button  
            android:id="@+id/btn_add_qrcode"  
            android:layout_width="wrap_content"  
            android:layout_height="wrap_content"  
            android:layout_gravity="center_horizontal"  
            android:layout_margin="10dp"  
            android:text="生成二维码" />  

    </LinearLayout>  
</ScrollView>  
```
在Activity界面当中初始化控件  
```
TextView resultTextView = (TextView) this.findViewById(R.id.tv_scan_result);  
EditText qrStrEditText = (EditText) this.findViewById(R.id.et_qr_string);  
ImageView qrImgImageView = (ImageView) this.findViewById(R.id.iv_qr_image);  
CheckBox mCheckBox = (CheckBox) findViewById(R.id.logo);  
Button scanBarCodeButton = (Button) this.findViewById(R.id.btn_scan_barcode);  
Button generateQRCodeButton = (Button) this.findViewById(R.id.btn_add_qrcode);  
```
**2、扫描二维码**  

在导入的ZXing库中，有一个CaptureActivity的页面，如果要扫描二维码启动该页面即可，如何接收该页面的回调值，就可以得到扫描出来的信息。  

```
// 打开扫描界面扫描条形码或二维码  
Intent openCameraIntent = new Intent(MainActivity.this, CaptureActivity.class);  
startActivityForResult(openCameraIntent, 0);  
```
接收返回值  
```
@Override  
protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
     super.onActivityResult(requestCode, resultCode, data);  
     if (resultCode == RESULT_OK) {  
         Bundle bundle = data.getExtras();  
         String scanResult = bundle.getString("result");  
         resultTextView.setText(scanResult);  
     }  
}  
```
**3、生成二维码**  

对于生成二维码，目前支持两种样式，一种是没有logo的二维码，一种是有logo的二维码。  
```
String contentString = qrStrEditText.getText().toString();  
   if (!TextUtils.isEmpty(contentString)) {  
      // 根据字符串生成二维码图片并显示在界面上，第二个参数为图片的大小（350*350）  
      Bitmap qrCodeBitmap = EncodingUtils.createQRCode(contentString, 350, 350,mCheckBox.isChecked() ? BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher) : null);  
      qrImgImageView.setImageBitmap(qrCodeBitmap);  
   } else {  
      Toast.makeText(MainActivity.this, "Text can not be empty", Toast.LENGTH_SHORT).show();  
}  
```
要生成二维码需要借助于ZXing库中的EncodingUtils类中的createQRCode，该方法有四个参数，分别是String content, int widthPix, int heightPix, Bitmap logoBm。content表示上下文对象、widthPix表示生成二维码宽度以px为单位、heightPix表示生成二维码高度以px为单位、logoBm表示图标，如果不显示图片就设置其为null。  

**注意：在扫描二维码的时候可能存在一个IO异常。**
这是因为在Android6.0+之后的版本调用照相机需要动态申请权限，否则无法启动相机。

动态申请相机权限：
```
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
        // 申请CAMERA权限
        ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CAMERA}, 1);
}
```
权限申请结果处理：
```
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if(1 == requestCode) {
         // todo 做相应的处理逻辑
    }
}
```

[Github地址](https://github.com/zrunker/ZQrcodeZxing)
[阅读原文](http://www.ibooker.cc/article/40/detail)

----------
![微信公众号：书客创作](http://upload-images.jianshu.io/upload_images/3480018-0714fc08b5280681..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
