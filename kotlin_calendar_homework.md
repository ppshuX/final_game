# 📅 Kotlin Calendar 大作业开发日志

> **项目名称**：Android 日历应用（Kotlin Calendar Homework）
> 
> **开发平台**：Android Studio + Kotlin
> 
> **项目周期**：2025年11月4日 - 2025年11月14日（预计10天）
> 
> **所属计划**：Show Way Plan 第二阶段 - 腾讯青英班大作业
> 
> **当前状态**：🚀 进行中

---

## 📊 项目整体进度

```
████████████████████░░░░░░░░░░░░░░░░░░░░ 40%

Day 1: 基础日历界面 ✅
Day 2: 添加和显示日程 ✅
Day 3: Room 数据库集成 ✅
Day 4: RecyclerView 列表优化 ✅
Day 5-10: 待开发 ⏳
```

**项目启动**：2025年11月4日  
**预计完成**：2025年11月14日  
**开发状态**：进行中  
**当前阶段**：Day 4 已完成 - RecyclerView 性能优化！

---

## 📅 10天开发进度总览

| 天数 | 主要任务 | 状态 | 用时 | 备注 |
|------|---------|------|------|------|
| **Day 1** | 把日历显示出来 | ✅ 完成 | 3h | 解决依赖冲突，原生CalendarView |
| **Day 2** | 能添加和显示日程 | ✅ 完成 | 2h | Material Dialog + 卡片布局 |
| **Day 3** | Room 数据库集成 | ✅ 完成 | 2h | Room + 协程 + 真机测试 |
| **Day 4** | RecyclerView 列表优化 | ✅ 完成 | 1h | Adapter + ViewHolder + 卡片样式 |
| **Day 5** | 编辑和删除 | ⏳ 计划中 | - | CRUD完整实现 |
| **Day 6** | 时间选择器 | ⏳ 计划中 | - | TimePicker + DatePicker |
| **Day 7** | 多视图切换 | ⏳ 计划中 | - | 周视图、日视图 |
| **Day 8** | 提醒功能 | ⏳ 计划中 | - | Notification + AlarmManager |
| **Day 9** | 扩展功能 + 优化 | ⏳ 计划中 | - | 性能优化、功能完善 |
| **Day 10** | 文档和提交 | ⏳ 计划中 | - | 整理文档、项目收尾 |

**状态图例**：⏳ 未开始 | 🚀 进行中 | ✅ 完成

**开始时间**：2025年11月4日  
**完成时间**：预计 2025年11月14日

---

## 🎯 项目目标

### 核心功能需求
- [x] 显示日历月视图
- [x] 日期选择交互
- [ ] 添加日程功能
- [ ] 显示日程列表
- [ ] 编辑日程
- [ ] 删除日程
- [ ] 日程提醒（可选）
- [ ] 数据持久化

### 技术目标
- ✅ 学习 Kotlin 基础语法
- ✅ 掌握 Android 开发基础
- ⏳ 掌握数据存储（SharedPreferences 或 SQLite）
- ⏳ 掌握 RecyclerView（列表展示）
- ⏳ 掌握 UI 设计与交互

---

## 📅 每日开发日志

> 💡 **记录说明**：每天完成后简单记录做了什么、遇到了什么坑，流水账形式，方便以后回顾。

---

### Day 01 - 搭建基础日历界面 ✅

**日期**：2025年11月04日  
**用时**：约3小时（包括踩坑时间）  
**完成度**：✅ 100%

#### 📋 今日任务完成情况

- [x] 成功运行 Android 项目
- [x] 日历完整显示（月视图）
- [x] 实现日期选择交互
- [x] 显示选中的日期（中文格式 + 星期）
- [x] 解决依赖冲突问题

#### 💻 完整代码实现

**1. 布局文件 (activity_main.xml)**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <!-- Android 原生日历 -->
    <CalendarView
        android:id="@+id/calendarView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <!-- 显示选中日期的文本 -->
    <TextView
        android:id="@+id/tvSelectedDate"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="请选择日期"
        android:textSize="18sp"
        android:gravity="center" />
</LinearLayout>
```

**设计思路**：
- 用 `LinearLayout` 垂直布局，简单直接
- 日历占上半部分，自适应高度
- 下面用 `TextView` 显示选中的日期

**2. 主程序 (MainActivity.kt)**

```kotlin
package com.ncu.kotlincalendar

import android.os.Bundle
import android.widget.CalendarView
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import java.text.SimpleDateFormat
import java.util.*

class MainActivity : AppCompatActivity() {
    
    // 声明组件（lateinit = 延迟初始化，onCreate 时再赋值）
    private lateinit var calendarView: CalendarView
    private lateinit var tvSelectedDate: TextView
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 通过 findViewById 获取布局中的组件
        calendarView = findViewById(R.id.calendarView)
        tvSelectedDate = findViewById(R.id.tvSelectedDate)
        
        // 默认显示今天的日期
        showDate(System.currentTimeMillis())
        
        // 设置日期选择监听（Lambda 表达式）
        calendarView.setOnDateChangeListener { view, year, month, dayOfMonth ->
            // 创建 Calendar 对象
            val calendar = Calendar.getInstance()
            calendar.set(year, month, dayOfMonth)
            // 显示选中的日期
            showDate(calendar.timeInMillis)
        }
        
        Toast.makeText(this, "日历加载成功！点击日期试试", Toast.LENGTH_SHORT).show()
    }
    
    /**
     * 显示日期的辅助函数
     * @param timeInMillis 时间戳（毫秒）
     */
    private fun showDate(timeInMillis: Long) {
        // 创建日期格式化对象（yyyy年MM月dd日 星期X）
        val dateFormat = SimpleDateFormat("yyyy年MM月dd日 EEEE", Locale.CHINESE)
        val dateStr = dateFormat.format(Date(timeInMillis))
        tvSelectedDate.text = "选中日期：$dateStr"
    }
}
```

**代码要点**：
1. **`lateinit var`**：延迟初始化，在 `onCreate` 时赋值
2. **`findViewById<T>(R.id.xxx)`**：通过 ID 获取组件（类似 Web 的 `document.getElementById`）
3. **`setOnDateChangeListener`**：日期改变监听器（Lambda 表达式）
4. **`SimpleDateFormat`**：日期格式化工具，`EEEE` 表示完整的星期名
5. **`Locale.CHINESE`**：中文本地化

**3. 颜色资源 (colors.xml)**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    
    <!-- Material Design 主题色 -->
    <color name="purple_500">#FF6200EE</color>
    <color name="purple_700">#FF3700B3</color>
    <color name="teal_200">#FF03DAC5</color>
    <color name="teal_700">#FF018786</color>
    <color name="red">#FFF44336</color>
    <color name="blue">#FF2196F3</color>
    <color name="green">#FF4CAF50</color>
</resources>
```

**为什么添加这些颜色**：
- Material Design 标准色
- 后面会用到（按钮、提示等）

#### 🐛 遇到的坑（详细版）

**坑 1：第三方日历库依赖冲突**

**问题现象**：
```
ClassNotFoundException: Didn't find class "android.support.v4.widget.EdgeEffectCompat"
```

**原因分析**：
- `material-calendarview:1.4.3` 是 2016 年的库
- 内部使用旧的 Support Library
- 我们项目用的是新的 AndroidX
- 两者不兼容，导致运行时找不到类

**尝试的解决方案**：
1. ❌ 排除冲突依赖 → 还是报错
2. ❌ 换 `kizitonwose.calendar` → IDE 识别不了
3. ✅ **最终方案**：用 Android 原生 `CalendarView`

**学到的经验**：
- 第三方库要看维护情况，太老的别用
- AndroidX 和 Support Library 不能混用
- 遇到依赖问题，原生 API 是最稳的

**坑 2：颜色资源找不到**

**问题现象**：
```
error: resource color/purple_500 not found
```

**原因**：
- 项目初始 `colors.xml` 只有黑白两色
- 布局里用了 `@color/purple_500` 但没定义

**解决方案**：
- 在 `app/src/main/res/values/colors.xml` 里添加颜色定义

**学到的**：
- Android 资源都要预先定义
- 颜色、字符串、尺寸等都在 `res/values/` 下

**坑 3：Gradle 同步问题**

**问题现象**：
- 修改 `build.gradle.kts` 后，IDE 报红色波浪线
- `Unresolved reference`

**解决方案**：
1. 点击 "Sync Now"
2. 或 File → Sync Project with Gradle Files
3. 必要时 Clean Project + Rebuild

**学到的**：
- 每次改依赖都要同步 Gradle
- 缓存问题可以 Invalidate Caches

#### 📚 今日学到的知识点

**Kotlin 语法**

1. **lateinit var**
   ```kotlin
   private lateinit var calendarView: CalendarView
   // 声明但不初始化，后面再赋值
   ```

2. **Lambda 表达式**
   ```kotlin
   setOnDateChangeListener { view, year, month, day ->
       // 相当于 JavaScript 的箭头函数
   }
   ```

3. **字符串模板**
   ```kotlin
   tvSelectedDate.text = "选中日期：$dateStr"
   // 类似 JS 的 `选中日期：${dateStr}`
   ```

**Android 基础**

1. **Activity 生命周期**
   - `onCreate()` - 创建时调用，类似 Vue 的 `mounted`

2. **findViewById**
   - 通过 ID 获取布局中的组件
   - 类似 `document.getElementById`

3. **布局文件 (XML)**
   - UI 用 XML 定义，类似 HTML
   - `android:id="@+id/xxx"` 定义 ID
   - `android:layout_width/height` 设置宽高

4. **监听器 (Listener)**
   - `setOnDateChangeListener` - 日期改变监听
   - 类似 Web 的 `addEventListener`

#### 5. UI 组件（Day 2）

**TextInputLayout（Material Design 输入框）**
```xml
<com.google.android.material.textfield.TextInputLayout
    android:hint="提示文本"
    app:boxBackgroundMode="outline">
    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etInput" />
</com.google.android.material.textfield.TextInputLayout>
```

**AlertDialog（对话框）**
```kotlin
AlertDialog.Builder(this)
    .setTitle("标题")
    .setView(view)  // 自定义布局
    .setPositiveButton("确定") { _, _ -> }
    .setNegativeButton("取消", null)
    .show()
```

**LayoutInflater（布局加载器）**
```kotlin
val view = layoutInflater.inflate(R.layout.dialog_add_event, null)
val input = view.findViewById<EditText>(R.id.xxx)
```

**长按事件**
```kotlin
view.setOnLongClickListener {
    // 处理长按
    true  // 返回 true 表示消费事件
}
```

**Android vs Web 对比**

| Android | Web | 说明 |
|---------|-----|------|
| `findViewById` | `getElementById` | 获取元素 |
| `setOnClickListener` | `addEventListener('click')` | 点击事件 |
| `TextView` | `<div>` / `<p>` | 文本显示 |
| `LinearLayout` | `flex-direction: column` | 垂直布局 |
| `activity_main.xml` | `index.html` | 界面文件 |
| `MainActivity.kt` | `main.js` | 逻辑代码 |

#### 6. RecyclerView（Day 4）

**Adapter（适配器）**
```kotlin
class EventAdapter(
    private var events: List<Event>,
    private val onItemClick: (Event) -> Unit
) : RecyclerView.Adapter<EventAdapter.EventViewHolder>() {
    
    // ViewHolder - 持有控件
    class EventViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val tvTitle: TextView = view.findViewById(R.id.tvTitle)
    }
    
    // 创建 ViewHolder
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): EventViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_event, parent, false)
        return EventViewHolder(view)
    }
    
    // 绑定数据
    override fun onBindViewHolder(holder: EventViewHolder, position: Int) {
        val event = events[position]
        holder.tvTitle.text = event.title
        holder.itemView.setOnClickListener { onItemClick(event) }
    }
    
    override fun getItemCount() = events.size
}
```

**复用机制**

| 方式 | 创建 View 数 | 内存占用 | 性能 |
|------|-------------|---------|------|
| TextView | 100 个 | 高 🔴 | 卡 🔴 |
| RecyclerView | 5-7 个 | 低 ✅ | 流畅 ✅ |

**对比前端**：

| RecyclerView | Vue/React | 说明 |
|--------------|-----------|------|
| 物理复用 View | 虚拟 DOM | Android 更底层 |
| 只渲染可见 | 渲染全部 | 性能更优 |
| ViewHolder 缓存 | VDOM Diff | 不同优化策略 |

#### 7. Room 数据库（Day 3）

**Entity（实体类）**
```kotlin
@Entity(tableName = "events")
data class Event(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val title: String
)
```

**DAO（数据访问对象）**
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

**Database（数据库类）**
```kotlin
@Database(entities = [Event::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun eventDao(): EventDao
    
    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null
        
        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context,
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

**Room vs Django ORM**

| Room | Django | 说明 |
|------|--------|------|
| `@Entity` | `models.Model` | 定义模型 |
| `@PrimaryKey` | `AutoField(primary_key=True)` | 主键 |
| `@Query("SELECT...")` | `Event.objects.all()` | 查询 |
| `@Insert` | `Event.objects.create()` | 插入 |
| `@Delete` | `event.delete()` | 删除 |
| `suspend fun` | `async def` | 异步函数 |
| `res/values/colors.xml` | CSS 变量 | 颜色定义 |

#### 🎯 今日成果

**功能演示**
- ✅ 日历显示：November 2025
- ✅ 日期选择：点击任意日期，高亮显示
- ✅ 日期展示：选中日期: 2025年11月04日 星期二
- ✅ Toast 提示：日历加载成功

**代码统计**
- 新增文件：3 个
  - MainActivity.kt（主程序）
  - activity_main.xml（布局文件）
  - colors.xml（颜色资源）
- 修改文件：2 个
  - build.gradle.kts（依赖配置）
  - AndroidManifest.xml（应用配置）
- 核心代码行数：约 50 行

#### 💡 心得体会

**进展顺利的地方**
- ✅ Kotlin 语法和 JavaScript 很像，上手快
- ✅ Android Studio 提示很智能，写代码很流畅
- ✅ 原生 CalendarView 简单好用
- ✅ 有 Final 系列的基础，学习新技术更快

**遇到的挑战**
- ⚠️ 依赖冲突踩了不少坑
- ⚠️ 第一次接触 Gradle，同步概念需要理解
- ⚠️ XML 布局方式和 Web 不同，需要适应

**经验总结**
1. **遇到库冲突，优先考虑原生 API**
2. **每次改依赖记得 Sync Gradle**
3. **报错先看 Logcat，找关键信息**
4. **边做边学比看完教程再做更有效**
5. **有 Web 开发基础，学 Android 会轻松很多**

#### 📝 明日计划

**Day 2 目标**：实现添加和显示日程

**核心任务**：
- [ ] 创建添加日程的界面（Dialog 或新 Activity）
- [ ] 实现添加日程功能（标题、时间）
- [ ] 在列表中显示日程（RecyclerView）
- [ ] 点击日期弹出添加界面

**预计难度**：⭐⭐⭐  
**预计用时**：3-4 小时

#### 📸 效果截图

**最终效果**：
- ✅ 日历月视图显示正常
- ✅ 选中日期高亮显示
- ✅ 中文日期格式：2025年11月04日 星期二

---

**Day 1 评分**：⭐⭐⭐⭐ (4/5)  
**评价**：有坑但都解决了，收获满满！从 Web 到 Android，开始新的征程！

---

## 🔧 技术栈

### 开发环境
- **IDE**：Android Studio
- **语言**：Kotlin
- **Android SDK**：API Level 34
- **构建工具**：Gradle (Kotlin DSL)

### 核心库
- **AndroidX**：现代 Android 支持库
- **Material Components**：Material Design UI 组件
- **CalendarView**：Android 原生日历组件

### 计划使用
- **RecyclerView**：列表展示（Day 2）
- **SharedPreferences** 或 **SQLite**：数据存储（Day 3-4）
- **Notification**：日程提醒（可选）

---

## 📚 学习笔记

### Kotlin 基础语法

#### 1. 变量声明（Day 1）
```kotlin
// 不可变变量（类似 JS 的 const）
val name = "Calendar"

// 可变变量（类似 JS 的 let）
var count = 0

// 延迟初始化（稍后赋值）
lateinit var textView: TextView
```

#### 2. Lambda 表达式
```kotlin
// 完整写法
calendarView.setOnDateChangeListener({ view, year, month, day ->
    // 处理逻辑
})

// 简化写法（Lambda 在最后可以提到括号外）
calendarView.setOnDateChangeListener { view, year, month, day ->
    // 处理逻辑
}
```

#### 3. 字符串模板
```kotlin
val name = "小明"
val age = 18
val str = "我叫$name，今年$age岁"  // 简单变量用 $
val str2 = "明年${age + 1}岁"      // 表达式用 ${}
```

#### 4. 函数定义（Day 1）
```kotlin
// 无返回值
fun showDate(time: Long) {
    // 函数体
}

// 有返回值
fun getDate(time: Long): String {
    return "2025-11-04"
}

// 单表达式函数（自动推断返回类型）
fun double(x: Int) = x * 2
```

#### 5. Kotlin 实用技巧（Day 2）

**buildString {}**
```kotlin
val str = buildString {
    append("第一行\n")
    append("第二行")
}
// 高效的字符串构建器，类似 JavaScript 的数组 join()
```

**字符串判断**
```kotlin
str.isNotEmpty()  // 非空判断
str.isEmpty()     // 空判断
str.trim()        // 去除首尾空格
```

**mapIndexed**
```kotlin
eventsList.mapIndexed { index, event ->
    "日程 ${index + 1}"
}
// 带索引的 map，类似 JS 的 map((item, index) => ...)
```

**默认参数**
```kotlin
fun addEvent(title: String, description: String = "") {
    // description 有默认值，调用时可省略
}
```

### Android 基础

#### 1. Activity 生命周期（Day 1）
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        // Activity 创建时调用（类似 Vue 的 mounted）
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}
```

#### 2. 视图绑定
```kotlin
// findViewById 方式（传统）
val textView = findViewById<TextView>(R.id.tvSelectedDate)

// 也可以不指定类型（Kotlin 类型推断）
val textView: TextView = findViewById(R.id.tvSelectedDate)
```

#### 3. 布局文件 (XML)
```xml
<!-- ID 定义 -->
<TextView
    android:id="@+id/tvSelectedDate"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="请选择日期"
    android:textSize="18sp"
    android:padding="16dp"
    android:gravity="center" />
```

**属性说明**：
- `android:id`：组件 ID
- `match_parent`：填满父容器
- `wrap_content`：适应内容大小
- `sp`：字体大小单位（Scale-independent Pixels）
- `dp`：尺寸单位（Density-independent Pixels）

#### 4. Toast 提示
```kotlin
Toast.makeText(this, "提示内容", Toast.LENGTH_SHORT).show()
// this: 上下文
// Toast.LENGTH_SHORT: 短时间显示（2秒）
// Toast.LENGTH_LONG: 长时间显示（3.5秒）
```

---

## 🎓 Kotlin vs JavaScript 对比

### 语法对比

| 特性 | Kotlin | JavaScript |
|------|--------|------------|
| **变量** | `val` / `var` | `const` / `let` |
| **函数** | `fun name() {}` | `function name() {}` |
| **Lambda** | `{ x -> x * 2 }` | `(x) => x * 2` |
| **字符串模板** | `"Hello $name"` | `` `Hello ${name}` `` |
| **类** | `class Person {}` | `class Person {}` |
| **空安全** | `String?` | （无，需手动检查） |
| **类型** | 静态类型 | 动态类型 |

### 相似点
- ✅ 都支持 Lambda 表达式
- ✅ 都支持字符串模板
- ✅ 都支持类和继承
- ✅ 都有闭包概念

### 不同点
- ❌ Kotlin 是静态类型（编译时检查）
- ❌ Kotlin 有空安全（`?` 表示可空）
- ❌ Kotlin 没有 `undefined`（只有 `null`）
- ❌ Kotlin 函数参数类型必须声明

---

## 📈 项目统计

### 累计统计（截至 Day 4）
- **完成天数**：4 天
- **累计用时**：8 小时
- **总文件数**：11 个
  - **业务代码**：
    - MainActivity.kt（主程序）
    - Event.kt（实体类）
    - EventDao.kt（数据访问接口）
    - AppDatabase.kt（数据库单例）
    - EventAdapter.kt（RecyclerView 适配器）
  - **布局文件**：
    - activity_main.xml（主布局）
    - dialog_add_event.xml（对话框布局）
    - item_event.xml（卡片布局）
  - **配置文件**：
    - colors.xml（颜色资源）
    - build.gradle.kts（依赖配置）
    - AndroidManifest.xml（应用配置）
- **累计代码行数**：约 310 行
- **功能完成**：4/8（50%）
- **遇到的坑**：9 个（全部解决）
- **数据库规模**：1 张表（events），4 个字段

### Day 4 统计
- **新增文件**：2 个（EventAdapter.kt、item_event.xml）
- **修改文件**：2 个（MainActivity.kt、activity_main.xml）
- **新增代码行数**：约 80 行
- **开发用时**：1 小时
- **遇到的坑**：0 个（流畅完成）
- **进度提升**：30% → 40%
- **技术突破**：RecyclerView、ViewHolder 复用、Material Card

---

## 🗓️ 开发计划（10天）

### 第一阶段：基础功能（Day 1-4）
- [x] **Day 1**：搭建基础日历界面 ✅
- [ ] **Day 2**：添加和显示日程
- [ ] **Day 3**：数据持久化（SharedPreferences）
- [ ] **Day 4**：编辑和删除日程

### 第二阶段：功能完善（Day 5-7）
- [ ] **Day 5**：UI 优化和美化
- [ ] **Day 6**：日程分类和标签
- [ ] **Day 7**：搜索和筛选功能

### 第三阶段：高级功能（Day 8-10）
- [ ] **Day 8**：日程提醒（Notification）
- [ ] **Day 9**：数据导出和备份
- [ ] **Day 10**：最终优化和测试

---

## 💡 Show Way Plan 第二阶段

### 阶段目标
- **时间**：2025年11月4日 - 2025年11月14日（10天）
- **核心任务**：
  1. 完成腾讯青英班大作业（Kotlin 日历应用）
  2. 八股文记诵（计算机基础、算法、Java、框架等）
- **目标**：为面试做准备

### 第二阶段与 Final 系列的关系
- **Final 系列**（第一阶段）：技术深度 + 项目经验
- **Kotlin 日历**（第二阶段）：Android 开发 + Kotlin 语言
- **八股文记诵**（第二阶段）：理论基础 + 面试准备

### 技术栈拓展
```
Final 系列（已完成）
  ├─ 前端：JavaScript、Vue3
  ├─ 后端：Django、Spring Boot
  └─ 数据库：MySQL、Redis

Kotlin 日历（进行中）
  ├─ 移动端：Android
  ├─ 语言：Kotlin
  └─ 平台：Android SDK
```

---

## 🎯 项目目标

### 功能目标
- [ ] 基础日历显示（月视图）✅
- [ ] 添加日程功能
- [ ] 显示日程列表
- [ ] 编辑日程
- [ ] 删除日程
- [ ] 数据持久化
- [ ] 日程提醒（可选）
- [ ] UI 美化

### 学习目标
- [ ] 掌握 Kotlin 基础语法
- [ ] 掌握 Android 开发流程
- [ ] 掌握 Android UI 设计
- [ ] 掌握数据存储方案
- [ ] 掌握 RecyclerView 使用

### 时间目标
- **预计完成**：2025年11月14日
- **每天投入**：3-4 小时
- **总投入**：约 30-40 小时

---

## 📊 开发进度追踪

| 日期 | 任务 | 状态 | 用时 | 完成度 |
|------|------|------|------|--------|
| 11/4 | 搭建基础日历界面 | ✅ 完成 | 3h | 10% |
| 11/5 | 添加和显示日程 | ⏳ 计划中 | - | - |
| 11/6 | 数据持久化 | ⏳ 计划中 | - | - |
| 11/7 | 编辑和删除日程 | ⏳ 计划中 | - | - |
| 11/8 | UI 优化 | ⏳ 计划中 | - | - |
| 11/9 | 高级功能 | ⏳ 计划中 | - | - |
| 11/10 | 提醒功能 | ⏳ 计划中 | - | - |
| 11/11-14 | 预留时间 | ⏳ 计划中 | - | - |

---

## 🔗 相关链接

- **项目仓库**：（待创建 Gitee 仓库）
- **Android 官方文档**：https://developer.android.com/
- **Kotlin 官方文档**：https://kotlinlang.org/docs/home.html

---

## 📝 备注

### 与 Final 系列的对比
- **Final_KOF**：2天完成，前端游戏
- **Final_MySpace**：3天完成，Vue3全栈
- **Final_AcApp**：7天完成，Django后端
- **Final_KOB**：15天完成，Spring Boot企业级
- **Kotlin Calendar**：预计10天，Android移动端 ← 当前

### 技术栈扩展
从 Web 全栈到移动端开发，技术栈进一步拓展：
- ✅ JavaScript → ✅ Kotlin
- ✅ Vue3 → ⏳ Android UI
- ✅ Spring Boot → ⏳ Android 架构
- ✅ MySQL → ⏳ SQLite

---

> "Keep building, keep recording, keep believing." 💪
> 
> "From web to mobile — 技术的边界在不断扩展！" 🚀

---

**Day 1 完成！明天继续加油！** 💪

**今日评分**：⭐⭐⭐⭐ (4/5) - 有坑但都解决了，收获满满！

---

### Day 02 - 添加和显示日程 ✅

**日期**：2025年11月04日  
**用时**：约2小时  
**完成度**：✅ 100%

#### 📋 今日任务完成情况

- [x] 实现添加日程对话框（Material Design 风格）
- [x] 支持输入标题 + 描述
- [x] 日程列表卡片式显示
- [x] 实现长按删除功能
- [x] 优化界面样式和用户体验

#### 💻 写了哪些代码

**1. 自定义对话框布局 (dialog_add_event.xml)**

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:padding="16dp">

    <!-- Material 输入框 -->
    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="日程标题"
        app:boxBackgroundMode="outline">
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etTitle"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>
    
    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="详细描述（可选）"
        app:boxBackgroundMode="outline">
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etDescription"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>
</LinearLayout>
```

**2. 添加日程功能 (MainActivity.kt)**

```kotlin
// 添加日程函数
private fun addEvent(title: String, description: String = "") {
    val event = buildString {
        append("┌────────────────────────\n")
        append("│ 📅 $dateStr\n")
        append("│ 📝 $title\n")
        if (description.isNotEmpty()) {
            append("│ 💬 $description\n")
        }
        append("└────────────────────────")
    }
    eventsList.add(event)
    updateEventsList()
}

// 显示添加对话框
private fun showAddEventDialog() {
    val view = layoutInflater.inflate(R.layout.dialog_add_event, null)
    val etTitle = view.findViewById<TextInputEditText>(R.id.etTitle)
    val etDescription = view.findViewById<TextInputEditText>(R.id.etDescription)
    
    AlertDialog.Builder(this)
        .setTitle("添加日程")
        .setView(view)
        .setPositiveButton("确定") { _, _ ->
            val title = etTitle.text.toString().trim()
            val desc = etDescription.text.toString().trim()
            if (title.isNotEmpty()) {
                addEvent(title, desc)
            }
        }
        .setNegativeButton("取消", null)
        .show()
}
```

**3. 长按删除功能**

```kotlin
// 长按删除
tvEvents.setOnLongClickListener {
    showDeleteDialog()
    true
}

// 删除对话框
private fun showDeleteDialog() {
    if (eventsList.isEmpty()) {
        Toast.makeText(this, "没有日程可删除", Toast.LENGTH_SHORT).show()
        return
    }
    
    val items = eventsList.mapIndexed { index, event ->
        "日程 ${index + 1}"
    }.toTypedArray()
    
    AlertDialog.Builder(this)
        .setTitle("选择要删除的日程")
        .setItems(items) { _, which ->
            eventsList.removeAt(which)
            updateEventsList()
            Toast.makeText(this, "已删除", Toast.LENGTH_SHORT).show()
        }
        .setNegativeButton("取消", null)
        .show()
}
```

#### 🎨 界面优化亮点

1. **Material Design 输入框**
   - 更专业的 UI
   - 外边框样式 (`boxBackgroundMode="outline"`)
   - 浮动提示标签

2. **卡片式布局**
   - 用 Unicode 字符绘制边框
   - 清晰的视觉层次
   - Emoji 图标：📅📝💬

3. **长按删除**
   - 更符合移动端操作习惯
   - 弹出选择对话框
   - 删除后 Toast 提示

4. **等宽字体**
   - 让框线对齐美观
   - 使用 `android:fontFamily="monospace"`

5. **浅灰背景 + 白色卡片**
   - 层次分明
   - 符合 Material Design 规范

#### 💡 今日学到的知识

**Kotlin 实用技巧**

1. **buildString {}**
   ```kotlin
   val str = buildString {
       append("第一行\n")
       append("第二行")
   }
   // 类似 JavaScript 的数组 join()
   ```

2. **字符串判断**
   ```kotlin
   str.isNotEmpty()  // 非空判断
   str.isEmpty()     // 空判断
   str.trim()        // 去除首尾空格
   ```

3. **mapIndexed**
   ```kotlin
   eventsList.mapIndexed { index, event ->
       "日程 ${index + 1}"
   }
   // 带索引的 map，类似 JS 的 map((item, index) => ...)
   ```

4. **默认参数**
   ```kotlin
   fun addEvent(title: String, description: String = "") {
       // description 有默认值，调用时可省略
   }
   ```

**Android UI 组件**

1. **TextInputLayout**
   - Material Design 输入框
   - 支持浮动标签
   - 支持错误提示

2. **AlertDialog**
   - 标准对话框
   - `.setTitle()` 设置标题
   - `.setView()` 自定义布局
   - `.setPositiveButton()` 确定按钮
   - `.setNegativeButton()` 取消按钮
   - `.setItems()` 列表选择

3. **LayoutInflater**
   - 将 XML 布局转为 View 对象
   - `layoutInflater.inflate(R.layout.xxx, null)`

4. **长按事件**
   ```kotlin
   view.setOnLongClickListener {
       // 处理长按
       true  // 返回 true 表示消费事件
   }
   ```

#### 🐛 遇到的小坑

**坑 1：Material 组件找不到**

**问题**：
```
Unresolved reference: TextInputLayout
```

**解决**：
- 在 `build.gradle.kts` 添加 Material 依赖
- `implementation("com.google.android.material:material:1.10.0")`
- Sync Gradle

**坑 2：输入框获取不到值**

**原因**：
- 用了 `EditText` 而不是 `TextInputEditText`
- Material 组件需要配套使用

**解决**：
- 改为 `TextInputEditText`

**坑 3：长按删除没反应**

**原因**：
- 忘记 `return true`
- 事件被其他监听器消费了

**解决**：
- `setOnLongClickListener` 返回 `true`

#### 🎯 今日成果

**功能演示**
- ✅ 点击"添加日程"按钮弹出对话框
- ✅ 输入标题和描述
- ✅ 确定后显示在列表中（卡片式）
- ✅ 长按日程列表弹出删除选择
- ✅ 选择后删除，Toast 提示

**代码统计**
- 新增文件：1 个（dialog_add_event.xml）
- 修改文件：2 个（MainActivity.kt、activity_main.xml）
- 新增代码：约 80 行
- 累计代码：约 130 行

**界面效果**
- Material Design 风格
- 卡片式日程展示
- 响应式交互

#### 💭 心得体会

**进展顺利**
- ✅ Material 组件很好用，UI 质量高
- ✅ buildString 比字符串拼接优雅多了
- ✅ Kotlin 的扩展函数很方便（isNotEmpty 等）
- ✅ 进度超预期，比计划快 1 小时

**遇到的挑战**
- ⚠️ Material 组件需要额外依赖
- ⚠️ 第一次用 LayoutInflater，需要理解
- ⚠️ Unicode 边框在不同字体下可能不对齐

**经验总结**
1. Material Design 组件能大幅提升 UI 质量
2. 自定义对话框比直接输入灵活得多
3. 长按交互更符合移动端习惯
4. Kotlin 的语法糖让代码更简洁

#### 📝 明日计划

**Day 3 目标**：Room 数据库 - 让数据永久保存

**核心任务**：
- [ ] 添加 Room 依赖（数据库框架）
- [ ] 创建 Event 实体类（@Entity）
- [ ] 创建 EventDao（数据访问接口）
- [ ] 创建 AppDatabase（数据库实例）
- [ ] 实现数据库 CRUD 操作
- [ ] 重启 App 数据还在

**预计难度**：⭐⭐⭐⭐  
**预计用时**：3-4 小时

---

**Day 2 完成！界面美观功能完整！** 🎉

**今日评分**：⭐⭐⭐⭐⭐ (5/5) - 超出预期，比计划快！

---

### Day 03 - Room 数据库集成 ✅

**日期**：2025年11月05日  
**用时**：约2小时（包括模拟器配置时间）  
**完成度**：✅ 100%

#### 📋 今日任务完成情况

- [x] 添加 Room 数据库依赖和 KSP 插件
- [x] 创建 Event 实体类
- [x] 创建 EventDao 数据访问接口
- [x] 创建 AppDatabase 单例类
- [x] 改造 MainActivity 使用数据库存储
- [x] 测试数据持久化成功（真机 + 虚拟机）
- [x] 配置轻量级虚拟机（Pixel 2 API 30）

#### 💻 写了哪些代码

**1. Event 实体类 (Event.kt)**

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
- 默认参数值 - `id = 0`, `description = ""`

---

**2. EventDao 接口 (EventDao.kt)**

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

**代码要点**：
- `@Dao` - Data Access Object（数据访问对象）
- `@Query` - 自定义 SQL 查询
- `@Insert`、`@Update`、`@Delete` - Room 自动实现
- `suspend fun` - 协程函数，支持异步操作

---

**3. AppDatabase 类 (AppDatabase.kt)**

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
- 避免多次创建导致资源浪费
- `@Volatile` 保证多线程可见性
- `synchronized(this)` 保证线程安全

**类似 Django**：
```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

---

**4. MainActivity 改造**

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
            
            // 重新加载数据
            val events = eventDao.getAllEvents()
            withContext(Dispatchers.Main) {
                eventsList.clear()
                eventsList.addAll(events)
                updateEventsList()
                Toast.makeText(this@MainActivity, "✅ 添加成功！", Toast.LENGTH_SHORT).show()
            }
        }
    }
    
    // 删除日程
    private fun deleteEvent(event: Event) {
        lifecycleScope.launch(Dispatchers.IO) {
            eventDao.delete(event)
            loadAllEvents()
        }
    }
}
```

**协程调度器**：
- `Dispatchers.IO` - 用于数据库、网络等 IO 操作
- `Dispatchers.Main` - 用于 UI 更新
- `withContext()` - 切换线程

---

**5. build.gradle.kts 配置**

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

**依赖说明**：
- `room-runtime` - Room 核心库
- `room-ktx` - Kotlin 扩展（协程支持）
- `room-compiler` - 编译时代码生成（用 KSP 处理）
- `kotlinx-coroutines-android` - 协程库
- `lifecycle-runtime-ktx` - 生命周期感知协程

#### 🐛 遇到的坑（详细版）

**坑 1：模拟器配置太高导致电脑卡死** ⚠️

**问题现象**：
```
创建 Medium Phone API 36.1 虚拟机
↓
运行应用
↓
电脑内存占用 94%
↓
鼠标无法移动
↓
强制重启电脑 💥
```

**原因分析**：
- API 36 是 Android 最新版本（2024年10月发布）
- 需要大量内存和 CPU 资源
- 同时运行：Chrome（8个标签）+ Android Studio + 虚拟机
- 电脑总内存：16GB
- 可用内存：< 2GB
- 虚拟机默认配置：2GB RAM（占用后系统崩溃）

**尝试的解决方案**：
1. ❌ 关闭 Chrome → 还是卡
2. ❌ 降低虚拟机画质 → 运行前就卡死
3. ✅ **先用真机测试**（华为手机 API 29）
4. ✅ **创建轻量级虚拟机**（Pixel 2 API 30，1.5GB RAM）

**最终配置**：
- 设备：Pixel 2
- API 级别：30（Android 11）
- RAM：1.5GB
- 存储：2GB
- 分辨率：1080x1920（降低到 720p）

**学到的经验**：
- ✅ 真机调试效率最高、资源占用最少
- ✅ 虚拟机选择旧版本 API（API 28-30 最稳定）
- ✅ 开发时关闭不必要的应用
- ✅ 虚拟机 RAM 配置不要超过系统可用内存的 1/3

---

**坑 2：协程和线程调度** 🔄

**问题现象**：
```kotlin
// ❌ 错误写法
fun addEvent() {
    eventDao.insert(event)  // 报错：Cannot access database on main thread
}
```

**错误信息**：
```
java.lang.IllegalStateException: Cannot access database on the main thread
```

**原因**：
- Android 规定：**主线程不能执行耗时操作**
- 数据库操作属于 IO 操作，可能卡住 UI
- Room 强制要求在后台线程执行

**解决方案**：
```kotlin
// ✅ 正确写法
lifecycleScope.launch(Dispatchers.IO) {  // 后台线程执行数据库操作
    val events = eventDao.getAllEvents()
    
    withContext(Dispatchers.Main) {      // 切换到主线程更新 UI
        eventsList.clear()
        eventsList.addAll(events)
        updateEventsList()  // 更新界面
    }
}
```

**线程调度器对比**：

| 调度器 | 用途 | 类比 Web |
|--------|------|---------|
| `Dispatchers.Main` | UI 操作 | 主线程（DOM 操作） |
| `Dispatchers.IO` | 数据库、网络 | Web Worker |
| `Dispatchers.Default` | CPU 密集计算 | Web Worker |

**学到的知识**：
- `lifecycleScope` - 生命周期感知的协程作用域
- Activity 销毁时自动取消协程，避免内存泄漏
- `withContext()` 切换线程不会创建新协程

---

**坑 3：从 Flow 到 List 的简化** 📊

**最初设计（复杂版）**：
```kotlin
@Query("SELECT * FROM events ORDER BY dateTime ASC")
fun getAllEvents(): Flow<List<Event>>  // 响应式数据流

// 使用
eventDao.getAllEvents().collect { events ->
    updateUI(events)
}
```

**遇到的问题**：
- Flow 需要持续监听，生命周期管理复杂
- 数据变化时自动更新，但对于简单场景过于复杂
- 初学者理解成本高

**简化版本（最终采用）**：
```kotlin
@Query("SELECT * FROM events ORDER BY dateTime ASC")
suspend fun getAllEvents(): List<Event>  // 简单查询

// 使用
val events = eventDao.getAllEvents()  // 直接获取结果
```

**对比**：

| 方案 | Flow | suspend List |
|------|------|--------------|
| **复杂度** | ⭐⭐⭐⭐ | ⭐⭐ |
| **实时性** | 自动更新 | 手动刷新 |
| **适用场景** | 实时聊天、股票 | 日历、笔记 |
| **学习成本** | 高 | 低 |

**学到的经验**：
- 简单场景不要过度设计
- Flow 适合需要实时监听的场景
- 手动刷新对日历应用足够了

#### 💡 今日学到的知识

**1. Room 数据库三件套**

```
┌─────────────────────────────────────┐
│         Room Database               │
│  (抽象数据库类，单例模式)           │
│                                     │
│  @Database(entities = [Event::class])│
│  abstract class AppDatabase         │
└─────────────────────────────────────┘
              ↓
┌─────────────────────────────────────┐
│         DAO (数据访问层)            │
│  (定义 CRUD 操作)                   │
│                                     │
│  @Dao                               │
│  interface EventDao {               │
│    @Query, @Insert, @Delete         │
│  }                                  │
└─────────────────────────────────────┘
              ↓
┌─────────────────────────────────────┐
│         Entity (实体类)             │
│  (对应数据库表)                     │
│                                     │
│  @Entity(tableName = "events")      │
│  data class Event                   │
└─────────────────────────────────────┘
```

**对比 Django**：

| Room | Django | 说明 |
|------|--------|------|
| `@Entity` | `models.Model` | 定义数据模型 |
| `@PrimaryKey` | `AutoField(primary_key=True)` | 主键 |
| `@Dao` | `objects` | 数据访问接口 |
| `@Query("SELECT...")` | `filter()`, `all()` | 查询 |
| `@Insert` | `create()` | 插入 |
| `@Delete` | `delete()` | 删除 |

---

**2. Kotlin 协程（Coroutines）**

**基础概念**：
```kotlin
// 1. 启动协程
lifecycleScope.launch {
    // 异步代码
}

// 2. 挂起函数（suspend）
suspend fun getAllEvents(): List<Event> {
    // 可以在协程中调用
}

// 3. 线程切换
withContext(Dispatchers.IO) {
    // 切换到 IO 线程
}
```

**对比 JavaScript async/await**：

| Kotlin 协程 | JavaScript | 说明 |
|------------|------------|------|
| `launch {}` | `async function()` | 启动异步 |
| `suspend fun` | `async function` | 异步函数 |
| `withContext()` | - | 切换线程（JS单线程） |
| `Dispatchers.IO` | - | IO线程池 |

**对比 Python asyncio**：

| Kotlin | Python | 说明 |
|--------|--------|------|
| `launch {}` | `asyncio.create_task()` | 创建任务 |
| `suspend fun` | `async def` | 异步函数 |
| `Dispatchers.IO` | `asyncio.to_thread()` | 线程池执行 |

---

**3. 数据持久化原理**

**完整流程图**：
```
用户添加日程
    ↓
MainActivity.addEvent()
    ↓
lifecycleScope.launch(Dispatchers.IO) ← 切换到后台线程
    ↓
eventDao.insert(event)
    ↓
Room 框架
    ↓
生成 SQL: INSERT INTO events (title, description, dateTime, createdAt) VALUES (?, ?, ?, ?)
    ↓
执行 SQL 写入 SQLite
    ↓
数据写入文件: /data/data/com.ncu.kotlincalendar/databases/calendar_database
    ↓
返回插入的行 ID
    ↓
重新查询所有数据
    ↓
withContext(Dispatchers.Main) ← 切换回主线程
    ↓
更新 UI 显示
    ↓
完成！✅

────────────────────────────

关闭 App（进程结束，内存清空）
    ↓
SQLite 文件还在手机存储中 ✅
    ↓
重新打开 App
    ↓
AppDatabase.getDatabase()
    ↓
Room 打开 SQLite 文件
    ↓
loadAllEvents()
    ↓
执行 SQL: SELECT * FROM events ORDER BY dateTime ASC
    ↓
从 SQLite 文件读取数据
    ↓
转换为 Event 对象列表
    ↓
显示在界面上
    ↓
数据恢复！🎉
```

**就像**：
- Django 的 `db.sqlite3` 文件
- MySQL 的 `/var/lib/mysql/` 数据文件
- 你保存的 Word 文档（关机后还在）

---

**4. 移动应用架构理解**

**Web 应用（集中式）**：
```
┌──────────┐
│  用户 A  │ ─┐
└──────────┘  │
              │
┌──────────┐  │    ┌──────────────┐    ┌──────────────┐
│  用户 B  │ ─┼───→│  云服务器    │───→│   MySQL 库   │
└──────────┘  │    │  (Django)    │    │  (唯一数据源)│
              │    └──────────────┘    └──────────────┘
┌──────────┐  │
│  用户 C  │ ─┘
└──────────┘

所有用户共享同一个数据库
```

**移动应用（分布式）**：
```
┌──────────────────────┐
│  用户 A 的手机       │
│  ┌────────────────┐  │
│  │   SQLite A     │  │  ← 独立的数据库
│  │  (只有 A 的数据)│  │
│  └────────────────┘  │
└──────────────────────┘

┌──────────────────────┐
│  用户 B 的手机       │
│  ┌────────────────┐  │
│  │   SQLite B     │  │  ← 独立的数据库
│  │  (只有 B 的数据)│  │
│  └────────────────┘  │
└──────────────────────┘

每个用户的数据完全独立
```

**如果需要多设备同步**：
```
┌────────────┐
│ 手机 SQLite│ ←┐
└────────────┘  │
                ↓
            ┌────────┐
            │  API   │ ←→ ┌──────────────┐
            │ 服务器 │    │ 云端 MySQL   │
            └────────┘    └──────────────┘
                ↑
┌────────────┐  │
│ 平板 SQLite│ ←┘
└────────────┘

本地存储 + 云端同步（双向）
```

**这就是**：
- 微信的聊天记录存储方式
- 有道云笔记的离线功能
- Keep Notes 的本地优先策略

#### 🎯 今日成果

**功能完成**
- ✅ 数据库增删查改（CRUD）完整实现
- ✅ 数据持久化存储（重启不丢失）
- ✅ 协程异步处理（不卡 UI）
- ✅ 真机测试通过（华为 API 29）
- ✅ 虚拟机测试通过（Pixel 2 API 30）

**代码统计**
- 新增文件：3 个
  - Event.kt（实体类）
  - EventDao.kt（数据访问接口）
  - AppDatabase.kt（数据库单例）
- 修改文件：2 个
  - MainActivity.kt（业务逻辑改造）
  - build.gradle.kts（依赖配置）
- 新增代码：约 100 行
- 累计代码：约 230 行

**测试结果**
- ✅ 真机测试：华为手机 API 29 - 成功
- ✅ 虚拟机测试：Pixel 2 API 30 - 成功
- ✅ 数据持久化：重启应用数据保留 - 成功
- ✅ 多条日程：添加 10+ 条日程无卡顿 - 成功

**技术突破**
- 🎯 掌握 Room 数据库框架
- 🎯 理解协程和线程调度
- 🎯 实现真正可用的数据持久化
- 🎯 学会真机和虚拟机调试

#### 💭 心得体会

**进展顺利**
- ✅ Room 比原生 SQLite 简单太多了
- ✅ 协程让异步操作变得优雅
- ✅ 真机调试体验比虚拟机好
- ✅ 数据库配置一次就能用，很稳定

**遇到的挑战**
- ⚠️ 虚拟机资源占用导致电脑卡死（已解决）
- ⚠️ 第一次理解协程和线程切换（已掌握）
- ⚠️ Flow vs List 选择纠结（最终简化）

**经验总结**
1. **硬件资源很重要** - 虚拟机配置要根据电脑性能调整
2. **真机调试最高效** - 响应快、资源占用少
3. **不要过度设计** - Flow 虽强大但简单场景用 List 就够了
4. **协程是必修课** - Android 开发必须掌握的技能

**对比之前项目**
- **类似 Final_KOB 的 MySQL**：都是持久化存储
- **类似 Django 的 ORM**：Room = Android 版的 Django ORM
- **类似 JavaScript 的 async/await**：协程 = Kotlin 版的异步

#### 📝 明日计划

**Day 4 目标**：优化列表显示（RecyclerView）

**核心任务**：
- [ ] 用 RecyclerView 替代 TextView
- [ ] 创建 EventAdapter 适配器
- [ ] 设计列表项布局（item_event.xml）
- [ ] 实现列表项点击查看详情
- [ ] 优化列表性能和样式
- [ ] 添加滑动删除功能（ItemTouchHelper）

**预计难度**：⭐⭐⭐  
**预计用时**：2-3 小时

**技术要点**：
- RecyclerView（类似 Web 的虚拟滚动）
- ViewHolder 模式（性能优化）
- Adapter 适配器模式
- ItemTouchHelper（滑动删除）

---

**Day 3 完成！数据库搞定，应用已经能真正使用了！** 🎉

**今日评分**：⭐⭐⭐⭐⭐ (5/5) - 虽然遇到硬件问题，但最终完美解决！核心功能已实现！

---

### Day 04 - 列表显示优化 ⏳

**日期**：2025年11月07日（预计）  
**完成度**：⏳ 待开始

---

### Day 05 - 编辑功能 + 时间选择器 ⏳

**日期**：2025年11月06日（预计）  
**预计用时**：2-3 小时  
**完成度**：⏳ 待开始

#### 计划任务
- [ ] 点击详情时能编辑日程
- [ ] 实现编辑对话框（复用添加对话框）
- [ ] 添加日期选择器（DatePickerDialog）
- [ ] 添加时间选择器（TimePickerDialog）
- [ ] 日程支持具体时间（时分秒）
- [ ] 更新 Event 实体类

---

### Day 06 - 数据筛选与搜索 ⏳

**日期**：2025年11月07日（预计）  
**完成度**：⏳ 待开始

---

### Day 07 - 多视图切换 ⏳

**日期**：2025年11月10日（预计）  
**完成度**：⏳ 待开始

---

### Day 08 - 提醒功能 ⏳

**日期**：2025年11月11日（预计）  
**完成度**：⏳ 待开始

---

### Day 09 - 扩展功能 + 优化 ⏳

**日期**：2025年11月12日（预计）  
**完成度**：⏳ 待开始

---

### Day 10 - 文档和提交 ⏳

**日期**：2025年11月13-14日（预计）  
**完成度**：⏳ 待开始

