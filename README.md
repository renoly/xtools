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
    implementation 'com.github.renoly:xtools:1.0.6'
}
```



### 使用网络库

1. 声明权限


```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

2. Kotlin使用示例：

```kotlin
val provider = RetrofitProvider(
   baseUrl = "http://127.0.0.1:4523/m1/6823482-6537445-default/",
   isDebug = true
)
val api = provider.create(SampleApi::class.java)


api.getInfo()
   .applyIoToMainSchedulers()
   .compose(apiResponseToNetworkResult())
   .subscribe(object : BaseObserver<Info>() {
      override fun onStart() {
         Log.d(TAG, "onStart: ")
      }

      override fun onLoading() {
         Log.d(TAG, "onLoading: ")
      }

      override fun onSuccess(data: Info) {
         Log.d(TAG, "onSuccess: ${data}")
         runOnUiThread {
            test.text = data.name
         }
      }

      override fun onError(error: NetworkResult.Error) {
         // success=false 或者其他业务错误回调
         Log.e(TAG, "onError: ${error}")
      }

      override fun onFinish() {
         Log.d(TAG, "onFinish: ")
      }

   })
```


网络接口会返回如下数据：
```json
{
   "success": true,
   "code": 0,
   "message": "ok",
   "data": "aaaa"
}
```





