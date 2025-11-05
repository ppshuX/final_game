# 📅 Kotlin Calendar 日历应用开发日志

> **项目名称**：Android 日历应用（Kotlin Calendar Homework）  
> **开发平台**：Android Studio + Kotlin  
> **项目周期**：2025年11月4日 - 2025年11月14日（10天）  
> **所属计划**：Show Way Plan 第二阶段 - 腾讯青英班大作业  
> **当前状态**：🎉 核心功能已完成！

---

## 📊 项目整体进度

```
████████████████████████████████░░░░░░░░ 80%

Day 1: 基础日历界面 ✅
Day 2: 添加和显示日程 ✅
Day 3: Room 数据库集成 ✅
Day 4: RecyclerView 列表优化 ✅
Day 5: 编辑功能 ✅
Day 6: 时间选择器 ✅
Day 7: 跳过（可选功能）⏭️
Day 8: 提醒功能 ✅
Day 9-10: 待开发 ⏳
```

**项目启动**：2025年11月4日  
**预计完成**：2025年11月14日  
**开发状态**：🎉 核心功能已完成！  
**当前阶段**：Day 8 已完成 - 提醒功能实现完毕！基本要求 100% 达成！

---

## 📅 每日开发日志

| 天数 | 主要任务 | 状态 | 用时 | 备注 | 详细日志 |
|------|---------|------|------|------|---------|
| **Day 1** | 搭建基础日历界面 | ✅ 完成 | 3h | 解决依赖冲突，原生CalendarView | [📖 查看](./day01_calendar_view.md) |
| **Day 2** | 添加和显示日程 | ✅ 完成 | 2h | Material Dialog + 卡片布局 | [📖 查看](./day02_add_events.md) |
| **Day 3** | Room 数据库集成 | ✅ 完成 | 2h | Room + 协程 + 真机测试 | [📖 查看](./day03_room_database.md) |
| **Day 4** | RecyclerView 列表优化 | ✅ 完成 | 1h | Adapter + ViewHolder + 卡片样式 | [📖 查看](./day04_recyclerview.md) |
| **Day 5** | 编辑功能 | ✅ 完成 | 0.5h | 编辑对话框 + 更新数据库 | [📖 查看](./day05_edit_feature.md) |
| **Day 6** | 时间选择器 | ✅ 完成 | 0.5h | TimePickerDialog + 时间格式化 | [📖 查看](./day06_time_picker.md) |
| **Day 7** | 多视图切换 | ⏭️ 跳过 | - | 可选功能，优先完成核心需求 | [📖 查看](./day07_skipped.md) |
| **Day 8** | 提醒功能 | ✅ 完成 | 1h | AlarmManager + Notification + 权限 | [📖 查看](./day08_reminder.md) |
| **Day 9** | 扩展功能 + 优化 | ⏳ 计划中 | - | 性能优化、功能完善 | - |
| **Day 10** | 文档和提交 | ⏳ 计划中 | - | 整理文档、项目收尾 | - |

**状态图例**：⏳ 未开始 | 🚀 进行中 | ✅ 完成 | ⏭️ 跳过

---

## 🎯 作业完成度

### 基本要求（3个）✅

1. ✅ **日历视图展示**（月视图）
   - CalendarView 显示
   - 日期选择交互
   - 中文日期格式

2. ✅ **日程增删改查**
   - 添加日程（标题、描述、时间）
   - 编辑日程（预填充、更新）
   - 删除日程（长按确认）
   - 查看详情（点击卡片）
   - RecyclerView 列表展示
   - Room 数据库持久化

3. ✅ **日程提醒功能**
   - AlarmManager 定时触发
   - Notification 通知显示
   - 提醒时间可选（5/15/30/60/1440 分钟）
   - 权限管理完善

**🎉 基本要求 100% 完成！**

---

## 📈 项目统计

### 累计统计（截至 Day 8）

- **完成天数**：8 天（Day 7 跳过）
- **累计用时**：10 小时
- **总文件数**：13 个
- **累计代码行数**：约 530 行
- **功能完成**：8/10（80%）
- **作业要求**：✅ 100% 完成（3个基本要求全部实现）
- **遇到的坑**：9 个（全部解决）
- **数据库规模**：1 张表（events），5 个字段（v2）

### 文件清单

**业务代码**：
- MainActivity.kt（主程序）
- Event.kt（实体类 v2，新增提醒字段）
- EventDao.kt（数据访问接口）
- AppDatabase.kt（数据库单例 v2，含迁移）
- EventAdapter.kt（RecyclerView 适配器）
- AlarmReceiver.kt（广播接收器）
- ReminderManager.kt（提醒管理器）

**布局文件**：
- activity_main.xml（主布局）
- dialog_add_event.xml（对话框，含时间和提醒选择）
- item_event.xml（卡片布局）

**配置文件**：
- colors.xml（颜色资源）
- build.gradle.kts（依赖配置）
- AndroidManifest.xml（权限 + 接收器配置）

---

## 🔧 技术栈

### 开发环境
- **IDE**：Android Studio
- **语言**：Kotlin
- **Android SDK**：API Level 29-34
- **构建工具**：Gradle (Kotlin DSL)

### 核心库
- **AndroidX**：现代 Android 支持库
- **Material Components**：Material Design UI 组件
- **Room**：数据库框架（版本 2.6.0）
- **Coroutines**：协程异步处理
- **Lifecycle**：生命周期感知组件

### 系统组件
- **CalendarView**：原生日历组件
- **RecyclerView**：列表展示
- **AlarmManager**：定时任务
- **NotificationManager**：通知管理
- **BroadcastReceiver**：广播接收

---

## 🎓 学习资源

- [Kotlin 学习笔记](./learning_notes.md) - Kotlin 语法、Android 基础
- [项目概览](./overview.md) - 技术架构、开发计划
- [常见问题](./faq.md) - 遇到的坑和解决方案

---

## 💡 技术亮点

- ✨ **Material Design 风格** - 专业的 UI 设计
- ✨ **协程异步处理** - 流畅的用户体验
- ✨ **ViewHolder 复用** - 高性能列表滚动
- ✨ **Room 数据库迁移** - 无损版本升级
- ✨ **AlarmManager 定时任务** - 精确的提醒功能
- ✨ **Notification 系统通知** - 原生通知体验
- ✨ **权限动态请求** - 规范的权限管理

---

## 📝 开发心得

从 Web 全栈到移动端开发，技术栈进一步拓展：
- ✅ JavaScript → Kotlin
- ✅ Vue3 → Android UI
- ✅ Spring Boot → Android 架构
- ✅ MySQL → Room (SQLite)

10 天完成核心功能开发，收获满满！🎉

---

> "Keep building, keep recording, keep believing." 💪  
> "From web to mobile — 技术的边界在不断扩展！" 🚀

