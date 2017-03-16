# 013_Retrofit2引入和简介
## 学习目标
- 了解使用 Retrofit2 的作用

## 学习基础要求

## 引言和回顾
下面我们学习本项目使用的网络请求工具 Retrofit2。

## 课堂内容
### 1. Retrofit2 简介
Retrofit2 是 Square 公司推出的类型安全的 Http 客户端，主要用于 Android 和 Java。Retrofit 可以利用接口、方法和注解参数（parameter annotations）来声明式定义一个请求应该如何被创建，将网络请求变成方法的调用，使用起来非常简洁方便。 Retrofit 要求用在 Java 7 or Android 2.3 以上版本上，先简单了解下 Retrofit 的使用步骤：

1.把 HTTP API 变成 Java 的接口
```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```


在 GithubService 接口中有一个方法 listRepos，这个方法用了 @GET 的方式注解，表明这是一个 GET 请求。在后面的括号中的 users/{user}/repos 是请求的路径，其中的 {user} 表示的是这一部分是动态变化的，它的值由方法的参数传递过来，而这个方法的参数`@Path("user") String user`即是用于替换 {user} 。另外这个方法的返回值是 `Call<List<Repo>>`，可以看出 Retrofit 用注解的方式来描述一个 Http 网络请求相关的数据，并且根据这个接口创建请求对象。

2.发出这个网络请求
```java
//使用构建者模式创建 Retrofit 对象
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://api.github.com/")
        .addConverterFactory(GsonConverterFactory.create())
        .build();

GitHubService service = retrofit.create(GitHubService.class);
Call<List<Repo>> repos = service.listRepos("octocat");
repos.enqueue(new Callback<List<Repo>>() {
    @Override
    public void onResponse(Call<List<Repo>> call, Response<List<Repo>> response) {
            
    }
    @Override
    public void onFailure(Call<List<Repo>> call, Throwable t) {

    }
});
```
a.先是构建了一个 Retrofit 对象，其中传入了 baseUrl 参数，baseUrl 和上面的 GET 方法后面的路径组合起来才是一个完整的 url。除了 baseUrl，还有一个 converterFactory，它是用于把返回的 http response 转换成 Java 对象，对应方法的返回值 Call<List<Repo>> 中的 List<Repo>>，其中 Repo 是自定义的类。
b.接着调用它的 create 方法创建了 GitHubService 的实例，然后就可以调用这个实例的方法来请求网络了。调用 listRepo 方法得到一个 Call 对象，然后可以使用 enqueue 或者 execute 来执行发起请求，enqueue 是异步执行，而 execute 是同步执行。

### 2. Retrofit2 引入
在 Module 的 build.gradle 文件中添加 retrofit 相关依赖

```java
dependencies {
    ....
    // 网络访问工具
    compile 'com.squareup.retrofit2:retrofit:2.1.0'
    compile 'com.google.code.gson:gson:2.2.4'
    compile 'com.squareup.retrofit2:converter-gson:2.1.0'
}
```

> - `compile 'com.squareup.retrofit2:retrofit:2.1.0'` 添加 Retrofit 依赖
> - `compile 'com.google.code.gson:gson:2.2.4'` 添加解析工具的依赖
> - `compile 'com.squareup.retrofit2:converter-gson:2.1.0'` 添加Retrofit和解析工具关联的依赖




