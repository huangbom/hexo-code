---
title: Retrofit基本操作
date: 2017-05-06 14:18:19
tags: Retrofit
category: Retrofit
---

## 如何描述API接口

正如您已经知道在{% post_link retrofit-getting-started 开始教程 %}中我们描述了所有的Retrofit请求，在我们的第一个例子里，我们定义了一个接口类展示了一个功能，如下所示：
您
```java
public interface GitHubClient {  
    @GET("/users/{user}/repos")
    Call<List<GitHubRepo>> reposForUser(
        @Path("user") String user
    );
}
```
 
现在让我们来看看这些选项里面更详细的信息。

<!--more-->

# HTTP Method

我们在Java interface里的方法上使用注解来描述单个的API最终请求的处理。

您要做的第一件事就是定义HTTP的请求方法，比如`GET`，`POST`， `PUT`， `DELETE`等等。Retrofit对于每一个标准的请求方法都提供了一个注解：`@GET`， `@POST`， `@PUT`， `@DELETE`， `@PATCH` or `@HEAD`。您只需要在每个HTTP方法上使用合适的Retrofit注解就行。

您必须在您的app里面指定请求方法。如果您从来没有听说过HTTP请求方法，请在	[维基百科的HTTP页面](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)上了解它。

下面是几个简单的示例：`@GET`， `@PUT` and `@DELETE`:

```java
public interface FutureStudioClient {  
    @GET("/user/info")
    Call<UserInfo> getUserInfo();

    @PUT("/user/info")
    Call<UserInfo> updateUserInfo(
        @Body UserInfo userInfo
    );

    @DELETE("/user")
    Call<Void> deleteUser();
}
```

# HTTP资源路径

此外，还需要将URL的相对路径添加到注解的字符串参数中，例如`@GET("/user/info")`。

在大多数情况下，您会通过一个相对的URL，而不是一个完整的URL（像`http://futurestud.io/api/user/info`)。这样做的好处是Retrofit只需要定义一次的基础URL（`http://futurestud.io`），当您想改变基础URL的时候，您只需要改动一个地方。此外，它还使一些更高级的事情变得更容易，例如：动态基础网址。当然您也可以指定一个完整的URL。如果您想了解更多关于URL的处理，以及如何将这些基础URL和相对URL放在一起，请随时阅读我们的指南[URL handling，resolution and parsing](https://futurestud.io/tutorials/retrofit-2-url-handling-resolution-and-parsing)。

再次，一些简单的例子：

```java
public interface FutureStudioClient {  
    @GET("/user/info")
    Call<UserInfo> getUserInfo();

    @PUT("/user/info")
    Call<UserInfo> updateUserInfo(
        @Body UserInfo userInfo
    );

    @DELETE("/user")
    Call<Void> deleteUser();

    // example for passing a full URL
    @GET("https://futurestud.io/tutorials/rss/")
    Call<FutureStudioRssFeed> getRssFeed();
}
```

# 方法名和返回类型

您现在已经知道了如何使用HTTP请求方法的注解。然而，我们还没有谈到实际的java方法声明：`Call<UserInfo> getUserInfo();`。

它包含三个部分：

1. 方法名
2. 方法返回类型
3. 方法参数

让我们从最简单的方法名称开始。您可以自由定义方法名。Retrofit不关心，它不会对功能有任何的影响。不过，您应该选择一个名称帮助于您和其他开发人员了解这是什么API请求。

返回类型：您必须定义您期望从服务器获得的数据类型。例如，当您请求的用户的一些信息，您可以指定它叫`Call<UserInfo>`。`UserInfo`类包含的属性将保存用户数据。Retrofit 会自动映射，您不必做任何手动解析。如果您想要源响应对象(Raw Response)，您可以使用`ResponseBody`代替一个特定的类。如果您根本不关心服务器响应，您可以使用`Void`。在所有这些情况下，您必须将其封装成Retrofit类型:`Call<>` class。

最后，将参数传递给方法有多种方式，我们会给您一些选项的链接：

* @Body: [发送一个Java对象作为请求体](https://futurestud.io/tutorials/retrofit-send-objects-in-request-body)。
* @Url: [使用动态URL](https://futurestud.io/tutorials/retrofit-2-how-to-use-dynamic-urls-for-requests)。
* @Field: [发送 form-urlencoded 字段数据](https://futurestud.io/tutorials/retrofit-send-data-form-urlencoded)。

一些示范操作:

```java
public interface FutureStudioClient {  
    @GET("/user/info")
    Call<UserInfo> getUserInfo();

    @PUT("/user/info")
    Call<Void> updateUserInfo(
        @Body UserInfo userInfo
    );

    @GET
    Call<ResponseBody> getUserProfilePhoto(
        @Url String profilePhotoUrl
    );
}
```

由于路径和查询参数非常常见，我们将在接下来的两个章节中更详细地讨论它们。

## 路径参数

REST API是在动态URL上生成的。通过替换的URL去访问资源，例如让我们的第三个教程页面‘http://futurestud.io/api/tutorials/3’。结尾的3指定您想要访问哪一个教程。Retrofit提供了一个简单的办法来取代这些所谓的路径参数。例如在入门教程中看到的一个例子：

```java
public interface GitHubClient {  
    @GET("/users/{user}/repos")
    Call<List<GitHubRepo>> reposForUser(
        @Path("user") String user
    );
}
```

在这里，`{user}`是一个占位符，表示它的值是动态的，将在请求生成的时候创建。如果您的URL中包含路径参数，您需要在方法参数中添加一个`@Path()`参数，`@Path`的值匹配URL中的占位符（在这个例子中，它是`@Path("user")`）。您可以使用多个占位符，如果必要的话。只要确保您有足够的匹配参数。您甚至可以使用[可选的路径参数](https://futurestud.io/tutorials/retrofit-optional-path-parameters)。

## 查询参数

动态URL的另一大部分是查询参数。如果您使用我们的过滤器，您可以在我们的网站上看到它：`https://futurestud.io/tutorials?filter=video`。`?filter=video`是一个查询参数，它进一步描述了请求资源。与路径参数不同，您不需要将它们添加到注解url。您可以简单地添加一个`@Query()`和查询参数名称的方法参数。Retrofit将自动附加到请求上。如果将空值传递为查询参数，则将忽略它。您还可以添加[多个查询参数](https://futurestud.io/tutorials/retrofit-2-add-multiple-query-parameter-with-querymap)。

```java
public interface FutureStudioClient {  
    @GET("/tutorials")
    Call<List<Tutorial>> getTutorials(
        @Query("page") Integer page
    );

    @GET("/tutorials")
    Call<List<Tutorial>> getTutorials(
            @Query("page") Integer page,
            @Query("order") String order,
            @Query("author") String author,
            @Query("published_at") Date date
    );
} 
```

在上面的例子中，您也可以移除第一个`getTutorials()`方法，第二个方法使用一个`null`值传递给最后三个参数。

# 下一步是什么

这只是一篇API端点的介绍。您已经学习了在接口中添加新API的基本知识。您可以调整资源位置、HTTP方法、返回类型、路径和查询参数。

Retrofit提供了更多的选项，以进一步修改请求。例如，我们还没有谈到headers。还有很多要学习，所以请继续阅读更多教程！

如果您有反馈或问题，请在评论中让我们知道或在推特上 [@futurestud_io](https://futurestud.io/tutorials/twitter.com/futurestud_io) 我们。

让它摇滚并享Coding！

