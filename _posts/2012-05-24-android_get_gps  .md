---
layout: post
title: 关于android开发中如何获取gps位置信息的方法 
date: 2012-05-24 
tag: android
---

```java
LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);   
locationManager.requestLocationUpdates("gps", 60000, 1,locationListener); // 每秒更新位置信息，不考虑距离变化。
Location loc = locationManager.getLastKnownLocation(LocationManager.GPS_PROVIDER);
locInfo.setText("维度："+String.valueOf(loc.getLatitude())+" 经度："+String.valueOf(loc.getLongitude()));
```
但是通常此时这里的loc为null，因此导致后面的经纬度信息获取不到从而产生NullPointExcetion.
目前我的解决办法是再多获取几次，不知道这算不算android的bug,一次获取位置信息获取不到，但是多获取几次就可以获取到了。代码如下：

```java
LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
   Location loc = locationManager
     .getLastKnownLocation(LocationManager.GPS_PROVIDER);

   while (loc == null) {
    locationManager.requestLocationUpdates("gps", 60000, 1,
      locationListener); // 每秒更新位置信息，不考虑距离变化。
    loc = locationManager
      .getLastKnownLocation(LocationManager.GPS_PROVIDER);
   }
```

这个是上面所提到的locationListener，我这里是做个例子，什么也没干。

```java
private void initlocationListener() {
  locationListener = new LocationListener() {
   // implement necessary methods
   public void onLocationChanged(Location location) {
    locInfo.setText("维度："+String.valueOf(location.getLatitude())+" 经度："+String.valueOf(location.getLongitude()));
    

   }

   public void onProviderDisabled(String provider) {
    // called when the provider be disabled by user

   }

   public void onProviderEnabled(String provider) {
    // called when the provider be enabled
   }

   public void onStatusChanged(String provider, int status,
     Bundle extras) {
    // TODO Auto-generated method stub

   }
  };
 }
```