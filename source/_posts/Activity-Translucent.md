---
title: Android 笔记
date: 2018-11-21 10:59:47
tags: activity translucent
category: Android
---

# Activity 透明主题

```xml
    <style name="PopupContainer">
        <item name="android:windowBackground">@color/translucent</item>
        <item name="android:colorBackgroundCacheHint">@null</item>
        <item name="android:windowContentOverlay">@null</item>
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowDrawsSystemBarBackgrounds">true</item>
        <item name="android:statusBarColor">@color/translucent</item>
    </style>
```

# ImageView 设置圆角的方法

**1,使用Glide，适用于网络图片**

```kotlin
Glide.with(logo.context)
        .load(it.brandlogoroundimageUrl)
        .apply(RequestOptions().transforms(CenterCrop(), RoundedCorners(resources.getDimensionPixelSize(R.dimen.round_corner_radius))))
        .into(logo)
```

如果使用kotlin，还可以这样

```kotlin
fun ImageView.loadRounderCornerImage(string: String?, roundingRadius: Int = resources.getDimensionPixelSize(R.dimen.round_corner_radius)){
    Glide.with(context)
            .load(string)
            .apply(RequestOptions().transforms(RoundedCorners(roundingRadius)))
            .into(this)
}
```

**2,使用Drawable**

```java
GradientDrawable gd = new GradientDrawable();
gd.setColor(argb);
gd.setCornerRadius(radius);
gd.setStroke(1, Color.DKGRAY);
return gd;
```

