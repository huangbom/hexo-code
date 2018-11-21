---
title: Activity Translucent
date: 2018-11-21 10:59:47
tags: activity translucent
category: Android
---

# Activity 透明主题

```
    <style name="PopupContainer">
        <item name="android:windowBackground">@color/translucent</item>
        <item name="android:colorBackgroundCacheHint">@null</item>
        <item name="android:windowContentOverlay">@null</item>
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowDrawsSystemBarBackgrounds">true</item>
        <item name="android:statusBarColor">@color/translucent</item>
    </style>
```