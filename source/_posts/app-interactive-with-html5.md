---
title: App 与 HTML5 的交互
date: 2017-05-06 12:18:19
tags: HTMl5
category: Android
description: App 与 HTML5 的交互，是一个可以大做文章的话题。有的团队直接使用 PhoneGap 来 实现交互的功能，而我则认为 PhoneGap 太重了。我们完全可以把这些交互操作在底层封装好，然后给开发人员使用。
---


# App 与 HTML5 的交互

## HTML5

```html
<a onclick="baobao.callAndroidMethod(100,100,'ccc',true)"> CallAndroidMethod</a>
```

## Android

创建一个 `JSInterface1` 类，包括 `callAndroidMethod` 方法的实现:

```java
class JSInteface1 {
    public void callAndroidMethod(int a, float b,String c, boolean d) {
	if (d) {
	    String strMessage = "-" + (a + 1) + "-" + (b + 1) + "-" + c + "-" + d;
	    new AlertDialog.Builder(MainActivity.this)
		.setTitle("title")
		.setMessage(strMessage)
		.show();
	} 
    }
}
```
/Users/huangyaoshi/Documents/hexo
同时，需要注册 baobao 和 JSInterface1 的对应关系:

```
wvAds.addJavascriptInterface(new JSInteface1(), "baobao");
```

## App 和 HTML5 之间定义跳转协议

根据上面的例子，运营团队就找到了在 App 中搞活动的解决方案。不必等到 App 每次 发新版才能看到新的活动页面，而是每次做一个 HTML5 的活动页面，然后通过 MobileAPI 把这个 HTML5 页面的地址告诉 App，由 App 加载这个 HTML5 页面即可。
在这个 HTML5 页面中，我们可以定义各种 JavaScript 点击事件，从而跳转回 App 的任 意 Native 页面。
为此，HTML5 团队需要事先和 App 团队约定好一个格式，例如:

- gotoPersonCenter 
- gotoMovieDetail:movieId=100
- gotoNewsList:cityId=1&cityName=北京
- gotoUrl:http:// www.sina.com

这个协议具体在 HTML5 页面中是这样的，以 gotoNewsList 为例:

```html
<a onclick="baobao.gotoAnyWhere( 'gotoNewsList:cityId=(int)12&cityName= 北京 ')">gotoAnyWhere</a>
```

其中，有些协议是不需要参数的，比如说 gotoPersonCenter，也就是个人中心;有些则 需要跳转到具体的电影详情页，我们需要知道 movieId ;有时候 1 个参数不够用，我们需要 更多的参数，才能准确获取到我们想要的数据，比如说 gotoNewsList，我们想要跳转到 2014 年 12 月 31 号北京的所有新闻信息，就不得不需要 cityId 和 createdTime 两个参数，处理协议 的代码如下所示:

```java
public void gotoAnyWhere(String url) {
    if (url != null) {
	if (url.startsWith("gotoMovieDetail:")) {
	    String strMovieId = url.substring(24);
	    int movieId = Integer.valueOf(strMovieId);
	    Intent intent = new Intent(MainActivity.this, MovieDetailActivity.class);
	    intent.putExtra("movieId", movieId);
	    startActivity(intent);
	} else if (url.startsWith("gotoNewsList:")) { 
    	    // as above
	} else if (url.startsWith("gotoPersonCenter")) {
	    Intent intent = new Intent(MainActivity.this, PersonCenterActivity.class);
	    startActivity(intent);
	} else if (url.startsWith("gotoUrl:")) {
	    String strUrl = url.substring(8);
	    wvAds.loadUrl(strUrl);
	}
    }
}
```
这里的 if 分支逻辑太多，我们要想办法将其进行抽象

## 页面分发器

```html
<a onclick="baobao.gotoAnyWhere( 'gotoMovieDetail:movieId=12')">gotoAnyWhere</a>
```

我们将其改写为:

```html
<a onclick="baobao.gotoAnyWhere( 'com.example.youngheart.MovieDetailActivity, iOS.MovieDetailViewController:movieId=(int)123')">gotoAnyWhere</a>
```

我们看到，协议的内容分成 3 段

- 第一段是 Android 要跳转到的 Activity 的名称
- 第二 段是 iOS 要跳转到的 ViewController 的名称
- 第三段是需要传递的参数，以 key-value 的形式 进行组装

我们接下来要做的就是从协议 URL 中取出第 1 段，将其反射为一个 Activity 对象，取出 第 3 段，将其解析为 key-value 的形式，然后从当前页面跳转到目标页面并配以正确的参数。 其中，写一个辅助函数 getAndroidPageName，用来获取 Activity 名称:

```java
public class BaseActivity extends Activity {
    private String getAndroidPageName(String key) {
	String pageName = null;
	int pos = key.indexOf(",");
	if (pos == -1) {
   	    pageName = key;
	} else {
	    pageName = key.substring(0, pos);
	}
	return pageName;
    }

    public void gotoAnyWhere(String url) 
	if (url == null)
	    return;		
			
	String pageName = getAndroidPageName(url);
	if (pageName == null || pageName.trim() == "")
  	    return;
	Intent intent = new Intent();
	int pos = url.indexOf(":");
	if (pos > 0) {
	    String strParams = url.substring(pos);
	    String[] pairs = strParams.split("&");
	    for (String strKeyAndValue : pairs) {
	    	String[] arr = strKeyAndValue.split("=");
	    	String key = arr[0];
	    	String value = arr[1];
	    	if (value.startsWith("(int)")) {
	    		intent.putExtra(key, Integer.valueOf(value.substring(5)));
	    	} else if (value.startsWith("(Double)")) {
	    		intent.putExtra(key,Double.valueOf(value.substring(8)));
	    	} else {
	    		intent.putExtra(key, value);
	    	}
	    }
	}

	try {
	    intent.setClass(this, Class.forName(pageName));
	} catch (ClassNotFoundException e) {
	    e.printStackTrace();
	}
	
	startActivity(intent);
    }
}
```

**注意：**在协议中定义这些简单数据类型的时候，String 是不需要指定类型的，这是使用 最广泛的类型。对于 int、Double 等简单类型，我们要在值前面加上类似 (int) 这样的约定， 这样才能在解析时不出问题。

## Android 经典场景设计 

### 在 App 中内置 HTML5 页面
什么时候在 App 中内置 HTML5 页面?根据我的经验，当有些 UI 不太容易在 App 中使 用原生语言实现时，比如画一个奇形怪状的表格，这是 HTML5 所擅长的领域，只要调整好 屏幕适配，就可以很好地应用在 App 中。
下面详细介绍如何在页面中显示一个表格，表格里的数据都是动态填充的。 1)首先定义两个 HTML5 文件，放在 assets 目录下。
其中，102.html 是静态页:

```html
<html>
        <head> </head>
        <body>
	<table> <data1DefinedByBaobao>
        </table>
        </body>
</html>
```

而 `data1_template.html` 是一个数据模板，它负责提供表格中一行的样式:

```html
<tr>
	<td><name> </td>
	<td><price></td> 
</tr>
```

像 `<name>`、`<price>` 和 `<data1DefinedByBaobao>` 都是占位符，我们接下来会使用真实 的数据来替换这些占位符。
2)在 MovieDetailActivity 中，通过遍历 movieList 这个集合，我们把数据填充到 sbContent 中，最终，把拼接好的字符串替换 `<data1DefinedByBaobao>` 标签:

```java
String template = getFromAssets("data1_template.html");
StringBuilder sbContent = new StringBuilder();
ArrayList<MovieInfo> movieList = organizeMovieList();
for (MovieInfo movie : movieList) {
	String rowData;
	rowData = template.replace("<name>", movie.getName());
	rowData = rowData.replace("<price>", movie.getPrice());
	sbContent.append(rowData);
}
String realData = getFromAssets("102.html");
realData = realData.replace("<data1DefinedByBaobao>",sbContent.toString());
wvAds.loadData(realData, "text/html", "utf-8");
```
 



