# 📅 Day 10: AcWing 平台集成与三客户端架构完成

> **日期**：2025年11月6日  
> **用时**：全天（约8小时）  
> **难度**：⭐⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 今日目标

### 核心任务
1. ✅ 实现完整的三客户端架构
2. ✅ 开发 AcWing 平台端（Vue3 + Vuex）
3. ✅ 部署到 AcWing 服务器
4. ✅ 验证三端数据同步

### 完成情况

**进度**：100% ✅  
**Git 提交**：20+ 次  
**新增代码**：2000+ 行  
**构建次数**：15+ 次  
**部署次数**：5+ 次

---

## 🏗️ 三客户端架构设计

### 架构全景图

```
┌─────────────────────────────────────────────────────────────┐
│              三客户端统一架构 (Three-Client Architecture)      │
└─────────────────────────────────────────────────────────────┘

    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
    │ Android App  │    │   Web App    │    │ AcWing App   │
    │  (adapp/)    │    │   (web/)     │    │  (acapp/)    │
    ├──────────────┤    ├──────────────┤    ├──────────────┤
    │   Kotlin     │    │  Vue3+Vite   │    │ Vue3+VueCLI  │
    │   + Room     │    │ + Bootstrap  │    │   + Vuex     │
    │  Material    │    │+ FullCalendar│    │  Scoped CSS  │
    └──────┬───────┘    └──────┬───────┘    └──────┬───────┘
           │                   │                   │
           │       HTTP/REST API (JSON)            │
           └───────────────────┼───────────────────┘
                               │
                   ┌───────────▼────────────┐
                   │   Django Backend       │
                   │   (backend/)           │
                   ├────────────────────────┤
                   │ - Event CRUD API       │
                   │ - Lunar Calendar API   │
                   │ - iCalendar Service    │
                   │ - CORS Configuration   │
                   └───────────┬────────────┘
                               │
                   ┌───────────▼────────────┐
                   │   SQLite Database      │
                   │   (共享数据源)         │
                   └────────────────────────┘
```

### 目录结构重构

**重命名**：
- `acapp/` → `adapp/` (Android Development App)

**新建**：
- `acapp/` (AcWing App - 只保留 README 和构建产物)
- `acapp_frontend/` (AcWing 源码 - 不提交到 Git)

**最终结构**：

```
KotlinCalendar/
├── adapp/                      # Android 客户端（重命名）
│   └── README.md              # 说明文档（源码不上传）
│
├── web/                        # Web 客户端
│   ├── calendar_web/          # Vue3 源码
│   └── README.md
│
├── acapp/                      # AcWing 客户端
│   ├── static/                # 构建产物（提交 Git）
│   │   ├── js/app.js         # 120 KB
│   │   └── css/app.css       # 9.5 KB
│   └── README.md
│
├── acapp_frontend/            # AcWing 源码（本地开发）
│   ├── src/
│   │   ├── views/
│   │   │   └── MainView.vue
│   │   ├── components/
│   │   │   ├── CalendarGrid.vue
│   │   │   ├── CalendarHeader.vue
│   │   │   ├── CalendarGridView.vue
│   │   │   ├── TodayCard.vue
│   │   │   ├── ToolBar.vue
│   │   │   ├── EventList.vue
│   │   │   ├── EventDetail.vue
│   │   │   └── AddEventForm.vue
│   │   ├── store/
│   │   │   └── index.js       # Vuex 状态管理
│   │   ├── assets/
│   │   │   └── scripts/
│   │   │       └── Calendar.js # 导出类
│   │   └── App.vue
│   ├── public/
│   ├── vue.config.js          # Vue CLI 配置
│   └── package.json
│
└── backend/                    # Django 后端
    └── (保持不变)
```

---

## 🔧 AcWing 前端开发

### 1. 技术栈选型

| 技术 | 选型 | 原因 |
|-----|------|------|
| **框架** | Vue3 + Vue CLI | AcWing 官方推荐 |
| **构建** | Vue CLI Library Mode | 单文件 ES Module 输出 |
| **状态** | Vuex 4 | 模拟路由系统 |
| **样式** | Scoped CSS | 避免全局污染 |
| **路由** | Vuex 模拟 | 参考 AcWing 课件 |

**为什么不用 Vue Router？**
- AcWing 平台只需要单页应用
- Vuex 更轻量级
- 参考官方课件设计

### 2. Vuex 模拟路由系统（创新设计）

#### 核心原理

```javascript
// store/index.js
export default createStore({
  state: {
    router_name: 'calendar',      // 当前视图名称
    router_params: {},            // 视图参数
  },
  mutations: {
    updateRouterName(state, router_name) {
      state.router_name = router_name
    },
    updateRouterParams(state, router_params) {
      state.router_params = router_params
    },
  },
})
```

#### 使用方式

```vue
<!-- MainView.vue -->
<template>
  <CalendarGrid v-if="router_name === 'calendar'" />
  <EventList v-else-if="router_name === 'event_list'" />
  <EventDetail v-else-if="router_name === 'event_detail'" />
  <AddEventForm v-else-if="router_name === 'add_event'" />
</template>

<script>
export default {
  computed: {
    router_name() {
      return this.$store.state.router_name
    }
  }
}
</script>
```

#### 路由跳转

```javascript
// 跳转到事件列表
this.$store.commit('updateRouterName', 'event_list')

// 跳转到事件详情，带参数
this.$store.commit('updateRouterParams', { id: 123 })
this.$store.commit('updateRouterName', 'event_detail')
```

**优势**：
- ✅ 轻量级（无需 Vue Router）
- ✅ 简单明了
- ✅ 易于维护
- ✅ 符合 AcWing 平台特性

### 3. 组件架构设计

#### 分层架构

```
MainView (视图层)
├── CalendarGrid (容器组件)
│   ├── CalendarHeader (头部导航)
│   ├── CalendarGridView (日历网格)
│   ├── TodayCard (今日信息)
│   └── ToolBar (工具栏)
├── EventList (列表视图)
├── EventDetail (详情视图)
└── AddEventForm (表单视图)
```

#### 组件职责

**MainView.vue** - 视图容器
- 路由控制（根据 `router_name` 切换）
- 组件组装
- 布局管理

**CalendarGrid.vue** - 日历容器
```vue
<template>
  <div class="calendar-grid">
    <CalendarHeader />
    <CalendarGridView />
    <TodayCard />
    <ToolBar />
  </div>
</template>
```

**CalendarHeader.vue** - 月份导航
- 上一月/下一月切换
- 显示当前年月
- 今天按钮

**CalendarGridView.vue** - 日历网格
- 7x6 日期网格
- 日期高亮
- 事件标记
- 点击选择日期

**TodayCard.vue** - 今日信息
- 显示当前日期
- 农历信息（待集成）
- 节假日提示

**ToolBar.vue** - 工具栏
- 添加事件按钮
- 查看列表按钮
- 其他操作

**EventList.vue** - 事件列表
- 显示所有事件
- 点击查看详情
- 删除操作

**EventDetail.vue** - 事件详情
- 显示完整信息
- 编辑按钮
- 删除按钮
- 返回按钮

**AddEventForm.vue** - 添加表单
- 标题输入
- 描述输入
- 日期时间选择
- 提交/取消

### 4. ES Module 导出（AcWing 平台要求）

#### Calendar.js 导出类

```javascript
// assets/scripts/Calendar.js
import { createApp } from 'vue'
import App from '@/App.vue'
import store from '@/store'

export class Calendar {
  constructor(parent, AcWingOS) {
    // 处理字符串 ID 参数（AcWing 平台特性）
    if (typeof parent === 'string') {
      this.parent = document.querySelector('#' + parent)
    } else {
      this.parent = parent
    }
    
    this.AcWingOS = AcWingOS
    
    // 创建 Vue 应用
    this.app = createApp(App)
    this.app.use(store)
    this.app.mount(this.parent)
    
    console.log('Calendar initialized successfully')
  }
  
  // 销毁方法
  destroy() {
    this.app.unmount()
  }
}
```

#### Vue CLI 构建配置

```javascript
// vue.config.js
module.exports = {
  publicPath: './',
  outputDir: '../acapp/static/',  // 输出到 acapp/static/
  configureWebpack: {
    entry: './src/assets/scripts/Calendar.js',
    output: {
      filename: 'js/app.js',
      library: {
        type: 'module',  // ES Module 格式
      },
    },
    experiments: {
      outputModule: true,  // 启用 ES Module 实验特性
    },
  },
  css: {
    extract: {
      filename: 'css/app.css',
    },
  },
  chainWebpack: config => {
    config.optimization.delete('splitChunks')  // 单文件输出
  },
}
```

#### AcWing 平台使用方式

```html
<!-- AcWing 平台加载 -->
<script type="module">
  import { Calendar } from 'https://app7626.acapp.acwing.com.cn/static/js/app.js'
  
  class KotlinCalendar extends AcApp {
    constructor(id) {
      super(id)
      this.calendar = new Calendar(id, this.AcWingOS)
    }
  }
</script>
```

### 5. 构建产物

**输出文件**：
- `acapp/static/js/app.js` (120 KB)
- `acapp/static/css/app.css` (9.5 KB)

**构建命令**：
```bash
cd acapp_frontend
npm run build
```

---

## 🚀 服务器部署

### 1. Git 仓库优化

#### .gitignore 配置

```gitignore
# Android 源码（只保留 README）
adapp/*
!adapp/README.md

# AcWing 前端源码（本地开发）
acapp_frontend/

# Web 前端 node_modules
web/calendar_web/node_modules/
web/calendar_web/dist/

# Python
backend/__pycache__/
backend/*.pyc
backend/db.sqlite3
backend/venv/

# IDE
.idea/
.vscode/
*.swp
```

**效果**：
- 优化前：200+ MB
- 优化后：2.2 MB ✅

#### 提交策略

- ✅ Android: 只提交 README 和说明文档
- ✅ AcWing: 只提交构建产物（`acapp/static/`）
- ✅ Web: 提交源码（较小）
- ✅ Backend: 提交源码和配置

### 2. Nginx 配置

#### 添加 AcWing 路径

```nginx
server {
    listen 443 ssl;
    server_name app7626.acapp.acwing.com.cn;
    
    # SSL 证书配置
    ssl_certificate cert/acapp.pem;
    ssl_certificate_key cert/acapp.key;
    
    # Web 端（保持不变）
    location / {
        root /home/acs/KotlinCalendar/web/calendar_web/dist;
        index index.html;
        try_files $uri $uri/ /index.html;
    }
    
    # AcWing 平台端（新增）
    location /acapp/ {
        alias /home/acs/KotlinCalendar/acapp/;
        
        # CORS 配置
        add_header 'Access-Control-Allow-Origin' 'https://www.acwing.com' always;
        add_header 'Access-Control-Allow-Methods' 'GET, OPTIONS' always;
        add_header 'Access-Control-Allow-Headers' '*' always;
    }
    
    # Django API（保持不变）
    location /api/ {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:8000;
    }
}
```

**简化前后对比**：
- 优化前：99 行
- 优化后：66 行
- 删除冗余配置，提高可读性

### 3. Django CORS 配置

```python
# backend/calendar_backend/settings.py

CORS_ALLOWED_ORIGINS = [
    'http://localhost:5173',           # 本地开发
    'https://www.acwing.com',          # AcWing 平台 ✅ 新增
    'https://app7626.acapp.acwing.com.cn',  # 生产环境
]

# 允许携带凭证
CORS_ALLOW_CREDENTIALS = True

# 允许的请求头
CORS_ALLOW_HEADERS = [
    'accept',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
]
```

### 4. 部署流程

```bash
# 1. 本地构建
cd acapp_frontend
npm run build  # 输出到 acapp/static/

# 2. 提交到 Git
git add acapp/static/
git commit -m "build: Update AcWing app"
git push

# 3. 服务器部署
ssh acs@app7626.acapp.acwing.com.cn
cd KotlinCalendar
git pull

# 4. 重启服务
uwsgi --ini uwsgi.ini --daemonize uwsgi.log
sudo nginx -s reload

# 5. 验证
curl https://app7626.acapp.acwing.com.cn/acapp/static/js/app.js
```

---

## 🧪 功能验证

### 1. Web 端农历 API 测试

**问题**：农历 API 返回空值

**原因**：前端传参格式错误

**解决**：

```javascript
// 修复前
const { data } = await api.getLunarDate(year, month, day)
// 传参: ?year=2025&month=11&day=6

// 修复后
const params = { year: 2025, month: 11, day: 6 }
const { data } = await api.get('/lunar/', { params })
// 正确传参
```

**测试结果**：
```json
{
  "year": 2025,
  "month": 9,
  "day": 16,
  "isleap": false,
  "lunarDate": "2025年九月十六"
}
```

### 2. AcWing 平台测试

**测试项**：

| 测试项 | 状态 | 说明 |
|--------|------|------|
| Calendar 类导出 | ✅ | ES Module 正常 |
| 容器 ID 识别 | ✅ | 字符串参数处理成功 |
| CORS 跨域 | ✅ | www.acwing.com 白名单生效 |
| Vue 应用挂载 | ✅ | 正常渲染 |
| Vuex 路由切换 | ✅ | 4 个视图正常切换 |

**调试日志**：

```javascript
console.log('Calendar initialized successfully')
console.log('Parent:', this.parent)
console.log('AcWingOS:', this.AcWingOS)
```

### 3. 三端数据同步验证

**测试场景**：acapp 删除事件 → web 端同步

**步骤**：
1. acapp 端删除事件 ID 123
2. 发送 DELETE 请求到 `/api/events/123/`
3. Django 后端删除数据库记录
4. web 端刷新列表

**结果**：✅ 实时同步成功

**数据流**：

```
acapp (Vue3)
    ↓ DELETE /api/events/123/
Django Backend
    ↓ 删除数据库记录
SQLite Database
    ↓ 数据已删除
web (Vue3) 刷新
    ↓ GET /api/events/
显示最新列表（事件 123 已消失）
```

---

## 🐛 解决的问题

### 问题 1: Vue UI 无法在非 C 盘创建项目

**报错**：
```
Error: EPERM: operation not permitted
```

**原因**：Vue UI 权限限制

**解决方案**：手动创建所有文件

```bash
# 手动初始化项目
npm init vue@latest acapp_frontend
cd acapp_frontend

# 安装依赖
npm install
npm install vuex@next

# 手动创建目录结构
mkdir -p src/{views,components,store,assets/scripts}
```

### 问题 2: Calendar 类导出失败

**报错**：
```
Uncaught SyntaxError: The requested module does not provide an export named 'Calendar'
```

**原因**：构建配置错误

**解决方案**：

```javascript
// vue.config.js
configureWebpack: {
  output: {
    library: {
      type: 'module',  // 必须设置为 module
    },
  },
  experiments: {
    outputModule: true,  // 启用实验特性
  },
}
```

### 问题 3: AcWing 平台空白页面

**现象**：控制台无报错，但页面空白

**调试**：

```javascript
// 添加调试日志
constructor(parent, AcWingOS) {
  console.log('Received parent:', parent, typeof parent)
  console.log('Received AcWingOS:', AcWingOS)
  
  // 发现 parent 是字符串 "calendar-container"
  if (typeof parent === 'string') {
    this.parent = document.querySelector('#' + parent)
    console.log('Found element:', this.parent)
  }
}
```

**解决**：处理字符串 ID 参数 ✅

### 问题 4: CORS 跨域错误

**报错**：
```
Access to XMLHttpRequest blocked by CORS policy
Origin 'https://www.acwing.com' has been blocked
```

**解决**：

```python
# Django settings.py
CORS_ALLOWED_ORIGINS = [
    'https://www.acwing.com',  # 添加到白名单
]
```

### 问题 5: Git 仓库体积过大

**问题**：Android 源码导致仓库 200+ MB

**解决**：

```gitignore
# .gitignore
adapp/*
!adapp/README.md
```

**效果**：2.2 MB ✅

---

## 💡 技术亮点

### 1. 创新的 Vuex 路由设计

**传统方案**：Vue Router
- 需要额外依赖
- 配置复杂
- 打包体积大

**本项目方案**：Vuex 模拟路由
- ✅ 无额外依赖
- ✅ 配置简单
- ✅ 体积更小
- ✅ 更灵活

### 2. 组件化设计原则

**单一职责原则**：
- CalendarHeader 只负责导航
- CalendarGridView 只负责显示日期
- TodayCard 只负责显示信息

**高复用性**：
- 组件独立，易于复用
- Scoped CSS 避免样式污染

### 3. ES Module 单文件构建

**优势**：
- ✅ 符合 AcWing 平台要求
- ✅ 现代化模块系统
- ✅ Tree Shaking 优化
- ✅ 120 KB 紧凑体积

### 4. 三端统一 API 设计

**RESTful API**：
- GET /api/events/ - 获取列表
- POST /api/events/ - 创建事件
- PUT /api/events/:id/ - 更新事件
- DELETE /api/events/:id/ - 删除事件

**优势**：
- ✅ 三端共用同一套 API
- ✅ 标准化接口
- ✅ 易于维护

---

## 📊 工作量统计

### 代码统计

| 指标 | 数量 |
|-----|------|
| **新增组件** | 9 个 |
| **新增代码** | 2000+ 行 |
| **配置文件** | 5 个 |
| **Git 提交** | 20+ 次 |
| **构建次数** | 15+ 次 |
| **部署次数** | 5+ 次 |

### 文件清单

**创建的文档**：
- `ARCHITECTURE.md` - 三客户端架构说明
- `PRODUCT_ROADMAP.md` - 产品商业化规划
- `FUTURE_PLAN.md` - 未来开发计划
- `acapp_frontend/ROUTER_USAGE.md` - 路由使用说明
- `acapp_frontend/VUEX_USAGE.md` - Vuex 使用说明
- `acapp_frontend/ACWING_AUTH.md` - AcWing 登录集成
- `acapp_frontend/QQ_AUTH.md` - QQ 登录集成
- `acapp_frontend/PROJECT_STRUCTURE.md` - 项目结构

**删除的冗余文档**：
- FIX_GIT_CONNECTION.md（临时文档）
- UPLOAD_TO_SERVER.ps1（临时脚本）
- UPLOAD_COMMANDS.txt（临时命令）
- SERVER_STRUCTURE.md（已合并）
- DEPLOY_ACAPP.md（已合并）

---

## 🎉 今日成果

### ✅ 三客户端全部上线

| 客户端 | 技术栈 | 状态 | 访问方式 |
|--------|--------|------|---------|
| **Android** | Kotlin + Room | ✅ 完成 | 本地 APK |
| **Web** | Vue3 + Vite | ✅ 运行中 | https://app7626.acapp.acwing.com.cn/ |
| **AcWing** | Vue3 + VueCLI | ✅ 运行中 | AcWing 平台打开 |
| **Backend** | Django + DRF | ✅ 运行中 | https://app7626.acapp.acwing.com.cn/api/ |

### ✅ 数据同步验证

**实测场景**：
1. acapp 删除事件 → API 删除成功 → web 端同步删除 ✅
2. web 添加事件 → API 创建成功 → acapp 同步显示 ✅
3. Android 本地修改 → 待网络集成（Day 11）

**特点**：
- ✅ 三个客户端共享同一数据库
- ✅ 实时同步，无延迟
- ✅ 数据一致性保证

---

## 🌟 技术多样性总结

### 多语言开发

- **Kotlin** - Android 端（Material Design）
- **Python** - Django 后端（REST API）
- **JavaScript** - Web 端（Vue3）

### 多构建工具

- **Gradle** - Android 构建
- **Vite** - Web 端构建（快速、现代）
- **Vue CLI** - AcWing 端构建（Library 模式）

### 多 UI 方案

- **Material Design** - Android 端
- **Bootstrap 5** - Web 端（响应式）
- **Scoped CSS** - AcWing 端（轻量级）

### 统一后端

- **Django REST Framework** - 一套 API 服务三端
- **CORS 配置** - 支持跨域请求
- **SQLite** - 共享数据源

---

## 📝 待办事项（Day 11）

### 高优先级

1. [ ] 完善 acapp 日历 UI（节假日标注）
2. [ ] 集成农历 API 到今日卡片
3. [ ] 添加节假日数据（2025 年完整）
4. [ ] Android 端网络集成（Retrofit）

### 中优先级

5. [ ] 优化样式和动画
6. [ ] 添加加载状态
7. [ ] 错误处理优化
8. [ ] 用户认证系统（AcWing OAuth）

### 低优先级

9. [ ] 准备演示材料
10. [ ] 撰写项目报告
11. [ ] 性能优化

---

## 🎓 学习收获

### 1. AcWing 平台特性

- ✅ 使用 ES Module 导出类
- ✅ 容器 ID 是字符串参数
- ✅ 需要配置 CORS 允许 `www.acwing.com`
- ✅ 不能使用全局样式库（如 Bootstrap）
- ✅ 需要 Scoped CSS

### 2. Vue CLI Library 模式

```javascript
configureWebpack: {
  output: {
    library: { type: 'module' },
  },
  experiments: {
    outputModule: true,
  },
}
```

### 3. 组件化设计思想

- **View 层**：页面级，负责路由和组装
- **Component 层**：功能级，可复用
- **单一职责**：每个组件只做一件事
- **便于维护**：修改不影响其他组件

### 4. Git 部署策略

- **源码**：本地开发（不提交大文件）
- **构建产物**：提交 Git
- **服务器**：`git pull` 自动部署
- **优势**：简单、高效

---

## 🎊 总结

### 今天完成了从 0 到 1 的 AcWing 平台集成！

**成就**：
- ✅ 从创建项目 → 开发 → 构建 → 部署 → 上线
- ✅ 解决了 10+ 个技术问题
- ✅ 实现了三客户端数据同步
- ✅ 验证了产品可行性
- ✅ 完成了架构重构

**这是一个里程碑式的进展！** 🚀

### 技术成长

**掌握的技能**：
1. Vue CLI Library 模式开发
2. ES Module 构建配置
3. Vuex 路由设计
4. CORS 跨域配置
5. Nginx 反向代理
6. Git 仓库优化

**工程化能力**：
- ✅ 模块化开发
- ✅ 自动化部署
- ✅ 版本控制
- ✅ 文档驱动

---

**工作时长**: ~8 小时  
**代码行数**: 2000+ 行  
**Git 提交**: 20+ 次  
**解决问题**: 10+ 个  

**辛苦了！今天的工作非常高效！Day 10 完美收官！** 💪🔥

