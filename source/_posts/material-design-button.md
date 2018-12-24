---
title: Material Design Button
date: 2018-12-24 17:13:14
tags: material design
category: android
---

Android中Button的默认颜色为灰色,并且带有默认的点击效果,在5.0以上的系统还有涟漪的效果。通常在开发时我们都会修改Button的颜色,最常用的方式就是修改Button的背景颜色,即“android:background”;只是修改简单的修改背景颜色的话Button就没有点击效果,这时我们一般会使用selector来实现点击效果;这样处理不仅会增加工作量,也会丢失掉5.0以上系统的涟漪效果。

## 使用Style修改Button颜色并保持默认点击效果

Android中我们也可以通过Style来设置Button的样式

### Style中Button样式的设置 

首先我们需要查看默认的Button样式是如何设置的,在`.../res/styles.xml`文件中我们可以看到应用默认的style

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> 
	<!-- Customize your theme here. --> 
	<item name="colorPrimary">@color/colorPrimary</item> 
	<item name="colorPrimaryDark">@color/colorPrimaryDark</item> 
	<item name="colorAccent">@color/colorAccent</item> 
</style>
```

按住command键点击`Theme.AppCompat.Light.DarkActionBar`,或者选中按“command+B”快捷键继续跟踪查看

```xml
<style name="Theme.AppCompat.Light.DarkActionBar" parent="Base.Theme.AppCompat.Light.DarkActionBar"/> 
```

继续跟踪查看`Base.Theme.AppCompat.Light.DarkActionBar`

```xml
<style name="Base.Theme.AppCompat.Light.DarkActionBar" parent="Base.Theme.AppCompat.Light"> 
	<item name="actionBarPopupTheme">@style/ThemeOverlay.AppCompat.Light</item> 
	<item name="actionBarWidgetTheme">@null</item> 
	<item name="actionBarTheme">@style/ThemeOverlay.AppCompat.Dark.ActionBar</item> 
	<!-- Panel attributes --> 
	<item name="listChoiceBackgroundIndicator">@drawable/abc_list_selector_holo_dark</item> 
	<item name="colorPrimaryDark">@color/primary_dark_material_dark</item> 
	<item name="colorPrimary">@color/primary_material_dark</item> 
</style> 
```

继续跟踪查看`Base.Theme.AppCompat.Light`时会提示有不同版本,通过一个个跟踪查看可以知道,最终继承的parent style都是`Base.V7.Theme.AppCompat.Light`,在`Base.V7.Theme.AppCompat.Light`中可以找到对Button样式的定义

```xml
<style name="Base.V7.Theme.AppCompat.Light" parent="Platform.AppCompat.Light"> 
	... 
	<!-- Button styles --> 
	<item name="buttonStyle">@style/Widget.AppCompat.Button</item> 
	<item name="buttonStyleSmall">@style/Widget.AppCompat.Button.Small</item> 
	<item name="android:textAppearanceButton">@style/TextAppearance.AppCompat.Widget.Button</item> 
	... 
</style> 
```

其中有设置了buttonStyle,继续跟踪查看`Widget.AppCompat.Button`

有values.xml和values-v21.xml

> values.xml

```xml
<style name="Base.Widget.AppCompat.Button" parent="android:Widget">
	<item name="android:background">@drawable/abc_btn_default_mtrl_shape</item>
	<item name="android:textAppearance">?android:attr/textAppearanceButton</item> 
	<item name="android:minHeight">48dip</item> 
	<item name="android:minWidth">88dip</item> 
	<item name="android:focusable">true</item> 
	<item name="android:clickable">true</item> 
	<item name="android:gravity">center_vertical|center_horizontal</item> 
</style> 
```

> values-v21.xml

```xml
<style name="Base.Widget.AppCompat.Button" parent="android:Widget.Material.Button"/> 
	... 
	<style name="Widget.Material.Button"> 
	<item name="background">@drawable/btn_default_material</item> 
	<item name="textAppearance">?attr/textAppearanceButton</item> 
	<item name="minHeight">48dip</item> 
	<item name="minWidth">88dip</item> 
	<item name="stateListAnimator">@anim/button_state_list_anim_material</item> 
	<item name="focusable">true</item> 
	<item name="clickable">true</item> 
	<item name="gravity">center_vertical|center_horizontal</item> 
</style> 
```

可以看到针对不同版本分别设置了Button的样式。 

### btttonStyle中颜色的设置 

通过分析Style,可以知道通过buttonStyle来指定Button的样式

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> 
	<!-- Customize your theme here. --> 
	<item name="colorPrimary">@color/colorPrimary</item> 
	<item name="colorPrimaryDark">@color/colorPrimaryDark</item> 
	<item name="colorAccent">@color/colorAccent</item> 
	<!-- 设置button的Style --> 
	<item name="buttonStyle">@style/Xxx</item> 
</style> 
```

在V7支持包中,提供了5个预置的Button的Style,如下:

```xml
<style name="Widget.AppCompat.Button" parent="Base.Widget.AppCompat.Button"/> 
<style name="Widget.AppCompat.Button.Small" parent="Base.Widget.AppCompat.Button.Small"/> 
<style name="Widget.AppCompat.Button.Colored" parent="Base.Widget.AppCompat.Button.Colored"/> 
<style name="Widget.AppCompat.Button.Borderless" parent="Base.Widget.AppCompat.Button.Borderless"/> 
<style name="Widget.AppCompat.Button.Borderless.Colored" parent="Base.Widget.AppCompat.Button.Borderless.Colored"/> 

```
![imgae](image.jpeg)

### 通过buttonStyle修改Button颜色

#### 全局Button设置

直接在应用主题中设置buttonStyle,然后通过相应的属性修改Button的背景色或文字颜色,如下:

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar"> 
	<!-- Customize your theme here. --> 
	<item name="colorPrimary">@color/colorPrimary</item> 
	<item name="colorPrimaryDark">@color/colorPrimaryDark</item> 
	<item name="colorAccent">@color/colorAccent</item> 
	<item name="buttonStyle">@style/Widget.AppCompat.Button</item> 
	<item name="colorButtonNormal">@color/colorBlue</item> 
</style> 
```

#### 单个Button设置

在styles.xml文件中添加一个style,修改Button的背景色或文字颜色,然后在需要修改的layout的Button中添加`android:theme="@style/RedButton"`属性即可,如下:

```xml
<style name="RedButton" parent="AppTheme"> 
	<item name="buttonStyle">@style/Widget.AppCompat.Button</item> 
	<item name="colorButtonNormal">@color/colorRed</item> 
</style> 
```

在layout中使用:

```xml
<Button 
	android:layout_width="wrap_content" 
	android:layout_height="wrap_content" 
	android:text="Button_1" 
	android:theme="@style/RedButton" /> 
<!-- 注意是android:theme属性,不是style属性 --> 
```

### 修改Button圆角大小 

目前只找到了全局修改Button圆角大小的方法,
在`dimes.xml`文件中复写`abc_control_corner_material`的值,可以修改应用内所有Button的圆角大小

```
<dimen name="abc_control_corner_material" tools:override="true">8dp</dimen> 
```