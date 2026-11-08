# GitHub Actions 自动编译 APK 指南

## 🚀 快速开始（3 步完成）

### 1️⃣ 创建 GitHub 仓库
- 打开 https://github.com/new
- 仓库名：`shiftwork`（或任意名）
- 选择 **Public**（私有仓库也可，但 Actions 分钟数有限制）
- **不要**勾选 "Add a README file"（我们已有）

### 2️⃣ 上传项目代码
**方法 A：网页上传（适合小文件）**
- 进仓库主页 → `Add file` → `Upload files`
- 把 `E:/ZCode/CarLIfe/shiftwork` 文件夹里**所有文件**拖进去
- 点击 `Commit changes`

**方法 B：命令行（推荐，需安装 git）**
```bash
cd E:/ZCode/CarLIfe/shiftwork
git init
git add .
git commit -m "Initial commit: ShiftWork v1.0.0"
git branch -M main
git remote add origin https://github.com/你的用户名/shiftwork.git
git push -u origin main
```

### 3️⃣ 触发编译
- 推送代码后，**自动开始编译**
- 或进仓库 `Actions` 标签 → 选 `Build Android APK` → `Run workflow` → `Run workflow`

---

## 📥 下载 APK

编译成功后（约 5-10 分钟）：
1. 进 `Actions` 标签页
2. 点击最新的绿色 ✅ `Build Android APK`
3. 页面底部 `Artifacts` → 下载 `shiftwork-debug-apk.zip`
4. 解压得到 `app-debug.apk`，传到手机安装

---

## ⚙️ 进阶配置

### 启用 Release 版本（需签名）
1. 生成密钥库：
   ```bash
   keytool -genkey -v -keystore release.keystore -alias shiftwork -keyalg RSA -keysize 2048 -validity 10000
   ```
2. Base64 编码密钥库：
   ```bash
   base64 -i release.keystore -o release.keystore.base64
   cat release.keystore.base64
   ```
3. GitHub 仓库 `Settings` → `Secrets and variables` → `Actions` → 新增：
   - `KEYSTORE_BASE64` = 上面的 base64 字符串
   - `KEYSTORE_PASSWORD` = 密钥库密码
   - `KEY_ALIAS` = shiftwork
   - `KEY_PASSWORD` = 密钥密码

4. 修改 `.github/workflows/build.yml`：
   ```yaml
   - name: Build Release APK
     if: true  # 改为 true
   ```

### 自定义应用信息
修改 `app/build.gradle.kts`：
```kotlin
defaultConfig {
    applicationId = "com.shiftwork.db"
    versionCode = 2  // 每次发布加 1
    versionName = "1.0.1"
}
```

---

## 🔧 常见问题

| 问题 | 解决 |
|------|------|
| 编译失败 `license not accepted` | Actions 已自动接受 SDK license |
| `OutOfMemoryError` | 在 `gradle.properties` 加 `org.gradle.jvmargs=-Xmx2048m` |
| 签名失败 | 检查 Secrets 名称大小写、密码正确性 |
| 下载不到 APK | 确保 workflow 绿色 ✅，重新运行一次 |

---

## 📱 直接安装到手机

1. 手机浏览器打开 GitHub 仓库 `Actions` → 下载 `shiftwork-debug-apk.zip`
2. 文件管理器解压 → 点击 `app-debug.apk` 安装
3. 或用微信/钉钉发送给手机打开安装

> ⚠️ 安装时需允许"未知来源应用"，首次运行需授予通知权限

---

## 🎯 一键触发新版本

以后修改代码后：
```bash
git add .
git commit -m "feat: 新功能"
git push
```
GitHub Actions 会**自动编译**，无需手动操作！

---

## 📂 项目结构确认

上传前确认这些文件存在：
```
shiftwork/
├── .github/workflows/build.yml   ← 关键：自动编译配置
├── app/
├── gradle/
├── gradlew
├── gradlew.bat
├── build.gradle.kts
├── settings.gradle.kts
├── gradle.properties
└── README.md
```