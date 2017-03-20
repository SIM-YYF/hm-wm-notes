# 014_使用Retrofit2的步骤
## 学习目标
- 熟练使用 Retrofit2 进行发送 Http 请求

## 引言
下面我们结合外卖这个 MVP 架构项目来使用 Retrofit 实现登陆请求


## 回顾

- 创建Retorfit.Builder对象，通过Builder指定基本配置信息。

- 通过Builder构建Retorfit对象

- 定义 java 接口描述网络请求接口的 API

- 调用接口方法创建 call 对象

- 调用 Call.enqueue 方法发起异步网络请求



## 课堂内容
### 1. 创建 Retrofit.Builder 对象
```java
// 第一步：创建Builder，配置 baseUrl 和数据解析工具
Retrofit.Builder builder = new Retrofit.Builder();
builder.baseUrl(Constant.BASEURL);
builder.addConverterFactory(GsonConverterFactory.create());
```

### 2. 创建 Retrofit 对象
Retrofit retrofit = builder.build();

### 3. 定义 java 接口描述网络请求接口的 API
在 java 接口中的方法和参数上添加注解来描述 api

    1. 在方法上使用 @GET, @POST, @PUT, @DELETE, @HEAD 来描述 http 请求方式
    2. 使用 @GET("group/{id}/users") 等注解的参数，描述 http 请求 url 地址
    3. 使用 @Path("id") 注解在参数上，描述 URL 地址中包含的占位符的值
    4. 使用 @Query("username") 注解在参数上，描述 URL 地址中包含的查询参数

```java
public interface ResponseInfoAPI {

    /**
    * 1.@GET 注解描述 API 请求的方式
    * 2.@GET(Constant.LOGIN) 注解中的参数描述请求 URL 中的路径
    * 3.@Query 注解描述请求 URL 中的参数
    * 方法必须返回 Call<ResponseInfo> 类型对象，其中范型是返回的 json 对应的 JavaBean 类
    */
    @GET(Constant.LOGIN)
    Call<ResponseInfo> login(@Query("username") String username, @Query("password") String password);
}
```

### 4. 根据接口文档定义响应数据的 JavaBean
定义 ResponseInfo 类
```java
public class ResponseInfo {

    private String code;
    private String data;

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }
}
```

### 5. 创建 api 接口对象
```java
ResponseInfoAPI api = retrofit.create(ResponseInfoAPI.class);
```

> `retrofit.create(ResponseInfoAPI.class)` 实际上是创建了 ResponseInfoAPI 接口的动态代理对象

### 6. 调用接口方法创建 call 对象
调用接口方法创建 Call 对象，请求相关数据都被封装到 Call 对象里了

### 7. 调用 Call.enqueue 方法发起异步网络请求
调用 Call.enqueue 方法发起异步网络请求，比处理网络请求结果
注意：5、6、7 步可以使用链式编程
```java
public void login(String username, String password){
    //4.创建 Retrofit 对象
    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(Constant.HOME) //配置 api 接口的 baseUrl
            .addConverterFactory(GsonConverterFactory.create()) //配置json响应数据的 json 解析工具转换器
            .build();

    //5.创建 api 接口对象
    ResponseInfoAPI responseInfoAPI = retrofit.create(ResponseInfoAPI.class);
    //6.调用接口方法创建 call 对象
    Call<ResponseInfo> login = responseInfoAPI.login(username, password);
    //7.调用 Call.enqueue 方法发起异步网络请求
    login.enqueue(new Callback<ResponseInfo>() {
        @Override
        public void onResponse(Call<ResponseInfo> call, Response<ResponseInfo> response) {
            ResponseInfo body = response.body();
            if("0".equals(body.getCode())) {
                // 登陆成功
                activity.success();
            }
        }

        @Override
        public void onFailure(Call<ResponseInfo> call, Throwable t) {
            //登陆失败
            activity.failed();
        }
    });
}
```

## 拓展和参考资料
- [Say hello to retrofit](http://blog.csdn.net/ghost_programmer/article/-details/52372065)

    http://blog.csdn.net/ghost_programmer/article/-details/52372065
    
- [retrofit github](https://github.com/square/retrofit) 

    https://github.com/square/retrofit
    
- [retrofit 官网](http://square.github.io/retrofit/)

    http://square.github.io/retrofit/
    
- [Android Retrofit源码解析](https://segmentfault.com/a/1190000006767113)

    https://segmentfault.com/a/1190000006767113

