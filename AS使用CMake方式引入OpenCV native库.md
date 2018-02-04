# AS使用CMake方式引入OpenCV native库

把从opencv官网中下载好的android包解压，你将看到如下文件结构
![opencv静态库文件目录结构](http://upload-images.jianshu.io/upload_images/1638086-cd8dc1cca4ceb786.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![opencv头文件目录结构](http://upload-images.jianshu.io/upload_images/1638086-88d4ec76a0d7d512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们需要做的就是把`.a`,`.so`静态库动态库和`头文件`加入到Android项目中去


`CMakeLists.txt`如下：

```cmake
cmake_minimum_required(VERSION 3.4.1)

set(JNI_DIR "${CMAKE_SOURCE_DIR}/src/main/jni")
set(CPP_DIR "${CMAKE_SOURCE_DIR}/src/main/cpp")

add_library( native-lib
             SHARED
             src/main/cpp/native-lib.cpp )

#添加第三方mtnn.so库
add_library(mtnn
            SHARED
            IMPORTED)
set_target_properties(mtnn
                      PROPERTIES IMPORTED_LOCATION
                      ${JNI_DIR}/${ANDROID_ABI}/libmtnn.so )
# 指定头文件路径
include_directories(${JNI_DIR}/${ANDROID_ABI}/include/)

# 添加opencv
include_directories(${CPP_DIR}/opencv_include/)

add_library(libopencv_calib3d STATIC IMPORTED)
set_target_properties(libopencv_calib3d PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_calib3d.a)

add_library(libopencv_core STATIC IMPORTED)
set_target_properties(libopencv_core PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_core.a)

add_library(libopencv_dnn STATIC IMPORTED)
set_target_properties(libopencv_dnn PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_dnn.a)

add_library(libopencv_features2d STATIC IMPORTED)
set_target_properties(libopencv_features2d PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_features2d.a)

add_library(libopencv_flann STATIC IMPORTED)
set_target_properties(libopencv_flann PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_flann.a)

add_library(libopencv_highgui STATIC IMPORTED)
set_target_properties(libopencv_highgui PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_highgui.a)

add_library(libopencv_imgcodecs STATIC IMPORTED)
set_target_properties(libopencv_imgcodecs PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_imgcodecs.a)

add_library(libopencv_imgproc STATIC IMPORTED)
set_target_properties(libopencv_imgproc PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_imgproc.a)

add_library(libopencv_ml STATIC IMPORTED)
set_target_properties(libopencv_ml PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_ml.a)

add_library(libopencv_objdetect STATIC IMPORTED)
set_target_properties(libopencv_objdetect PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_objdetect.a)

add_library(libopencv_photo STATIC IMPORTED)
set_target_properties(libopencv_photo PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_photo.a)

add_library(libopencv_shape STATIC IMPORTED)
set_target_properties(libopencv_shape PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_shape.a)

add_library(libopencv_stitching STATIC IMPORTED)
set_target_properties(libopencv_stitching PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_stitching.a)

add_library(libopencv_superres STATIC IMPORTED)
set_target_properties(libopencv_superres PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_superres.a)

add_library(libopencv_video STATIC IMPORTED)
set_target_properties(libopencv_video PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_video.a)

add_library(libopencv_videoio STATIC IMPORTED)
set_target_properties(libopencv_videoio PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_videoio.a)

add_library(libopencv_videostab STATIC IMPORTED)
set_target_properties(libopencv_videostab PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_videostab.a)

add_library(libopencv_java3 SHARED IMPORTED)
set_target_properties(libopencv_java3 PROPERTIES IMPORTED_LOCATION
                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_java3.so)


find_library( log-lib log )

target_link_libraries( native-lib mtnn
libopencv_calib3d
libopencv_core
libopencv_dnn
libopencv_features2d
libopencv_flann
libopencv_highgui
libopencv_imgcodecs
libopencv_imgproc
libopencv_ml
libopencv_objdetect
libopencv_photo
libopencv_shape
libopencv_stitching
libopencv_superres
libopencv_video
libopencv_videoio
libopencv_videostab
libopencv_java3
${log-lib}
)
```


`build.gradle`如下：

```gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 26
    defaultConfig {
        applicationId "com.xxx.xxx"
        minSdkVersion 15
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        externalNativeBuild {
            // 重点
            cmake {
                cppFlags "-std=c++11 -frtti -fexceptions"
            }
        }
        // 重点
        ndk {
            abiFilter 'armeabi-v7a'
        }
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }
    // 重点
    sourceSets.main {
        jniLibs.srcDirs('src/main/jni')
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.1'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
}

```

遇到的错误

1. Error:error: undefined reference to 'carotene_o4t::cmpGT(carotene_o4t::Size2D const&, unsigned short const*, int, unsigned short const*, int, unsigned char*, int)'

You need to link with `libtegra_hal`.

It is included in the 3rdparty folder: `sdk/native/3rdparty/libs/armeabi-v7a/libtegra_hal.a`

2. Error:error: undefined reference to 'Imf::ChannelList::findChannel(char const*) const'
需要从`sdk/native/3rdparty/libs/armeabi-v7a/libIlmImf.a`

3. Error:undefined reference to 'TIFFNumberOfStrips'
删除`libopencv_java3.so`的引用
    ```cmake
    #add_library(libopencv_java3 SHARED IMPORTED)
    #set_target_properties(libopencv_java3 PROPERTIES IMPORTED_LOCATION
    #                     ${JNI_DIR}/${ANDROID_ABI}/libopencv_java3.so)
    ```

4. Error:error: undefined reference to 'gzgets'