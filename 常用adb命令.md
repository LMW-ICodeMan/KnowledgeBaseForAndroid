# 常用的ADB命令
---

查看当前Activity:
Linux ：adb shell dumpsys activity | grep "mFocusedActivity"
Windows ：adb shell dumpsys activity | findstr "mFocusedActivity"