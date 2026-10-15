# 自动连点器 (AutoClicker)

一个基于 Android 无障碍服务的自动连点工具，支持录制点击位置、随机间隔时间、循环执行和页面切换监测。

## 功能特性

- **录制点击动作**：在屏幕上选取多个点击位置，保存为录制方案
- **随机间隔时间**：设置最小和最大间隔时间，每次点击间隔在范围内随机取值
- **循环执行**：设置循环次数，自动重复执行录制方案
- **页面监测**：执行过程中自动检测当前页面是否与录制时一致，若页面切换则自动停止并通知
- **悬浮控制**：录制和执行期间显示悬浮控制面板，可随时停止

## 系统要求

- Android 7.0 (API 24) 及以上
- 需要开启**无障碍服务**和**悬浮窗权限**

## 构建方法

### 方式一：使用 Android Studio（推荐）

1. 打开 Android Studio
2. 选择 `File > Open`，定位到 `AutoClicker` 项目文件夹
3. 等待 Gradle 同步完成（Android Studio 会自动下载 Gradle 和依赖）
4. 点击 `Build > Build Bundle(s) / APK(s) > Build APK(s)`
5. 构建完成后，APK 位于 `app/build/outputs/apk/debug/app-debug.apk`

### 方式二：命令行构建

需要已安装 Android SDK 和 JDK 17：

```bash
cd AutoClicker
gradle wrapper        # 首次运行，生成 gradlew
./gradlew assembleDebug
```

生成的 APK 位于 `app/build/outputs/apk/debug/app-debug.apk`

## 使用方法

### 1. 开启权限

打开应用后，首先开启两个权限：

- **无障碍服务**：点击「开启无障碍服务」，在系统设置中找到「自动连点器」并开启
- **悬浮窗权限**：点击「开启悬浮窗权限」，允许显示在其他应用上层

### 2. 设置参数

- **最小间隔(ms)**：每次点击之间的最小等待时间
- **最大间隔(ms)**：每次点击之间的最大等待时间
- **循环次数**：录制方案重复执行的次数

### 3. 录制点击动作

1. 点击「录制点击动作」按钮，应用自动退到后台
2. 屏幕顶部出现悬浮控制面板（含「添加点击」和「完成」按钮）
3. 点击「添加点击」，全屏半透明遮罩出现
4. 在屏幕上点击想要自动点击的位置，该位置被记录
5. 重复步骤 3-4 添加更多点击点
6. 点击「完成」结束录制

### 4. 开始执行

1. 确保已设置好间隔时间和循环次数
2. 点击「开始执行」按钮，应用自动退到后台
3. 3 秒后开始自动点击，右上角显示「停止执行」悬浮按钮
4. 执行过程中如果检测到页面切换（如弹窗、跳转等），自动停止并发送通知

### 5. 停止执行

- 点击悬浮的「停止执行」按钮
- 或回到应用点击「停止执行」按钮

## 项目结构

```
AutoClicker/
├── settings.gradle              # Gradle 项目配置
├── build.gradle                  # 项目级构建脚本
├── gradle.properties             # Gradle 属性
├── gradle/wrapper/
│   └── gradle-wrapper.properties # Gradle 版本配置
├── app/
│   ├── build.gradle              # 应用级构建脚本
│   ├── proguard-rules.pro        # 代码混淆规则
│   └── src/main/
│       ├── AndroidManifest.xml   # 应用清单
│       ├── java/com/autoclicker/
│       │   ├── MainActivity.java       # 主界面，设置和操作入口
│       │   ├── AutoClickService.java   # 无障碍服务，核心逻辑
│       │   └── ClickRecord.java        # 点击录制数据模型
│       └── res/
│           ├── layout/
│           │   ├── activity_main.xml  # 主界面布局
│           │   ├── floating_control.xml # 录制时悬浮控制面板
│           │   └── floating_stop.xml   # 执行时悬浮停止按钮
│           ├── values/
│           │   ├── strings.xml         # 字符串资源
│           │   ├── colors.xml          # 颜色资源
│           │   └── themes.xml          # 主题
│           ├── xml/
│           │   ├── accessibility_config.xml # 无障碍服务配置
│           │   ├── backup_rules.xml        # 备份规则
│           │   └── data_extraction_rules.xml # 数据提取规则
│           ├── drawable/
│           │   └── ic_launcher_foreground.xml # 应用图标前景
│           └── mipmap-anydpi-v26/
│               ├── ic_launcher.xml      # 自适应图标
│               └── ic_launcher_round.xml # 圆形自适应图标
```

## 技术原理

- **无障碍服务 (AccessibilityService)**：通过 `dispatchGesture()` 在屏幕上执行点击操作，同时监听 `TYPE_WINDOW_STATE_CHANGED` 事件来检测页面切换
- **悬浮窗 (WindowManager)**：使用 `TYPE_APPLICATION_OVERLAY`（API 26+）或 `TYPE_PHONE`（API 24-25）显示控制面板和位置选择遮罩
- **随机间隔**：使用 `Random` 在最小-最大范围内生成随机延迟
- **数据持久化**：通过 `SharedPreferences` 以 JSON 格式存储录制方案

## 注意事项

- 无障碍服务需要手动在系统设置中开启
- 悬浮窗权限需要手动授权
- 页面监测基于应用包名比对，同一应用内的页面跳转可能不会被检测到
- 本应用仅用于辅助操作，请勿用于违反应用使用条款的场景
