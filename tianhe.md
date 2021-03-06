# 天河二号使用说明 #
-----------------
### Windows端登录

1. 使用IE登录[超算接入平台](https://vpn2.nscc-gz.cn:6443)，按提示安装`SSLVPN`后输入用户名密码。
2. 此时，SSLVPN插件会自动运行。如插件提示连接不上，则需重启电脑。 
3. 安装`XShell`，新建会话，属性按如下配置
 - 主机填写`172.16.22.11`
 - 用户身份验证选`Pubilc Key`
 - 用户密钥选择`nsfc2015_304.id`文件
 - 用户名填`nsfc2015_304`
 - 密码为空 

4. 安装`FileZilla`，协议选择SFTP，端口`23`，其它与`XShell`配置类似

### Linux端登录
1. 安装`vpnc`
2. 在`/etc/vpnc/default.conf`中加入
```
IPSec gateway vpn1.nscc-gz.cn
IPSec ID nsfc2015_304
IPSec secret “共享密钥”
Xauth username nsfc2015_304
Xauth password “密码”
```
(加入后可直接使用sudo vpnc-connect 进行连接vpnc)
3. 创建脚本`tianhe.sh`,内容为
```
#!/bin/bash
ssh -p 23 -i /path/nsfc2015_304.id nsfc2015_304@172.16.22.11
```
4. 终端中输入
```
sudo vpnc
./tianhe.sh
``` 
即可登录
(使用完后输入命令sudo vpnc-disconnect断开连接，不然无法上网)
### 超算中环境配置
- 天河使用`module`来管理环境变量，但由于配置项过多，所以可以写成脚本方便配置，目前默认配置为`~/module_add.sh`
- MXNet的module配置
```Bash
#!/bin/bash
module purge
module unload gcc/4.9.2
module load gcc/5.2.0 glib/2.44.1 atlas/3.10.2 Python/2.7.9 opencv/2.4.11 gmp/4.3.2 mpc/0.8.1 MPFR/2.4.2 ffmpeg/0.11.1 atlas/3.10.2 atk/2.16.0 pango/1.37.0 pixman/0.32.6 libgphoto2/2.5.8 intel-compilers/15.0.1 cairo/1.14.2 intel-compilers/mkl-15 freetype/2.6 ffmpeg/0.11.1
module list
export PKG_CONFIG_PATH=/HOME/nsfc2015_304/share/pkgconfig
```
- Caffe的module配置
```Bash
#!/bin/bash
module purge
module load caffe/v20160510-cpu3 java/jdk1.8.0_11 glibc/2.14 atlas/3.10.2 Python/2.7.9 gmp/4.3.2 mpc/0.8.1 MPFR/2.4.2 ffmpeg/0.11.1 MPI/Gnu/MPICH/3.1-4.9.2
module unload Python/2.7.9-fPIC 
module list
export PKG_CONFIG_PATH=/HOME/nsfc2015_304/share/pkgconfig
```
- `pkgconfig`中放有
```Bash
$ cat pkgconfig/opencv.pc 
#Package Information for pkg-config
prefix=/NSFCGZ/app/opencv/2.4.11
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir_old=${prefix}/include/opencv
includedir_new=${prefix}/include
Name: OpenCV
Description: Open Source Computer Vision Library
Version: 2.4.11
Libs: -L${exec_prefix}/lib  -lopencv_stitching -lopencv_objdetect -lopencv_superres -lopencv_videostab -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_video -lopencv_photo -lopencv_ml -lopencv_imgproc -lopencv_flann -lopencv_core
Libs.private: -L/lib64 -lwebp -lpng -lz -ltiff -ljasper -ljpeg -lImath -lIlmImf -lIex -lHalf -lIlmThread -lgtk-3 -lgdk-3 -lpangocairo-1.0 -lpango-1.0 -latk-1.0 -lcairo-gobject -lcairo -lgdk_pixbuf-2.0 -lgio-2.0 -lgthread-2.0 -lgstvideo-1.0 -lgstapp-1.0 -lgstbase-1.0 -lgstriff-1.0 -lgstpbutils-1.0 -lgstreamer-1.0 -lgobject-2.0 -lglib-2.0 -ldc1394 -lv4l1 -lv4l2 -lavcodec -lavformat -lavutil -lswscale -lavresample -lgphoto2 -lgphoto2_port -lexif -lbz2 -ldl -lm -lpthread -lrt
Cflags: -I${includedir_old} -I${includedir_new}
```

### 文件目录说明
- `~/tools`下放置自己的编译好工具
- `~/work`下放置开发的项目
- `~/data`下放置数据集
- `~/parafile`下为天河自动生成的项目性能报告
