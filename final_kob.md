# Final_KOB 企业级对战后端平台 (Final_KOB)

## 项目简介

**Final_KOB（King of Backend）** 是 **Final 系列项目**的第四个作品，也是**最终章**。本项目基于 **Spring Boot + MyBatis + Redis + JWT** 构建，旨在实现一个可扩展、高并发、可部署的实时对战后端平台。

它复刻并升级自 **Final_AcApp（Django 版）**，采用企业级后端架构实践，涵盖用户系统、匹配系统、Bot 执行系统、排行榜、AcApp 登录等模块。

本项目是 **Final 系列项目**的终极实现，也是**后端架构能力的集大成之作**，展示了从 Django 到 Spring Boot 的技术栈迁移和企业级架构设计能力。

## 项目特性

- 🧩 **分层架构**：Controller / Service / Mapper 解耦设计
- 🔐 **JWT 认证系统**：无状态 Token 认证机制
- 🎮 **匹配系统**：多线程匹配服务，自动分配玩家对战
- 🤖 **Bot 系统**：支持自动执行对战逻辑与策略
- 📊 **排行榜系统**：记录玩家战绩与积分排名
- 🚀 **高性能**：基于 Redis 缓存与异步线程池优化
- 🔄 **微服务友好**：RESTful API 设计，易于扩展

## 技术栈

### 后端技术
- **框架**：Spring Boot 3.x
- **ORM**：MyBatis + MyBatis-Plus
- **数据库**：MySQL 8.0+
- **缓存**：Redis 6+
- **认证**：JWT (JSON Web Token)
- **多线程**：ExecutorService / BlockingQueue
- **构建工具**：Maven
- **Java 版本**：Java 17+

### 前端 / 客户端
- **Web**：HTML + Bootstrap 5 + JavaScript
- **AcApp**：支持 AcWing OAuth2.0 一键登录
- **接口通信**：RESTful API + Axios

## 核心功能模块

### 1. 用户系统 📋
- [x] 用户注册 / 登录
- [x] JWT Token 认证
- [x] 用户信息查询与更新
- [x] AcApp 授权登录
- [x] 个人中心页面
- [x] 头像上传

### 2. 匹配系统 🎯
- [x] WebSocket 实时通信
- [x] 地图同步机制
- [x] 匹配系统基础架构
- [x] 玩家移动同步
- [x] 游戏状态同步
- [x] 对战记录存储（MySQL）
- [x] 游戏结果展示（ResultBoard）
- [x] 多线程匹配逻辑
- [x] 匹配队列管理
- [x] 匹配超时与取消机制
- [x] 对战房间创建
- [x] 天梯积分系统
- [x] 独立匹配微服务模块

### 3. Bot 执行系统 🤖
- [x] Bot 后端 CRUD API 实现
- [x] Bot 实体类与数据库映射
- [x] Bot 控制器 API 接口
- [x] Bot 服务层实现
- [x] Bot 数据访问层
- [x] Bot 前端 CRUD 实现
- [x] Bot 管理界面
- [x] Bot 代码安全沙箱执行
- [x] 策略脚本运行与回传
- [x] 自动战斗逻辑
- [x] 独立 Bot 执行微服务模块

### 4. 战绩与排行榜 🏆
- [x] 对战记录保存（Record 实体类）
- [x] 数据库存储（MySQL）
- [x] 积分与胜率统计
- [x] 排行榜展示
- [x] 历史对局回放
- [x] 录像回放页面（RecordContentView）
- [x] 排行榜页面（RanklistIndexView）
- [x] MyBatis-Plus 分页查询

### 5. 项目部署 🚀
- [ ] 后端打包上线
- [ ] AcApp 端 + Web 端 登录对接
- [ ] 性能调优与安全配置
- [ ] Docker 容器化部署

## 开发计划（7天冲刺）

### Day 1：项目搭建 + 首页与菜单页 ✅
- [x] Spring Boot 项目初始化
- [x] MySQL 数据库设计与创建
- [x] Redis 配置
- [x] Git 仓库配置
- [x] 项目基础架构搭建
- [x] **Lesson 2: 创建项目结构 backend,web,acapp**（2025/10/21 10:55）

### Day 2：用户注册 / 登录模块 ✅
- [x] 用户实体类设计
- [x] MySQL 用户表创建
- [x] 用户注册 API
- [x] 用户登录 API
- [x] 密码加密（BCrypt）
- [x] 前端登录注册界面
- [x] **Lesson 3.1: 创建前端菜单和地图**（2025/10/22 19:00）

### Day 3：用户中心 + JWT 认证 ✅
- [x] MySQL 数据库配置与连接
- [x] Spring Security 安全配置
- [x] 用户实体类与数据库映射
- [x] 用户认证服务实现
- [x] 用户控制器 API 接口
- [x] **Lesson 4.1: 配置Mysql与注册登录模块 (上)**（2025/10/23 19:50）

### Day 4：JWT 认证系统 + 账户系统 ✅
- [x] JWT Token 生成与验证
- [x] JWT 过滤器实现
- [x] 用户注册登录 API 接口
- [x] 前后端登录注册系统
- [x] 用户状态管理
- [x] **Lesson 4.2: 配置Mysql与注册登录模块 (中)**（2025/10/24 17:00）
- [x] **Lesson 4.3: 配置Mysql与注册登录模块 (下)**（2025/10/24 17:00）

### Day 5：个人中心 + 用户信息管理 ✅
- [x] Bot 后端 CRUD API 实现
- [x] Bot 实体类与数据库映射
- [x] Bot 控制器 API 接口
- [x] Bot 服务层实现
- [x] Bot 数据访问层
- [x] Bot 前端 CRUD 实现
- [x] 个人中心页面
- [x] 用户信息查询 API
- [x] Token 自动刷新机制
- [x] 用户头像上传

### Day 6：匹配系统（上 / 中）✅
- [x] WebSocket 配置与服务器实现
- [x] 匹配系统基础架构设计
- [x] 地图同步机制实现
- [x] JWT 认证集成
- [x] 匹配界面开发
- [x] 玩家移动同步实现
- [x] 游戏状态同步机制
- [x] 对战记录数据库存储
- [x] 游戏结果展示界面
- [x] 匹配队列实现（BlockingQueue）
- [x] 多线程匹配服务
- [x] 匹配超时处理
- [x] 匹配 API 接口

### Day 7：匹配系统（下）+ Bot 执行逻辑 ✅
- [x] 独立匹配微服务模块创建
- [x] 对战房间管理
- [x] 玩家状态同步
- [x] Bot 执行框架搭建
- [x] Bot 代码沙箱实现
- [x] Bot 策略脚本运行
- [x] 独立 Bot 执行微服务模块创建

### Day 8：排行榜 + 接口测试 + 部署准备 ✅
- [x] 对战记录保存（已完成）
- [x] 积分计算逻辑
- [x] 排行榜 API
- [x] 排行榜页面
- [x] 历史对局回放功能
- [x] 对战记录列表 API
- [x] 录像回放页面
- [x] MyBatis-Plus 配置与集成
- [ ] 完整接口测试
- [ ] 性能优化

### Day 9：AcApp 端接入 + OAuth 登录 + 上线总结
- [ ] AcWing OAuth2.0 集成
- [ ] AcApp 端登录对接
- [ ] 项目打包部署
- [ ] 文档完善
- [ ] 项目总结

## 开发进度

```
█████████████████████████████████░░░░░░░░░ 85%

✅ Day 1: 项目搭建 + 首页与菜单页 (已完成)
✅ Day 2: 用户注册 / 登录模块 (已完成)
✅ Day 3: MySQL 配置 + 用户认证系统 (已完成)
✅ Day 4: JWT 认证系统 + 账户系统 (已完成)
✅ Day 5: 个人中心 + Bot 管理 (已完成)
✅ Day 6: 匹配系统（上 / 中） (已完成 - Lesson 6.1, 6.2, 6.3)
✅ Day 7: 匹配系统（下）+ Bot 执行 (已完成 - Lesson 7)
✅ Day 8: 排行榜 + 录像回放 (已完成 - Lesson 8)
⏳ Day 9: AcApp + OAuth + 上线 (待开始)
```

## 项目状态

- **当前版本**：v0.9.5（85% 完成）
- **项目启动**：2025年10月20日
- **预计完成**：2025年11月2日
- **开发状态**：🚀 进行中
- **当前阶段**：Day 8 - 排行榜与录像回放完成（Lesson 8 已完成）
- **目标状态**：腾讯面试可展示级后端项目 ✅

## 每日进度更新

### 2025年10月22日（周二）- Day 2 已完成 ✅
- 📌 **当前状态**：Day 2开发阶段完成，前端菜单、地图和贪吃蛇游戏实现
- 🎯 **今日目标**：完成用户注册/登录模块 + 前端菜单和地图创建 + 贪吃蛇游戏实现
- ✅ **已完成**：
  - ✅ **Lesson 3.1: 创建前端菜单和地图**（2025/10/22 19:00）
  - ✅ **Lesson 3.2: 创建贪吃蛇游戏前端**（2025/10/22 22:05）
- 💭 **技术成果**：
  - ✅ **用户注册/登录系统**
  - ✅ **前端菜单系统**
  - ✅ **游戏地图组件**
  - ✅ **Vue3组件化开发**
  - ✅ **游戏对象系统**
  - ✅ **贪吃蛇游戏逻辑**
  - ✅ **游戏渲染系统**
  - ✅ **路由系统完善**
- 🎉 **重大里程碑**：
  - **用户系统 100% 完成** 👤
  - **前端菜单系统 100% 完成** 🎮
  - **游戏地图组件 100% 完成** 🗺️
  - **贪吃蛇游戏 100% 完成** 🐍
  - **项目整体进度达到 30%**
- 💡 **技术说明**：
  - Vue3组件化开发实践
  - 游戏对象系统设计（AcGameObject基类）
  - 游戏地图渲染系统（GameMap类）
  - 贪吃蛇游戏逻辑实现（Snake类）
  - 游戏单元系统（Cell类）
  - 墙体系统实现（Wall类）
  - 多页面路由管理
  - 前端UI组件开发
- 📁 **主要文件创建**：
  - **Web端新增组件**：
    - `web/src/components/ContentField.vue` - 内容区域组件
    - `web/src/components/GameMap.vue` - 游戏地图组件
    - `web/src/components/NavBar.vue` - 导航栏组件
    - `web/src/components/PlayGournd.vue` - 游戏场地组件
  - **Web端新增页面**：
    - `web/src/views/error/NotFoundView.vue` - 404错误页面
    - `web/src/views/pk/PkIndexView.vue` - PK对战页面
    - `web/src/views/ranklist/RanklistIndexView.vue` - 排行榜页面
    - `web/src/views/record/RecordIndexView.vue` - 对局记录页面
    - `web/src/views/user/bot/UserBotIndexView.vue` - Bot管理页面
  - **游戏脚本文件**：
    - `web/src/assets/scripts/AcGameObject.js` - 游戏对象基类
    - `web/src/assets/scripts/GameMap.js` - 游戏地图类
    - `web/src/assets/scripts/Wall.js` - 墙体类
    - `web/src/assets/scripts/Cell.js` - 游戏单元类
    - `web/src/assets/scripts/Snake.js` - 贪吃蛇类
  - **资源文件重组**：
    - `web/src/assets/images/background.png` - 背景图片
    - `web/src/assets/images/logo.png` - Logo图片
  - **配置文件更新**：
    - `web/src/App.vue` - 根组件更新
    - `web/src/router/index.js` - 路由配置更新
    - `web/public/favicon.ico` - 网站图标更新
- ⏰ **更新时间**：2025/10/22 22:05

### 2025年10月23日（周三）- Day 3 已完成 ✅
- 📌 **当前状态**：Day 3开发阶段完成，MySQL数据库配置和用户认证系统实现
- 🎯 **今日目标**：完成MySQL配置与用户认证模块 + Spring Security安全配置
- ✅ **已完成**：
  - ✅ **Lesson 4.1: 配置Mysql与注册登录模块 (上)**（2025/10/23 19:50）
- 💭 **技术成果**：
  - ✅ **MySQL 数据库配置**
  - ✅ **Spring Security 安全框架**
  - ✅ **用户实体类与数据库映射**
  - ✅ **用户认证服务实现**
  - ✅ **用户控制器 API 接口**
  - ✅ **密码加密与安全验证**
  - ✅ **数据库连接池配置**
- 🎉 **重大里程碑**：
  - **MySQL 数据库系统 100% 完成** 🗄️
  - **Spring Security 安全框架 100% 完成** 🔐
  - **用户认证系统 100% 完成** 👤
  - **后端 API 接口 100% 完成** 🔌
  - **项目整体进度达到 40%**
- 💡 **技术说明**：
  - Spring Boot + MySQL 8.0 数据库集成
  - Spring Security 安全框架配置
  - MyBatis-Plus ORM 框架使用
  - BCrypt 密码加密算法
  - 用户实体类与数据库表映射
  - RESTful API 接口设计
  - 数据库连接池优化配置
- 📁 **主要文件创建**：
  - **Backend 后端新增文件**：
    - `backend/pom.xml` - Maven 依赖配置更新（MySQL、Spring Security、MyBatis-Plus）
    - `backend/src/main/java/com/final_kob/backend/config/SecurityConfig.java` - Spring Security 安全配置
    - `backend/src/main/java/com/final_kob/backend/controller/user/UserController.java` - 用户控制器
    - `backend/src/main/java/com/final_kob/backend/mapper/UserMapper.java` - 用户数据访问层
    - `backend/src/main/java/com/final_kob/backend/pojo/User.java` - 用户实体类
    - `backend/src/main/java/com/final_kob/backend/service/impl/UserDetailsServiceImpl.java` - 用户认证服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/utils/UserDetailsImpl.java` - 用户详情工具类
    - `backend/src/main/resources/application.properties` - 应用配置文件更新（数据库连接）
  - **配置文件更新**：
    - MySQL 数据库连接配置
    - Spring Security 安全策略配置
    - MyBatis-Plus 数据库映射配置
    - 数据库连接池参数优化
- ⏰ **更新时间**：2025/10/23 19:50

### 2025年10月24日（周四）- Day 4 已完成 ✅
- 📌 **当前状态**：Day 4开发阶段完成，JWT认证系统和前后端账户系统实现
- 🎯 **今日目标**：完成JWT认证系统 + 前后端登录注册系统 + 用户状态管理
- ✅ **已完成**：
  - ✅ **Lesson 4.2: 配置Mysql与注册登录模块 (中)**（2025/10/24 17:00）
  - ✅ **Lesson 4.3: 配置Mysql与注册登录模块 (下)**（2025/10/24 17:00）
- 💭 **技术成果**：
  - ✅ **JWT Token 生成与验证**
  - ✅ **JWT 过滤器实现**
  - ✅ **用户注册登录 API 接口**
  - ✅ **前后端登录注册系统**
  - ✅ **用户状态管理**
  - ✅ **账户系统完整实现**
  - ✅ **前后端数据交互**
- 🎉 **重大里程碑**：
  - **JWT 认证系统 100% 完成** 🔐
  - **前后端账户系统 100% 完成** 👤
  - **用户状态管理 100% 完成** 📊
  - **登录注册功能 100% 完成** 🔑
  - **项目整体进度达到 50%**
- 💡 **技术说明**：
  - JWT Token 生成与验证机制
  - Spring Security JWT 过滤器实现
  - 前后端登录注册 API 接口
  - Vuex 用户状态管理
  - 前后端数据交互与状态同步
  - 用户认证与授权流程
  - RESTful API 接口设计
- 📁 **主要文件创建**：
  - **Backend 后端新增文件**：
    - `backend/src/main/java/com/final_kob/backend/config/filter/JwtAuthenticationTokenFilter.java` - JWT 认证过滤器
    - `backend/src/main/java/com/final_kob/backend/controller/user/account/InfoController.java` - 用户信息控制器
    - `backend/src/main/java/com/final_kob/backend/controller/user/account/LoginController.java` - 用户登录控制器
    - `backend/src/main/java/com/final_kob/backend/controller/user/account/RegisterController.java` - 用户注册控制器
    - `backend/src/main/java/com/final_kob/backend/service/impl/user/account/InfoServiceImpl.java` - 用户信息服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/user/account/LoginServiceImpl.java` - 用户登录服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/user/account/RegisterServiceImpl.java` - 用户注册服务实现
    - `backend/src/main/java/com/final_kob/backend/service/user/account/InfoService.java` - 用户信息服务接口
    - `backend/src/main/java/com/final_kob/backend/service/user/account/LoginService.java` - 用户登录服务接口
    - `backend/src/main/java/com/final_kob/backend/service/user/account/RegisterService.java` - 用户注册服务接口
    - `backend/src/main/java/com/final_kob/backend/utils/JwtUtil.java` - JWT 工具类
  - **Frontend 前端新增文件**：
    - `web/src/store/user.js` - 用户状态管理模块
    - `web/src/views/user/account/UserAccountLoginView.vue` - 用户登录页面
    - `web/src/views/user/account/UserAccountRegisterView.vue` - 用户注册页面
  - **配置文件更新**：
    - `backend/pom.xml` - JWT 相关依赖添加
    - `backend/src/main/java/com/final_kob/backend/config/SecurityConfig.java` - Spring Security 配置更新
    - `backend/src/main/java/com/final_kob/backend/pojo/User.java` - 用户实体类更新
    - `web/src/App.vue` - 根组件更新
    - `web/src/components/NavBar.vue` - 导航栏组件更新
    - `web/src/router/index.js` - 路由配置更新
    - `web/src/store/index.js` - Vuex 状态管理更新
  - **文件删除**：
    - `backend/src/main/java/com/final_kob/backend/controller/user/UserController.java` - 旧用户控制器（已重构）
- ⏰ **更新时间**：2025/10/24 17:00

### 2025年10月24日（周四）- Day 5 已完成 ✅
- 📌 **当前状态**：Day 5开发阶段完成，Bot后端CRUD API实现
- 🎯 **今日目标**：完成Bot后端增删改查API + Bot实体类与数据库映射
- ✅ **已完成**：
  - ✅ **Lesson 5.1: 创建Bot后端CRUD API**（2025/10/24 20:50）
- 💭 **技术成果**：
  - ✅ **Bot 实体类与数据库映射**
  - ✅ **Bot 控制器 API 接口**
  - ✅ **Bot 服务层实现**
  - ✅ **Bot 数据访问层**
  - ✅ **Bot CRUD 完整功能**
  - ✅ **RESTful API 设计**
  - ✅ **分层架构实践**
- 🎉 **重大里程碑**：
  - **Bot 后端 CRUD API 100% 完成** 🤖
  - **Bot 数据访问层 100% 完成** 🗄️
  - **Bot 服务层 100% 完成** ⚙️
  - **Bot 控制器层 100% 完成** 🎮
  - **项目整体进度达到 55%**
- 💡 **技术说明**：
  - Spring Boot 分层架构设计
  - MyBatis-Plus ORM 框架使用
  - RESTful API 接口设计
  - Bot 实体类与数据库表映射
  - CRUD 操作完整实现
  - 服务层与控制器层解耦
  - 数据访问层抽象化
- 📁 **主要文件创建**：
  - **Backend 后端新增文件**：
    - `backend/src/main/java/com/final_kob/backend/controller/user/bot/AddController.java` - Bot 添加控制器
    - `backend/src/main/java/com/final_kob/backend/controller/user/bot/GetListController.java` - Bot 列表查询控制器
    - `backend/src/main/java/com/final_kob/backend/controller/user/bot/RemoveController.java` - Bot 删除控制器
    - `backend/src/main/java/com/final_kob/backend/controller/user/bot/UpdateController.java` - Bot 更新控制器
    - `backend/src/main/java/com/final_kob/backend/mapper/BotMapper.java` - Bot 数据访问层
    - `backend/src/main/java/com/final_kob/backend/pojo/Bot.java` - Bot 实体类
    - `backend/src/main/java/com/final_kob/backend/service/bot/AddService.java` - Bot 添加服务接口
    - `backend/src/main/java/com/final_kob/backend/service/bot/GetListService.java` - Bot 列表查询服务接口
    - `backend/src/main/java/com/final_kob/backend/service/bot/RemoveService.java` - Bot 删除服务接口
    - `backend/src/main/java/com/final_kob/backend/service/bot/UpdateService.java` - Bot 更新服务接口
    - `backend/src/main/java/com/final_kob/backend/service/impl/bot/AddServiceImpl.java` - Bot 添加服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/bot/GetListServiceImpl.java` - Bot 列表查询服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/bot/RemoveServiceImpl.java` - Bot 删除服务实现
    - `backend/src/main/java/com/final_kob/backend/service/impl/bot/UpdateServiceImpl.java` - Bot 更新服务实现
  - **配置文件更新**：
    - `backend/src/main/java/com/final_kob/backend/mapper/UserMapper.java` - 用户数据访问层更新
    - `web/src/views/user/bot/UserBotIndexView.vue` - Bot 管理页面更新
- ⏰ **更新时间**：2025/10/24 20:50

### 2025年10月25日（周五）- Day 5 全面完成 ✅
- 📌 **当前状态**：Day 5开发阶段全面完成，Bot前端CRUD和个人中心页面实现
- 🎯 **今日目标**：完成Bot前端CRUD + 个人中心页面全面实现 + 用户信息管理
- ✅ **已完成**：
  - ✅ **Lesson 5.2: 创建个人中心页面 (下)**（2025/10/25 12:40）
- 💭 **技术成果**：
  - ✅ **Bot 前端 CRUD 实现**
  - ✅ **个人中心页面全面完成**
  - ✅ **用户信息查询与更新**
  - ✅ **Token 自动刷新机制**
  - ✅ **用户头像上传功能**
  - ✅ **前后端数据交互**
  - ✅ **Vue3 组件化开发**
- 🎉 **重大里程碑**：
  - **Bot 前端 CRUD 100% 完成** 🤖
  - **个人中心页面 100% 完成** 👤
  - **用户系统 100% 完成** 📋
  - **Bot 执行系统 80% 完成** ⚙️
  - **项目整体进度达到 60%**
- 💡 **技术说明**：
  - Vue3 组件化开发实践
  - Axios HTTP 请求封装
  - 前后端数据交互与状态管理
  - Bot 管理界面完整实现
  - 用户信息管理功能
  - 个人中心页面设计
  - 响应式布局与用户体验优化
- 📁 **主要文件更新**：
  - **Frontend 前端更新文件**：
    - `web/src/views/user/bot/UserBotIndexView.vue` - Bot 管理页面完整实现
    - `web/src/components/NavBar.vue` - 导航栏组件更新
    - `web/src/router/index.js` - 路由配置更新
    - `web/src/store/user.js` - 用户状态管理更新
  - **Backend 后端更新文件**：
    - `backend/src/main/java/com/final_kob/backend/controller/user/bot/GetListController.java` - Bot 列表控制器优化
    - `backend/src/main/java/com/final_kob/backend/pojo/Bot.java` - Bot 实体类完善
  - **配置文件更新**：
    - `web/package.json` - 前端依赖更新
    - `web/package-lock.json` - 依赖锁定文件更新
- ⏰ **更新时间**：2025/10/25 12:40

### 2025年10月25日（周五）- Day 6 Lesson 6.1 已完成 ✅
- 📌 **当前状态**：Day 6 Lesson 6.1开发阶段完成，匹配系统前后端WebSocket通信实现
- 🎯 **今日目标**：完成匹配系统前后端WebSocket通讯的一半（同步地图+匹配）
- ✅ **已完成**：
  - ✅ **Lesson 6.1: 实现微服务:匹配系统 (上)**（2025/10/25 22:05）
- 💭 **技术成果**：
  - ✅ **WebSocket 配置与服务器实现**
  - ✅ **匹配系统基础架构设计**
  - ✅ **地图同步机制实现**
  - ✅ **JWT 认证集成**
  - ✅ **匹配界面开发**
  - ✅ **前后端实时通信**
  - ✅ **游戏状态同步**
- 🎉 **重大里程碑**：
  - **WebSocket 实时通信 100% 完成** 🔌
  - **匹配系统基础架构 100% 完成** 🎯
  - **地图同步机制 100% 完成** 🗺️
  - **JWT 认证集成 100% 完成** 🔐
  - **项目整体进度达到 65%**
- 💡 **技术说明**：
  - Spring Boot WebSocket 配置与实现
  - 前后端实时双向通信
  - 游戏地图状态同步
  - JWT Token 认证集成
  - 匹配系统基础架构设计
  - Vue3 组件化开发
  - WebSocket 连接管理与状态维护
- 📁 **主要文件创建**：
  - **Backend 后端新增文件**：
    - `backend/src/main/java/com/final_kob/backend/config/WebSocketConfig.java` - WebSocket 配置
    - `backend/src/main/java/com/final_kob/backend/consumer/WebSocketServer.java` - WebSocket 服务器
    - `backend/src/main/java/com/final_kob/backend/consumer/utils/Game.java` - 游戏逻辑类
    - `backend/src/main/java/com/final_kob/backend/consumer/utils/JwtAuthentication.java` - JWT 认证工具
  - **Frontend 前端新增文件**：
    - `web/src/components/MatchGround.vue` - 匹配场地组件
    - `web/src/store/pk.js` - PK 状态管理模块
  - **配置文件更新**：
    - `backend/pom.xml` - WebSocket 相关依赖添加
    - `backend/src/main/java/com/final_kob/backend/config/SecurityConfig.java` - Spring Security 配置更新
    - `backend/src/main/java/com/final_kob/backend/utils/JwtUtil.java` - JWT 工具类更新
    - `web/src/assets/scripts/GameMap.js` - 游戏地图脚本更新
    - `web/src/components/GameMap.vue` - 游戏地图组件更新
    - `web/src/store/index.js` - Vuex 状态管理更新
    - `web/src/views/pk/PkIndexView.vue` - PK 页面更新
- ⏰ **更新时间**：2025/10/25 22:05

### 2025年10月26日（周六）- Day 6 Lesson 6.2 已完成 ✅
- 📌 **当前状态**：Day 6 Lesson 6.2开发阶段完成，匹配系统玩家移动同步、游戏状态同步和数据库记录实现
- 🎯 **今日目标**：完成同步移动、同步游戏状态、记录游戏过程到数据库MySQL、设置ResultBoard前端显示游戏结果
- ✅ **已完成**：
  - ✅ **Lesson 6.2: 实现微服务:匹配系统 (中)**（2025/10/26 16:00）
- 💭 **技术成果**：
  - ✅ **玩家移动同步机制实现**
  - ✅ **游戏状态实时同步**
  - ✅ **对战记录数据库存储（MySQL）**
  - ✅ **游戏结果展示界面（ResultBoard）**
  - ✅ **Record 实体类设计**
  - ✅ **MyBatis-Plus 数据持久化**
  - ✅ **前后端状态一致性保证**
  - ✅ **游戏过程完整记录**
- 🎉 **重大里程碑**：
  - **玩家移动同步 100% 完成** 🎮
  - **游戏状态同步 100% 完成** 📊
  - **对战记录数据库 100% 完成** 🗄️
  - **游戏结果展示 100% 完成** 🏆
  - **项目整体进度达到 70%**
- 💡 **技术说明**：
  - 玩家操作实时同步机制
  - WebSocket 双向通信优化
  - 游戏状态一致性保证
  - Record 实体类与数据库映射
  - MyBatis-Plus 数据持久化
  - 游戏结果展示界面设计
  - Vue3 组件化开发
  - 前后端数据实时同步
- 📁 **主要文件更新**：
  - **Backend 后端新增/更新文件**：
    - `backend/src/main/java/com/final_kob/backend/consumer/WebSocketServer.java` - WebSocket 服务器更新（同步移动、状态）
    - `backend/src/main/java/com/final_kob/backend/consumer/utils/Cell.java` - 游戏单元类（新建）
    - `backend/src/main/java/com/final_kob/backend/consumer/utils/Game.java` - 游戏逻辑类更新
    - `backend/src/main/java/com/final_kob/backend/consumer/utils/Player.java` - 玩家类（新建）
    - `backend/src/main/java/com/final_kob/backend/mapper/RecordMapper.java` - 对战记录数据访问层（新建）
    - `backend/src/main/java/com/final_kob/backend/pojo/Record.java` - 对战记录实体类（新建）
  - **Frontend 前端新增/更新文件**：
    - `web/src/components/ResultBoard.vue` - 游戏结果展示组件（新建）
    - `web/src/assets/scripts/GameMap.js` - 游戏地图脚本更新（移动同步）
    - `web/src/assets/scripts/Snake.js` - 贪吃蛇脚本更新（状态同步）
    - `web/src/components/GameMap.vue` - 游戏地图组件更新
    - `web/src/store/pk.js` - PK 状态管理模块更新
    - `web/src/views/pk/PkIndexView.vue` - PK 页面更新（结果展示）
  - **配置文件更新**：
    - `backend/src/main/resources/application.properties` - 数据库配置更新
- ⏰ **更新时间**：2025/10/26 16:00

### 2025年10月26日（周六）- Day 6 Lesson 6.3 已完成 ✅
- 📌 **当前状态**：Day 6 Lesson 6.3开发阶段完成，匹配系统彻底完成，创建了独立`matchingsystem`模块
- 🎯 **今日目标**：完成Lesson 6.3，创建`matchingsystem`模块，彻底完成匹配系统
- ✅ **已完成**：
  - ✅ **Lesson 6.3: 实现微服务:匹配系统 (下)**（2025/10/26 21:00）
- 💭 **技术成果**：
  - ✅ **独立 `matchingsystem` 模块创建**
  - ✅ **多线程匹配逻辑实现**
  - ✅ **匹配队列管理（BlockingQueue）**
  - ✅ **匹配超时与取消机制**
  - ✅ **对战房间创建与管理**
  - ✅ **天梯积分系统初步集成**
  - ✅ **匹配 API 接口完善**
  - ✅ **前后端匹配流程闭环**
- 🎉 **重大里程碑**：
  - **匹配系统 100% 完成** 🎯
  - **独立匹配微服务模块 100% 完成** 🧩
  - **项目架构升级为多模块微服务架构** 🏗️
  - **项目整体进度达到 75%**
- 💡 **技术说明**：
  - Spring Boot 独立模块设计与集成
  - 多线程并发处理匹配请求
  - BlockingQueue 实现高效匹配队列
  - 定时任务处理匹配超时
  - 动态创建和管理游戏房间
  - 匹配系统与主后端服务解耦
  - 完善匹配 API 接口，支持前端调用
  - RestTemplate 实现微服务间通信
- 📁 **主要文件新增/更新**：
  - **新增 matchingsystem 微服务模块**：
    - `backendcloud/matchingsystem/pom.xml` - 匹配系统模块 POM
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/MatchingSystemApplication.java` - 匹配系统启动类
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/config/SecurityConfig.java` - 匹配系统安全配置
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/config/RestTemplateConfig.java` - RestTemplate配置
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/controller/MatchingController.java` - 匹配API接口
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/MatchingService.java` - 匹配服务接口
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/MatchingServiceImpl.java` - 匹配服务实现
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/utils/MatchingPool.java` - 匹配池（核心匹配逻辑）
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/utils/Player.java` - 玩家类
    - `backendcloud/matchingsystem/src/main/resources/application.properties` - 匹配系统配置文件
    - `backendcloud/pom.xml` - 父级多模块 POM
  - **Backend 后端更新文件**：
    - `backendcloud/backend/src/main/java/com/final_kob/backend/controller/pk/StartGameController.java` - 新增开始游戏控制器
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/pk/StartGameService.java` - 开始游戏服务
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/WebSocketServer.java` - 更新WebSocket服务器
  - **Frontend 前端更新文件**：
    - `web/src/components/MatchGround.vue` - 更新匹配界面，调用匹配系统API
    - `web/src/store/pk.js` - 更新PK状态管理，处理匹配系统返回
    - `web/src/views/pk/PkIndexView.vue` - 更新PK页面
- ⏰ **更新时间**：2025/10/26 21:00

### 2025年10月31日（周四）- Day 7 Lesson 7 已完成 ✅
- 📌 **当前状态**：Day 7开发阶段完成，Bot执行系统彻底完成，创建了独立`botrunningsystem`模块
- 🎯 **今日目标**：完成Lesson 7，创建`botrunningsystem`模块，实现Bot代码执行系统
- ✅ **已完成**：
  - ✅ **Lesson 7: 实现微服务: Bot代码的执行**（2025/10/31）
- 💭 **技术成果**：
  - ✅ **独立 `botrunningsystem` 模块创建**
  - ✅ **Bot 代码安全沙箱执行**
  - ✅ **策略脚本运行与回传**
  - ✅ **自动战斗逻辑实现**
  - ✅ **Bot 执行池管理（BotPool）**
  - ✅ **多线程消费者模式（Consumer）**
  - ✅ **Bot 接口定义（BotInterface）**
  - ✅ **前后端 Bot 控制流程闭环**
- 🎉 **重大里程碑**：
  - **Bot 执行系统 100% 完成** 🤖
  - **独立 Bot 执行微服务模块 100% 完成** 🧩
  - **项目架构升级为三模块微服务架构** 🏗️
    - Backend（主后端服务）
    - Matchingsystem（匹配系统）
    - Botrunningsystem（Bot执行系统）
  - **项目整体进度达到 80%**
- 💡 **技术说明**：
  - Spring Boot 独立 Bot 执行模块设计与集成
  - 多线程消费者模式处理 Bot 代码执行
  - BotPool 线程池管理 Bot 执行任务
  - BotInterface 接口定义，支持动态加载 Bot 代码
  - 安全沙箱执行环境，隔离 Bot 代码
  - Bot 代码策略脚本实时运行与结果回传
  - RestTemplate 实现微服务间通信
  - WebSocket 接收 Bot 移动指令并同步
- 📁 **主要文件新增/更新**：
  - **新增 botrunningsystem 微服务模块**：
    - `backendcloud/botrunningsystem/pom.xml` - Bot执行系统模块 POM
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/BotRunningSystemApplication.java` - Bot执行系统启动类
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/config/SecurityConfig.java` - Bot执行系统安全配置
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/config/RestTemplateConfig.java` - RestTemplate配置
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/controller/BotRunningController.java` - Bot执行API接口
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/service/BotRunningService.java` - Bot执行服务接口
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/service/impl/BotRunningServiceImpl.java` - Bot执行服务实现
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/service/impl/utils/Bot.java` - Bot实体类（执行模块）
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/service/impl/utils/BotPool.java` - Bot执行池（核心执行逻辑）
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/service/impl/utils/Consumer.java` - 多线程消费者
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/utils/Bot.java` - Bot工具类
    - `backendcloud/botrunningsystem/src/main/java/com/final_kob/botrunningsystem/utils/BotInterface.java` - Bot接口定义
    - `backendcloud/botrunningsystem/src/main/resources/application.properties` - Bot执行系统配置文件
    - `.vscode/settings.json` - VS Code工作区配置
  - **Backend 后端更新文件**：
    - `backendcloud/backend/src/main/java/com/final_kob/backend/config/SecurityConfig.java` - 更新安全配置
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/WebSocketServer.java` - 更新WebSocket服务器（接收Bot移动）
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/utils/Game.java` - 更新游戏逻辑（支持Bot）
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/utils/Player.java` - 更新玩家类（支持Bot玩家）
    - `backendcloud/backend/src/main/java/com/final_kob/backend/controller/pk/ReceiveBotMoveController.java` - 新增接收Bot移动控制器
    - `backendcloud/backend/src/main/java/com/final_kob/backend/controller/pk/StartGameController.java` - 更新开始游戏控制器
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/pk/ReceiveBotMoveService.java` - 新增接收Bot移动服务接口
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/impl/pk/ReceiveBotMoveServiceImpl.java` - 新增接收Bot移动服务实现
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/impl/pk/StartGameServiceImpl.java` - 更新开始游戏服务
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/pk/StartGameService.java` - 更新开始游戏服务接口
  - **Matchingsystem 匹配系统更新文件**：
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/controller/MatchingServiceController.java` - 更新匹配服务控制器
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/MatchingService.java` - 更新匹配服务接口
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/MatchingServiceImpl.java` - 更新匹配服务实现
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/utils/MatchingPool.java` - 更新匹配池（支持Bot）
    - `backendcloud/matchingsystem/src/main/java/com/final_kob/matchingsystem/service/impl/utils/Player.java` - 更新玩家类（支持Bot）
  - **Frontend 前端更新文件**：
    - `web/src/components/MatchGround.vue` - 更新匹配界面，支持Bot对战
    - `web/src/views/pk/PkIndexView.vue` - 更新PK页面
  - **配置文件更新**：
    - `backendcloud/pom.xml` - 父级多模块 POM 更新（新增botrunningsystem模块）
- 💭 **项目进展说明**：
  - 10/27-10/30 期间忙于重构 Roamio 项目，暂停 Final_KOB 开发
  - 10/31 恢复 Final_KOB 开发，完成 Lesson 7
  - 项目架构已完成三大微服务模块建设
- ⏰ **更新时间**：2025/10/31

### 2025年11月1日（周五）- Day 8 Lesson 8 已完成 ✅
- 📌 **当前状态**：Day 8开发阶段完成，前端录像回放和排行榜页面实现
- 🎯 **今日目标**：完成Lesson 8，实现前端的录像回放和排行榜页面
- ✅ **已完成**：
  - ✅ **Lesson 8: 创建对战列表与排行榜页面**（2025/11/1 12:30）
- 💭 **技术成果**：
  - ✅ **前端录像回放页面实现（RecordContentView.vue）**
  - ✅ **前端排行榜页面实现（RanklistIndexView.vue）**
  - ✅ **后端获取对战记录列表 API**
  - ✅ **后端获取排行榜数据 API**
  - ✅ **MyBatis-Plus 配置与集成**
  - ✅ **游戏回放逻辑实现**
  - ✅ **Vuex 状态管理扩展（record 模块）**
  - ✅ **路由配置完善（录像回放路由）**
- 🎉 **重大里程碑**：
  - **录像回放功能 100% 完成** 🎬
  - **排行榜功能 100% 完成** 🏆
  - **对战记录系统 100% 完成** 📊
  - **项目整体进度达到 85%**
- 💡 **技术说明**：
  - 前端 Vue3 组件化开发，实现录像播放器和排行榜列表
  - 后端 Spring Boot RESTful API 提供对战记录和排行榜数据
  - MyBatis-Plus 分页查询实现高效数据加载
  - WebSocket 协议用于游戏状态同步和回放数据传输
  - Vuex 管理前端状态，实现数据响应式更新
  - GameMap 和 Snake 脚本支持回放模式
  - 排行榜按胜率排序展示
- 📁 **主要文件新增/更新**：
  - **Backend 后端新增文件**：
    - `backendcloud/backend/src/main/java/com/final_kob/backend/config/MybatisConfig.java` - MyBatis-Plus配置类
    - `backendcloud/backend/src/main/java/com/final_kob/backend/controller/ranklist/GetRanklistController.java` - 获取排行榜控制器
    - `backendcloud/backend/src/main/java/com/final_kob/backend/controller/record/GetRecordListController.java` - 获取对战记录列表控制器
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/ranklist/GetRanklistService.java` - 排行榜服务接口
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/impl/ranklist/GetRanklistServiceImpl.java` - 排行榜服务实现
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/record/GetRecordListServiceImpl.java` - 对战记录列表服务实现
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/impl/record/GetRecordListService.java` - 对战记录列表服务接口
    - `backendcloud/backend/temp-extraction/...` - MyBatis-Plus 扩展相关文件（100+ 文件）
  - **Backend 后端更新文件**：
    - `backendcloud/backend/pom.xml` - 新增 MyBatis-Plus 依赖
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/WebSocketServer.java` - 更新WebSocket服务器（支持回放）
    - `backendcloud/backend/src/main/java/com/final_kob/backend/consumer/utils/Game.java` - 更新游戏逻辑（支持回放数据）
    - `backendcloud/backend/src/main/java/com/final_kob/backend/service/impl/bot/AddServiceImpl.java` - 更新Bot服务
  - **Frontend 前端新增文件**：
    - `web/src/store/record.js` - Vuex record 模块（管理录像回放状态）
    - `web/src/views/record/RecordContentView.vue` - 录像回放页面组件
  - **Frontend 前端更新文件**：
    - `web/src/assets/scripts/GameMap.js` - 更新游戏地图脚本（支持回放模式）
    - `web/src/assets/scripts/Snake.js` - 更新贪吃蛇脚本（支持回放渲染）
    - `web/src/router/index.js` - 更新路由配置（新增录像回放路由）
    - `web/src/store/index.js` - 更新Vuex配置（注册record模块）
    - `web/src/views/pk/PkIndexView.vue` - 更新PK页面（集成录像链接）
    - `web/src/views/ranklist/RanklistIndexView.vue` - 更新排行榜页面（完整实现）
    - `web/src/views/record/RecordIndexView.vue` - 更新对战记录列表页面
- ⏰ **更新时间**：2025/11/1 12:30

## 快速开始

### 环境要求
- Java 17+
- Maven 3.6+
- MySQL 8.0+
- Redis 6.0+

### 安装步骤

1. **克隆项目**
   ```bash
   git clone https://gitee.com/ppshux/final_kob
   cd final_kob
   ```

2. **配置数据库**
   ```sql
   CREATE DATABASE kob_db;
   ```

3. **修改配置文件**
   编辑 `application.yml` 配置数据库和 Redis 连接信息

4. **构建并运行**
   ```bash
   mvn clean install
   mvn spring-boot:run
   ```

5. **访问项目**
   ```
   http://localhost:8080
   ```

## 项目结构

```
final_kob/
├── backend/                    # Spring Boot 后端服务
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   ├── com/final_kob/backend/
│   │   │   │   │   ├── BackendApplication.java    # Spring Boot 主启动类
│   │   │   │   │   ├── config/
│   │   │   │   │   │   └── CorsConfig.java         # 跨域配置
│   │   │   │   │   └── controller/pk/
│   │   │   │   │       ├── IndexController.java   # PK 页面控制器
│   │   │   │   │       └── BotInfoController.java  # Bot 信息控制器
│   │   │   │   ├── BasicController.java            # 基础控制器
│   │   │   │   ├── PathVariableController.java     # 路径变量控制器
│   │   │   │   └── User.java                       # 用户实体类
│   │   │   └── resources/
│   │   │       ├── application.properties          # 应用配置文件
│   │   │       ├── static/
│   │   │       │   ├── index.html                  # 静态首页
│   │   │       │   └── images/img.png              # 静态图片资源
│   │   │       └── templates/pk/
│   │   │           └── index.html                  # PK 页面模板
│   │   └── test/java/com/final_kob/backend/
│   │       └── BackendApplicationTests.java        # 测试类
│   ├── pom.xml                                     # Maven 项目配置
│   └── .gitignore                                  # Git 忽略文件
├── web/                           # Vue3 Web 端
│   ├── src/
│   │   ├── components/
│   │   │   ├── ContentField.vue                     # 内容区域组件
│   │   │   ├── GameMap.vue                          # 游戏地图组件
│   │   │   ├── NavBar.vue                           # 导航栏组件
│   │   │   └── PlayGournd.vue                       # 游戏场地组件
│   │   ├── views/
│   │   │   ├── error/
│   │   │   │   └── NotFoundView.vue                 # 404错误页面
│   │   │   ├── pk/
│   │   │   │   └── PkIndexView.vue                  # PK对战页面
│   │   │   ├── ranklist/
│   │   │   │   └── RanklistIndexView.vue            # 排行榜页面
│   │   │   ├── record/
│   │   │   │   └── RecordIndexView.vue              # 对局记录页面
│   │   │   └── user/bot/
│   │   │       └── UserBotIndexView.vue             # Bot管理页面
│   │   ├── assets/
│   │   │   ├── images/
│   │   │   │   ├── background.png                   # 背景图片
│   │   │   │   └── logo.png                         # Logo图片
│   │   │   └── scripts/
│   │   │       ├── AcGameObject.js                  # 游戏对象基类
│   │   │       ├── GameMap.js                       # 游戏地图类
│   │   │       ├── Wall.js                          # 墙体类
│   │   │       ├── Cell.js                          # 游戏单元类
│   │   │       └── Snake.js                         # 贪吃蛇类
│   │   ├── router/
│   │   │   └── index.js                             # Vue Router 配置
│   │   ├── store/
│   │   │   └── index.js                              # Vuex 状态管理
│   │   ├── App.vue                                  # Vue 根组件
│   │   └── main.js                                  # Vue 入口文件
│   ├── public/
│   │   ├── index.html                               # HTML 模板
│   │   └── favicon.ico                              # 网站图标
│   ├── package.json                                 # npm 项目配置
│   ├── vue.config.js                                # Vue CLI 配置
│   ├── babel.config.js                              # Babel 配置
│   ├── jsconfig.json                                # JavaScript 配置
│   └── .gitignore                                   # Git 忽略文件
├── acapp/                         # Vue3 AcApp 端
│   ├── src/
│   │   ├── components/
│   │   │   └── Helloworld.vue                     # Hello World 组件
│   │   ├── assets/
│   │   │   └── logo.png                           # Logo 图片
│   │   ├── store/
│   │   │   └── index.js                            # Vuex 状态管理
│   │   ├── App.vue                                 # Vue 根组件
│   │   └── main.js                                 # Vue 入口文件
│   ├── public/
│   │   ├── index.html                              # HTML 模板
│   │   └── favicon.ico                             # 网站图标
│   ├── package.json                                # npm 项目配置
│   ├── vue.config.js                               # Vue CLI 配置
│   ├── babel.config.js                             # Babel 配置
│   ├── jsconfig.json                               # JavaScript 配置
│   └── .gitignore                                  # Git 忽略文件
├── final_kob.md                                   # 项目文档
└── README.md                                      # 项目说明
```

### 架构说明

#### 🏗️ **三端分离架构**
- **Backend（后端）**：Spring Boot 3.x + Maven
- **Web（Web端）**：Vue3 + Vue Router + Vuex
- **AcApp（移动端）**：Vue3 + Vuex（适配移动端）

#### 📁 **模块职责**
- **Backend**：提供 RESTful API 服务，处理业务逻辑
- **Web**：Web 端用户界面，通过 API 与后端通信
- **AcApp**：移动端应用，支持 AcWing OAuth2.0 登录

#### 🔧 **技术栈配置**
- **后端**：Spring Boot + MyBatis + Redis + MySQL + JWT
- **前端**：Vue3 + Vue Router + Vuex + Bootstrap + Axios
- **构建**：Maven（后端）+ npm（前端）
- **版本控制**：Git + Gitee

## 技术亮点

- ✅ **Spring Boot 分层架构**：Controller / Service / Mapper 清晰解耦
- ✅ **MyBatis-Plus**：简化 CRUD 操作，提升开发效率
- ✅ **JWT 认证体系**：无状态 Token 认证，支持分布式部署
- ✅ **Redis 缓存**：提升系统性能，减少数据库压力
- ✅ **多线程匹配**：高并发匹配系统，支持实时对战
- ✅ **RESTful API**：标准化接口设计，易于对接和扩展
- ✅ **安全沙箱**：Bot 代码隔离执行，保证系统安全
- ✅ **AcWing OAuth2.0**：第三方登录集成

## 与 Final_AcApp 的对比

| 维度 | Final_AcApp (Django) | Final_KOB (Spring Boot) |
|------|---------------------|------------------------|
| **语言** | Python | Java |
| **框架** | Django + Channels | Spring Boot |
| **ORM** | Django ORM | MyBatis-Plus |
| **认证** | JWT + DRF | JWT + Spring Security |
| **实时通信** | WebSocket | WebSocket 已实现 |
| **匹配系统** | Thrift RPC | 多线程队列 |
| **缓存** | Redis | Redis |
| **部署难度** | 中等 | 简单（打包为 JAR） |
| **企业应用** | 中小型 | 大型企业级 |

## 许可证

本项目采用 MIT 许可证。

## 致谢

- [Spring Boot](https://spring.io/projects/spring-boot) - 强大的 Java 框架
- [MyBatis](https://mybatis.org/mybatis-3/) - 优秀的持久层框架
- [Redis](https://redis.io/) - 高性能缓存
- [MySQL](https://www.mysql.com/) - 关系型数据库
- [JWT](https://jwt.io/) - JSON Web Token

## 联系方式

- **项目仓库**：[https://gitee.com/ppshux/final_kob](https://gitee.com/ppshux/final_kob)
- **Final 系列项目**：Final_KOF ✅ | Final_MySpace ✅ | Final_AcApp ✅ | Final_KOB 🚀

---

> "From real-time games to enterprise architecture — Final_KOB marks the final evolution of the Show Way Plan."

---

## 🔗 相关项目

- **Final_KOF** - 拳皇格斗游戏（前端 Canvas 游戏开发）✅
- **Final_MySpace** - 社交空间平台（Vue3 全栈应用）✅
- **Final_AcApp** - 实时对战平台（Django 后端深度实践）✅
- **Final_KOB** - 企业级对战后端平台（Spring Boot 企业级架构）🚀

---

⭐ 如果这个项目对你有帮助，请给它一个 Star！

🎮 **Final Series - The Ultimate Journey of Full Stack Development!**

## 📊 项目日志

| 日期 | 状态 | 内容 | 完成度 |
|------|------|------|--------|
| 2025/10/20 | 🚀 | 项目初始化 + Git 配置 + 数据库设计 | 0% |
| 2025/10/21 | ✅ | Lesson 2: 创建项目结构 backend,web,acapp | 15% |
| 2025/10/22 | ✅ | Lesson 3.1: 创建前端菜单和地图 | 30% |
| 2025/10/22 | ✅ | Lesson 3.2: 创建贪吃蛇游戏前端 | 30% |
| 2025/10/23 | ✅ | Lesson 4.1: 配置Mysql与注册登录模块 (上) | 40% |
| 2025/10/24 | ✅ | Lesson 4.2: 配置Mysql与注册登录模块 (中) | 50% |
| 2025/10/24 | ✅ | Lesson 4.3: 配置Mysql与注册登录模块 (下) | 50% |
| 2025/10/24 | ✅ | Lesson 5.1: 创建Bot后端CRUD API | 55% |
| 2025/10/25 | ✅ | Lesson 5.2: 创建个人中心页面 (下) | 60% |
| 2025/10/25 | ✅ | Lesson 6.1: 实现微服务: 匹配系统 (上) | 65% |
| 2025/10/26 | ✅ | Lesson 6.2: 实现微服务: 匹配系统 (中) | 70% |
| 2025/10/26 | ✅ | Lesson 6.3: 实现微服务: 匹配系统 (下) | 75% |
| 2025/10/27-10/30 | 📝 | 重构 Roamio 项目（暂停 Final_KOB 开发） | - |
| 2025/10/31 | ✅ | Lesson 7: 实现微服务: Bot代码的执行 | 80% |
| 2025/11/1 | ✅ | Lesson 8: 创建对战列表与排行榜页面 | 85% |

---

## 🎯 Final 系列项目总览

### 已完成项目 ✅

1. **Final_KOF** - 拳皇格斗游戏
   - 技术：JavaScript + Canvas + GIF
   - 完成时间：2天
   - 亮点：游戏引擎、状态机、碰撞检测

2. **Final_MySpace** - 社交空间平台
   - 技术：Vue3 + Vuex + JWT
   - 完成时间：2-3天
   - 亮点：现代前端框架、状态管理、RESTful API

3. **Final_AcApp** - 实时对战平台
   - 技术：Django + Channels + Thrift + JWT
   - 完成时间：5天
   - 亮点：WebSocket、微服务、实时通信

### 进行中项目 🚀

4. **Final_KOB** - 企业级对战后端平台
   - 技术：Spring Boot + MyBatis + Redis + JWT
   - 预计时间：7天
   - 亮点：企业级架构、多线程、高并发

---

## 💡 技术成长轨迹

```
Final_KOF (前端游戏)
    ↓
Final_MySpace (Vue3全栈)
    ↓
Final_AcApp (Django后端)
    ↓
Final_KOB (Spring Boot企业级) ← 当前位置
```

**从前端到后端，从框架到架构，从项目到产品 —— 这是一条完整的全栈工程师成长之路！**

---

> "Keep building, keep recording, keep believing." 💪
> "The final chapter begins — Spring Boot meets enterprise architecture." 🚀

