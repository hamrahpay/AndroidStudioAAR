## اطلاعیه
لطفا از کتابخانه جدید همراه پی در لینک زیر استفاده نمایید 
[کتابخانه جدید](https://github.com/hamrahpay/HamrahPay)


# AndroidStudioAAR
HamrahPay Android Studio AAR Library
# کتابخانه پرداخت درون برنامه ای همراه پی


---
## ویدئو آموزشی

[مشاهده ویدئو](http://www.aparat.com/v/JPt8K)



## نحوه اضافه کردن کتابخانه
به آپارات همراه پی مراجعه فرمایید 


## نحوه استفاده از کتابخانه
کافیست کد زیر را در اکتیویتی یا سرویس مورد نظرتان اضافه کنید:<br>
بعنوان مثال در رویداد کلیک دکمه پرداخت

### کد ساده
فقط با اضافه کردن این دستورات، میتوانید برنامه خود را اجرا کنید
```java
String yourSKU = "hp_5415e384f37bf802917441";   // شناسه کالای شما در سایت همراه پی
new HamrahPay(MainActivity.this)                // اکتیویتی که می خواهید از آنجا پرداخت انجام شود  
	.sku(yourSKU)                               // اضافه کردن شناسه به صفحه پرداخت
	.listener(new HamrahPay.Listener() {        // لیسنر برای آگاهی شما از موفق بودن یا نبودن پرداخت
	  @Override
	  public void onErrorOccurred(String status, String message) {
	      // مشکلی در پرداخت روی داده است یا کاربر پرداخت را انجام نداده است
	      Toast.makeText(MainActivity.this,message,Toast.LENGTH_SHORT).show();
	      Log.e("HamrahPay", status + ": " + message);
	  }
	
	  @Override
	  public void onPaymentSucceed(String payCode) {
	      // کاربر با موفقیت پرداخت را انجام داده است
	      Log.i("HamrahPay", "payCode: " + payCode);
	  }
	})
	.startPayment();    // با اضافه کردن این دستور عملیات پرداخت آغاز خواهد شد
```

### ذخیره خرید و چک کردن آن
با استفاده از کد زیر میتوان چک کرد که آیا کاربر محصول مورد نظر را خریداری کرده است یا خیر:
```java
if (HamrahPay.isPremium(MainActivity.this,yourSKU)) {        // چک کردن خرید با ورودی شناسه کالا
    payButton.setEnabled(false);                             // غیر فعال کردن دکمه خرید اگر پرداخت انجام شده باشد
}
```

### پیکربندی نوع پرداخت
دو نوع پرداخت در همراه پی پشتیبانی میگردد:

1. پرداخت به ازای هر دستگاه :‌با این نوع پرداخت هر فردی که نرم افزار را خریداری میکند فقط بر روی همان دستگاهی که خریداری کرده است میتواند از نرم افزار استفاده نماید و پرداخت برای همان دستگاه قابل شناسایی میباشد.

2. پرداخت به ازای ایمیل :‌ در این نوع پرداخت مکانیزمی طراحی شده است که هر فردی که نرم افزار را خریداری میکند بتواند بر روی گوشی دیگری هم نصب کنم . به طور مثال با یک بار نرم افزار شما را روی گوشی و تبلت خود نصب نماید. این روش توسط گوگل پلی و دیگر مارکت ها استفاده میگردد.

برای اینکار به کد ساده کد زیر را اضافه کنید:
```java
.verificationType(HamrahPay.DEVICE_VERIFICATION)    // حالت اول
.verificationType(HamrahPay.EMAIL_VERIFICATION)     // حالت دوم
```

### تغییر کد دستگاه به کد یکتا دلخواه
```java
.setCustomDeviceID(String deviceID) // در پارامتر آن کد دلخواه خود را وارد نمایید.
```
### چک کردن وضعیت پرداخت محصولات خریدنی
```java
// context : Context
// sku : شناسه محصول
HamrahPay.isPremium(Context context,String sku); 
```

### متدهای مفید برای محصولات مصرف کردنی
```java
HamrahPay hamrahPay = new HamrahPay(MainActivity.this);
// sku : شناسه محصول مصرف کردنی
// value : یک مقدار عددی صحیح . مثلا عدد 100
String sku="hp_xxxxxxxxxx";شناسه محصول شما در همراه پی
Integer value=100;
hamrahPay.addScore(sku,value,this); // اضافه کردن امتیاز / سکه / بنزین و غیره
hamrahPay.minusScore(sku,value,this); // کسر کردن امتیاز / سکه / بنزین و غیره
hamrahPay.getScore(sku,this); // دریافت میزان امتیاز / سکه / بنزین و غیره باقیمانده
```
### دریافت اصلاعات آخرین خرید برنامه 
این متد زمانی کارایی دارد که محصولات شما نیازمند اشتراک زمانی هستند و با دادن مشخصات کالا اطلاعات آخرین پرداخت مشتری را برمیگرداند که شامل تاریخ آخرین خرید ، تاریخ روز ، شماره رسید پرداخت و غیره میباشد
```java
	final HamrahPay   hamrahPay  = new HamrahPay(MainActivity.this).sku(sku);
        Handler handler = new Handler(){
            @Override
            public void handleMessage(Message msg) {
                if (msg.what == 1) {
                    lastPurchase = hamrahPay.getLastPurchase();
                    Toast.makeText(MainActivity.this,lastPurchase.toString(),Toast.LENGTH_LONG).show();
                }
            }
        };
        hamrahPay.LastPurchaseRequest(sku,MainActivity.this,handler);
```

### نمایش کد دستگاه به کاربر
این متد زمانی مفید است که کد دستگاه کاربر تغییر کرده است و شما نیازمند کد دستگاه کاربر برای ویرایش تراکنش آن میباشید 
این متد کد دستگاه را به کاربر نمایش میدهد
```java
	String Device_ID = HamrahPay.showDeviceID(MainActivity.this);
```

### کنترل خطا
در هنگام رویداد خطا میتوانید آنرا بصورت دستی کنترل کنید
```java
.listener(new HamrahPay.Listener() {
    @Override
    public void onErrorOccurred(String status, String message) {
        switch (status) {
            case HamrahPay.STATUS_BAD_PARAMETERS:
                break;
            case HamrahPay.STATUS_INVALID_TRANSACTION:
                break;
            case HamrahPay.STATUS_NO_NETWORK_OR_SERVER:
                break;
            case HamrahPay.STATUS_SELLER_BLOCKED:
                break;
            case HamrahPay.STATUS_TRY_AGAIN:
                break;
        }
        Log.e("HamrahPay", status + ": " + message);
    }

    @Override
    public void onPaymentSucceed(String payCode) {
    
    }
})
```
## Progaurd
```
# Retrofit
-dontwarn retrofit2.**
-dontwarn org.codehaus.mojo.**
-keep class retrofit2.** { *; }
-keepattributes Signature

-dontwarn okio.**
-dontwarn javax.annotation.Nullable
-dontwarn javax.annotation.ParametersAreNonnullByDefault 
-dontnote retrofit2.Platform
-dontwarn retrofit2.Platform$Java8
-keepattributes Signature
-keepattributes Exceptions
-keepattributes *Annotation*

-keepattributes RuntimeVisibleAnnotations
-keepattributes RuntimeInvisibleAnnotations
-keepattributes RuntimeVisibleParameterAnnotations
-keepattributes RuntimeInvisibleParameterAnnotations

-keepattributes EnclosingMethod

-keepclasseswithmembers class * {
    @retrofit2.* <methods>;
}

-keepclasseswithmembers interface * {
    @retrofit2.* <methods>;
}
```

---
## پشتیبانی
لینک سایت همراه پی:

[![](https://hamrahpay.com/assets/home/theme/img/logo-red.png)](https://hamrahpay.com)

هرگونه سوالی در مورد کتابخانه دارید در قسمت مشکلات بپرسید.<br>
یک مثال بصورت یک برنامه اندرویدی نوشته شده است app همچنین در داخل فولدر 
(لینک صفحه مشکلات)[]
<a href="https://github.com/hamrahpay/HamrahPay/issues" target="_blank">

</a>

---
## لایسنس

    Copyright 2013 Square, Inc.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

