---
title: 创建一个可维护的Android Client
date: 2017-05-11 10:54:08
tags: Retrofit
category: Retrofit
---

原文[https://futurestud.io/tutorials/retrofit-2-creating-a-sustainable-android-client](https://futurestud.io/tutorials/retrofit-2-creating-a-sustainable-android-client)

Retrofit 提供了广泛的功能，有很多可能的配置。很多大型应用程序需要一些特定的设置，例如OAuth认证。为了实现简洁和稳定的项目，我们向您介绍我们的想法：一个可维护的`ServiceGenerator` Android客户端。

## ServiceGenerator

正如您知道{% post_link retrofit-getting-started 开始教程 %}，Retrofit 对象和它的builder是所有请求的核心。在这里您配置您的请求、响应、身份验证、日志记录和错误处理。不幸的是，我们已经看到太多的开发人员只是复制和粘贴这些部分，而不是分离成一个简洁的类。	`ServiceGenerator`将会给我们一个解决方案，这源于[Bart Kiers的想法](https://github.com/bkiers/retrofit-oauth/tree/master/src/main/java/nl/bigo/retrofitoauth)。

让我们从简单的代码开始。在当前状态下，它只定义了一个方法来创建一个给定的class/interface REST客户端，从接口返回一个service类。

<!-- more -->

```java
public class ServiceGenerator {

    private static final String BASE_URL = "https://api.github.com/";

    private static Retrofit.Builder builder =
            new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create());

    private static Retrofit retrofit = builder.build();

    private static OkHttpClient.Builder httpClient =
            new OkHttpClient.Builder();

    public static <S> S createService(
        Class<S> serviceClass) {
        return retrofit.create(serviceClass);
    }
}
```

`ServiceGenerator`类使用Retorfit的Builder与给定的API库的URL（BASE_URL）创建一个新的REST客户端。例如，GitHub的API库的URL是`https://api.github.com/`。

`createService`方法需要一个`serviceClass`作为参数创建并它的Class对象。获取到对象后，就可以执行网络请求了。

## 为什么ServiceGenerator里的东西都是静态的？

您可能会奇怪为什么我们使用静态字段和方法在ServiceGenerator类里面。实际上，它有一个简单的原因：在整个应用程序中我们要使用相同的对象（例如：`OkHttpClient`，`Retrofit`，…），打开一个socket连接处理所有的请求和响应，包括缓存和更多功能（通常大家都这么做）。

此外，为了加快速度，我们可以在移动设备上节省一点点宝贵的内存，而不必一次又一次重复创建相同的对象。所以我们需要标记所有字段和方法是静态的

## 使用 ServiceGenerator

还记得{% post_link retrofit-getting-started 开始教程 %}我们代码是怎样的吗？

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

GitHubClient client = retrofit.create(GitHubClient.class); 
```

对于一个请求，这看起来很好。只是，如果您的应用程序中有几十个网络请求，这管理起来将是一个噩梦。我们的`ServiceGenerator`，只需要一行代码：

```
GitHubClient client = ServiceGenerator.createService(GitHubClient.class); 
```

所有的准备工作都移到我们的`ServiceGenerator`。

不幸的是，在大多数情况下，ServiceGenerator不能一直这样简单。因此，上面的代码只是给您一个起点。您需要适应您的需求，就像我们在其他教程里做的一样。然后，在接下来的两个章节中我们将探讨一些可能的变化。

## 准备 Logging 日志

开发者最常见的愿望之一是想知道Retrofit实际上发送和接收的数据是什么样的。我们有一个完整的教程专用于[Retrofit与logging](https://futurestud.io/tutorials/retrofit-2-log-requests-and-responses)，在这里您可以知道得更多。

Retorift的logging日志通过回调一个`HttpLoggingInterceptor`拦截器。您需要添加这个拦截器实例到`OkHttpClient`。如下所示:

```java
public class ServiceGenerator {

    private static final String BASE_URL = "https://api.github.com/";

    private static Retrofit.Builder builder =
            new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create());

    private static Retrofit retrofit = builder.build();

    private static HttpLoggingInterceptor logging =
            new HttpLoggingInterceptor()
                    .setLevel(HttpLoggingInterceptor.Level.BODY);

    private static OkHttpClient.Builder httpClient =
            new OkHttpClient.Builder();

    public static <S> S createService(
        Class<S> serviceClass) {
        if (!httpClient.interceptors().contains(logging)) {
            httpClient.addInterceptor(logging);
            builder.client(httpClient.build());
            retrofit = builder.build();
        }

        return retrofit.create(serviceClass);
    }
}
```

有几件事您必须意识到。首先，确保不要多次加入拦截器！如果日志拦截器已经存在，我们用`httpClient.interceptors().contains(logging)`检查。其次，确保不要每一次调用`createSercice`的时候都去build `retrofit` 对象。否则的话`ServiceGenerator`就失去他的作用了。

## 准备认证

认证的要求有点不同。您可以在我们的教程[基本身份验证](https://futurestud.io/tutorials/android-basic-authentication-with-retrofit)，[Token 认证](https://futurestud.io/tutorials/retrofit-token-authentication-on-android)，[OAuth](https://futurestud.io/tutorials/oauth-2-on-android-with-retrofit)，甚至[Hawk 认证](https://futurestud.io/tutorials/retrofit-2-hawk-authentication-on-android)中去学习更多的认证。对于每一个认证的实现细节是有点不同的，您可能会改变`ServiceGenerator`。其中一个变化是，您需要通过额外的参数来`createService`创建一个客户端。

让我们看一个 Hawk 认证的例子：

```java
public class ServiceGenerator {

    private static final String BASE_URL = "https://api.github.com/";

    private static Retrofit.Builder builder =
            new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create());

    private static Retrofit retrofit = builder.build();

    private static HttpLoggingInterceptor logging =
            new HttpLoggingInterceptor()
                    .setLevel(HttpLoggingInterceptor.Level.BODY);

    private static OkHttpClient.Builder httpClient =
            new OkHttpClient.Builder();

    public static <S> S createService(
            Class<S> serviceClass, final HawkCredentials credentials) {
        if (credentials != null) {
            HawkAuthenticationInterceptor interceptor =
                    new HawkAuthenticationInterceptor(credentials);

            if (!httpClient.interceptors().contains(interceptor)) {
                httpClient.addInterceptor(interceptor);

                builder.client(httpClient.build());
                retrofit = builder.build();
            }
        }

        return retrofit.create(serviceClass);
    }
}
```

我们的`createService`现在有两个参数(`HawkCredentials`)。如果传递一个非空值，它将创建必要的Hawk身份验证拦截器并将其添加到Retrofit客户端。我们还需要rebuild Retrofit，以应用我们的变化到下一个请求。

一抬头，您可能会在其他教程中看到`ServiceGenerator`不同版本。不要混淆！我们也建议您保持您的`ServiceGenerator`简洁和专门的使用情况！

## 接下来是什么

在本教程中，您已经了解到为什么推荐集中生成您的Retrofit客户端。您见过一个方法您可以用`ServiceGenerator`类实现它。不过，您可能不得不调整您的目的。

如果您有反馈或问题，请在评论中让我们知道或[@ futurestud_io](https://futurestud.io/tutorials/twitter.com/futurestud_io)。

Make it rock & enjoy coding!

