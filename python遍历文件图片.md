# python遍历文件图片


### 1. 使用glob
```python
# -*- coding:utf-8 -*-
import glob as go
import cv2

img_path = gb.glob("your-path/*.jpg")
for a_path in img_path:
    img = cv2.imread(a_path)
```

### 2. 使用os
```python
# -*- coding: utf-8 -*-
import os
def traverse_path(file_path):
#遍历file_path下所有文件，包括子目录
  files = os.listdir(file_path)
  for fi in files:
    fi_d = os.path.join(file_path, fi)

    if os.path.isdir(fi_d):

        traverse_path(fi_d)
    else:
        print os.path.join(file_path, fi_d)

#递归遍历/root目录下所有文件
traverse_path('/root')
```

## 判断文件是否存在

> [reference_link](http://www.cnblogs.com/jhao/p/7243043.html)

- 判断文件/文件夹是否存在
    ```python
    import os
    os.path.exists(test_file.txt/file_dir)
    #True
    os.path.exists(no_exist_file.txt/file_dir)
    #False
    ```


- 只检查文件
    ```python
    import os
    os.path.isfile("test-data")
    ```

## 判断文件是否可读写操作
