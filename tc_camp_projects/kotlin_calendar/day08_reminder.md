# Day 08 - 提醒功能

**日期**：2025年11月05日  
**用时**：约1小时  
**完成度**：✅ 100%

---

## 📋 今日任务完成情况

- [x] 添加通知和闹钟权限
- [x] 创建 AlarmReceiver 广播接收器
- [x] 创建 ReminderManager 提醒管理器
- [x] 修改 Event 实体添加 reminderMinutes 字段
- [x] 升级数据库版本（v1 → v2）
- [x] 添加提醒下拉选项到对话框
- [x] 实现设置和取消提醒功能
- [x] 添加权限请求和调试日志
- [x] 测试通过：提醒功能正常运行

---

## 💻 写了哪些代码

### 1. AndroidManifest.xml（权限配置）

```xml
<!-- 提醒功能需要的权限 -->
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM" />
<uses-permission android:name="android.permission.USE_EXACT_ALARM" />

<!-- 提醒接收器 -->
<receiver
    android:name=".AlarmReceiver"
    android:enabled="true"
    android:exported="false" />
```

**权限说明**：
- `POST_NOTIFICATIONS` - 发送通知（Android 13+）
- `SCHEDULE_EXACT_ALARM` - 精确定时任务
- `USE_EXACT_ALARM` - 使用精确闹钟
- `exported="false"` - 仅应用内部使用

---

### 2. AlarmReceiver（广播接收器）

```kotlin
package com.ncu.kotlincalendar

import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import androidx.core.app.NotificationCompat

class AlarmReceiver : BroadcastReceiver() {
    companion object {
        const val CHANNEL_ID = "event_reminder_channel"
    }
    
    override fun onReceive(context: Context, intent: Intent) {
        val eventId = intent.getLongExtra("eventId", -1)
        val eventTitle = intent.getStringExtra("eventTitle") ?: "日程提醒"
        val eventDesc = intent.getStringExtra("eventDesc") ?: ""
        
        showNotification(context, eventId, eventTitle, eventDesc)
    }
    
    private fun showNotification(context: Context, id: Long, title: String, desc: String) {
        val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) 
            as NotificationManager
        
        // 1. 创建通知渠道（Android 8.0+）
        val channel = NotificationChannel(
            CHANNEL_ID,
            "日程提醒",
            NotificationManager.IMPORTANCE_HIGH
        ).apply {
            description = "用于日程提醒的通知渠道"
            enableVibration(true)
        }
        notificationManager.createNotificationChannel(channel)
        
        // 2. 创建通知
        val notification = NotificationCompat.Builder(context, CHANNEL_ID)
            .setSmallIcon(android.R.drawable.ic_dialog_info)
            .setContentTitle("📅 $title")
            .setContentText(desc.ifEmpty { "日程即将开始" })
            .setPriority(NotificationCompat.PRIORITY_HIGH)
            .setAutoCancel(true)
            .build()
        
        // 3. 显示通知
        notificationManager.notify(id.toInt(), notification)
    }
}
```

**代码要点**：
- `BroadcastReceiver` - 广播接收器，接收定时触发
- `NotificationChannel` - Android 8.0+ 必须创建
- `IMPORTANCE_HIGH` - 高优先级，会弹出
- `setAutoCancel(true)` - 点击后自动消失

---

### 3. ReminderManager（提醒管理）

```kotlin
package com.ncu.kotlincalendar

import android.app.AlarmManager
import android.app.PendingIntent
import android.content.Context
import android.content.Intent

class ReminderManager(private val context: Context) {
    private val alarmManager = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager
    
    // 设置提醒
    fun setReminder(event: Event) {
        if (event.reminderMinutes <= 0) return
        
        // 计算提醒时间 = 日程时间 - 提前分钟数
        val reminderTime = event.dateTime - (event.reminderMinutes * 60 * 1000)
        
        // 如果已过期，不设置
        if (reminderTime < System.currentTimeMillis()) return
        
        // 创建 Intent
        val intent = Intent(context, AlarmReceiver::class.java).apply {
            putExtra("eventId", event.id)
            putExtra("eventTitle", event.title)
            putExtra("eventDesc", event.description)
        }
        
        val pendingIntent = PendingIntent.getBroadcast(
            context,
            event.id.toInt(),
            intent,
            PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
        )
        
        // 设置精确闹钟
        alarmManager.setExactAndAllowWhileIdle(
            AlarmManager.RTC_WAKEUP,
            reminderTime,
            pendingIntent
        )
    }
    
    // 取消提醒
    fun cancelReminder(eventId: Long) {
        val intent = Intent(context, AlarmReceiver::class.java)
        val pendingIntent = PendingIntent.getBroadcast(
            context,
            eventId.toInt(),
            intent,
            PendingIntent.FLAG_NO_CREATE or PendingIntent.FLAG_IMMUTABLE
        )
        
        pendingIntent?.let {
            alarmManager.cancel(it)
            it.cancel()
        }
    }
}
```

**核心 API**：
- `setExactAndAllowWhileIdle()` - 精确定时，省电模式也触发
- `RTC_WAKEUP` - 使用系统时间，到时唤醒设备
- `FLAG_IMMUTABLE` - 不可变（Android 12+ 必须）

---

### 4. Event 实体类升级（v1 → v2）

```kotlin
@Entity(tableName = "events")
data class Event(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    
    val title: String,
    val description: String = "",
    val dateTime: Long,
    val createdAt: Long = System.currentTimeMillis(),
    
    // 新增：提醒时间（提前多少分钟）
    val reminderMinutes: Int = 0  // 0 表示不提醒
)
```

---

### 5. 数据库迁移

```kotlin
@Database(entities = [Event::class], version = 2)
abstract class AppDatabase : RoomDatabase() {
    companion object {
        val MIGRATION_1_2 = object : Migration(1, 2) {
            override fun migrate(database: SupportSQLiteDatabase) {
                database.execSQL(
                    "ALTER TABLE events ADD COLUMN reminderMinutes INTEGER NOT NULL DEFAULT 0"
                )
            }
        }
        
        fun getDatabase(context: Context): AppDatabase {
            return Room.databaseBuilder(...)
                .addMigrations(MIGRATION_1_2)
                .build()
        }
    }
}
```

**迁移说明**：
- 版本从 1 升级到 2
- 不会丢失原有数据
- 新增 `reminderMinutes` 列

---

### 6. 对话框添加提醒选项

```kotlin
// 提醒选项
val reminderOptions = arrayOf(
    "不提醒", 
    "提前5分钟", 
    "提前15分钟",   // ← 老师要求的标准
    "提前30分钟", 
    "提前1小时", 
    "提前1天"
)

spinnerReminder.adapter = ArrayAdapter(
    this, 
    android.R.layout.simple_spinner_dropdown_item, 
    reminderOptions
)
```

---

## 🔔 提醒功能工作流程

```
1. 用户添加日程
   - 日程时间：21:15
   - 提醒：提前15分钟
   ↓
2. 计算提醒时间
   reminderTime = 21:15 - 15分钟 = 21:00
   ↓
3. 设置 AlarmManager
   ↓
4. 系统到了 21:00（可能延迟2-5分钟）
   ↓
5. 触发 AlarmReceiver.onReceive()
   ↓
6. 显示通知
   ↓
7. 用户看到通知栏提醒！
```

---

## 💡 关于延迟问题

### 为什么会延迟 2-5 分钟？

**Android 省电机制**（Doze Mode）：
- Android 6.0+ 引入省电模式
- 系统会批量处理定时任务
- 延迟 2-5 分钟是正常现象

### 应用类型对比

| 应用类型 | 延迟要求 | API 选择 |
|---------|---------|---------|
| 📅 日历提醒 | 延迟几分钟 OK | `setExactAndAllowWhileIdle()` |
| ⏰ 闹钟 App | 必须精确到秒 | `setAlarmClock()` |
| 📧 邮件提醒 | 可以延迟 | `setAndAllowWhileIdle()` |

**我们的实现符合日历 App 的标准！** ✅

---

## 🎯 测试结果

### 功能测试
- ✅ 提醒功能正常运行
- ✅ 通知成功显示
- ✅ 延迟 2-5 分钟（符合预期）
- ✅ 删除日程自动取消提醒
- ✅ 编辑日程更新提醒

### 边界测试
- ✅ 过期日程不设置提醒
- ✅ reminderMinutes = 0 不设置提醒
- ✅ 多个日程提醒互不干扰

### 真机测试
- ✅ 华为手机 API 29 - 通过
- ✅ Pixel 2 模拟器 API 30 - 通过
- ✅ 锁屏状态下提醒显示 - 通过

---

## 📊 作业完成度

### 基本要求（3个）✅

1. ✅ **日历视图展示**（月视图）
2. ✅ **日程增删改查**
3. ✅ **日程提醒功能** ← 刚完成！

**🎉 基本要求 100% 完成！**

### 技术亮点
- ✨ Material Design 风格
- ✨ 协程异步处理
- ✨ ViewHolder 性能优化
- ✨ Room 数据库迁移
- ✨ AlarmManager 定时任务
- ✨ Notification 系统通知
- ✨ 权限动态请求

---

## 💻 代码统计

### 新增文件
- AlarmReceiver.kt（广播接收器）
- ReminderManager.kt（提醒管理器）

### 修改文件
- Event.kt（新增 reminderMinutes 字段）
- AppDatabase.kt（数据库版本升级 v1 → v2）
- MainActivity.kt（集成提醒功能）
- dialog_add_event.xml（新增 Spinner）
- AndroidManifest.xml（权限和接收器配置）

### 代码量
- **新增代码**：约 150 行
- **累计代码**：约 530 行

---

## 💭 心得体会

### 进展顺利
- ✅ AlarmManager 比想象中简单
- ✅ BroadcastReceiver 机制很强大
- ✅ 数据库迁移非常顺利
- ✅ Notification 显示效果很好

### 遇到的挑战
- ⚠️ 权限申请需要运行时请求（Android 13+）
- ⚠️ PendingIntent 的 FLAG 选择需要注意
- ⚠️ 延迟问题需要理解 Android 省电机制

### 经验总结
1. **提醒 = AlarmManager + BroadcastReceiver + Notification**
2. **数据库迁移用 Migration，不会丢数据**
3. **Android 13+ 通知需要动态申请权限**
4. **延迟是正常的，符合日历 App 标准**

### 对比之前项目
- **类似 Django 的 Celery**：定时任务调度
- **类似 Spring 的 @Scheduled**：定时执行
- **类似 Web 的 Web Worker**：后台任务

---

## 📝 明日计划

### Day 9：扩展功能 + 优化（可选）
- [ ] 日程分类/标签
- [ ] 搜索功能
- [ ] 数据导出/备份
- [ ] UI 美化和动画

### Day 10：文档整理 + 项目提交（必做）
- [ ] 完善开发文档
- [ ] 录制功能演示视频
- [ ] 整理项目结构
- [ ] 准备答辩 PPT
- [ ] 提交作业

---

**Day 8 完成！作业核心功能全部搞定！** 🎉🎉🎉

**今日评分**：⭐⭐⭐⭐⭐ (5/5) - 提醒功能完美实现！基本要求 100% 达成！

---

[⬅️ 上一天：Day 7](./day07_skipped.md) | [⬅️ 返回首页](./README.md)

