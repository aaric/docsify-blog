# 开发环境

## 1 Flutter China

```powershell
$env:PUB_HOSTED_URL = https://pub.flutter-io.cn
$env:FLUTTER_STORAGE_BASE_URL = https://storage.flutter-io.cn
```

## 2 Flutter Doctor

### 2.1 [✗] Android toolchain - develop for Android devices

```powershell
# Unable to locate Android SDK.
$env:ANDROID_HOME = E:\cache_app\sdk

# Android license status unknown.
flutter doctor --android-licenses
```

### 2.2 [✗] Visual Studio - develop for Windows

> [Select the installation locations in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/install/change-installation-locations?view=vs-2022)

```powershell
# https://docs.microsoft.com/en-us/visualstudio/install/create-an-offline-installation-of-visual-studio?view=vs-2022
.\vs_community.exe --layout E:\vs2022 --add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended --lang zh-CN
.\vs_community.exe --noweb --add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended --lang zh-CN
```

### 2.3 [✗] You need Java 11 or higher to build your app with this version of Gradle

**`<project_dir>\\android\\gradle.properties`**

```properties
org.gradle.java.home=D\:\\DevTools\\jdk-17.0.4
```

## 3 Flutter Env

### 3.1 Windows Env

```powershell
$env:ANDROID_HOME = D:\DevTools\sdk
$env:PUB_HOSTED_URL = https://pub.flutter-io.cn
$env:FLUTTER_STORAGE_BASE_URL = https://storage.flutter-io.cn
$env:FLUTTER_HOME = D:\DevTools\flutter
$env:DART_HOME = %FLUTTER_HOME%\bin\cache\dart-sdk
$env:JAVA_HOME = D:\DevTools\jdk-17.0.2
$env:GRADLE_HOME = D:\DevTools\gradle-7.4

$env:Path += ;"%ANDROID_HOME%\platform-tools"
$env:Path += ;"%FLUTTER_HOME%\bin"
$env:Path += ;"%DART_HOME%\bin"
$env:Path += ;"%JAVA_HOME%\bin"
$env:Path += ;"%GRADLE_HOME%\bin"

ls $env:
```

### 3.2 Flutter Support

```powershell
# windows
flutter config --enable-windows-desktop
start ms-settings:developers
flutter build windows --release

# macos
flutter config --enable-macos-desktop

# linux
flutter config --enable-linux-desktop
```

## 4 Gradle Settings

> *Windows Relative Path: `flutter\packages\flutter_tools\gradle\flutter.gradle`*

```groovy
buildscript {
    repositories {
        //google()
        //mavenCentral()
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/central' }
        //maven { url 'https://maven.aliyun.com/repository/public' }
    }
    dependencies {
        /* When bumping, also update ndkVersion above. */
        classpath 'com.android.tools.build:gradle:4.1.0'
    }
}
```
