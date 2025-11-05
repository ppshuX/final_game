# Day 03 - Room 数据库集成

**日期**：2025年11月05日  
**用时**：约2小时（包括模拟器配置时间）  
**完成度**：✅ 100%

---

## 📋 今日任务完成情况

- [x] 添加 Room 数据库依赖和 KSP 插件
- [x] 创建 Event 实体类
- [x] 创建 EventDao 数据访问接口
- [x] 创建 AppDatabase 单例类
- [x] 改造 MainActivity 使用数据库存储
- [x] 测试数据持久化成功（真机 + 虚拟机）
- [x] 配置轻量级虚拟机（Pixel 2 API 30）

---

## 💻 写了哪些代码

### 1. Event 实体类 (Event.kt)

```kotlin
package com.ncu.kotlincalendar

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "events")
data class Event(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    
    val title: String,              // 标题
    val description: String = "",   // 描述
    val dateTime: Long,             // 日期时间（时间戳）
    val createdAt: Long = System.currentTimeMillis()  // 创建时间
)
```

**类似 Django ORM**：
```python
class Event(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    date_time = models.DateTimeField()
```

**代码要点**：
- `@Entity(tableName = "events")` - 声明这是数据库表
- `@PrimaryKey(autoGenerate = true)` - 自增主键
- `data class` - Kotlin 数据类，自动生成 equals、hashCode、toString

---

### 2. EventDao 接口 (EventDao.kt)

```kotlin
package com.ncu.kotlincalendar

import androidx.room.*

@Dao
interface EventDao {
    // 查询所有日程（按时间升序）
    @Query("SELECT * FROM events ORDER BY dateTime ASC")
    suspend fun getAllEvents(): List<Event>
    
    // 插入日程
    @Insert
    suspend fun insert(event: Event): Long
    
    // 更新日程
    @Update
    suspend fun update(event: Event)
    
    // 删除日程
    @Delete
    suspend fun delete(event: Event)
    
    // 根据 ID 查询
    @Query("SELECT * FROM events WHERE id = :eventId")
    suspend fun getEventById(eventId: Long): Event?
}
```

**类似 Django QuerySet**：
```python
Event.objects.all()           # getAllEvents()
Event.objects.create(...)     # insert()
event.save()                  # update()
event.delete()                # delete()
Event.objects.get(id=1)       # getEventById()
```

---

### 3. AppDatabase 类 (AppDatabase.kt)

```kotlin
package com.ncu.kotlincalendar

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

@Database(entities = [Event::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {
    abstract fun eventDao(): EventDao
    
    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null
        
        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "calendar_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

**单例模式（Singleton Pattern）**：
- 确保全局只有一个数据库实例
- `@Volatile` 保证多线程可见性
- `synchronized(this)` 保证线程安全

---

### 4. MainActivity 改造

```kotlin
class MainActivity : AppCompatActivity() {
    // 数据库相关
    private lateinit var database: AppDatabase
    private lateinit var eventDao: EventDao
    private val eventsList = mutableListOf<Event>()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 初始化数据库
        database = AppDatabase.getDatabase(this)
        eventDao = database.eventDao()
        
        // 加载所有日程
        loadAllEvents()
    }
    
    // 加载所有日程
    private fun loadAllEvents() {
        lifecycleScope.launch(Dispatchers.IO) {
            val events = eventDao.getAllEvents()
            withContext(Dispatchers.Main) {
                eventsList.clear()
                eventsList.addAll(events)
                updateEventsList()
            }
        }
    }
    
    // 添加日程（保存到数据库）
    private fun addEvent(title: String, description: String = "") {
        lifecycleScope.launch(Dispatchers.IO) {
            val event = Event(
                title = title,
                description = description,
                dateTime = selectedDateMillis
            )
            eventDao.insert(event)
            loadAllEvents()
        }
    }
}
```

**协程调度器**：
- `Dispatchers.IO` - 数据库、网络操作
- `Dispatchers.Main` - UI 更新
- `withContext()` - 切换线程

---

### 5. build.gradle.kts 配置

```kotlin
plugins {
    id("com.google.devtools.ksp") version "1.9.0-1.0.13"
}

dependencies {
    // Room 数据库
    val room_version = "2.6.0"
    implementation("androidx.room:room-runtime:$room_version")
    implementation("androidx.room:room-ktx:$room_version")
    ksp("androidx.room:room-compiler:$room_version")
    
    // 协程
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.6.2")
}
```

---

## 🐛 遇到的坑

### 坑 1：模拟器配置太高导致电脑卡死 ⚠️

**问题现象**：
- 创建 Medium Phone API 36.1 虚拟机
- 运行后电脑内存占用 94%
- 鼠标无法移动，强制重启 💥

**原因分析**：
- API 36 是最新版本，资源占用高
- 同时运行：Chrome + Android Studio + 虚拟机
- 电脑总内存：16GB，可用 < 2GB

**解决方案**：
1. ✅ 先用真机测试（华为手机 API 29）
2. ✅ 创建轻量级虚拟机（Pixel 2 API 30，1.5GB RAM）

**最终配置**：
- 设备：Pixel 2
- API 级别：30（Android 11）
- RAM：1.5GB
- 分辨率：720p

---

### 坑 2：协程和线程调度 🔄

**问题**：
```
java.lang.IllegalStateException: Cannot access database on the main thread
```

**原因**：
- Android 规定主线程不能执行耗时操作
- Room 强制要求在后台线程执行

**解决方案**：
```kotlin
lifecycleScope.launch(Dispatchers.IO) {  // 后台线程
    val events = eventDao.getAllEvents()
    withContext(Dispatchers.Main) {      // 切换到主线程
        updateUI(events)
    }
}
```

---

### 坑 3：从 Flow 到 List 的简化

**复杂版**（最初设计）：
```kotlin
fun getAllEvents(): Flow<List<Event>>  // 响应式
```

**简化版**（最终采用）：
```kotlin
suspend fun getAllEvents(): List<Event>  // 简单查询
```

**原因**：
- Flow 适合实时监听场景（聊天、股票）
- 日历应用手动刷新足够了
- 避免过度设计

---

## 💡 核心知识点

### 1. Room 数据库三件套

```
Room Database (数据库)
    ↓
DAO (数据访问层)
    ↓
Entity (实体类)
```

### 2. Kotlin 协程

```kotlin
lifecycleScope.launch {       // 启动协程
    suspend fun getData()     // 挂起函数
    withContext(Dispatchers.IO) // 切换线程
}
```

### 3. 数据持久化原理

```
添加日程 → Room → SQLite 文件 → 存储在手机
关闭 App → 内存清空，文件保留 ✅
重新打开 → Room 读取文件 → 数据恢复！
```

### 4. 移动应用架构

**Web 应用**：所有用户 → 云服务器 → 一个数据库  
**移动应用**：每个用户手机 → 独立 SQLite

---

## 🎯 今日成果

### 功能完成
- ✅ 数据库增删查改（CRUD）
- ✅ 数据持久化（重启不丢失）
- ✅ 协程异步处理
- ✅ 真机 + 虚拟机测试通过

### 代码统计
- **新增文件**：3 个（Event.kt, EventDao.kt, AppDatabase.kt）
- **修改文件**：2 个（MainActivity.kt, build.gradle.kts）
- **新增代码**：约 100 行
- **累计代码**：约 230 行

### 测试结果
- ✅ 华为手机 API 29 - 成功
- ✅ Pixel 2 API 30 - 成功
- ✅ 数据持久化 - 成功
- ✅ 10+ 条日程无卡顿 - 成功

---

## 💭 心得体会

### 进展顺利
- ✅ Room 比原生 SQLite 简单太多
- ✅ 协程让异步操作变得优雅
- ✅ 真机调试体验比虚拟机好

### 经验总结
1. **硬件资源很重要** - 虚拟机配置要根据电脑性能
2. **真机调试最高效** - 响应快、资源占用少
3. **不要过度设计** - Flow 虽强大但 List 够用
4. **协程是必修课** - Android 开发必须掌握

---

## 📝 明日计划

**Day 4 目标**：RecyclerView 列表优化

**核心任务**：
- [ ] 用 RecyclerView 替代 TextView
- [ ] 创建 EventAdapter 适配器
- [ ] 设计列表项布局
- [ ] 优化列表性能和样式

**预计难度**：⭐⭐⭐  
**预计用时**：2-3 小时

---

**Day 3 完成！数据库搞定，应用已经能真正使用了！** 🎉

**今日评分**：⭐⭐⭐⭐⭐ (5/5) - 虽然遇到硬件问题，但最终完美解决！

---

[⬅️ 上一天：Day 2](./day02_add_events.md) | [⬅️ 返回首页](./README.md) | [➡️ 下一天：Day 4](./day04_recyclerview.md)

