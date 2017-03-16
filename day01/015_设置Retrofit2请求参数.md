# 015_设置Retrofit2请求参数
## 学习目标
- 熟练使用 Retrofit2 进行发送 Http 请求

## 课堂内容
### 1. Retrofit2 请求参数的配置

#### 1.1 @Path - 替换路径上的参数
```java
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId);
```

#### 1.2. @Query - 添加查询参数
```java
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```

#### 1.3. @QueryMap - 把多个查询参数组织成Map
```java
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);
```

