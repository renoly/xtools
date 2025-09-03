# xtools
Android自用工具库，实现的功能有：

- [x] 使用Retrofit+RxJava封装网络请求
- [ ] 进制转换工具



### 集成步骤

**如何查看Gradle版本**

- 在`项目根目录/gradle/wrapper/gradle-wrapper.properties`
- 或者Ctrl+Alt+Shift+S打开Project Structure->Project->Gradle Version

#### 配置远程仓库

- Gradle Version < 7.0，在`build.gradle`添加

  ```groovy
  allprojects {
      repositories {
          // JitPack 远程仓库：https://jitpack.io
          maven { url 'https://jitpack.io' }
      }
  }
  ```

- Gradle Version >= 7.0，在`settings.gradle`添加

  ```groovy
  dependencyResolutionManagement {
      repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
      repositories {
          mavenCentral()
          // JitPack 远程仓库：https://jitpack.io
          maven { url 'https://jitpack.io' }
      }
  }
  ```

#### 引入依赖

```groovy
dependencies {
    implementation 'com.github.renoly:xtools:1.0.0'
}
```



### 使用网络库

###### 调用getInfo接口

声明权限


```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

Kotlin使用示例：

```kotlin
val provider = RetrofitProvider(
    "http://127.0.0.1:4523/m1/6823482-6537445-default/", true
)
val api = provider.create(SampleApi::class.java)

api.getInfo<User>().applyIoToMainSchedulers().compose(apiResponseToNetworkResult())
   .subscribe(object : BaseObserver<User>() {
      override fun onStart() {
      }

      override fun onLoading() {
      }

      override fun onSuccess(data: User) {
         // success=true且业务回调正确
         Log.d(TAG, "onSuccess: ${data}")
      }

      override fun onError(error: NetworkResult.Error) {
         // success=false 或者其他业务错误回调
         Log.e(TAG, "onError: ${error}")
      }

      override fun onFinish() {
      }
   })
```

Java使用示例：

```java
RetrofitProvider provider = new RetrofitProvider(
        "http://127.0.0.1:4523/m1/6823482-6537445-default/",
        true
);
SampleApi api = provider.create(SampleApi.class);

api.<User>getInfo()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .compose(apiResponseToNetworkResult())
        .subscribe(new BaseObserver<User>() {
            @Override
            public void onStart() {
            }

            @Override
            public void onLoading() {
            }

            @Override
            public void onSuccess(User data) {
                Log.d(TAG, "onSuccess: " + data);
            }

            @Override
            public void onError(NetworkResult.Error error) {
                Log.e(TAG, "onError: " + error);
            }

            @Override
            public void onFinish() {
            }
        });
```



接口数据结构：

```json
{
   "success": true,
   "code": 0,
   "message": "ok",
   "data": {
      "id": 1,
      "name": "jake",
      "age": 18,
      "desc": "he likes to eat vegetables"
   }
}
```





