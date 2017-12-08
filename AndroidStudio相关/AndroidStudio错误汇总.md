# Gradle 特殊编译错误汇总

---

## 一直卡在 Gradle build running

原因：

没有对应版本的 gradle-all 的包。

解决方案：

> [解决AndroidStudio导入项目在 Building gradle project info 一直卡住](http://blog.csdn.net/yyh352091626/article/details/51490976)

## 编译速度很慢

原因：

没有勾选offline work，每次编译都得联网检查

解决方案：
> 

## transformClassesWithExtractJarsForDevDebug

原因：

AS自身的 Bug 

解决方案：
> [ android aspectJ报错“transformClassesWithExtractJarsForDebug“](http://blog.csdn.net/m0_38079174/article/details/72353741)

## 编译器找不到R文件（可以增量编译）

原因：

资源文件个数太多

解决方案：
> [你绝对想不到R文件找不到(cannot resolve symbol R)的原因](http://www.cnblogs.com/angrycode/p/6707475.html)

## build文件夹中的内容删除不了

原因：

Java程序在持有build文件夹下的内容，导致编译时更新build文件夹内容失败

解决方案：
>[androidstudio 不能删除build文件夹的解决办法](http://blog.csdn.net/mffandxx/article/details/52490728?locationnum=14&fps=1)

>***ps:最好在工程下创建一个一键清理所有lib下面的build文件的bat文件***

## app无法编译（提示：please select android sdk）

原因：

sdk/jdk的位置配置错误（导致这个错误可能是*.iml文件被胡乱修改了的问题，在团队开发时需要特别注意不要提交这些*.iml文件）


解决方案：

>检查以下几个地方的路径选择是否有问题：
>
>1. File -> setting -> Appearance & Behavior -> system settings -> android sdk
>2. File -> project structure -> sdk location(这里注意，取消勾选use embedded jdk)