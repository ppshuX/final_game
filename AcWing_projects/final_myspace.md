# MySpace 社交平台 (Final_MySpace)

## 项目简介

这是一个基于 Vue3 开发的现代社交媒体平台，完美重现了经典社交网络 MySpace 的核心功能。项目采用前后端分离架构，前端使用 Vue3 + Vue Router + Vuex + Bootstrap 5 构建响应式用户界面，后端通过 RESTful API 实现完整的数据交互和持久化存储。

本项目是 **Final 系列项目**的完整实现，展示了从用户认证、社交互动到数据管理的完整社交网络应用开发流程。

## 项目特性

- 🎨 **现代化 UI**：基于 Bootstrap 5 构建的响应式界面，完美适配各种设备
- 👤 **完整用户系统**：用户注册、登录、JWT Token 认证、个人资料管理
- 📝 **动态发布系统**：支持用户发布、删除动态内容，数据持久化存储
- 👥 **社交互动功能**：关注/取消关注用户，实时更新粉丝数量
- 🎯 **组件化开发**：Vue3 Composition API 实现高度模块化架构
- 🚀 **状态管理**：Vuex 全局状态管理，登录状态持久化
- 🔀 **路由管理**：Vue Router 实现单页应用导航，支持动态路由参数
- 🔐 **安全认证**：JWT Token 自动刷新机制，请求头自动携带认证信息
- 💾 **数据持久化**：所有操作通过后端 API 保存到数据库

## 技术栈

### 前端技术
- **前端框架**：Vue 3.2.13
- **状态管理**：Vuex 4.0.0
- **路由管理**：Vue Router 4.0.3
- **UI 框架**：Bootstrap 5.3.8
- **HTTP 请求**：jQuery Ajax 3.7.1
- **JWT 解析**：jwt-decode 4.0.0
- **构建工具**：Vue CLI 5.0.0
- **JavaScript**：ES6+
- **包管理器**：npm

### 后端技术
- **API 服务器**：RESTful API（第三方提供）
- **认证方式**：JWT (JSON Web Token)
- **数据库**：MySQL（通过后端 API 访问）

### 项目架构
- **架构模式**：前后端分离
- **通信方式**：RESTful API + JWT Token
- **状态管理**：Vuex 集中式状态管理
- **组件设计**：单一职责原则，组件高度解耦

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
│   │   ├── NavBar.vue              # 导航栏组件（支持登录状态切换）
│   │   ├── UserProfileInfo.vue     # 用户信息卡片组件
│   │   ├── UserProfilePosts.vue    # 用户动态列表组件（支持删除）
│   │   └── UserProfileWrite.vue    # 发布动态组件
│   ├── views/            # 页面视图
│   │   ├── HomeView.vue            # 首页
│   │   ├── LoginView.vue           # 登录页（JWT 认证）
│   │   ├── RegisterVIew.vue        # 注册页
│   │   ├── UserListView.vue        # 好友列表页
│   │   ├── UserProfileView.vue     # 用户个人主页（完整功能）
│   │   └── NotFoundView.vue        # 404 页面
│   ├── router/           # 路由配置
│   │   └── index.js     # 路由定义（支持动态参数）
│   ├── store/            # Vuex 状态管理
│   │   ├── index.js     # Store 主配置
│   │   └── user.js      # 用户模块（登录、登出、Token 管理）
│   ├── App.vue          # 根组件
│   └── main.js          # 入口文件
├── babel.config.js      # Babel 配置
├── jsconfig.json        # JavaScript 配置
├── vue.config.js        # Vue CLI 配置
├── package.json         # 项目依赖
└── README.md           # 项目文档
```

## 核心功能模块

### 1. 用户认证系统 ✅
- **用户登录**：支持用户名和密码登录
- **JWT Token 认证**：使用 JWT Token 进行身份验证
- **Token 自动刷新**：每 4.5 分钟自动刷新 Access Token
- **登录状态管理**：Vuex 全局管理用户登录状态
- **路由保护**：根据登录状态显示不同的导航菜单
- **自动登出**：Token 过期后自动退出登录

### 2. 用户系统 ✅
- **用户信息展示**：显示用户名、头像、粉丝数量
- **个人主页**：支持动态路由参数访问任意用户主页
- **用户资料加载**：从后端 API 获取用户详细信息
- **关注系统**：完整的关注/取消关注功能
- **粉丝统计**：实时更新并同步到后端数据库

### 3. 动态系统 ✅
- **发布动态**：用户可以在自己的主页发布文字内容
- **动态展示**：以时间倒序展示用户所有动态
- **删除动态**：支持删除自己发布的动态
- **数据持久化**：所有操作通过后端 API 保存到数据库
- **实时更新**：发布/删除后立即更新前端显示
- **权限控制**：只有在自己的主页才能发布动态

### 4. 社交互动 ✅
- **关注用户**：通过后端 API 关注其他用户
- **取消关注**：通过后端 API 取消关注
- **粉丝数量实时更新**：操作成功后立即更新显示
- **好友列表**：查看所有关注的用户
- **访问其他用户主页**：点击用户名跳转到对应主页

### 5. 路由系统 ✅
- `/` - 首页
- `/userlist` - 好友列表
- `/userprofile/:userId` - 用户个人主页（动态路由）
- `/login` - 登录页面
- `/register` - 注册页面
- `/404` - 404 错误页面
- `/:catchAll(.*)` - 所有未匹配路由重定向到 404

## 快速开始

### 环境要求

- Node.js 14.0 或更高版本
- npm 6.0 或更高版本
- 现代浏览器（Chrome 90+, Firefox 88+, Safari 14+, Edge 90+）

### 安装步骤

1. **克隆项目**

   ```bash
   git clone https://gitee.com/ppshux/final_myspace
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

### NavBar.vue - 导航栏组件
**功能特性**：
- 响应式设计，支持移动端折叠菜单
- 根据登录状态显示不同的导航选项
- 未登录时：显示"登录"和"注册"按钮
- 已登录时：显示用户名和"退出"按钮
- 点击用户名跳转到个人主页
- 退出时清空 Vuex 中的用户状态

### UserProfileInfo.vue - 用户信息卡片
**功能特性**：
- 显示用户头像（圆角设计）
- 显示用户名
- 显示粉丝数量（实时更新）
- 关注/取消关注按钮
- 父子组件通信（emit 事件）

### UserProfilePosts.vue - 动态列表组件
**功能特性**：
- 卡片式展示所有动态
- 每条动态右侧有删除按钮
- 调用后端 API 删除动态
- 删除成功后通知父组件更新列表
- 响应式布局

### UserProfileWrite.vue - 发布动态组件
**功能特性**：
- 多行文本输入框
- 发布按钮
- v-model 双向绑定
- 发布后自动清空输入框
- 通过 emit 将内容传递给父组件

### UserProfileView.vue - 个人主页视图
**功能特性**：
- 左侧：用户信息卡片 + 发布动态区域（仅自己可见）
- 右侧：用户动态列表
- 从路由参数获取用户 ID
- 调用后端 API 加载用户信息和动态列表
- 实现关注、发帖、删帖的完整业务逻辑
- 使用 computed 判断是否为当前登录用户

### ContentBase.vue - 内容容器组件
**功能特性**：
- 统一的内容区域样式
- 卡片式容器设计
- slot 插槽支持任意内容
- 响应式布局

## 技术亮点

### 1. Vue3 Composition API
- 使用 `setup()` 函数组织组件逻辑
- `reactive()` 创建响应式对象
- `computed()` 实现计算属性
- `ref()` 创建响应式基本类型
- 更好的代码复用和逻辑组织

### 2. Vuex 模块化状态管理
- **模块化设计**：user 模块独立管理用户状态
- **state**：存储用户 ID、用户名、Token 等信息
- **mutations**：updateUser、updateAccess、logout
- **actions**：login 异步登录操作
- **JWT Token 管理**：自动解析和刷新 Token

### 3. 组件通信
- **Props**：父组件向子组件传递数据（user, posts）
- **Emit**：子组件向父组件触发事件（follow, post_a_post, delete_a_post）
- **Vuex**：跨组件共享登录状态

### 4. 后端 API 集成
- **用户认证 API**：
  - `POST /api/token/` - 用户登录获取 Token
  - `POST /api/token/refresh/` - 刷新 Access Token
- **用户信息 API**：
  - `GET /myspace/getinfo/` - 获取用户详细信息
- **动态管理 API**：
  - `GET /myspace/post/` - 获取用户动态列表
  - `POST /myspace/post/` - 发布新动态
  - `DELETE /myspace/post/` - 删除指定动态
- **社交功能 API**：
  - `POST /myspace/follow/` - 关注用户
  - `DELETE /myspace/follow/` - 取消关注用户

### 5. 响应式设计
- Bootstrap 5 栅格系统（col-3 / col-9 布局）
- 移动端优先设计
- 响应式导航栏（navbar-toggler）
- 自适应卡片布局

### 6. 路由管理
- History 模式路由
- 动态路由参数（`:userId`）
- 404 错误处理（catchAll）
- 编程式导航（router.push）

### 7. 安全机制
- JWT Token 认证
- 请求头自动携带 Authorization
- Token 自动刷新（4.5 分钟间隔）
- 登出时清空敏感信息

## 使用说明

### 1. 用户登录
1. 访问 `/login` 登录页面
2. 输入用户名和密码
3. 点击"登录"按钮
4. 登录成功后跳转到好友列表页
5. 导航栏显示用户名和"退出"按钮

### 2. 查看个人主页
1. 登录后点击导航栏右上角的**用户名**
2. 自动跳转到自己的个人主页（`/userprofile/你的ID/`）
3. 左侧显示用户信息和发布动态区域
4. 右侧显示所有已发布的动态

### 3. 发布动态
1. 在个人主页左侧找到"Edit Your Posts Here"文本框
2. 输入想要发布的内容
3. 点击"发帖"按钮
4. 动态立即出现在右侧列表顶部
5. 数据已保存到数据库

### 4. 删除动态
1. 在个人主页右侧找到想要删除的动态
2. 点击动态右侧的"删除"按钮
3. 动态立即从列表中移除
4. 数据库中的记录也被删除

### 5. 关注其他用户
1. 访问其他用户的个人主页（通过链接或好友列表）
2. 左侧用户信息卡片显示"Follow"按钮
3. 点击按钮关注该用户
4. 粉丝数量自动增加
5. 按钮变为"UnFollow"

### 6. 退出登录
1. 点击导航栏右上角的"退出"链接
2. 清空登录状态
3. 导航栏恢复为"登录"和"注册"按钮

## 数据结构

### Vuex User State
```javascript
{
  id: String,              // 用户 ID
  username: String,        // 用户名
  photo: String,           // 头像 URL
  followerCount: Number,   // 粉丝数量
  access: String,          // JWT Access Token
  refresh: String,         // JWT Refresh Token
  is_login: Boolean        // 登录状态
}
```

### 用户对象（User）
```javascript
{
  id: Number,              // 用户 ID
  username: String,        // 用户名
  photo: String,           // 头像 URL
  followerCount: Number,   // 粉丝数量
  is_followed: Boolean     // 是否已关注
}
```

### 动态对象（Post）
```javascript
{
  id: Number,          // 动态 ID（数据库主键）
  userId: Number,      // 发布用户 ID
  content: String      // 动态内容
}
```

### 动态列表（Posts）
```javascript
{
  count: Number,       // 动态总数
  posts: Array<Post>   // 动态数组
}
```

## API 接口说明

### 认证接口
- **POST** `/api/token/`
  - 请求：`{ username, password }`
  - 响应：`{ access, refresh }`
  
- **POST** `/api/token/refresh/`
  - 请求：`{ refresh }`
  - 响应：`{ access }`

### 用户接口
- **GET** `/myspace/getinfo/`
  - 参数：`user_id`
  - 响应：`{ id, username, photo, followerCount, is_followed }`

### 动态接口
- **GET** `/myspace/post/`
  - 参数：`user_id`
  - 响应：`Array<Post>`
  
- **POST** `/myspace/post/`
  - 请求：`{ content }`
  - 响应：`{ result: "success", post: {...} }`
  
- **DELETE** `/myspace/post/`
  - 请求：`{ post_id }`
  - 响应：`{ result: "success" }`

### 社交接口
- **POST** `/myspace/follow/`
  - 请求：`{ target_id }`
  - 响应：`{ result: "success" }`
  
- **DELETE** `/myspace/follow/`
  - 请求：`{ target_id }`
  - 响应：`{ result: "success" }`

## 项目完成清单

### 已完成 ✅（100%）

**项目架构**
- [x] 项目初始化和框架搭建
- [x] Vue3 + Vue Router + Vuex 完整配置
- [x] Bootstrap 5 UI 框架集成
- [x] jQuery 和 jwt-decode 依赖配置

**路由系统**
- [x] 6 个页面路由配置
- [x] 动态路由参数支持（`:userId`）
- [x] 404 错误处理
- [x] 路由导航守卫

**用户认证**
- [x] 登录页面 UI 和功能
- [x] JWT Token 认证实现
- [x] Token 自动刷新机制
- [x] Vuex 用户状态管理
- [x] 登出功能

**导航系统**
- [x] 响应式导航栏组件
- [x] 根据登录状态动态显示菜单
- [x] 用户名点击跳转个人主页
- [x] 移动端适配

**用户系统**
- [x] 用户信息卡片组件
- [x] 用户个人主页视图
- [x] 从后端 API 加载用户数据
- [x] 粉丝数量实时显示

**动态系统**
- [x] 发布动态组件
- [x] 动态列表展示组件
- [x] 发布动态功能（后端 API）
- [x] 删除动态功能（后端 API）
- [x] 数据持久化存储
- [x] 实时更新前端显示

**社交功能**
- [x] 关注用户功能（后端 API）
- [x] 取消关注功能（后端 API）
- [x] 粉丝数量同步更新
- [x] 关注状态显示

**组件开发**
- [x] ContentBase 容器组件
- [x] NavBar 导航栏组件
- [x] UserProfileInfo 用户信息组件
- [x] UserProfilePosts 动态列表组件
- [x] UserProfileWrite 发布动态组件

**状态管理**
- [x] Vuex Store 模块化配置
- [x] User 模块完整实现
- [x] mutations 和 actions 定义
- [x] 组件与 Store 的集成

**样式设计**
- [x] Bootstrap 5 响应式布局
- [x] 自定义样式优化
- [x] 移动端适配
- [x] 卡片式设计

**后端集成**
- [x] RESTful API 完整对接
- [x] JWT Token 请求头配置
- [x] 错误处理和用户提示
- [x] 数据双向同步

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
- 保持代码整洁和可维护性

## 版本信息

- **当前版本**：v1.0.0（100% 完成 🎉）
- **最后更新**：2025年10月12日
- **开发状态**：已完成 ✅
- **兼容性**：现代浏览器（Chrome 90+, Firefox 88+, Safari 14+, Edge 90+）

### 项目规模（实际统计）
- **核心代码文件**: 17 个
  - Vue 组件: 12 个（ContentBase、NavBar、UserProfileInfo、UserProfilePosts、UserProfileWrite、HomeView、LoginView、RegisterView、UserListView、UserProfileView、NotFoundView、App）
  - Vuex 模块: 2 个（index.js、user.js）
  - Router 配置: 1 个（router/index.js）
  - Main 入口: 1 个（main.js）
  - 配置文件: 1 个（vue.config.js）
- **估计代码行数**: 3,000+ 行
- **API 接口调用**: 10+ 个
- **路由页面**: 6 个

## 更新日志

### v1.0.0 (2025-10-12) 🎉
- ✅ **项目完成**：所有核心功能开发完成
- ✅ **用户认证系统**：JWT Token 登录、自动刷新、登出
- ✅ **动态系统完整实现**：发布、删除、持久化存储
- ✅ **社交功能完整实现**：关注、取消关注、粉丝统计
- ✅ **后端 API 完整对接**：所有接口测试通过
- ✅ **状态管理优化**：Vuex 模块化管理用户状态
- ✅ **导航系统完善**：根据登录状态动态显示
- ✅ **Bug 修复**：解决 store.state 访问错误
- ✅ **功能优化**：发帖后立即可删除，数据实时同步
- 🎨 **UI 完善**：Bootstrap 5 完整响应式设计
- 🔐 **安全加固**：JWT 认证、Token 自动刷新

### v0.5.0 (2025-10-11) 🚧
- ✅ **项目框架搭建**：完成 Vue3 + Vue Router + Vuex 基础架构
- ✅ **UI 框架集成**：集成 Bootstrap 5.3.8
- ✅ **路由系统**：实现 6 个页面路由
- ✅ **导航组件**：完成响应式导航栏
- ✅ **用户个人主页**：实现用户信息展示、动态发布、关注功能
- ✅ **组件化开发**：完成 5 个核心组件开发

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
- [jQuery](https://jquery.com/) - 快速的 JavaScript 库
- [jwt-decode](https://github.com/auth0/jwt-decode) - JWT Token 解析库

## 联系方式

- **项目仓库**：[https://gitee.com/ppshux/final_myspace](https://gitee.com/ppshux/final_myspace)
- **Final 系列项目**：Final_KOF ✅ | Final_MySpace ✅

---

> "连接世界，分享生活 — MySpace 让社交更简单。"

---

⭐ 如果这个项目对你有帮助，请给它一个 Star！

## 开发进度

```
████████████████████████████████████████ 100% 完成 ✅

✅ 前端框架搭建
✅ 核心组件开发
✅ 基础功能实现
✅ 用户认证系统
✅ 后端 API 集成
✅ 社交功能开发
✅ 状态管理优化
✅ 测试和调试
```

## 项目截图

### 登录页面
用户可以通过用户名和密码登录系统，支持 JWT Token 认证。

### 个人主页
- 左侧：用户信息卡片，显示头像、用户名、粉丝数量，以及关注/取消关注按钮
- 左侧：发布动态区域（仅在自己的主页显示）
- 右侧：动态列表，显示所有已发布的动态，每条动态都有删除按钮

### 导航栏
- 未登录：显示"首页"、"好友列表"、"登录"、"注册"
- 已登录：显示"首页"、"好友列表"、用户名（点击跳转到个人主页）、"退出"

## 功能演示

1. **用户登录** → JWT Token 认证 → 自动跳转到好友列表
2. **点击用户名** → 访问个人主页 → 显示用户信息和动态
3. **发布动态** → 输入内容 → 点击发帖 → 立即显示在列表顶部
4. **删除动态** → 点击删除按钮 → 动态从列表中移除
5. **关注用户** → 点击 Follow 按钮 → 粉丝数量增加
6. **取消关注** → 点击 UnFollow 按钮 → 粉丝数量减少
7. **退出登录** → 点击退出 → 清空登录状态 → 返回未登录界面

---

## 技术总结

### 前端亮点
- ✅ Vue3 Composition API 完整应用
- ✅ Vuex 模块化状态管理
- ✅ 组件化开发和父子通信
- ✅ 响应式设计和移动端适配
- ✅ 路由动态参数和编程式导航

### 后端集成
- ✅ RESTful API 完整对接
- ✅ JWT Token 认证和自动刷新
- ✅ 请求头自动携带 Authorization
- ✅ 数据持久化和双向同步

### 项目特色
- ✅ 完整的用户认证流程
- ✅ 实时的社交互动功能
- ✅ 优雅的错误处理和用户提示
- ✅ 清晰的代码结构和注释
- ✅ 可扩展的模块化设计

---

## 🔗 相关项目（Final 系列）

| 序号 | 项目 | 状态 | 技术栈 |
|------|------|------|--------|
| 1 | Final_KOF | ✅ 100% | JavaScript + Canvas |
| 2 | **Final_MySpace** | ✅ 100% | **Vue3 + Vuex** |
| 3 | Final_AcApp | ✅ 100% | Django + Channels |
| 4 | Final_KOB | ✅ 100% | Spring Boot + 微服务 |

**Final 系列全部完成！** 🎉

---

> "From KOF battles to social connections — this is where code meets communities." 💭  
> "从格斗游戏到社交平台 — 每一个项目都是技术成长的见证。" 🚀

---

🎉 **项目已完成！感谢使用 MySpace 社交平台！**
