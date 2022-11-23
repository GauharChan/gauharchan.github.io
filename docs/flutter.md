<Banner />
[flutter中文网](https://flutter.cn/docs)

## 起步

下载 flutter，国内建议下载压缩包。

### 添加环境变量

> 具体的路径由你自己把控，sdk，avd 虚拟机通过 android studio 下载
>
> 1. 打开 **Android Studio SDK Manager**
> 2. 找到 Android SDK 这个选项卡，取消勾选 **Hide Obsolete Packages**
> 3. 下载 **Android SDK Tools (Obsolete)**

### tips

> 安装好 android sdk 和 AVD 安卓手机模拟器后，打开命令行，执行`flutter doctor`
>
> 如果提示`flutter doctor --android-licenses`，就执行该命令，全部输入`y`

说明：

1. `ANDROID_HOME`：安卓 SDK 文件夹路径 默认 c 盘位置：`C:\Users\gauhar\AppData\Local\Android\Sdk`
2. `ANDROID_SDK_HOME`：安卓手机模拟器路径 默认 c 盘位置：`C:\Users\gauhar\.android\avd`
3. 其他两个是镜像地址，如果你可以科学上网可以不用设置

| 变量名                   | 变量值                        |
| ------------------------ | ----------------------------- |
| ANDROID_HOME             | D:\AndroidSDK                 |
| ANDROID_SDK_HOME         | D:\android_avd                |
| FLUTTER_STORAGE_BASE_URL | https://storage.flutter-io.cn |
| PUB_HOSTED_URL           | https://pub.flutter-io.cn     |

## 创建项目

1. vscode 中安装`flutter`插件，同时他会自动安装`dart`插件
2. `ctrl + shift + p`打开命令面板，输入`flutter`，选择`Flutter: New Project`
3. 输入项目名，创建之后慢慢等待
4. 按`F5`运行项目，如果没有模拟器，去`android studio`新建一个
5. 如果是真机调试，注意看手机的安装提示，**过了时间就默认不安装了**
6. 修改`main.dart`文件
