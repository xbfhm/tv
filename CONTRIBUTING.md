# 贡献指南

感谢你愿意帮助 AnimeTV。这个项目最初是为一台 2016 年的海信电视做的，
所以**"在老设备上是否流畅"永远是第一优先级**。

---

## 报告问题

提 Issue 时请尽量包含：

1. **设备信息**：机型 / Android 版本 / 运行内存（电视请写品牌型号，如 VIDAA V1）；
2. **版本号**：设置 → 关于 → 版本；
3. **复现步骤**：从哪个入口进入、点了什么；
4. **现象**：截图或录屏最好；
5. **日志**（可选）：
   ```bash
   adb logcat -s AnimeTV:V
   ```
   崩溃堆栈可在 `/sdcard/Android/data/com.animetv/files/crash.txt` 找到。

请不要在 Issue 里粘贴完整的第三方片源地址（容易被删帖），给出番名 + 集数即可。

---

## 提交代码

```bash
# 1. Fork 并克隆
git clone git@github.com:<你的用户名>/tv.git

# 2. 解压源码包（仓库只放 zip，源码在里面）
unzip ad7.0-java.zip -d ad70_java

# 3. 修改后重新打包（保持目录结构）
cd ad70_java && zip -qr ../ad7.0-java.zip .

# 4. 提交
git add . && git commit -m "fix: 描述" && git push
```

> 也可以在 PR 描述里直接说明改动，由维护者代为打包。

### 代码风格

- Java 8 语法（项目已启用 desugaring），**不要**引入 Kotlin / Java 11+ API；
- 目标设备是 API 21，任何 API 23+ 的调用都要加 `Build.VERSION.SDK_INT` 判断；
- 新增依赖前请先确认能显著改善体验，APK 每增加 1MB 都在增加老电视的安装成本；
- 动画只用 `ViewPropertyAnimator` / 属性动画，不要用 RenderScript、实时模糊、大位图；
- 网络请求必须在后台线程，UI 更新必须回到主线程。

### 提交信息

```
feat: 新增 XXX
fix: 修复 XXX
perf: 优化 XXX
docs: 更新 XXX
refactor: 重构 XXX
```

---

## 新增解析规则

优先向上游 [Predidit/KazumiRules](https://github.com/Predidit/KazumiRules) 提交，
合并后再同步到本项目 `app/src/main/assets/rules.json`。

规则接入要求：

- 只收录**无需登录**即可播放的站点；
- 有反爬 / Cloudflare 盾 / 强制验证码的站点直接跳过；
- 提交前请在真机（或电视）上验证搜索 + 详情 + 播放三段链路。

---

## 关于片源

本项目只做**聚合与播放**，不接受"内置片源 CDN""自建转码服务"这类会引入
版权与带宽风险的改动。新增任何直链托管功能都会被拒绝。

---

## 许可

提交代码即表示你同意以 **GPL-3.0** 授权你的贡献。
