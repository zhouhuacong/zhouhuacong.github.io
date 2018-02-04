# AS使用cmake方式运行找不到.so问题

这真的是很无语的一个SB问题，不知道这个cmake和gradle是如何协同工作的，居然必须在`build.gradle`文件中指定的目录下且是一级目录下放`.so`文件，不然的话就 居！然！找！不！到！

1. 必须在` build.gradle`中写上，这是我在尝试换其他目录行不行，别直接复制，需要改成自己的目录
    ```gradle
    sourceSets.main {
        jniLibs.srcDirs('src/main/jni', 'src/main/jni/armeabi-v7a/lib')
    }
    ```
2. 把要用的`.so`放在指定目录的一级目录下，就是上述的`src/main/jni/${ANDROID_ABI}/`下，举个例子就是
    - `src/main/jni/armeabi-v7a/libnn.so`路径下就OK了
    - 放在这里是不行：`src/main/jni/armeabi-v7a/lib/libnn.so`，即使我在`build.gradle`中好像指定了这个路径，但是这样真不work！

3. `CMakeLists.txt`文件就按照我们指定的路径找到`.so`文件即可，头文件的查找可以和`.so`分离开，这个没关系，也就是说头文件的路径和`.so`文件的路径没关系，在 `CMakeLists.txt`中指定好就可以了.
    ```cmake
    add_library(mtnn SHARED IMPORTED)
    set_target_properties(mtnn PROPERTIES IMPORTED_LOCATION
                          ${CMAKE_SOURCE_DIR}/src/main/jni/${ANDROID_ABI}/libmtnn.so)
    include_directories(${CMAKE_SOURCE_DIR}/src/main/jni/${ANDROID_ABI}/include/)
    ```
    可以看到上述中的`include_directories`和`set_target_properties`指定的路径不一样，这个没问题.
