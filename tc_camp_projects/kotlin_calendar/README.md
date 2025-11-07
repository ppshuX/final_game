# 📅 Kotlin Calendar 日历应用开发日志

> **项目名称**：Android 日历应用（Kotlin Calendar Homework）  
> **开发平台**：Android Studio + Kotlin  
> **项目周期**：2025年11月4日 - 2025年11月14日（10天）  
> **所属计划**：Show Way Plan 第二阶段 - 腾讯青英班大作业  
> **当前状态**：🎉 核心功能已完成！

---

## 📊 项目整体进度

```
██████████████████████████████████████████████████ 100%

Day 1: 基础日历界面 ✅
Day 2: 添加和显示日程 ✅
Day 3: Room 数据库集成 ✅
Day 4: RecyclerView 列表优化 ✅
Day 5: 编辑功能 ✅
Day 6: 时间选择器 ✅
Day 7: 跳过（可选功能）⏭️
Day 8: 提醒功能 ✅
Day 9: Django 后端 + Vue3 Web 端 ✅
Day 10: AcWing 平台集成 ✅
Day 11: 用户认证 + UI优化 + 功能规划 ✅
Day 12: AcWing OAuth2 一键登录 ✅
Day 13: Web 端 AcWing OAuth2 登录 ✅
Day 14: QQ OAuth2 登录 + 代码清理 ✅
```

**项目启动**：2025年11月4日  
**最近更新**：2025年11月7日  
**开发状态**：🎉 持续优化中！  
**当前阶段**：Day 14 已完成 - QQ OAuth2 登录 + 模型模块化重构 + 代码清理完成！

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
| **Day 9** | Django 后端 + Vue3 Web 端 | ✅ 完成 | 8h | 全栈架构 + 扩展功能完成 | [📖 查看](./day09_fullstack_integration.md) |
| **Day 10** | AcWing 平台集成 | ✅ 完成 | 8h | Vue3+VueCLI + Vuex 路由 + 三端上线 | [📖 查看](./day10_acwing_platform_integration.md) |
| **Day 11** | 用户认证 + UI优化 + 功能规划 | ✅ 完成 | 8h | JWT认证 + 三端优化 + 3500+行规划文档 | [📖 查看](./day11_user_authentication_and_optimization.md) |
| **Day 12** | AcWing OAuth2 一键登录 | ✅ 完成 | 3h | OAuth2授权 + Token验证 + Vuex模块化 | [📖 查看](./day12_acwing_oauth2_login.md) |
| **Day 13** | Web 端 AcWing OAuth2 登录 | ✅ 完成 | 6h | Web OAuth2 + 环境变量 + 静态文件 | [📖 查看](./day13_web_acwing_oauth2_login.md) |
| **Day 14** | QQ OAuth2 + 用户中心 + JWT修复 | ✅ 完成 | 8h | QQ登录 + 个人中心 + JWT核心问题修复 | [📖 查看](./day14_qq_oauth2_login_and_code_cleanup.md) |

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

### 扩展功能（额外）✅

1. ✅ **数据导出/备份**
   - Django REST API（JSON 格式）
   - 云端数据同步
   - 批量导入导出

2. ✅ **网络日历订阅**
   - PublicCalendar 模型
   - iCalendar (.ics) 订阅服务
   - 第三方日历集成（Google Calendar 等）

3. ✅ **农历显示**
   - LunarCalendar Python 库
   - 农历转换 API (`/api/lunar/`)
   - 实时阳历转农历

4. ✅ **全栈架构**
   - Django 后端（REST API + JWT认证）
   - Vue3 Web 管理端（FullCalendar + 登录注册）
   - Vue3 AcWing 端（Vuex路由 + 紧凑UI）
   - Android 客户端（Kotlin + Room）
   - 三端数据互通

5. ✅ **用户认证系统**
   - JWT Token 认证
   - 自动刷新机制
   - 登录注册页面
   - 权限管理

6. ✅ **产品化规划**
   - 日历共享规划（1214行）
   - 地图集成规划（1172行）
   - AI助手规划
   - 商业模式设计

7. ✅ **多端 OAuth2 登录**
   - AcWing 登录（Web + AcApp）
   - QQ 登录（Web）
   - 用户头像显示
   - 统一的 JWT 认证

8. ✅ **用户个人中心**
   - 用户信息展示
   - 统计信息（总日程/今日/即将到来）
   - 第三方账号绑定管理
   - 个人信息编辑
   - 修改密码功能
   - 智能账号保护（至少保留一种登录方式）

**🎉 扩展功能 600% 完成（远超预期）！**

---

## 📈 项目统计

### 累计统计（截至 Day 14）

- **完成天数**：14 天（Day 7 跳过）
- **累计用时**：49 小时
- **总文件数**：64+ 个（Android 13 + Backend 21 + Web 22 + AcWing 13）
- **累计代码行数**：约 10000+ 行（代码清理后更精简）
- **功能完成**：14/11（127%）✅
- **作业要求**：✅ 100% 完成（3个基本要求全部实现）
- **扩展功能**：✅ 600% 完成（三客户端 + 四种登录方式 + 用户中心 + 功能规划）
- **遇到的坑**：36+ 个（全部解决，含 JWT 核心问题）
- **数据库规模**：4 张表（events, public_calendars, acwing_users, qq_users）
- **技术栈**：Kotlin + Django + Vue3 (Vite) + Vue3 (VueCLI) + JWT + OAuth2 (AcWing+QQ) - 全栈
- **规划文档**：5 个（3500+ 行）

### 三客户端文件清单（Day 10 更新）

**Android 端 - adapp/（Day 1-8）**：
- MainActivity.kt（主程序）
- Event.kt（实体类 v2，新增提醒字段）
- EventDao.kt（数据访问接口）
- AppDatabase.kt（数据库单例 v2，含迁移）
- EventAdapter.kt（RecyclerView 适配器）
- AlarmReceiver.kt（广播接收器）
- ReminderManager.kt（提醒管理器）
- 3个布局文件 + 资源文件
- README.md（说明文档，源码不上传）

**Django 后端 - backend/（Day 9）**：
- models.py（Event + PublicCalendar 模型）
- serializers.py（DRF 序列化器）
- views.py（API 视图集）
- urls.py（API 路由）
- settings.py（Django + CORS 配置）
- requirements.txt（Python 依赖）

**Vue3 Web 端 - web/（Day 9）**：
- CalendarView.vue（日历主视图）
- NavBar.vue（导航栏）
- EventDialog.vue（添加/编辑弹窗）
- EventDetail.vue（详情弹窗）
- api/index.js（Axios 封装）
- router/index.js（Vue Router 配置）
- package.json（npm 依赖）

**Vue3 AcWing 端 - acapp/（Day 10-11）**：
- static/js/app.js（构建产物 120KB）
- static/css/app.css（构建产物 9.5KB）
- acapp_frontend/（源码，本地开发）
  - MainView.vue（视图容器）
  - CalendarGrid.vue（日历容器 - 紧凑化）
  - CalendarHeader.vue（月份导航）
  - CalendarGridView.vue（日历网格 - 48px格子）
  - ToolBar.vue（工具栏）
  - EventList.vue（事件列表）
  - EventDetail.vue（事件详情）
  - AddEventForm.vue（添加表单 - 优化布局）
  - store/index.js（Vuex 状态管理）
  - Calendar.js（ES Module 导出）
  - vue.config.js（Library 模式配置）

**用户认证模块 - backend + web（Day 11-14）**：
- backend/api/models/（模块化重构）
  - user.py（AcWingUser + QQUser）
  - event.py（Event）
  - calendar.py（PublicCalendar）
- backend/api/views/auth.py（register, login, me, acwing_login, qq_login）
- backend/api/views/oauth_callback.py（OAuth2 回调处理）
- backend/settings.py（JWT + OAuth2 配置）
- backend/.env（环境变量：AcWing + QQ 凭证）
- web/views/account/LoginView.vue（登录注册 + AcWing + QQ）
- web/views/account/AcWingCallback.vue（AcWing 回调）
- web/views/account/QQCallback.vue（QQ 回调）
- web/api/index.js（Token 自动刷新拦截器）
- acapp/main.js（OAuth2 授权流程 + Token 验证）
- acapp/store/（Vuex 模块化：user, events, router）

**规划文档 - docs/（Day 11 新增）**：
- CALENDAR_SHARING_PLAN.md（1214行 - 日历共享）
- MAP_INTEGRATION_PLAN.md（1172行 - 地图集成）
- AI_ASSISTANT_PLAN.md（400+行 - AI助手）
- FINAL_ARCHITECTURE.md（350+行 - 最终架构）
- FEATURES_SUMMARY.md（400+行 - 功能总结）

---

## 🔧 技术栈

### 全栈技术栈（Day 9 更新）

**移动端（Android）**：
- **IDE**：Android Studio
- **语言**：Kotlin
- **Android SDK**：API Level 29-34
- **核心库**：Room, Coroutines, Material Components
- **系统组件**：CalendarView, RecyclerView, AlarmManager

**后端（Django）**：
- **框架**：Django 5.0 + Django REST Framework
- **语言**：Python 3.10+
- **数据库**：SQLite (开发) / PostgreSQL (生产)
- **核心库**：django-cors-headers, LunarCalendar
- **API 设计**：RESTful + JSON

**Web 前端（Vue3 + Vite）**：
- **框架**：Vue 3 + Vite
- **UI 库**：Element Plus + Bootstrap 5
- **日历库**：FullCalendar 6
- **HTTP 客户端**：Axios
- **路由**：Vue Router 4

**AcWing 前端（Vue3 + Vue CLI）**：
- **框架**：Vue 3 + Vue CLI
- **构建模式**：Library Mode (ES Module)
- **状态管理**：Vuex 4
- **路由方案**：Vuex 模拟路由（创新设计）
- **样式方案**：Scoped CSS
- **输出**：单文件 app.js (120KB) + app.css (9.5KB)

**通信协议**：
- **协议**：HTTP/REST
- **数据格式**：JSON
- **跨域**：CORS 配置

---

## 🎓 学习资源

- [Kotlin 学习笔记](./learning_notes.md) - Kotlin 语法、Android 基础
- [项目概览](./overview.md) - 技术架构、开发计划
- [常见问题](./faq.md) - 遇到的坑和解决方案

---

## 💡 技术亮点

**Android 端**：
- ✨ **Material Design 风格** - 专业的 UI 设计
- ✨ **协程异步处理** - 流畅的用户体验
- ✨ **ViewHolder 复用** - 高性能列表滚动
- ✨ **Room 数据库迁移** - 无损版本升级
- ✨ **AlarmManager 定时任务** - 精确的提醒功能
- ✨ **Notification 系统通知** - 原生通知体验
- ✨ **权限动态请求** - 规范的权限管理

**全栈架构（Day 9-11）**：
- ✨ **三客户端架构** - Android + Web + AcWing
- ✨ **JWT 认证系统** - Token 自动刷新机制
- ✨ **RESTful API 设计** - 标准化接口
- ✨ **FullCalendar 集成** - 专业日历组件（Web 端）
- ✨ **Vuex 模拟路由** - 轻量级路由系统（AcWing 端）
- ✨ **ES Module 构建** - 现代化模块系统
- ✨ **农历转换功能** - 中国特色功能
- ✨ **iCalendar 订阅** - 第三方日历集成
- ✨ **响应式 Web 设计** - 移动端适配
- ✨ **CORS 跨域支持** - 前后端分离
- ✨ **组件化设计** - View + Components 分层
- ✨ **三端数据同步** - 实时互通
- ✨ **紧凑化 UI** - 适配 630x521 小容器
- ✨ **产品化规划** - 3500+ 行详细规划文档

---

## 📝 开发心得

从 Web 全栈到移动端开发，再到完整的三端架构：
- ✅ JavaScript → Kotlin
- ✅ Vue3 → Android UI
- ✅ Spring Boot → Django REST Framework
- ✅ MySQL → Room (SQLite) + Django ORM

**技术成长轨迹**：
- **Day 1-8**: Android 移动端开发（Kotlin + Room + AlarmManager）
- **Day 9**: Django 后端 + Vue3 Web 端（Vite + FullCalendar）
- **Day 10**: AcWing 平台集成（Vue CLI + Vuex + ES Module）
- **Day 11**: 用户认证 + UI优化 + 产品规划（JWT + 紧凑UI + 3500行文档）

**收获**：
1. ✅ 掌握 Android 原生开发全流程
2. ✅ 理解前后端分离架构设计
3. ✅ 实现三客户端数据互通
4. ✅ 学会 RESTful API 设计
5. ✅ 掌握 Vue CLI Library 模式
6. ✅ 创新 Vuex 路由系统设计
7. ✅ ES Module 构建配置
8. ✅ CORS 跨域配置
9. ✅ Nginx 反向代理部署
10. ✅ Git 仓库优化策略
11. ✅ JWT 认证与 Token 自动刷新
12. ✅ UI 紧凑化设计（适配小容器）
13. ✅ 数据模型重构（start_time/end_time）
14. ✅ 产品化思维（商业模式 + 市场分析）
15. ✅ 详细规划文档撰写能力

**11 天完成三客户端架构 + 用户认证 + 产品规划，从作业到产品的完美升级！** 🎉🚀🔥

---

## 🚀 当前可用服务

### 三客户端运行状态

| 客户端 | 技术栈 | 状态 | 访问方式 |
|--------|--------|------|---------|
| **Android App** | Kotlin + Room + Material | ✅ 完成 | 本地 APK |
| **Web App** | Vue3 + Vite + FullCalendar | ✅ 运行中 | https://app7626.acapp.acwing.com.cn/ |
| **AcWing App** | Vue3 + VueCLI + Vuex | ✅ 运行中 | AcWing 平台打开 |
| **Django Backend** | Django 5.0 + DRF | ✅ 运行中 | https://app7626.acapp.acwing.com.cn/api/ |

**功能特性**：
- ✅ Event CRUD API（增删改查）
- ✅ JWT 用户认证（注册/登录/Token刷新）
- ✅ 农历转换 API（阳历 → 农历）
- ✅ 网络日历订阅（iCalendar）
- ✅ 三端实时数据同步
- ✅ CORS 跨域支持
- ✅ FullCalendar 日历组件（Web）
- ✅ Vuex 路由系统（AcWing）
- ✅ 响应式布局（Web + AcWing）
- ✅ Token 自动刷新（无感刷新）
- ✅ 紧凑化 UI（适配 630x521 容器）
- ✅ 时间段支持（start_time/end_time）

**三客户端架构已完整上线 + 用户认证系统完成！** 🎉🚀

**未来规划**：
- 📋 日历共享（选择性同步创新功能）
- 📋 地图集成（智能出发提醒）
- 📋 AI 助手（语音创建事件）

---

> "Keep building, keep recording, keep believing." 💪  
> "From web to mobile to full-stack — 技术的边界在不断扩展！" 🚀  
> "一天完成两个端，全栈工程师的修炼之路！" 🔥

