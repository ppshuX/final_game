# 📚 Kotlin & Android 学习笔记

> 从 Web 开发到 Android 开发的学习记录

---

## 📖 目录

- [Kotlin 基础语法](#kotlin-基础语法)
- [Android 基础](#android-基础)
- [Room 数据库](#room-数据库)
- [RecyclerView](#recyclerview)
- [系统组件](#系统组件)
- [对比学习](#对比学习)

---

## Kotlin 基础语法

### 1. 变量声明

```kotlin
// 不可变变量（类似 JS 的 const）
val name = "Calendar"

// 可变变量（类似 JS 的 let）
var count = 0

// 延迟初始化（稍后赋值）
lateinit var textView: TextView
```

### 2. Lambda 表达式

```kotlin
// Lambda 在最后可以提到括号外
calendarView.setOnDateChangeListener { view, year, month, day ->
    // 处理逻辑
}

// 类似 JavaScript
calendarView.setOnDateChangeListener((view, year, month, day) => {
    // 处理逻辑
})
```

### 3. 字符串模板

```kotlin
val name = "小明"
val age = 18
val str = "我叫$name，今年$age岁"     // 简单变量用 $
val str2 = "明年${age + 1}岁"          // 表达式用 ${}
```

### 4. data class

```kotlin
data class Event(
    val id: Long = 0,
    val title: String
)
// 自动生成 equals、hashCode、toString、copy
```

### 5. 协程

```kotlin
lifecycleScope.launch(Dispatchers.IO) {  // 后台线程
    val data = fetchData()
    withContext(Dispatchers.Main) {      // 切换到主线程
        updateUI(data)
    }
}
```

---

## Android 基础

### 1. Activity 生命周期

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        // 创建时调用（类似 Vue 的 mounted）
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}
```

### 2. findViewById

```kotlin
// 通过 ID 获取组件
val textView = findViewById<TextView>(R.id.tvSelectedDate)

// 类似 Web 的
document.getElementById('tvSelectedDate')
```

### 3. 布局文件 (XML)

```xml
<TextView
    android:id="@+id/tvSelectedDate"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="请选择日期"
    android:textSize="18sp"
    android:padding="16dp" />
```

### 4. 监听器

```kotlin
// 点击事件
view.setOnClickListener {
    // 处理点击
}

// 长按事件
view.setOnLongClickListener {
    // 处理长按
    true  // 返回 true 表示消费事件
}
```

---

## Room 数据库

### 三件套：Entity + DAO + Database

#### 1. Entity（实体类）

```kotlin
@Entity(tableName = "events")
data class Event(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val title: String
)
```

#### 2. DAO（数据访问对象）

```kotlin
@Dao
interface EventDao {
    @Query("SELECT * FROM events")
    suspend fun getAllEvents(): List<Event>
    
    @Insert
    suspend fun insert(event: Event): Long
    
    @Delete
    suspend fun delete(event: Event)
}
```

#### 3. Database（数据库类）

```kotlin
@Database(entities = [Event::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun eventDao(): EventDao
    
    companion object {
        fun getDatabase(context: Context): AppDatabase {
            // 单例模式
        }
    }
}
```

### 数据库迁移

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL(
            "ALTER TABLE events ADD COLUMN reminderMinutes INTEGER NOT NULL DEFAULT 0"
        )
    }
}
```

---

## RecyclerView

### Adapter 模式

```kotlin
class EventAdapter(
    private var events: List<Event>
) : RecyclerView.Adapter<EventAdapter.EventViewHolder>() {
    
    // ViewHolder - 持有控件引用
    class EventViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val tvTitle: TextView = view.findViewById(R.id.tvTitle)
    }
    
    // 创建 ViewHolder
    override fun onCreateViewHolder(...): EventViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_event, parent, false)
        return EventViewHolder(view)
    }
    
    // 绑定数据
    override fun onBindViewHolder(holder: EventViewHolder, position: Int) {
        holder.tvTitle.text = events[position].title
    }
    
    override fun getItemCount() = events.size
}
```

### 复用机制

- 只创建屏幕可见的 View（5-7个）
- 滚动时复用 View，只改数据
- 比 TextView 显示100个项性能高10倍+

---

## 系统组件

### 1. TimePickerDialog

```kotlin
TimePickerDialog(
    context,
    { _, hour, minute ->
        // 选择后的回调
    },
    12,    // 初始小时
    0,     // 初始分钟
    true   // 24小时制
).show()
```

### 2. AlarmManager

```kotlin
val alarmManager = context.getSystemService(Context.ALARM_SERVICE) as AlarmManager

alarmManager.setExactAndAllowWhileIdle(
    AlarmManager.RTC_WAKEUP,
    triggerTime,
    pendingIntent
)
```

### 3. NotificationManager

```kotlin
val notification = NotificationCompat.Builder(context, CHANNEL_ID)
    .setSmallIcon(R.drawable.ic_notification)
    .setContentTitle("标题")
    .setContentText("内容")
    .build()

notificationManager.notify(id, notification)
```

---

## 对比学习

### Android vs Web

| Android | Web | 说明 |
|---------|-----|------|
| `findViewById` | `getElementById` | 获取元素 |
| `setOnClickListener` | `addEventListener('click')` | 点击事件 |
| `TextView` | `<div>` / `<p>` | 文本显示 |
| `LinearLayout` | `flex-direction: column` | 垂直布局 |
| `activity_main.xml` | `index.html` | 界面文件 |
| `MainActivity.kt` | `main.js` | 逻辑代码 |

### Room vs Django ORM

| Room | Django | 说明 |
|------|--------|------|
| `@Entity` | `models.Model` | 定义模型 |
| `@PrimaryKey` | `AutoField(primary_key=True)` | 主键 |
| `@Query("SELECT...")` | `Event.objects.all()` | 查询 |
| `@Insert` | `Event.objects.create()` | 插入 |
| `suspend fun` | `async def` | 异步函数 |

### Kotlin vs JavaScript

| Kotlin | JavaScript | 说明 |
|--------|------------|------|
| `val` / `var` | `const` / `let` | 变量声明 |
| `fun name() {}` | `function name() {}` | 函数定义 |
| `{ x -> x * 2 }` | `(x) => x * 2` | Lambda/箭头函数 |
| `"Hello $name"` | `` `Hello ${name}` `` | 字符串模板 |
| 静态类型 | 动态类型 | 类型系统 |

---

## 💡 关键要点

### 1. 协程线程调度

- `Dispatchers.IO` - 数据库、网络操作
- `Dispatchers.Main` - UI 更新
- `withContext()` - 切换线程

### 2. ViewHolder 复用

- `onCreateViewHolder` - 创建 View（只调用几次）
- `onBindViewHolder` - 绑定数据（每次显示都调用）

### 3. 数据库迁移

- 修改 Entity 后，更新 version
- 添加 Migration 逻辑
- 不会丢失原有数据

### 4. 权限管理

- Android 13+ 通知需要动态申请
- PendingIntent 需要 FLAG_IMMUTABLE

---

## 📚 学习资源

- [Kotlin 官方文档](https://kotlinlang.org/docs/)
- [Android 开发者指南](https://developer.android.com/)
- [Room 持久化库](https://developer.android.com/training/data-storage/room)
- [协程指南](https://kotlinlang.org/docs/coroutines-guide.html)

---

[⬅️ 返回首页](./README.md)

