# AS使用NDK Cmake方式依赖第三方库注意事项
> AS2.2以后支持Cmake了，以前的`Android.mk`的方式可以告别了，[官方教程](https://developer.android.com/studio/projects/add-native-code.html)


## CMakeLists.txt文件的书写
1. 引入第三方库使用`add_library()`需要指定是`SHARED`->`.so` or `STATIC`->`.a`，和`IMPORTED`，并且每次只能写一个库，如果引入2个第三方库就要写2个`add_library()`
    ```
    add_library(
            nn
            SHARED
            IMPORTED
            )
    ```
2. `include_directories()`用于指定头文件的，一般是在`include`文件夹下，可以用于第三方库或者自己源文件的头文件.
    ```
    include_directories(src/main/jni/armeabi-v7a/include/)
    ```
3. 在引入第三方库时还需要指定`set_target_properties()`库的路径，也就是`.so`or`.a`库文件的路径，这里要注意的是为了区分CPU架构，不同版本的`.so`文件放的路径也不一样，所以要用`${ANDROID_ABI}`来指定路径，这也让我们必须把对应CPU架构的`.so`文件放在指定CPU架构目录下（这里只有`armeabi-v7a`）:
        ![cpu架构.png](http://upload-images.jianshu.io/upload_images/1638086-d0faebf2a7f41dee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
        
        set_target_properties(nn
                      PROPERTIES IMPORTED_LOCATION
                      ${CMAKE_SOURCE_DIR}/src/main/jni/${ANDROID_ABI}/lib/libnn.so
                      )
    
    如果直接写相对路径可能会出现 `Error:error: '../../../../src/main/libs/libjsoncpp.a', needed by '../obj/armeabi/libnative-lib.so', missing and no known rule to make it`
4. 如果要依赖NDK中的库
    ```
    find_library( jnigraphics-lib
    jnigraphics )
    target_link_libraries( StackBlur
    ${log-lib}
    ${m-lib}
    ${jnigraphics-lib} )
    ```

附上CMakeLists.txt全部内容：
```
cmake_minimum_required(VERSION 3.4.1)

add_library( native-lib
             SHARED
             src/main/cpp/native-lib.cpp )

# 添加实验室nn.so库
add_library(
            nn
            SHARED
            IMPORTED
            )

set_target_properties(nn
                      PROPERTIES IMPORTED_LOCATION
                      ${CMAKE_SOURCE_DIR}/src/main/jni/${ANDROID_ABI}/lib/libnn.so
                      )
# 指定头文件路径
include_directories(src/main/jni/armeabi-v7a/include/)

find_library( log-lib
              log )

target_link_libraries( native-lib nn ${log-lib} )
```
## Gradle的书写
AS创建时就是使用的cmake，所以Gradle基本都没啥问题，需要添加的可能有一下几点

1. 指定需要编译的`abi`版本，在`android{}`->`defaultCondfig{}`下
    ```gradle
    ndk {
        abiFilter 'armeabi-v7a'
    }
    ```
2. 指定c++版本和异常处理
    ```gradle
    externalNativeBuild {
        cmake {
            cppFlags "-std=c++11 -frtti-fexceptions"
        }
    }
    ```
