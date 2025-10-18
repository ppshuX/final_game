# AcApp 实时对战平台 (Final_AcApp)

## 项目简介

这是一个基于 Django 开发的实时在线对战平台，集成了 WebSocket 通信、用户认证系统、智能匹配系统和 AI 对手功能。项目采用前后端分离架构，后端使用 Django + Django Channels 构建 WebSocket 服务，实现实时对战通信。

本项目是 **Final 系列项目**的第三个作品，也是**后端实践的综合项目**，展示了从用户系统、实时通信到游戏逻辑的完整后端开发流程。

## 项目特性

- 🎮 **实时对战系统**：基于 WebSocket 的实时游戏通信
- 👤 **用户认证系统**：用户注册、登录、JWT Token 认证
- 🤝 **智能匹配系统**：自动匹配在线玩家进行对战
- 🤖 **AI 对手功能**：支持与人机 AI 进行对战
- 📊 **战绩统计**：记录和展示玩家对战历史
- 🚀 **高性能架构**：Django Channels + Redis 实现异步通信

## 技术栈

### 后端技术
- **Web 框架**：Django 4.x
- **实时通信**：Django Channels（WebSocket）
- **消息队列**：Redis
- **数据库**：MySQL
- **认证系统**：JWT Token
- **Python 版本**：Python 3.8+

### 前端技术
- **游戏渲染**：Canvas（已实现）
- **游戏对象系统**：JavaScript 类继承架构（已实现）
- **粒子系统**：动态特效渲染（已实现）
- **技能系统**：火球等技能实现（已实现）
- **WebSocket 客户端**：原生 WebSocket API（已实现）
- **实时通信**：WebSocket 实时对战（开发中）
- **UI 框架**：Bootstrap 5（计划）

## 核心功能模块

### 1. 用户系统 📋
- [x] 用户注册和登录
- [x] JWT Token 认证
- [x] 用户信息管理
- [x] AcWing OAuth2.0 集成
- [x] Web端一键登录
- [x] AcApp端一键登录
- [ ] 头像上传

### 2. 实时对战系统 📋
- [x] WebSocket 连接
- [x] 房间创建和管理
- [x] 实时状态同步
- [x] 游戏逻辑处理
- [x] 多人联机对战
- [x] 聊天系统

### 3. 匹配系统 📋
- [ ] 玩家匹配队列
- [ ] 自动匹配算法
- [ ] 匹配取消功能

### 4. AI 对手系统 📋
- [x] AI 算法实现
- [x] 难度等级设置
- [x] 人机对战模式

### 5. 战绩系统 📋
- [ ] 对局记录保存
- [ ] 胜率统计
- [ ] 排行榜功能

## 开发计划（5天冲刺）

### Day 1：项目搭建 + 前端界面 ✅
- [x] Django 项目初始化
- [x] 数据库和 Redis 配置
- [x] 菜单界面创建
- [x] 游戏界面创建（前端）
- [x] 用户模型和认证 API
- [x] JWT Token 实现

### Day 2：用户系统开发 ✅
- [x] 用户模型和认证 API
- [x] JWT Token 实现
- [x] AcWing OAuth2.0 集成
- [x] Web端一键登录
- [x] AcApp端一键登录
- [x] 账号系统打通

### Day 3：WebSocket 通信 + 房间管理 ✅
- [x] Django Channels 集成
- [x] WebSocket Consumer 实现
- [x] 房间创建和管理逻辑
- [x] 消息广播机制
- [x] 多人联机对战实现

### Day 4：聊天系统 + 游戏优化 🚧
- [x] 聊天系统实现
- [x] 实时消息传输
- [x] 聊天界面优化
- [ ] 游戏性能优化
- [ ] 用户体验完善

### Day 5：AI 系统 + 战绩系统 ⏳
- [ ] AI 算法实现
- [ ] 人机对战模式
- [ ] 对局记录保存
- [ ] 战绩统计 API

## 快速开始

### 环境要求
- Python 3.8+
- MySQL 5.7+
- Redis 5.0+

### 安装步骤

1. **克隆项目**
   ```bash
   git clone http://gitee.com/ppshux/final_acapp
   cd final_acapp
   ```

2. **创建虚拟环境**
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   ```

3. **安装依赖**
   ```bash
   pip install -r requirements.txt
   ```

4. **配置数据库并启动**
   ```bash
   python manage.py migrate
   python manage.py runserver
   ```

## 开发进度

```
████████████████████████████████████████░░ 80%

✅ Day 1: 项目搭建 + 前端界面 (已完成)
✅ Day 2: 用户系统开发 (已完成)
✅ Day 3: WebSocket 通信 + 房间管理 (已完成)
🚧 Day 4: 聊天系统 + 游戏优化 (进行中 80%)
⏳ Day 5: AI 系统 + 战绩系统
```

## 项目状态

- **当前版本**：v0.2.0（80% 完成）
- **项目启动**：2025年10月13日
- **预计完成**：2025年10月18日（5天冲刺）
- **开发状态**：🚧 开发中
- **当前阶段**：Day 4 - 聊天系统完成，游戏优化进行中

## 每日进度更新

### 2025年10月17日（周四）- Day 4 进行中 🚧
- 📌 **当前状态**：Day 4开发阶段进行中，聊天系统完成，游戏功能日趋完善
- 🎯 **今日目标**：完成聊天系统 + 游戏优化 + 准备最终测试
- ✅ **已完成**：
  - ✅ **Lesson 7.2: 多人联机对战（下半部分）**（2025/10/17 22:00）
  - ✅ **Lesson 8: Chat Field 聊天系统**（2025/10/17 22:30）
- 💭 **技术成果**：
  - ✅ **多人联机对战功能完整实现**
  - ✅ **实时游戏同步机制完善**
  - ✅ **网络通信和延迟处理优化**
  - ✅ **玩家交互体验增强**
  - ✅ **游戏内聊天系统实现**
  - ✅ **聊天输入框和消息显示区域**
  - ✅ **实时消息传输机制**
  - ✅ **聊天界面用户体验优化**
- 🎉 **重大里程碑**：
  - **多人联机对战系统 100% 完成** 🎮
  - **聊天系统 100% 完成** 💬
  - **项目整体进度达到 80%**
- 💡 **技术说明**：
  - WebSocket实时通信优化完成
  - 游戏状态同步机制完善
  - 多人对战平衡性调整
  - 聊天消息路由和分发机制
  - 前端组件化开发实践
  - 静态资源同步管理
- 📁 **主要文件更新**：
  - `game/consumers/multiplayer/index.py` - 多人游戏后端逻辑 + 聊天消息处理
  - `game/static/js/src/playground/chat_field/zbase.js` - **新增聊天组件**
  - `game/static/js/src/playground/player/zbase.js` - 玩家实体类优化
  - `game/static/js/src/playground/socket/multiplayer/zbase.js` - Socket通信增强
  - `game/static/js/src/playground/zbase.js` - 游戏主场景更新
  - `game/static/css/game.css` - 聊天界面样式优化
- ⏰ **更新时间**：2025/10/17 22:30

### 2025年10月16日（周三）- Day 3 已完成 ✅
- 📌 **当前状态**：Day 3开发阶段完成，多人联机对战实现一半
- 🎯 **今日目标**：完成账号系统开发 + 前端游戏标准化 + 启动联机对战
- ✅ **已完成**：
  - ✅ **Lesson 6.1: 登录注册功能**（2025/10/15 17:40）
  - ✅ **Lesson 6.2: web端AcWing一键登录**（2025/10/15 21:55）
  - ✅ **Lesson 6.3: acapp端AcWing一键登录**（2025/10/16 11:25）
  - ✅ **统一游戏度量衡**（2025/10/16 下午）
  - ✅ **Lesson 7.1: 多人联机对战（上半部分）**（2025/10/16 晚上）
- 💭 **技术成果**：
  - ✅ 用户模型和数据库设计
  - ✅ 登录注册API接口
  - ✅ 前端登录注册界面
  - ✅ 用户信息管理功能
  - ✅ AcWing OAuth2.0集成
  - ✅ Web端一键登录功能
  - ✅ **AcApp端一键登录功能**
  - ✅ **账号系统基本打通**
  - ✅ **游戏坐标系统和尺寸标准化**
  - ✅ **为联机对战做好前端准备**
  - ✅ **Django Channels 集成完成**
  - ✅ **WebSocket Consumer 实现**
  - ✅ **房间创建和管理逻辑**
  - ✅ **WebSocket 实时连接建立**
- 🎉 **重大里程碑**：
  - 账号系统开发完成，支持多种登录方式
  - 前端游戏度量衡统一，为联机功能奠定基础
  - **WebSocket 实时通信架构搭建完成** 🎮
  - **多人联机对战框架实现 50%**
- 💡 **技术说明**：
  - 统一了游戏场景的坐标系统
  - 标准化了角色尺寸、移动速度等参数
  - Django Channels + Redis 实现 WebSocket 服务
  - 实现了房间管理和玩家连接逻辑
  - 下一步将实现实时状态同步和游戏逻辑处理
- ⏰ **更新时间**：2025/10/16 晚上

### 2025年10月14日（周一）- Day 2 进行中 🚧
- 📌 **当前状态**：Day 2开发阶段进行中
- 🎯 **今日目标**：WebSocket通信 + 房间管理
- 💭 **计划内容**：
  - Django Channels 集成
  - WebSocket Consumer 实现
  - 房间创建和管理逻辑
  - 消息广播机制
- ✅ **已完成**：
  - ✅ **Lesson 5: 配置nginx和对接acapp**（2025/10/14 21:40）
- ⏰ **更新时间**：2025/10/14 22:00

### 2025年10月13日（周日）- Day 1 已完成 ✅
- 📌 **完成内容**：
  - ✅ 完成项目规划
  - ✅ 确定技术栈：Django + Channels + Redis + MySQL
  - ✅ 创建项目文档
  - ✅ Django 项目初始化
  - ✅ 数据库和 Redis 配置
  - ✅ Docker 和 Git 环境配置
  - ✅ **Lesson 3: 创建菜单界面**（2025/10/13 15:30）
  - ✅ **Lesson 4: 创建游戏界面（前端）**（2025/10/13 16:00）
- 🎯 **下一步**：实现用户模型和认证 API
- 💭 **总结**：已完成第4课内容，前端界面框架搭建完成，游戏对象系统初步实现，开始用户系统开发

### 2025年10月13日上午 - 项目启动 🚀
- 📌 **完成内容**：
  - ✅ 完成项目规划
  - ✅ 确定技术栈：Django + Channels + Redis + MySQL
  - ✅ 创建项目文档
- 🎯 **下一步**：搭建 Django 项目基础环境
- 💭 **总结**：Final 系列第三个项目，聚焦后端实时通信技术

---

## 技术亮点

- ✅ **Django Channels**：异步 WebSocket 处理
- ✅ **WebSocket 实时通信**：房间管理和实时连接
- ✅ **JWT 认证**：无状态认证机制
- ✅ **AcWing OAuth2.0**：第三方登录集成
- ✅ **多端登录**：Web端 + AcApp端一键登录
- 🚧 **实时对战**：多人联机对战框架（50% 完成）
- ✅ **AI 对手**：决策树算法
- ✅ **高性能**：异步处理 + Redis 缓存

## 许可证

本项目采用 MIT 许可证。

## 致谢

- [Django](https://www.djangoproject.com/) - Python Web 框架
- [Django Channels](https://channels.readthedocs.io/) - Django 异步扩展
- [Redis](https://redis.io/) - 高性能缓存
- [MySQL](https://www.mysql.com/) - 关系型数据库

## 联系方式

- **项目仓库**：[http://gitee.com/ppshux/final_acapp](http://gitee.com/ppshux/final_acapp)
- **Final 系列项目**：Final_KOF ✅ | Final_MySpace ✅ | Final_AcApp 🚀

---

> "Real-time battles, intelligent matching — where Django meets WebSocket magic."

---

## 🔗 相关项目

- **Final_KOF** - 拳皇格斗游戏（前端 Canvas 游戏开发）✅
- **Final_MySpace** - 社交空间平台（Vue3 全栈应用）✅
- **Final_AcApp** - 实时对战平台（Django 后端深度实践）🚀

---

⭐ 如果这个项目对你有帮助，请给它一个 Star！

🎮 **Let's build something amazing together!**
