# Day 05 - 编辑功能

**日期**：2025年11月05日  
**用时**：约30分钟  
**完成度**：✅ 100%

---

## 📋 今日任务完成情况

- [x] 实现编辑功能（点击详情 → 编辑）
- [x] 添加/编辑对话框支持编辑模式
- [x] 更新数据库记录
- [x] 优化详情对话框（编辑/删除/关闭三个按钮）

---

## 💻 写了哪些代码

### 编辑功能实现

```kotlin
// 1. 对话框支持编辑模式
private fun showAddEventDialog(eventToEdit: Event? = null) {
    val dialogView = layoutInflater.inflate(R.layout.dialog_add_event, null)
    
    // 如果是编辑模式，填充现有数据
    if (eventToEdit != null) {
        etTitle?.setText(eventToEdit.title)
        etDesc?.setText(eventToEdit.description)
        calendar.timeInMillis = eventToEdit.dateTime
    }
    
    // 保存时判断是新增还是编辑
    if (eventToEdit != null) {
        updateEvent(eventToEdit.id, title, desc, dateTime)
    } else {
        addEvent(title, desc, dateTime)
    }
}

// 2. 更新日程到数据库
private fun updateEvent(id: Long, title: String, description: String, dateTime: Long) {
    lifecycleScope.launch(Dispatchers.IO) {
        val event = Event(id = id, title = title, description = description, dateTime = dateTime)
        eventDao.update(event)
        loadAllEvents()
    }
}

// 3. 详情对话框新增"编辑"按钮
AlertDialog.Builder(this)
    .setTitle("📋 日程详情")
    .setMessage(...)
    .setPositiveButton("编辑") { _, _ ->
        showAddEventDialog(event)  // 传入 event 进入编辑模式
    }
    .setNegativeButton("删除") { _, _ -> deleteEvent(event) }
    .setNeutralButton("关闭", null)
    .show()
```

---

## 🎯 功能流程

```
用户点击卡片
    ↓
显示详情对话框
    ↓
点击"编辑"按钮
    ↓
打开对话框（编辑模式）
    ↓
预填充原数据
    ↓
修改内容
    ↓
点击"保存"
    ↓
数据库 UPDATE
    ↓
重新加载列表
    ↓
界面刷新！
```

---

## 💡 技术要点

### 1. 可选参数实现编辑模式

```kotlin
fun showAddEventDialog(eventToEdit: Event? = null) {
    // eventToEdit 为 null → 新增模式
    // eventToEdit 不为 null → 编辑模式
}
```

### 2. AlertDialog 三个按钮

```kotlin
.setPositiveButton("编辑") { ... }    // 右侧
.setNegativeButton("删除") { ... }    // 左侧
.setNeutralButton("关闭", null)      // 中间
```

---

## 📊 今日成果

### 功能完成
- ✅ 编辑功能完整实现
- ✅ 对话框支持编辑模式
- ✅ 数据库更新操作
- ✅ UI/UX 优化

### 代码统计
- **修改文件**：1 个（MainActivity.kt）
- **新增代码**：约 30 行

---

**Day 5 完成！编辑功能完美运行！** ✅

**今日评分**：⭐⭐⭐⭐⭐ (5/5) - 功能实现顺利！

---

[⬅️ 上一天：Day 4](./day04_recyclerview.md) | [⬅️ 返回首页](./README.md) | [➡️ 下一天：Day 6](./day06_time_picker.md)

