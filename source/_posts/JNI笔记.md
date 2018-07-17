---
title: JNI基础
date: 2018/7/17 星期二 9:24:06  
tags: hexo
---
## C基本语法
* include 类似java import  stdlib.h stdio.h
* main(){}  system("pause");
* C 基本数据类型  C没有boolean byte  C long 4 char 1 长度跟java有区别
* signed unsigned 只能用来修饰 char short int long 整形变量
* printf("输出的内容 + 占位符", 变量) 占位符不要乱用 要选择对应的占位符
* C的字符串 char 数组 c 数组声明的时候 [] 要放到变量名后面
* char数组结束符 '\0'
* char array[] = {'a','b','\0'};
* char array[] = "abcd";
* char* str = "abcd";
* scanf("占位符",内存地址); 取地址符 &
* c 字符串不检测越界 
* 内存地址 
* 指针  int i = 123;  int* pointer = &i;
*   pointer 取到的是i的地址   *pointer 取到的是i的值   &pointer 取出pointer自己的地址
*   野指针 指针赋值操作之前要先初始化 给指针变量赋一个程序中声明的变量的地址
*   int* 指向int类型变量的地址
* 值传递和引用传递 都是传递的值 但是引用传递传递的是地址值 子函数修改main函数的变量的值 一定要用引用传递
* 指针和数组的关系  数组就是连续的内存空间 拿到数组的首地址 就可以对数组的每一个元素进行操作  通过指针的位移运算 位移运算 指针变量+1移动的字节数跟指针的类型有关
* 指针的长度  32位操作系统4个字节 64位 8字节
* 多级指针  几级指针就有几个星  int i = 123; 
* 结构体  类似class struct Student  结构体只能声明函数指针 
* 结构体长度 大于或等于所有成员的长度 是最长的成员的长度的整数倍
* (* pointer).age  ->
* 联合体 
* 枚举 
* typedef

## AndroidStudio3.0配置NDK
### 环境：win10x64，之前电脑上没有配置过NDK开发环境。
* 步骤：新建工程，然后勾上include c++ support选项。创建工程后，提示ndk没安装，就安装。cmaketext没安装，继续安装。ok了，并没有一些什么七七八八的配置。

### 环境：win10x64，在app工程里面建多个JNI module
- 新建module，在project视图下的module根目录，新建CMakeLists.txt，CMakeLists配置见其他项目的CMakeList源码，简单几个英文，按照英文配置。

- 在java代码中写方法，跟调用so包的静态代码块，然后到java包下执行javah命令。

- 编写c文件代码。鼠标右键module，linked with c or c++ support.完成。

* JNI编译时错误 编码GBK的不可映射字符 解决方法
`javah -jni -encoding UTF-8 com.example.XXXX.XXXX.MainActivity`   


## 交叉编译
* 在一个平台上去编译另一个平台上可以执行的本地代码
* cpu平台 arm x86 mips
* 操作系统平台  windows linux mac os
* 原理 模拟不同平台的特性去编译代码 

## jni开发工具
* ndk native develop kit 
* ndk目录
   * docs 帮助文档
	* platforms  好多平台版本文件夹 选择时选择项目支持的最小版本号对应的文件夹
	* 每一个版本号的文件夹中放了 不同cpu架构的资源文件
	* include文件夹 jni开发中常用的 .h头文件
	* lib 文件夹 google打包好的 提供给开发者使用的 .so文件
	* samples google官方提供的样例工程 可以参考进行开发
	* android-ndk-r9d\build\tools linux系统下的批处理文件 在交叉编译时会自动调用
	* ndk-build 交叉编译的命令
* cdt eclipse的插件 高亮C代码 C的代码提示

## jnihelloworld
* jni开发的步骤
 * ①写java代码 声明本地方法 用到native关键字 本地方法不用去实现
 * ②项目根目录下创建jni文件夹
 * ③在jni文件夹下创建.c文件
 	* 本地函数命名规则: Java_包名_类名_本地方法名
 	* JNIENV* env JNIEnv 是JniNativeInterface这个结构体的一级指针 
 	* JniNativeInterface这个结构体定义了大量的函数指针
 	* env 就是结构体JniNativeInterface这个结构体的二级指针
 	* (*env)->调用结构体中的函数指针
 	* 第二个参数jobject 调用本地函数的java对象就是这个jobject
 * ④ 导入<jni.h>
 * ⑤ 创建Android.mk makefile 告诉编译器.c的源文件在什么地方,要生成的编译对象的名字是什么
 	
	LOCAL_PATH := $(call my-dir)

    include $(CLEAR_VARS)

    LOCAL_MODULE    := hello   #指定了生成的动态链接库的名字
    LOCAL_SRC_FILES := hello.c #指定了C的源文件叫什么名字

    include $(BUILD_SHARED_LIBRARY)
 * ⑥ 调用ndk-build编译c代码生成动态链接库.so文件 文件的位置 lib->armeabi->.so
 * ⑦ 在java代码中加载动态链接库 System.loadlibrary("动态链接库的名字"); Android.mkLOCAL_MODULE所指定的名字
##jni开发中的常见错误
* java.lang.UnsatisfiedLinkError: Native method not found: 本地方法没有找到
	* 本地函数名写错
	* 忘记加载.so文件 没有调用System.loadlibrary 
* findLibrary returned null
	* System.loadLibrary("libhello"); 加载动态链接库时 动态链接库名字写错
	* 平台类型错误 把只支持arm平台的.so文件部署到了 x86cpu的设备上 
		* 在jni目录下创建 Application.mk 在里面指定 
		* APP_ABI := armeabi
		  APP_PLATFORM := android-14
* javah 
	* jdk 1.7 项目 src目录下运行javah
	* jdk 1.6 项目 bin目录下 classes文件夹
	* javah native方法声明的java类的全类名 
## jni简便开发流程
* ① 写java代码 native 声明本地方法
* ② 添加本地支持 右键单击项目->andorid tools->add native surport
	* 如果发现 finish不能点击需要给工作空间配置ndk目录的位置
	* window->preferences->左侧选择android->ndk 把ndk解压的目录指定进来
* ③ 如果写的是.c的文件 先修改一下生成的.cpp文件的扩展名 不要忘了 相应修改Android.mk文件中LOCAL_SRC_FILES的值
* ④ javah生成头文件 在生成的头文件中拷贝c的函数名到.c的文件
* ⑤ 解决CDT插件报错的问题
* 右键单击项目选择 properties 选测 c/c++ general->paths and symbols->include选项卡下->点击add..->file system 选择ndk目录下 platforms文件夹 对应平台下(项目支持的最小版本)
 usr 目录下 arch-arm -> include  确定后 会解决代码提示和报错的问题
* ⑥编写C函数 如果需要单独编译一下c代码就在c/c++视图中找到小锤子 
*  如果想直接运行到模拟器上 就不用锤子了
* ⑦ java代码中不要忘了 system.loadlibrary();  
## C代码中向logcat输出内容

	Android.mk文件增加以下内容
	LOCAL_LDLIBS += -llog
	C代码中增加以下内容
	#include <android/log.h>
	#define LOG_TAG "System.out"
	#define LOGD(...) __android_log_print(ANDROID_LOG_DEBUG, LOG_TAG, __VA_ARGS__)
	#define LOGI(...) __android_log_print(ANDROID_LOG_INFO, LOG_TAG, __VA_ARGS__)
* define C的宏定义 起别名  #define LOG_TAG "System.out" 给"System.out"起别名LOG_TAG 
* #define LOGI(...) __android_log_print(ANDROID_LOG_INFO, LOG_TAG, __VA_ARGS__)
* 给 __android_log_print函数起别名  写死了前两个参数 第一个参数 优先级 第二个参数TAG
* __VA_ARGS__ 可变参数的固定写法
* LOGI(...)在调用的时候 用法跟printf()一样
## C代码回调java方法

* ① 找到字节码对象 
	*  //jclass      (*FindClass)(JNIEnv*, const char*); 
	*  //第二个参数 要回调的java方法所在的类的路径 "com/itheima/callbackjava/JNI"
* ② 通过字节码对象找到方法对象
	* //jmethodID   (*GetMethodID)(JNIEnv*, jclass, const char*, const char*);
	* 第二个参数 字节码对象 第三个参数 要反射调用的java方法名 第四个参数 要反射调用的java方法签名
	* javap -s 要获取方法签名的类的全类名 项目/bin/classes 运行javap 
* ③ 通过字节码创建 java对象(可选) 如果本地方法和要回调的java方法在同一个类里可以直接用 jni传过来的java对象 调用创建的Method
	* jobject obj =(*env)->AllocObject(env,claz);
	* 当回调的方法跟本地方法不在一个类里 需要通过刚创建的字节码对象手动创建一个java对象
	* 再通过这个对象来回调java的方法
	* 需要注意的是 如果创建的是一个activity对象 回调的方法还包含上下文 这个方法行不通!!!回报空指针异常 
* ④ 反射调用java方法
	* //void        (*CallVoidMethod)(JNIEnv*, jobject, jmethodID, ...);
	* 第二个参数 调用java方法的对象 第三个参数 要调用的jmethodID对象 可选的参数 调用方法时接收的参数 