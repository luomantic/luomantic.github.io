1，Android 不Root查看App /data/data/目录文件
	
	条件： 首先必须是Android Debug签名的应用才可以
	adb shell
	run-as 应用包名
	cd /data/data/应用包名
	
2，adb通过wifi连接android设备

	一. adb USB连接到WIFI
	1. 连上数据线
	2. adb tcpip 5555
	3. adb connect 192.168.1.173:5555（完成）
	
	二. adb无连接到WIFI(需root权限)
	1. 终端
	//需要取得超级管理员权限执行su，再执行
	setprop service.adb.tcp.port 5555
	stop adbd
	start adbd
	
	2. PC端	
	//连接wifi
	adb connect 192.168.1.173
	
	-------------------------------------------
	测 试 板 卡：192.168.1.173
	HM NOTE 1S：192.168.1.101
	
	其他：
	通过adb命令查看设备IP地址：adb shell  netcfg
	前提：（设备已经和PC建立了usb连接）