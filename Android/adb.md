# adb
* adb help  帮助文档
* adb root  获取root权限
* adb remount  重新挂载，脚本中常用于判断设备是否连接正常
* adb logcat -s > C:\xxx   记录log并存入c:\xxx
* adb shell am start -n 包名/包名＋类名（-n 类名,-a action,-d date,-m MIME-TYPE,-c category,-e 扩展数据,等）。 启动android四大组件
* adb push <source> <destination></destination></source>  移动文件到设备
* adb pull <source> <destination></destination></source>  移动文件到PC
* adb devices  脚本中常用于判断PC是否能识别设备
* adb -s <设备序列号> shell  在设备环境中运行shell
* adb shell sqlite3  查询设备上的数据库
* adb install -r 应用程序.apk  安装
* adb start-server  启动adb服务
* adb kill-server  停止adb服务，2条连用达到重启adb的目的
* adb reboot [bootloader|recovery]  重启
* adb uninstall [-k] <package>  卸载
* 详见：https://github.com/mzlogin/awesome-adb
