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
- [ ] 用户注册 / 登录
- [ ] JWT Token 认证
- [ ] 用户信息查询与更新
- [ ] AcApp 授权登录
- [ ] 个人中心页面
- [ ] 头像上传

### 2. 匹配系统 🎯
- [ ] 多线程匹配逻辑
- [ ] 匹配队列管理
- [ ] 匹配超时与取消机制
- [ ] 对战房间创建
- [ ] 玩家状态同步
- [ ] 天梯积分系统

### 3. Bot 执行系统 🤖
- [ ] Bot 代码安全沙箱执行
- [ ] 策略脚本运行与回传
- [ ] 自动战斗逻辑
- [ ] Bot 管理界面

### 4. 战绩与排行榜 🏆
- [ ] 对战记录保存
- [ ] 积分与胜率统计
- [ ] 排行榜展示
- [ ] 历史对局回放

### 5. 项目部署 🚀
- [ ] 后端打包上线
- [ ] AcApp 端 + Web 端 登录对接
- [ ] 性能调优与安全配置
- [ ] Docker 容器化部署

## 开发计划（7天冲刺）

### Day 1：项目搭建 + 首页与菜单页
- [ ] Spring Boot 项目初始化
- [ ] MySQL 数据库设计与创建
- [ ] Redis 配置
- [ ] Git 仓库配置
- [ ] 首页与菜单界面创建
- [ ] 项目基础架构搭建

### Day 2：用户注册 / 登录模块
- [ ] 用户实体类设计
- [ ] MySQL 用户表创建
- [ ] 用户注册 API
- [ ] 用户登录 API
- [ ] 密码加密（BCrypt）
- [ ] 前端登录注册界面

### Day 3：用户中心 + JWT 认证
- [ ] JWT Token 生成与验证
- [ ] Spring Security 配置
- [ ] JWT 过滤器实现
- [ ] 用户信息查询 API
- [ ] 个人中心页面
- [ ] Token 自动刷新机制

### Day 4：匹配系统（上 / 中）
- [ ] 匹配系统架构设计
- [ ] 匹配队列实现（BlockingQueue）
- [ ] 多线程匹配服务
- [ ] 匹配超时处理
- [ ] 匹配 API 接口

### Day 5：匹配系统（下）+ Bot 执行逻辑
- [ ] 对战房间管理
- [ ] 玩家状态同步
- [ ] Bot 执行框架搭建
- [ ] Bot 代码沙箱实现
- [ ] Bot 策略脚本运行

### Day 6：排行榜 + 接口测试 + 部署准备
- [ ] 对战记录保存
- [ ] 积分计算逻辑
- [ ] 排行榜 API
- [ ] 排行榜页面
- [ ] 完整接口测试
- [ ] 性能优化

### Day 7：AcApp 端接入 + OAuth 登录 + 上线总结
- [ ] AcWing OAuth2.0 集成
- [ ] AcApp 端登录对接
- [ ] 项目打包部署
- [ ] 文档完善
- [ ] 项目总结

## 开发进度

```
░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 0%

⏳ Day 1: 项目搭建 + 首页与菜单页 (进行中)
⏳ Day 2: 用户注册 / 登录模块 (待开始)
⏳ Day 3: 用户中心 + JWT 认证 (待开始)
⏳ Day 4: 匹配系统（上 / 中） (待开始)
⏳ Day 5: 匹配系统（下）+ Bot 执行 (待开始)
⏳ Day 6: 排行榜 + 测试 (待开始)
⏳ Day 7: AcApp + OAuth + 上线 (待开始)
```

## 项目状态

- **当前版本**：v0.1.0（0% 完成）
- **项目启动**：2025年10月20日
- **预计完成**：2025年10月27日（7天冲刺）
- **开发状态**：🚀 启动中
- **当前阶段**：Day 1 - 项目初始化与基础架构搭建
- **目标状态**：腾讯面试可展示级后端项目 ✅

## 每日进度更新

### 2025年10月20日（周日）- Day 1 进行中 🚀
- 📌 **当前状态**：项目启动，进行基础环境配置
- 🎯 **今日目标**：完成项目初始化 + Git 配置 + 数据库设计 + 首页菜单
- 💭 **计划内容**：
  - Spring Boot 项目创建
  - Maven 依赖配置
  - MySQL 数据库设计
  - Redis 环境配置
  - Git 仓库初始化
  - 首页与菜单界面
- ⏰ **更新时间**：2025/10/20 项目启动

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
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/kob/backend/
│   │   │       ├── controller/      # 控制层
│   │   │       ├── service/         # 业务逻辑层
│   │   │       ├── mapper/          # 数据访问层
│   │   │       ├── pojo/            # 实体类
│   │   │       ├── config/          # 配置类
│   │   │       ├── utils/           # 工具类
│   │   │       └── BackendApplication.java
│   │   └── resources/
│   │       ├── application.yml      # 配置文件
│   │       ├── mapper/              # MyBatis XML
│   │       └── static/              # 静态资源
│   └── test/                        # 测试代码
├── pom.xml                          # Maven 配置
└── README.md                        # 项目文档
```

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
| **实时通信** | WebSocket | 预留接口 |
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
| 2025/10/21 | ⚙️ | 实现 JWT 登录注册模块 | - |
| 2025/10/22 | 🧩 | 完成个人中心模块 | - |
| 2025/10/23 | 🎯 | 匹配系统开发 | - |
| 2025/10/24 | 🤖 | Bot 执行系统上线 | - |
| 2025/10/25 | 🏆 | 排行榜模块完成 | - |
| 2025/10/26 | 🚀 | 部署准备 + 接口测试 | - |
| 2025/10/27 | ✅ | 项目打包上线 + AcApp 接入 | - |

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

