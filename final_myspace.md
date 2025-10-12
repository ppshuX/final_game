# MySpace 社交平台 (Final_MySpace)

## 项目简介

这是一个基于 Vue3 开发的现代社交媒体平台，旨在重现经典社交网络 MySpace 的核心功能。项目采用前后端分离架构，前端使用 Vue3 + Vue Router + Vuex + Bootstrap 5 构建响应式用户界面。

本项目是 **Final 系列项目**的一部分，专注于构建一个功能完整、用户体验优秀的社交网络应用。

## 项目特性

- 🎨 **现代化 UI**：基于 Bootstrap 5 构建的响应式界面
- 👤 **用户系统**：完整的用户注册、登录和个人资料管理
- 📝 **动态发布**：支持用户发布和浏览动态内容
- 👥 **社交功能**：关注/取消关注用户，查看好友列表
- 🎯 **组件化开发**：Vue3 Composition API 实现模块化架构
- 🚀 **状态管理**：Vuex 全局状态管理
- 🔀 **路由管理**：Vue Router 实现单页应用导航

## 技术栈

- **前端框架**：Vue 3.2.13
- **状态管理**：Vuex 4.0.0
- **路由管理**：Vue Router 4.0.3
- **UI 框架**：Bootstrap 5.3.8
- **构建工具**：Vue CLI 5.0.0
- **JavaScript**：ES6+
- **包管理器**：npm

## 项目结构

```
final_myspace/
├── public/                # 静态资源
│   └── images/           # 图片资源
│       └── avatar.png    # 用户头像
├── src/                  # 源代码目录
│   ├── assets/           # 资源文件
│   │   └── logo.png     # 项目 Logo
│   ├── components/       # 可复用组件
│   │   ├── ContentBase.vue         # 内容容器基础组件
│   │   ├── NavBar.vue              # 导航栏组件
│   │   ├── UserProfileInfo.vue     # 用户信息卡片组件
│   │   ├── UserProfilePosts.vue    # 用户动态列表组件
│   │   └── UserProfileWrite.vue    # 发布动态组件
│   ├── views/            # 页面视图
│   │   ├── HomeView.vue            # 首页
│   │   ├── LoginView.vue           # 登录页
│   │   ├── RegisterVIew.vue        # 注册页
│   │   ├── UserListView.vue        # 好友列表页
│   │   ├── UserProfileView.vue     # 用户个人主页
│   │   └── NotFoundView.vue        # 404 页面
│   ├── router/           # 路由配置
│   │   └── index.js     # 路由定义
│   ├── store/            # Vuex 状态管理
│   │   └── index.js     # Store 配置
│   ├── App.vue          # 根组件
│   └── main.js          # 入口文件
├── babel.config.js      # Babel 配置
├── jsconfig.json        # JavaScript 配置
├── vue.config.js        # Vue CLI 配置
├── package.json         # 项目依赖
└── README.md           # 项目文档
```

## 核心功能模块

### 1. 用户系统
- **用户信息展示**：显示用户名、头像、粉丝数量
- **用户资料管理**：个人主页展示和编辑
- **关注系统**：关注/取消关注其他用户
- **粉丝统计**：实时更新粉丝数量

### 2. 动态系统
- **发布动态**：用户可以发布文字内容
- **动态展示**：瀑布流展示用户动态
- **动态管理**：发布、删除、编辑动态（开发中）

### 3. 社交互动
- **好友列表**：查看所有关注的用户
- **动态浏览**：查看其他用户的动态
- **互动功能**：点赞、评论（计划中）

### 4. 路由系统
- `/` - 首页
- `/userlist` - 好友列表
- `/userprofile` - 用户个人主页
- `/login` - 登录页面
- `/register` - 注册页面
- `/404` - 404 错误页面

## 快速开始

### 环境要求

- Node.js 14.0 或更高版本
- npm 6.0 或更高版本

### 安装步骤

1. **克隆项目**

   ```bash
   git clone https://gitee.com/ppshux/final_myspace.git
   cd final_myspace
   ```

2. **安装依赖**

   ```bash
   npm install
   ```

3. **启动开发服务器**

   ```bash
   npm run serve
   ```

   项目将在 `http://localhost:8080` 启动

4. **构建生产版本**

   ```bash
   npm run build
   ```

5. **代码检查和修复**

   ```bash
   npm run lint
   ```

## 组件说明

### NavBar.vue
导航栏组件，提供全局导航功能：
- 品牌 Logo
- 页面导航链接（首页、好友列表、用户动态）
- 用户操作（登录、注册）
- 响应式设计，支持移动端

### UserProfileInfo.vue
用户信息卡片组件：
- 显示用户头像
- 显示用户名
- 显示粉丝数量
- 关注/取消关注按钮
- 响应式布局

### UserProfilePosts.vue
用户动态列表组件：
- 动态内容展示
- 支持动态列表渲染
- 卡片式设计
- 响应式布局

### UserProfileWrite.vue
发布动态组件：
- 文本输入框
- 发布按钮
- 实时更新动态列表
- 表单验证

### ContentBase.vue
内容容器基础组件：
- 统一的内容区域样式
- 响应式容器
- 可复用的布局组件

## 技术亮点

### 1. Vue3 Composition API
- 使用 `setup()` 函数组织组件逻辑
- `reactive()` 和 `computed()` 实现响应式数据
- 更好的代码复用和逻辑组织

### 2. 组件通信
- **Props**：父组件向子组件传递数据
- **Emit**：子组件向父组件触发事件
- **Vuex**：全局状态管理（待扩展）

### 3. 响应式设计
- Bootstrap 5 栅格系统
- 移动端优先设计
- 响应式导航栏
- 自适应布局

### 4. 路由管理
- History 模式路由
- 路由懒加载（可优化）
- 404 错误处理
- 导航守卫（待实现）

### 5. 组件化架构
- 单文件组件（SFC）
- 可复用的基础组件
- 清晰的组件层次结构
- Props 验证和类型检查

## 开发计划

### 已完成 ✅（约 50%）
- [x] 项目初始化和框架搭建
- [x] 基础路由配置（6个页面）
- [x] 导航栏组件（响应式设计）
- [x] 用户个人主页布局
- [x] 用户信息卡片组件
- [x] 用户动态列表组件
- [x] 发布动态功能（前端逻辑）
- [x] 关注/取消关注功能（前端逻辑）
- [x] 粉丝数量实时更新
- [x] 基础样式和 UI 设计
- [x] Bootstrap 5 集成
- [x] Vuex Store 配置

### 进行中 🚧
- [ ] 登录页面功能实现
- [ ] 注册页面功能实现
- [ ] 好友列表页面内容
- [ ] 首页内容设计和实现

### 计划中 📋

**用户认证系统**
- [ ] JWT Token 认证
- [ ] 用户登录/注册 API 对接
- [ ] 路由权限守卫
- [ ] 登录状态持久化
- [ ] 密码加密和验证

**后端集成**
- [ ] RESTful API 设计
- [ ] 后端服务器搭建
- [ ] 数据库设计和实现
- [ ] API 接口对接
- [ ] 数据持久化

**社交功能增强**
- [ ] 好友列表数据加载
- [ ] 用户搜索功能
- [ ] 动态点赞功能
- [ ] 动态评论系统
- [ ] 私信功能
- [ ] 消息通知系统

**用户体验优化**
- [ ] 图片上传和预览
- [ ] 个人资料编辑
- [ ] 头像上传功能
- [ ] 动态加载（懒加载）
- [ ] 下拉刷新
- [ ] 无限滚动
- [ ] 加载动画和过渡效果

**功能完善**
- [ ] 动态删除功能
- [ ] 动态编辑功能
- [ ] 用户设置页面
- [ ] 深色模式支持
- [ ] 多语言支持

**性能优化**
- [ ] 路由懒加载
- [ ] 组件按需加载
- [ ] 图片懒加载
- [ ] 打包优化
- [ ] CDN 加速
- [ ] Service Worker（PWA）

**测试和部署**
- [ ] 单元测试
- [ ] 集成测试
- [ ] E2E 测试
- [ ] 项目部署
- [ ] CI/CD 配置

## 使用说明

### 用户个人主页
1. 访问 `/userprofile` 查看用户个人主页
2. 左侧显示用户信息和发布动态区域
3. 右侧显示用户已发布的动态列表
4. 点击 "Follow" 按钮关注用户
5. 点击 "UnFollow" 按钮取消关注
6. 在输入框中输入内容并发布动态

### 导航使用
- 点击导航栏的 "首页" 跳转到主页
- 点击 "好友列表" 查看关注的用户
- 点击 "用户动态" 查看个人主页
- 点击 "登录" 或 "注册" 进入相应页面

## 数据结构

### 用户对象（User）
```javascript
{
  id: Number,              // 用户 ID
  username: String,        // 用户名
  lastName: String,        // 姓氏
  firstName: String,       // 名字
  followerCount: Number,   // 粉丝数量
  is_followed: Boolean     // 是否已关注
}
```

### 动态对象（Post）
```javascript
{
  id: Number,          // 动态 ID
  userId: Number,      // 发布用户 ID
  content: String      // 动态内容
}
```

### 动态列表（Posts）
```javascript
{
  count: Number,       // 动态总数
  posts: Array         // 动态数组
}
```

## 贡献指南

欢迎提交 Issue 和 Pull Request 来改进这个项目！

### 贡献步骤
1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 代码规范
- 遵循 ESLint 规则
- 使用 Vue3 Composition API
- 组件命名使用 PascalCase
- 变量命名使用 camelCase
- 添加必要的注释

## 版本信息

- **当前版本**：v0.5.0（50% 完成度）
- **最后更新**：2025年10月12日
- **开发状态**：开发中
- **兼容性**：现代浏览器（Chrome 90+, Firefox 88+, Safari 14+, Edge 90+）

## 更新日志

### v0.5.0 (2025-10-12) 🚧
- ✅ **项目框架搭建**：完成 Vue3 + Vue Router + Vuex 基础架构
- ✅ **UI 框架集成**：集成 Bootstrap 5.3.8
- ✅ **路由系统**：实现 6 个页面路由
- ✅ **导航组件**：完成响应式导航栏
- ✅ **用户个人主页**：实现用户信息展示、动态发布、关注功能
- ✅ **组件化开发**：完成 5 个核心组件开发
- 🎨 **现代化 UI**：Bootstrap 5 响应式设计
- 🎯 **Composition API**：使用 Vue3 最新特性

### v0.1.0 (2025-10-10)
- ✨ 项目初始化
- 📦 依赖安装和配置
- 🎨 基础项目结构搭建

## 浏览器支持

| Chrome | Firefox | Safari | Edge |
|:------:|:-------:|:------:|:----:|
| 90+    | 88+     | 14+    | 90+  |

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 致谢

- [Vue.js](https://vuejs.org/) - 渐进式 JavaScript 框架
- [Bootstrap](https://getbootstrap.com/) - 强大的前端框架
- [Vue Router](https://router.vuejs.org/) - Vue.js 官方路由
- [Vuex](https://vuex.vuejs.org/) - Vue.js 状态管理

## 联系方式

- **项目仓库**：https://gitee.com/ppshux/final_myspace
- **Final 系列项目**：Final_KOF ✅ | Final_MySpace 🚧

---

> "连接世界，分享生活 — MySpace 让社交更简单。"

---

⭐ 如果这个项目对你有帮助，请给它一个 Star！

## 开发进度

```
████████████████████░░░░░░░░░░░░░░░░░░░░ 50% 完成

✅ 前端框架搭建
✅ 核心组件开发
✅ 基础功能实现
🚧 用户认证系统
🚧 后端 API 集成
⏳ 高级功能开发
⏳ 性能优化
⏳ 测试和部署
```

## 🔗 相关项目

- **Final_KOF** - 拳皇格斗游戏 (已完成 ✅)
- **Final_MySpace** - 社交空间平台 (进行中 🚧 - 50%)
- **更多项目敬请期待...**

---

> "From KOF battles to social connections — this is where code meets communities." 💭  
> "从格斗游戏到社交平台 — 每一个项目都是技术成长的见证。" 🚀
