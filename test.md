####  常用ADB命令 

1. 连接的安卓设备

```
adb devices    # 连接的设备列表
adb devices -l # 连接的设备列表包括设备信息
adb devices -s sn号 # 多个设备使用此方式进行选择
adb forward --list             # list all forward socket connections
adb forward tcp:6123 tcp:7123  #set up port forwarding  sets up forwarding of computer port 6123 to Android device port 7123
adb kill-server    # 终止adb服务器进程
adb usb # 以USB模式重启

```

2. 重启到9008刷机

```
adb reboot edl 
```

3. 重启到fastboot模式刷机 

```
adb reboot fastboot
```

4. 查看安卓设备串口号 和 usb设备,gpio端口，电池状态

```
adb shell 
su 
ls -a /dev/tty*
lsusb
ls /sys/class/gpio/*
adb shell dumpsys battery
cat sys/class/power_supply/bms/battery_type   //电池曲线是否加载
getprop |grep cutoff  //查看电池终止电压
cat proc/kmsg > /data/kmsg.txt   保存日志
adb pull sys/firmware/fdt 
```

5. 模拟按键操作 , * 对应键值 :1-menu;3-home;4-back;82-unlock;26-power

```
adb shell input keyevent *
```

6. 截图 ,屏幕操作

```
adb shell screencap -p >screen.png
adb shell screencap -p /sdcard/screen.png
adb shell screenrecord  --time-limit 10 --size 1920*1200 /sdcard/demo.mp4
--time-limit：录屏时长，默认180s
--size：视频分辨率
adb shell settings put system screen_brightness value   
--value取值范围0-255，设置超过255的值表示亮度最大
adb shell settings get system screen_off_timeout  查询息屏时间
adb shell settings put system screen_off_timeout value   设置息屏时间
--value取值范围1-2147483647
```

7. 卸载内置系统安装包

``` 
pm uninstall -k --user 0 com.android.launcher3 

#获取当前打开的app的信息，这里面包含package名称
adb shell dumpsys window|findstr mCurrent

adb shell pm uninstall -k --user 0 你需要卸载的app的package名称
#可实现不需要root进行卸载

adb shell cmd package install-existing 你需要恢复的app的package名称
#例如adb shell cmd package install-existing com.oppo.launcher


adb install test.apk                                 # 安装apk
adb install-multiple test.apk test2.apk              # 安装多个apk
adb install-multi-package test.apk demo.apk          # 应用安装-将一个或多个软件包推送到设备并自动安装
adb install -r test.apk                              # 替换现有的应用程序 重新安装现有应用程序，保留其数据
adb install -t test.apk                              # 允许测试包
adb install -d test.apk                              # 允许版本代码降级 仅可调试软件包
adb install -g test.apk                              # 授予所有运行时权限 授予应用清单中列出的所有权限
adb install --instant test.apk                       # 将应用程序安装为临时安装应用程序
adb install --fastdeploy test.apk                    # 使用快速部署
adb install --no-streaming test.apk                  # 始终将APK推送到设备并调用软件包管理器作为单独的步骤
adb uninstall test.apk            # 卸载apk
adb uninstall -k test.apk         # 删除软件包后保留数据和缓存目录

```



8. 安装应用

```
adb install xxx.apk 
adb shell pm list packages                        # 打印所有安装apk的包名
adb shell pm list packages -f                     # 查看他们相关的APK文件
adb shell pm list packages -a                     # 所有已知的软件包 但不包括APEXes（Android Pony EXpress）
adb shell pm list packages --apex-only            # --apex-only
adb shell pm list packages -d                     # 过滤只显示被禁用的包
adb shell pm list packages -e                     # 过滤只显示启用的包
adb shell pm list packages -s                     # 过滤只显示系统包
adb shell pm list packages -3                     # 过滤以仅显示第三方软件包
adb shell pm list packages -i                     # see the installer for the packages
adb shell pm list packages -l                     # ignored (used for compatibility with older releases)
adb shell pm list packages -U                     # also show the package UID
adb shell pm list packages -u                     # also include uninstalled packages
adb shell pm list packages --show-versioncode     # also show the version code
adb shell pm list packages --uid                  # UID filter to only show packages with the given UID
adb shell pm list packages --user                 # USER_ID  only list packages belonging to the given user

```

9. 文件管理

   ```
   adb shell pm clear 包名       # clearing app data, cache
   adb shell pm path 包名
   adb  pull /mnt/sdcard/Download/test.apk pc.apk            # 从设备复制文件/目录
   adb  pull -a /mnt/sdcard/Download/test.apk pc.apk         # 保留文件时间戳和模式
   adb root
   adb disable-verity
   adb reboot
   adb root
   adb remount
   adb push pc.apk /storage/self/primary/Download/test.apk            # 将本地文件/目录复制到Android设备
   adb push --sync pc.apk /mnt/sdcard/Download/test.apk               # 仅推送主机上比Android设备更新的文件
   
   android 在版本7之后，会对相应的分区进行验证，比如system分区。 所以不能像之前版本一样，直接adb root; adb remount 对system分区进行重新挂载。需要使用disable-verity来关闭分区检测功能，disable-verity需要root权限，所以user权限的机器需要先获取root权限才能使用；流程如下
   adb root  //获取root权限
   adb disable-verity //关闭检测功能
   adb reboot //关闭检测之后需要重启机器
   重启机器成功之后：
   adb root  //再次获取root权限
   adb remount  //重启挂载system分区为可读写分区
   ```

   

10. shell命令

    ```
    adb shell ls
    adb shell cd
    adb shell rm
    adb shell mkdir
    adb shell touch
    adb shell pwd
    adb shell cp
    adb shell mv
    
    ```

    

11. fastboot 刷新部分固件

```
adb reboot bootloaderfastboot flash aboot   xxxxfastboot flash dtbo   xxxxfastboot flash boot   xxxxfastboot flash aboot emmc_mbnfastboot rebootfastboot flash splash splash.img  开机log
```

9. 查看手机内存使用情况

```
adb shell dumpsys meminfo
```

10. 打开手机对应页面

``` 
adb shell am start com.android.settings/com.android.settings.Settingsadb shell am start com.android.settings/com.android.settings.SecuritySettings手机无线信息adb shell am start com.android.settings/com.android.settings.RadioInfo更多页面com.android.settings.AccessibilitySettings 辅助功能设置com.android.settings.ActivityPicker 选择活动com.android.settings.ApnSettings APN设置com.android.settings.ApplicationSettings 应用程序设置com.android.settings.BandMode 设置GSM/UMTS波段com.android.settings.BatteryInfo 电池信息com.android.settings.DateTimeSettings 日期和坝上旅游网时间设置com.android.settings.DateTimeSettingsSetupWizard 日期和时间设置com.android.settings.DevelopmentSettings 开发者设置com.android.settings.DeviceAdminSettings 设备管理器com.android.settings.DeviceInfoSettings 关于手机com.android.settings.Display 显示——设置显示字体大小及预览com.android.settings.DisplaySettings 显示设置com.android.settings.DockSettings 底座设置com.android.settings.IccLockSettings SIM卡锁定设置com.android.settings.InstalledAppDetails 语言和键盘设置com.android.settings.LanguageSettings 语言和键盘设置com.android.settings.LocalePicker 选择手机语言com.android.settings.LocalePickerInSetupWizard 选择手机语言com.android.settings.ManageApplications 已下载（安装）软件列表com.android.settings.MasterClear 恢复出厂设置com.android.settings.MediaFormat 格式化手机闪存com.android.settings.PhysicalKeyboardSettings 设置键盘com.android.settings.PrivacySettings 隐私设置com.android.settings.ProxySelector 代理设置com.android.settings.RadioInfo 手机信息com.android.settings.RunningServices 正在运行的程序（服务）com.android.settings.SecuritySettings 位置和安全设置com.android.settings.Settings 系统设置com.android.settings.SettingsSafetyLegalActivity 安全信息com.android.settings.SoundSettings 声音设置com.android.settings.TestingSettings 测试——显示手机信息、电池信息、使用情况统计、Wifi information、服务信息com.android.settings.TetherSettings 绑定与便携式热点com.android.settings.TextToSpeechSettings 文字转语音设置com.android.settings.UsageStats 使用情况统计com.android.settings.UserDictionarySettings 用户词典com.android.settings.VoiceInputOutputSettings 语音输入与输出设置com.android.settings.WirelessSettings 无线和网络设置adb shell dumpsys window|grep mCurrentFocus  查看当前包名
```

11. 控制gpio，建议su后操作

```
/sys/class/gpio# echo 44 > export   导出/sys/class/gpio/gpio44# echo out > direction  设置方向/sys/class/gpio/gpio44# cat direction  查看方向 /sys/class/gpio/gpio44# echo 1 > value  设置值/sys/class/gpio/gpio44# cat value  查看值
```

12. 修改屏幕分辨率  ,dpi

    ```
    adb shell wm size 1136x2480adb shell wm sizeadb shell wm size resetadb shell wm density  查看当前adb shell wm density 160adb shell screencap /mnt/sdcard/Download/test.pngadb shell screenrecordadb shell screenrecord --size 1280x720adb shell screenrecord --bit-rate 4000000adb shell screenrecord --rotateadb shell screenrecord --bugreportadb shell screenrecord --time-limit=120adb shell screenrecord --verbose
    ```

    13. 关闭log 

    ```
    echo 0 > /proc/sys/kernel/printk
    ```

    14. 飞行模式 

        ```
        adb shell settings put global airplane_mode_on 1  开启飞行模式adb shell settings put global airplane_mode_on 0  关闭飞行模式
        ```


15. 

    ```
    https://blog.csdn.net/RedKeyer/article/details/116493482 取消锁屏
    ```

    

16. 系统相关

    ```
    adb rootadb unrootadb sideload /mnt/sdcard/Download/ota.zip            # 侧面加载给定的完整OTA软件包adb shell psadb shell top                                        # 实时显示流程活动adb shell top -H                                     # 显示主题Show threadsadb shell top -o %CPU,%MEM,TIME+                     # Show FIELDS (def PID,USER,PR,NI,VIRT,RES,SHR,S,%CPU,%MEM,TIME+,CMDLINE)adb shell top -m 50                                  # Maximum number of tasks to showadb shell getpropadb shell getprop -T                                 # 显示属性类型而不是值adb shell getprop gsm.sim.operator.alpha             # 获取SIM卡运营商adb shell getprop ro.ril.oem.imei                    # 获取设备IEMIadb shell setprop <key> <value># 修改网络叹号问题adb shell "settings put global captive_portal_http_url http://connect.rom.miui.com/generate_204"adb shell "settings put global captive_portal_https_url https://connect.rom.miui.com/generate_204" 
    ```

    17. 打包解包

        ```
        ./apksigner sign --ks labsim.jks --ks-key-alias labsim 430_286_.apk  签名apktool -s --no-src d 430.apk  解包apktool b 430 -o 430_new.apk  打包
        ```

        

常见开发问题整理

https://blog.csdn.net/weixin_44021334?type=blog

安卓获取wifi或蓝牙不用请求定位的方式

https://blog.csdn.net/Theo_Yan/article/details/105600237
