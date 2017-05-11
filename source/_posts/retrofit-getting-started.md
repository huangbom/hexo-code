---
title: 开始教程
date: 2017-05-06 14:10:19
tags: Retrofit
category: Retrofit
---
原文：[https://futurestud.io/tutorials/retrofit-getting-started-and-android-client](https://futurestud.io/tutorials/retrofit-getting-started-and-android-client)
## 开始创建一个Android Client

在这个教程中我们将通过基础知识、创建一个Retrofit Android API/HTTP 客户端 ，去请求GitHub。

## Retrofit 是什么？

官方描述：
 
> 一个类型安全的REST Android、java客户端。

您将使用注解来定义HTTP请求，默认支持URL参数替换和查询参数。此外，它提供了自定义headers，多个请求体，文件上传和下载，模拟响应以及更多特性。

<!--more-->

在以后的教程中，我们将看到更多详细的信息。

## 准备您的Android项目

让我们把手放到键盘上，如果您已经创建了Android项目，请跳过这一节。否则，在您最喜爱的IDE中创建一个新项目。我们比较喜欢使用Android Studio和Gradle去构建系统，但您也可以用您喜欢的IDE或Maven或是别的工具。

## 定义依赖：Gradle或Maven

首先，为您的项目添加依赖。在您的`build.gradle`或`pom.xml`文件中定义依赖项。build代码时，生成系统将自动下载并为项目提供库。

### Retrofit 2

我们推荐使用Retrofit 2，因为它使用OkHttp作为默认的网络层，在它的基础上建立。您不需要显式地定义OkHttp作为项目的依赖，除非您有一个特定的版本要求。  

Retrofit 2需要一个自动转换请求和响应的依赖。
如果您使用Retrofit 2，并且想用Gson去作为Converter请添加下面的依赖：

##### build.gradle 

```
dependencies {  
    // Retrofit & OkHttp
    compile 'com.squareup.retrofit2:retrofit:2.2.0'
    compile 'com.squareup.retrofit2:converter-gson:2.2.0'
}
```

##### pom.xml

```
<dependency>  
    <groupId>com.squareup.retrofit2</groupId>
    <artifactId>retrofit</artifactId>
    <version>2.2.0</version>
</dependency>  
<dependency>  
    <groupId>com.squareup.retrofit2</groupId>
    <artifactId>converter-gson</artifactId>
    <version>2.2.0</version>
</dependency>
```

> **Gson：**如果您从来没有听说过Gson或者想了解更多，可以随时查看我们的[Gson系列](https://futurestud.io/tutorials/gson-getting-started-with-java-JSON-serialization-deserialization)。

### Retrofit 1.9
如果您仍然使用1.9，您需要声明OkHttp。

#### build.gradle

```
dependencies {  
    // Retrofit & OkHttp
    compile 'com.squareup.retrofit:retrofit:1.9.0'
    compile 'com.squareup.okhttp:okhttp:2.7.2'
}
```

#### pom.xml

```
<dependency>  
    <groupId>com.squareup.retrofit</groupId>
    <artifactId>retrofit</artifactId>
    <version>1.9.0</version>
</dependency>  
<dependency>  
    <groupId>com.squareup.okhttp</groupId>
    <artifactId>okhttp</artifactId>
    <version>2.7.2</version>
</dependency>  
```

## Android的网络权限

Retrofit对Internet上某个服务器上运行的API执行HTTP请求。Android应用程序执行这些请求需要在AndroidManifest.xml文件中定义网络权限。如果您没有定义，请添加以下行在您的xml中：

```
<uses-permission android:name="android.permission.INTERNET" /> 
```

现在，您的项目已经准备好Retrofit，让我们创建一个Android的API / HTTP客户端。

## 如何定义API请求

在本教程中，我们会定义一个简单的GitHub的请求。我们将在后面的教程中更[详细地介绍API](https://futurestud.io/tutorials/retrofit-2-basics-of-api-description)请求。


首先，您必须创建一个接口并定义所需方法。

### GitHub Client
下面的代码定义了一个`GithubClient`和`reposForUser`方法，这个方法返回一个用户的库列表。`GET`注解声明此请求使用HTTP GET方法。代码段还说明了Retrofit的路径参数替换功能的用法：
用户给定的user变量的值，在调用方法时将替换掉`reposForUser` 方法上 `@GET`注解中的`{user}`。

#### Retrofit 2

```
public interface GitHubClient {  
    @GET("/users/{user}/repos")
    Call<List<GitHubRepo>> reposForUser(
        @Path("user") String user
    );
}
```

#### Retrofit 1.9

```
public interface GitHubClient {  
    @GET("/users/{user}/repos")
    void reposForUser(
        @Path("user") String user，
        Callback<List<GitHubRepo>> callback
    );
}
```

这里定义了一个模型类`GithubRepo`。此类包含映射响应数据所需的类属性。

```
public class GitHubRepo {  
    private int id;
    private String name;

    public GitHubRepo() {
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

关于前面提到的JSON映射的`GithubClient`接口定义了一个`reposForUser`方法的返回类型为`List< GithubRepo>`
>（如果响应数据与给定的类不匹配，修复它确保与服务器的响应数据得到正确映射）。

## Retrofit REST Client

在定义API接口和对象模型之后，是时候准备一个实际的请求了。在这两个版本中RestAdapter（v1.9）或Retrofit（2 +）Class，您很流畅的创建和配置了它们的API。为此，您可以使用生成器为所有请求设置一些通用选项，即基本URL或转换器。

我们将在以后的教程中更详细的讨论所有可用的选项。

一旦创建了适配器，就可以创建客户端，您将使用客户端来执行实际请求。

### Retrofit 2

```java
String API_BASE_URL = "https://api.github.com/";

OkHttpClient.Builder httpClient = new OkHttpClient.Builder();

Retrofit.Builder builder =  
    new Retrofit.Builder()
            .baseUrl(API_BASE_URL)
            .addConverterFactory(
                GsonConverterFactory.create()
            );

Retrofit retrofit =  
    builder
        .client(
            httpClient.build()
        )
        .build();

GitHubClient client =  retrofit.create(GitHubClient.class);  
```

### Retrofit 1.9

```java
String API_BASE_URL = "http://your.api-base.url";

RestAdapter.Builder builder =  
    new RestAdapter.Builder()
        .setEndpoint(API_BASE_URL)
        .setClient(
            new OkClient(new OkHttpClient())
        );

RestAdapter adapter = builder.build();

GitHubClient client = adapter.create(GitHubClient.class);
```

在我们的例子中，GitHub的API库的网址是`https://api.github.com/`。在上面的代码片段中，我们只使用了最小选项。有很多选项可以设置。但它足以使我们的第一个请求达到目的！

## JSON Mapping
在大多数情况下，请求服务器并从服务器返回的数据不是java对象。它们映射到一些语言中立的格式，如JSON。从GitHub的API使用JSON，我们需要准备改造它。

Retrofit1.9默认使用（谷歌的Gson解析JSON和java对象）。所以您需要做的是定义您的响应对象的类，然后它将被自动映射。

使用Retrofit 2，需要添加一个converter。在上面的代码中，我们已经在我们的build.gradle中添加了下面的代码去为Retrofit导入Gson converter。

```
compile 'com.squareup.retrofit2:converter-gson:2.2.0'  
```

如果您从一开始就跟随我们的教程，您已经完成了！

尽管如此，在本教程中，我们还可以看看其他的[数据格式转换器](https://futurestud.io/tutorials/retrofit-2-introduction-to-multiple-converters)，所以您也可以用XML或protobuf的API进行交互。

## 使用Retrofit

在做了大量的准备工作之后，是时候去发起您的请求获取数据了。好消息：只会有几行代码！

创建客户端对象的第一行代码已经很熟悉了。实际执行请求的方式取决于Retrofit 的版本。

### Retrofit 1.9

对于Retrofit 1.9，您传递回调作为最后一个参数。回调在请求执行完成后立即执行，得到响应并解析它。也有一个简单的方法，使用同步请求，但这对于大多数Android程序来说是不可用的。

```
// 创建一个非常简单的GithubClient
GitHubClient client = adapter.create(GitHubClient.class);

// 获取Github 仓库列表.    
client.reposForUser("fs-opensource"， new Callback<List<GitHubRepo>>() {  
    @Override
    public void success(List<GitHubRepo> repos， Response response) {
        // 请求成功，并且我们得到了一个Response
        // TODO: 使用仓库列表并显示它
    }

    @Override
    public void failure(RetrofitError error) {
        // 请求失败 或者server发送错误
        // TODO: 捕捉了异常
    }
});    
```

### Retrofit 2

在Retrofit 2中，您还使用`client`对象。然而，在这里您不传递您的回调作为最后一个参数。而是使用`client`去获取`call`对象。然后调用它的`.enqueue`方法。

也有一个可选的[同步请求](https://futurestud.io/tutorials/retrofit-synchronous-and-asynchronous-requests)，但我们以后再看。

```
// 创建一个非常简单的GithubClient
GitHubClient client =  retrofit.create(GitHubClient.class);

// 获取Github 仓库列表.
Call<List<GitHubRepo>> call =  
    client.reposForUser("fs-opensource");

// 执行这个异步请求，获得一个成功或失败的回掉. 
call.enqueue(new Callback<List<GitHubRepo>>() {  
    @Override
    public void onResponse(Call<List<GitHubRepo>> call， Response<List<GitHubRepo>> response) {
        // 请求成功，并且我们得到了一个Response
        // TODO: use the repository list and display it
    }

    @Override
    public void onFailure(Call<List<GitHubRepo>> call， Throwable t) {
        // the network call was a failure
        // TODO: handle error
    }
});
```

在这两种情况下，您应该能够正常使用您的第一个Retrofit 请求了。如果一切顺利，Retrofit将返回给您一个list列表`List<GithubRepo>`，可进一步用来在应用程序中显示它。

最初，Retrofit看起来相当复杂。我们承诺，如果您正在一个大型项目中工作，这是非常简洁而有利的。如果您多花点时间阅读一些教程，相信您很快也会喜欢。

