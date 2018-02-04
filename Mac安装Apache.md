# Mac使用AB(Apache Bench)测试问题

## 问题 apr_socket_recv: Connection reset by peer (54)

这个错误一般是因为MacOSX默认自带的ab限制了并发数导致的
需要重新下载并编译`apache`，备份原来的ab替换原来的ab。

### 安装Apache
[apr下载地址](http://apr.apache.org/download.cgi)
下载
- apr-1.6.3.tar.bz2
- apr-util-1.6.1.tar.bz2

先安装`apr`解压后进入根目录下开始编译、安装

安装路径默认是`/usr/local/apr`

```shell
$ ./configure
$ sudo make install
```

安装`apr-util`
```shell
$ ./configure --with-apr=/usr/local/apr
$ sudo make install
```

安装httpd
```shell

```