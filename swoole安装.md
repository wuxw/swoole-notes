#环境centos6.5和php 5.6.23(编译安装的php),编译安装swoole的1.8.8-rc1

#####jemalloc是一个比glibc malloc更高效的内存池技术在Facebook公司被大量使用，在FreeBSD和FireFox项目中使用了jemalloc作为默认的内存管理器。与tcmalloc不同的是jemalloc使用纯C语言开发。而tcmalloc是使用C++开发的
```bash
wget https://github.com/jemalloc/jemalloc/archive/4.2.1.tar.gz -O jemalloc.tar.gz
tar -zxvf  jemalloc.tar.gz
cd jemalloc-*
 ./autogen.sh
./configure
make -j 9
make install
#可能有个文档错误提示，可以忽略
ldconfig
cd ../
#如果出现libjemalloc.so警告后的问题
ln -fs /usr/local/lib/libjemalloc.so.2 /usr/lib64/

#redis官方提供的hiredis库实现。Swoole提供了__call魔术方法，来映射绝大部分Redis指令。
wget https://github.com/redis/hiredis/archive/v0.13.3.tar.gz -O hiredis.tar.gz
cd hiredis-*
make -j 9
make install
ldconfig
cd ../
#如果出现libhiredis.so警告后的问题
ln -sf /usr/local/lib/libhiredis.so.0.13 /usr/lib64/


wget https://github.com/swoole/swoole-src/archive/1.8.8-rc1.tar.gz -O swoole1.8.tar.gz
tar -zxvf swoole1.8.tar.gz 
cd swoole-*
phpize
./configure --enable-swoole-debug --enable-sockets  --enable-async-redis  --enable-openssl --enable-jemalloc
make && make install
make test
#php.ini存在extension=swoole.so,请忽略
echo 'extension=swoole.so'>>/etc/php.ini
php --ri swoole
至此，swoole的功能全部开启

#yum安装php的时候,swoole源码安装的时候会报个socket的警告(如是yum安装php，pecl安装swoole则不会出现警告)，解决方式如下：
#编辑sockets.ini(Centos在/etc/php.d/下)，注释掉sockets.so
在php.ini加上以下内容，注意顺序
extension=sockets.so
extension=swoole.so
```
