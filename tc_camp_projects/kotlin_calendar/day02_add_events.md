# Day 02 - 添加和显示日程

**日期**：2025年11月04日  
**用时**：约2小时  
**完成度**：✅ 100%

---

## 📋 今日任务完成情况

- [x] 实现添加日程对话框（Material Design 风格）
- [x] 支持输入标题 + 描述
- [x] 日程列表卡片式显示
- [x] 实现长按删除功能
- [x] 优化界面样式和用户体验

---

## 💻 写了哪些代码

### 1. 自定义对话框布局 (dialog_add_event.xml)

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

---

### 2. 添加日程功能 (MainActivity.kt)

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

---

### 3. 长按删除功能

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

---

## 🎨 界面优化亮点

### 1. Material Design 输入框
- 更专业的 UI
- 外边框样式 (`boxBackgroundMode="outline"`)
- 浮动提示标签

### 2. 卡片式布局
- 用 Unicode 字符绘制边框
- 清晰的视觉层次
- Emoji 图标：📅📝💬

### 3. 长按删除
- 更符合移动端操作习惯
- 弹出选择对话框
- 删除后 Toast 提示

### 4. 等宽字体
- 让框线对齐美观
- 使用 `android:fontFamily="monospace"`

### 5. 浅灰背景 + 白色卡片
- 层次分明
- 符合 Material Design 规范

---

## 💡 今日学到的知识

### Kotlin 实用技巧

**1. buildString {}**
```kotlin
val str = buildString {
    append("第一行\n")
    append("第二行")
}
// 类似 JavaScript 的数组 join()
```

**2. 字符串判断**
```kotlin
str.isNotEmpty()  // 非空判断
str.isEmpty()     // 空判断
str.trim()        // 去除首尾空格
```

**3. mapIndexed**
```kotlin
eventsList.mapIndexed { index, event ->
    "日程 ${index + 1}"
}
// 带索引的 map，类似 JS 的 map((item, index) => ...)
```

**4. 默认参数**
```kotlin
fun addEvent(title: String, description: String = "") {
    // description 有默认值，调用时可省略
}
```

---

### Android UI 组件

**1. TextInputLayout**
- Material Design 输入框
- 支持浮动标签
- 支持错误提示

**2. AlertDialog**
- 标准对话框
- `.setTitle()` 设置标题
- `.setView()` 自定义布局
- `.setPositiveButton()` 确定按钮
- `.setNegativeButton()` 取消按钮
- `.setItems()` 列表选择

**3. LayoutInflater**
- 将 XML 布局转为 View 对象
- `layoutInflater.inflate(R.layout.xxx, null)`

**4. 长按事件**
```kotlin
view.setOnLongClickListener {
    // 处理长按
    true  // 返回 true 表示消费事件
}
```

---

## 🐛 遇到的小坑

### 坑 1：Material 组件找不到

**问题**：
```
Unresolved reference: TextInputLayout
```

**解决**：
- 在 `build.gradle.kts` 添加 Material 依赖
- `implementation("com.google.android.material:material:1.10.0")`
- Sync Gradle

---

### 坑 2：输入框获取不到值

**原因**：
- 用了 `EditText` 而不是 `TextInputEditText`
- Material 组件需要配套使用

**解决**：
- 改为 `TextInputEditText`

---

### 坑 3：长按删除没反应

**原因**：
- 忘记 `return true`
- 事件被其他监听器消费了

**解决**：
- `setOnLongClickListener` 返回 `true`

---

## 🎯 今日成果

### 功能演示
- ✅ 点击"添加日程"按钮弹出对话框
- ✅ 输入标题和描述
- ✅ 确定后显示在列表中（卡片式）
- ✅ 长按日程列表弹出删除选择
- ✅ 选择后删除，Toast 提示

### 代码统计
- **新增文件**：1 个（dialog_add_event.xml）
- **修改文件**：2 个（MainActivity.kt、activity_main.xml）
- **新增代码**：约 80 行
- **累计代码**：约 130 行

### 界面效果
- Material Design 风格
- 卡片式日程展示
- 响应式交互

---

## 💭 心得体会

### 进展顺利
- ✅ Material 组件很好用，UI 质量高
- ✅ buildString 比字符串拼接优雅多了
- ✅ Kotlin 的扩展函数很方便（isNotEmpty 等）
- ✅ 进度超预期，比计划快 1 小时

### 遇到的挑战
- ⚠️ Material 组件需要额外依赖
- ⚠️ 第一次用 LayoutInflater，需要理解
- ⚠️ Unicode 边框在不同字体下可能不对齐

### 经验总结
1. Material Design 组件能大幅提升 UI 质量
2. 自定义对话框比直接输入灵活得多
3. 长按交互更符合移动端习惯
4. Kotlin 的语法糖让代码更简洁

---

## 📝 明日计划

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

[⬅️ 上一天：Day 1](./day01_calendar_view.md) | [⬅️ 返回首页](./README.md) | [➡️ 下一天：Day 3](./day03_room_database.md)

