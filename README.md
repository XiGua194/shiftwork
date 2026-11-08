# 倒班助手 - ShiftWork

一个功能完整的 Android 倒班助手应用，包名：`com.shiftwork.com`

## 功能特性

### 📅 班次管理
- **固定班次**：设置固定的上下班时间，支持每天/每周/每月/双周重复
- **轮班排班**：支持自定义轮班周期（如四班三运转），可配置轮班模式
- **自定义排班**：复杂的排班规则，支持按周、按月、自定义周期
- **班次模板**：内置常用模板（标准三班倒、两班倒、四班三运转、医护三班倒、白班制）

### 📥 班次导入
- **CSV 导入**：支持标准 CSV 格式，自动检测编码 (UTF-8/GBK/ISO-8859-1)
- **Excel 导入**：支持 .xls 和 .xlsx 格式
- **智能列映射**：自动识别中英文表头，支持自定义列映射
- **模板下载**：提供 CSV 和 Excel 标准导入模板
- **预览确认**：导入前预览数据，错误行高亮提示
- **导入记录**：保存导入历史，支持查看成功/失败详情

### 🌙 昼夜模式
- **跟随系统**：自动跟随系统深色/浅色模式切换
- **强制浅色**：始终使用浅色主题
- **强制深色**：始终使用深色主题
- **一键切换**：工具栏快速切换，循环三种模式

### 🔔 智能提醒
- **班次提醒**：班次开始前提前提醒 (可配置 0-120 分钟)
- **铃声/震动**：可独立开启提醒铃声和震动
- **开机自启**：重启后自动恢复提醒服务
- **精确调度**：使用 WorkManager 定期检查，Android 12+ 支持精确闹钟

### 📊 日历视图
- **月视图**：直观显示每日班次，颜色标记
- **日期详情**：点击日期查看当日所有班次详情
- **快速导航**：上月/下月/今天快速切换
- **周起始日**：可自定义周一/周日开始

### ⚙️ 个性化设置
- **显示选项**：班次颜色、时长、紧凑模式开关
- **数据管理**：导出/导入备份 (JSON)，一键清空数据
- **统计信息**：班次总数、模板数、导入记录数

## 技术架构

### 核心技术栈
- **语言**：Kotlin 1.9.22
- **UI 框架**：Jetpack Compose (ViewBinding + Material3)
- **架构**：MVVM + Repository + Hilt 依赖注入
- **数据库**：Room 2.6.1 (支持 Flow 响应式)
- **异步**：Kotlin Coroutines + Flow
- **导航**：Navigation Component
- **后台任务**：WorkManager
- **日期时间**：kotlinx-datetime
- **序列化**：Kotlinx Serialization
- **文件解析**：OpenCSV + Apache POI

### 项目结构
```
com.shiftwork.com
├── data
│   ├── entity          # 数据实体
│   ├── dao             # 数据访问接口
│   ├── database        # Room 数据库配置
│   ├── repository      # 数据仓库
│   └── di              # Hilt 模块
├── ui
│   ├── main            # 主页
│   ├── calendar        # 日历视图
│   ├── shift           # 班次列表/编辑
│   ├── importx         # 导入向导
│   ├── settings        # 设置页
│   └── di              # UI 依赖注入
├── utils               # 工具类
│   ├── ImportParser    # 导入解析器
│   ├── CustomScheduleManager  # 自定义排班
│   └── ThemeManager    # 主题管理
├── worker              # 后台任务
│   └── ShiftReminderWorker    # 提醒 Worker
├── notification        # 通知管理
└── ShiftWorkApplication # 应用入口
```

## 构建要求

- Android Studio Hedgehog (2023.1.1) 或更高版本
- JDK 17+
- Android SDK 34
- Minimum SDK 24 (Android 7.0)

## 依赖版本

| 依赖 | 版本 |
|------|------|
| Android Gradle Plugin | 8.4.0 |
| Kotlin | 1.9.22 |
| KSP | 1.9.20-1.0.14 |
| Room | 2.6.1 |
| Hilt | 2.48.1 |
| Material3 | 1.11.0 |
| Navigation | 2.7.6 |
| WorkManager | 2.9.0 |
| kotlinx-datetime | 0.5.0 |
| kotlinx-serialization | 1.6.0 |
| OpenCSV | 5.8 |
| Apache POI | 5.2.5 |

## 构建命令

```bash
# 克隆项目
cd shiftwork

# 构建 Debug APK
./gradlew assembleDebug

# 构建 Release APK
./gradlew assembleRelease

# 运行单元测试
./gradlew test

# 运行插桩测试
./gradlew connectedAndroidTest
```

## 权限说明

| 权限 | 用途 |
|------|------|
| INTERNET | 网络访问 (预留同步功能) |
| READ_EXTERNAL_STORAGE | 读取导入文件 |
| WRITE_EXTERNAL_STORAGE | 导出模板/备份文件 (Android 10 以下) |
| POST_NOTIFICATIONS | 发送班次提醒通知 (Android 13+) |
| RECEIVE_BOOT_COMPLETED | 开机自启恢复提醒服务 |
| SCHEDULE_EXACT_ALARM | 精确闹钟提醒 (Android 12+) |
| USE_EXACT_ALARM | 使用精确闹钟 (Android 12+) |
| VIBRATE | 提醒震动 |
| WAKE_LOCK | 保持 CPU 唤醒发送通知 |
| FOREGROUND_SERVICE | 前台服务 (预留) |

## 导入文件格式

### CSV 模板示例
```csv
name,displayName,startTime,endTime,restMinutes,color,type,repeatRule,rotationCycleDays,rotationPattern,rotationStartDate,note
早班,早班 (08:00-16:00),08:00,16:00,60,#4CAF50,FIXED,DAILY,,,
中班,中班 (16:00-00:00),16:00,00:00,30,#FF9800,FIXED,DAILY,,,
夜班,夜班 (00:00-08:00),00:00,08:00,60,#9C27B0,FIXED,DAILY,,,
轮班早班,轮班早班 (06:00-14:00),06:00,14:00,30,#4CAF50,ROTATING,DAILY,28,早早早早休休中中中中休休晚晚晚晚休休,2024-01-01,
```

### 支持的列名 (中英文)
| 字段 | 中文别名 | 英文别名 |
|------|----------|----------|
| 班次名称 | name, 班次名称, 班次, shift_name | |
| 显示名称 | displayName, 显示名称, display_name | |
| 开始时间 | startTime, 开始时间, 上班时间, start_time | |
| 结束时间 | endTime, 结束时间, 下班时间, end_time | |
| 休息时长 | restMinutes, 休息时长, 休息分钟, rest_minutes | |
| 颜色 | color, 颜色, 颜色值, colour | |
| 类型 | type, 类型, 班次类型, shift_type | |
| 重复规则 | repeatRule, 重复规则, 重复, repeat_rule | |
| 轮班周期 | rotationCycleDays, 轮班周期, 周期天数 | |
| 轮班模式 | rotationPattern, 轮班模式, 轮班规则 | |
| 起始日期 | rotationStartDate, 轮班起始日期, 起始日期 | |
| 备注 | note, 备注, 说明, remark | |

## 主题定制

应用使用 Material Design 3 动态配色，支持：
- `values/colors.xml` - 浅色主题色值
- `values-night/colors.xml` - 深色主题色值
- `values/themes.xml` - 完整主题配置
- `values/shapes.xml` - 组件圆角形状
- `values/text_appearances.xml` - 字体样式

## 许可证

MIT License

## 更新日志

### v1.0.0 (2024)
- ✅ 初始版本发布
- ✅ 固定/轮班/自定义班次支持
- ✅ CSV/Excel 导入导出
- ✅ 内置班次模板
- ✅ 昼夜模式 (跟随系统/强制浅色/强制深色)
- ✅ 班次提醒通知
- ✅ 日历视图
- ✅ 数据备份恢复