# 📅 Day 12: AcWing OAuth2 一键登录实现

> **日期**：2025年11月7日  
> **用时**：约3小时  
> **难度**：⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 今日目标

### 核心任务
1. ✅ 实现 AcApp 端 AcWing OAuth2 一键登录
2. ✅ 集成 JWT Token 认证体系
3. ✅ Vuex Store 模块化重构
4. ✅ Token 有效性验证机制
5. ✅ 完整的授权流程测试

### 完成情况

**进度**：100% ✅  
**Git 提交**：5 次  
**解决的 Bug**：5 个  
**新增文件**：4 个  
**修改文件**：10+ 个

---

## 🔐 AcWing OAuth2 授权流程

### 1. OAuth2 授权码模式

#### 完整流程图

```
┌─────────────────────────────────────────────────────────────┐
│              AcWing OAuth2 Authorization Flow                │
└─────────────────────────────────────────────────────────────┘

[1] 用户打开 AcApp
        ↓
[2] 检查本地是否有 access_token
        ↓
   有 token → 验证 token 有效性
        ↓               ↓
      有效           无效/不存在
        ↓               ↓
   加载事件      [3] 调用 AcWingOS.api.oauth2.authorize()
                        ↓
                 [4] 用户在 AcWing 授权页面授权
                        ↓
                 [5] AcWing 重定向到 redirect_uri
                     携带 code 和 state 参数
                        ↓
                 [6] 后端 /api/oauth2/receive_code/
                     返回 JSON: {code, state}
                        ↓
                 [7] 前端收到回调，调用 /api/auth/acwing/login/
                     POST {code, state}
                        ↓
                 [8] 后端用 code 换取 access_token 和 openid
                        ↓
                 [9] 后端用 access_token 获取用户信息
                        ↓
                 [10] 后端创建/更新 AcWingUser
                      生成 JWT token
                        ↓
                 [11] 前端保存 JWT token 到 localStorage
                        ↓
                 [12] 跳转到主界面，加载事件
```

### 2. 关键技术要点

#### AcWingOS OAuth2 API

```javascript
// 调用授权 API
this.AcWingOS.api.oauth2.authorize(
  appid,           // AcWing AppID (7626)
  redirect_uri,    // 回调地址
  scope,           // 权限范围 (userinfo)
  state,           // 状态码（防止 CSRF）
  callback         // 回调函数
)
```

**关键点**：
- ✅ `redirect_uri` 必须是后端 API 端点
- ✅ 后端必须返回纯 JSON（不能是 HTML）
- ✅ `callback` 函数接收 `{code, state}` 参数

---

## 🔧 后端实现

### 1. AcWingUser 模型

```python
# backend/api/models.py

from django.db import models
from django.contrib.auth.models import User

class AcWingUser(models.Model):
    """AcWing 用户信息"""
    user = models.OneToOneField(
        User,
        on_delete=models.CASCADE,
        related_name='acwing'
    )
    openid = models.CharField(max_length=100, unique=True, verbose_name="AcWing OpenID")
    username = models.CharField(max_length=100, verbose_name="AcWing 用户名")
    photo = models.URLField(blank=True, verbose_name="头像URL")
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = 'acwing_users'
        verbose_name = 'AcWing 用户'
        verbose_name_plural = 'AcWing 用户'
    
    def __str__(self):
        return f"{self.username} (OpenID: {self.openid})"
```

**设计要点**：
- ✅ OneToOne 关联 Django User
- ✅ `openid` 唯一标识 AcWing 用户
- ✅ 存储用户名和头像

### 2. 环境变量配置

```python
# backend/calendar_backend/settings.py

import os

# AcWing OAuth2 配置
ACWING_APPID = os.environ.get('ACWING_APPID', '7626')
ACWING_SECRET = os.environ.get('ACWING_SECRET', '')

# 安全：Secret 不能硬编码
if not ACWING_SECRET:
    raise ValueError('ACWING_SECRET environment variable is required')
```

**服务器配置**：

```bash
# ~/.bashrc
export ACWING_APPID="7626"
export ACWING_SECRET="your_secret_here"

# 重新加载
source ~/.bashrc
```

### 3. OAuth2 回调处理

```python
# backend/api/views/oauth_callback.py

from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def acwing_oauth_callback(request):
    """
    AcWing OAuth2 回调端点
    
    接收 AcWing 重定向的 code 和 state 参数
    返回 JSON 格式（不是 HTML）
    """
    code = request.GET.get('code', '')
    state = request.GET.get('state', '')
    
    # 返回纯 JSON（关键！）
    return JsonResponse({
        'code': code,
        'state': state
    })
```

**为什么必须返回 JSON？**
- AcWingOS 的 `oauth2.authorize` 回调函数期望 JSON
- 如果返回 HTML，`code` 和 `state` 会是 `undefined`

### 4. AcWing 登录接口

```python
# backend/api/views/auth.py

import requests
from django.conf import settings
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from rest_framework import status
from rest_framework_simplejwt.tokens import RefreshToken
from django.contrib.auth.models import User
from api.models import AcWingUser

@api_view(['POST'])
@permission_classes([AllowAny])
def acwing_login(request):
    """
    AcWing 一键登录
    
    流程：
    1. 接收前端传来的 code 和 state
    2. 用 code 换取 access_token 和 openid
    3. 用 access_token 获取用户信息
    4. 创建或更新用户
    5. 生成 JWT token 返回
    """
    code = request.data.get('code')
    state = request.data.get('state')
    
    if not code:
        return Response(
            {'error': 'Code is required'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    # 1. 用 code 换取 access_token
    apply_access_token_url = 'https://www.acwing.com/third_party/api/oauth2/access_token/'
    params = {
        'appid': settings.ACWING_APPID,
        'secret': settings.ACWING_SECRET,
        'code': code
    }
    
    try:
        response = requests.get(apply_access_token_url, params=params)
        data = response.json()
        
        if 'errcode' in data:
            return Response(
                {'error': f"AcWing error: {data.get('errmsg')}"},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        access_token = data.get('access_token')
        openid = data.get('openid')
        
        # 2. 用 access_token 获取用户信息
        get_userinfo_url = 'https://www.acwing.com/third_party/api/meta/identity/getinfo/'
        params = {
            'access_token': access_token,
            'openid': openid
        }
        
        response = requests.get(get_userinfo_url, params=params)
        userinfo = response.json()
        
        if 'errcode' in userinfo:
            return Response(
                {'error': f"AcWing error: {userinfo.get('errmsg')}"},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        username = userinfo.get('username')
        photo = userinfo.get('photo')
        
        # 3. 创建或更新用户
        acwing_user, created = AcWingUser.objects.get_or_create(
            openid=openid,
            defaults={
                'username': username,
                'photo': photo
            }
        )
        
        if not created:
            # 更新用户信息
            acwing_user.username = username
            acwing_user.photo = photo
            acwing_user.save()
        else:
            # 创建 Django User
            django_user = User.objects.create_user(
                username=f'acwing_{openid}',
                password=None  # OAuth 用户不需要密码
            )
            acwing_user.user = django_user
            acwing_user.save()
        
        # 4. 生成 JWT token
        refresh = RefreshToken.for_user(acwing_user.user)
        
        return Response({
            'access': str(refresh.access_token),
            'refresh': str(refresh),
            'user': {
                'id': acwing_user.user.id,
                'username': acwing_user.username,
                'photo': acwing_user.photo
            }
        })
        
    except Exception as e:
        return Response(
            {'error': f'Internal error: {str(e)}'},
            status=status.HTTP_500_INTERNAL_SERVER_ERROR
        )
```

### 5. URL 路由

```python
# backend/api/urls.py

from django.urls import path
from api.views.auth import acwing_login
from api.views.oauth_callback import acwing_oauth_callback

urlpatterns = [
    # OAuth2 回调
    path('oauth2/receive_code/', acwing_oauth_callback, name='acwing_callback'),
    
    # AcWing 登录
    path('auth/acwing/login/', acwing_login, name='acwing_login'),
    
    # ... 其他路由
]
```

---

## 🌐 前端实现

### 1. 主入口 - 授权流程

```javascript
// acapp_frontend/src/main.js

import { createApp } from 'vue'
import App from './App.vue'
import store from './store'

export class Calendar {
  constructor(parent, AcWingOS) {
    if (typeof parent === 'string') {
      this.parent = document.querySelector('#' + parent)
    } else {
      this.parent = parent
    }
    
    this.AcWingOS = AcWingOS
    
    // 先检查并登录，成功后再创建应用
    this.checkAndLogin()
  }
  
  /**
   * 检查并登录
   */
  async checkAndLogin() {
    const token = localStorage.getItem('access_token')
    
    if (!token) {
      console.log('❌ 未登录，触发 AcWing 授权...')
      this.requestAcWingLogin()
      return
    }
    
    // 验证 token 有效性
    try {
      const response = await fetch('https://app7626.acapp.acwing.com.cn/api/auth/me/', {
        headers: {
          'Authorization': `Bearer ${token}`
        }
      })
      
      if (response.ok) {
        const user = await response.json()
        console.log('✅ Token 有效，用户:', user.username)
        
        // Token 有效，创建应用
        this.createApp()
      } else {
        console.log('❌ Token 无效，清除并重新授权...')
        localStorage.removeItem('access_token')
        localStorage.removeItem('refresh_token')
        this.requestAcWingLogin()
      }
    } catch (error) {
      console.error('❌ Token 验证失败:', error)
      localStorage.removeItem('access_token')
      localStorage.removeItem('refresh_token')
      this.requestAcWingLogin()
    }
  }
  
  /**
   * 请求 AcWing 授权
   */
  requestAcWingLogin() {
    console.log('🔐 开始 AcWing OAuth2 授权流程...')
    
    this.AcWingOS.api.oauth2.authorize(
      '7626',  // AppID
      'https://app7626.acapp.acwing.com.cn/api/oauth2/receive_code/',  // 回调地址（关键！）
      'userinfo',  // 权限范围
      'random_state',  // 状态码
      async (resp) => {
        console.log('📥 收到 AcWing 回调:', resp)
        
        const code = resp.code
        const state = resp.state
        
        if (!code) {
          console.error('❌ 授权失败：未获取到 code')
          return
        }
        
        console.log('✅ 授权码获取成功:', code)
        
        // 调用后端登录接口
        try {
          const response = await fetch('https://app7626.acapp.acwing.com.cn/api/auth/acwing/login/', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ code, state })
          })
          
          const data = await response.json()
          
          if (response.ok) {
            console.log('✅ 登录成功:', data.user.username)
            
            // 保存 token
            localStorage.setItem('access_token', data.access)
            localStorage.setItem('refresh_token', data.refresh)
            localStorage.setItem('user', JSON.stringify(data.user))
            
            // 创建应用
            this.createApp()
          } else {
            console.error('❌ 登录失败:', data.error)
            alert('登录失败：' + data.error)
          }
        } catch (error) {
          console.error('❌ 登录请求失败:', error)
          alert('登录失败，请重试')
        }
      }
    )
  }
  
  /**
   * 创建 Vue 应用
   */
  createApp() {
    console.log('🚀 创建 Calendar 应用...')
    
    this.app = createApp(App)
    this.app.use(store)
    this.app.mount(this.parent)
    
    // 加载事件
    store.dispatch('fetchEvents')
    
    console.log('✅ Calendar 初始化完成')
  }
  
  destroy() {
    if (this.app) {
      this.app.unmount()
    }
  }
}
```

**关键点**：
1. ✅ 先检查 token 有效性，再决定是否授权
2. ✅ `redirect_uri` 指向后端 API 端点
3. ✅ 回调函数接收 `{code, state}` 参数
4. ✅ 登录成功后创建 Vue 应用

### 2. Vuex Store 模块化

#### 主 Store

```javascript
// acapp_frontend/src/store/index.js

import { createStore } from 'vuex'
import user from './modules/user'
import events from './modules/events'
import router from './modules/router'

export default createStore({
  modules: {
    user,
    events,
    router
  }
})
```

#### User 模块

```javascript
// acapp_frontend/src/store/modules/user.js

export default {
  state: {
    user: null,
    isLoggedIn: false
  },
  mutations: {
    setUser(state, user) {
      state.user = user
      state.isLoggedIn = !!user
    },
    clearUser(state) {
      state.user = null
      state.isLoggedIn = false
    }
  },
  actions: {
    async fetchUser({ commit }) {
      const userStr = localStorage.getItem('user')
      if (userStr) {
        const user = JSON.parse(userStr)
        commit('setUser', user)
      }
    },
    logout({ commit }) {
      localStorage.removeItem('access_token')
      localStorage.removeItem('refresh_token')
      localStorage.removeItem('user')
      commit('clearUser')
    }
  }
}
```

#### Events 模块

```javascript
// acapp_frontend/src/store/modules/events.js

export default {
  state: {
    events: [],
    loading: false,
    error: null
  },
  mutations: {
    setEvents(state, events) {
      state.events = events
    },
    addEvent(state, event) {
      state.events.push(event)
    },
    updateEvent(state, updatedEvent) {
      const index = state.events.findIndex(e => e.id === updatedEvent.id)
      if (index !== -1) {
        state.events.splice(index, 1, updatedEvent)
      }
    },
    deleteEvent(state, eventId) {
      state.events = state.events.filter(e => e.id !== eventId)
    },
    setLoading(state, loading) {
      state.loading = loading
    },
    setError(state, error) {
      state.error = error
    }
  },
  actions: {
    async fetchEvents({ commit }) {
      commit('setLoading', true)
      
      try {
        const token = localStorage.getItem('access_token')
        const response = await fetch('https://app7626.acapp.acwing.com.cn/api/events/', {
          headers: {
            'Authorization': `Bearer ${token}`
          }
        })
        
        if (response.ok) {
          const data = await response.json()
          commit('setEvents', data)
          console.log('✅ 事件加载成功:', data.length, '个')
        } else {
          throw new Error('Failed to fetch events')
        }
      } catch (error) {
        console.error('❌ 事件加载失败:', error)
        commit('setError', error.message)
      } finally {
        commit('setLoading', false)
      }
    }
  }
}
```

#### Router 模块

```javascript
// acapp_frontend/src/store/modules/router.js

export default {
  state: {
    router_name: 'calendar',  // 当前视图
    router_params: {}          // 视图参数
  },
  mutations: {
    updateRouterName(state, router_name) {
      state.router_name = router_name
    },
    updateRouterParams(state, router_params) {
      state.router_params = router_params
    }
  }
}
```

### 3. 组件状态访问修复

#### MainView.vue

```vue
<template>
  <div class="main-view">
    <!-- 修复前：$store.state.router_name -->
    <!-- 修复后：$store.state.router.router_name -->
    
    <CalendarGrid v-if="$store.state.router.router_name === 'calendar'" />
    <EventList v-else-if="$store.state.router.router_name === 'event_list'" />
    <EventDetail v-else-if="$store.state.router.router_name === 'event_detail'" />
    <AddEventForm v-else-if="$store.state.router.router_name === 'add_event'" />
  </div>
</template>
```

#### EventList.vue

```vue
<script>
import { mapState, mapMutations } from 'vuex'

export default {
  computed: {
    // 修复前：...mapState(['events', 'loading'])
    // 修复后：明确指定模块路径
    ...mapState({
      events: state => state.events.events,
      loading: state => state.events.loading
    })
  },
  methods: {
    ...mapMutations(['updateRouterName', 'updateRouterParams'])
  }
}
</script>
```

---

## 🐛 问题解决记录

### 问题 1: CORS 错误

**报错**：
```
Access to fetch at 'https://app7626.acapp.acwing.com.cn/'
from origin 'https://www.acwing.com' has been blocked by CORS policy:
No 'Access-Control-Allow-Origin' header is present
```

**原因分析**：
- `redirect_uri` 指向了前端路由 `/`
- 前端是静态资源，没有 CORS 配置

**解决方案**：
```javascript
// 错误
const redirect_uri = 'https://app7626.acapp.acwing.com.cn/'

// 正确
const redirect_uri = 'https://app7626.acapp.acwing.com.cn/api/oauth2/receive_code/'
```

### 问题 2: 回调返回 HTML

**现象**：
```javascript
console.log(resp.code)  // undefined
console.log(resp.state) // undefined
```

**原因分析**：
- Django 视图返回了 HTML 页面
- AcWingOS 期望纯 JSON 响应

**错误代码**：
```python
def acwing_oauth_callback(request):
    code = request.GET.get('code', '')
    return render(request, 'callback.html', {'code': code})  # ❌
```

**正确代码**：
```python
from django.http import JsonResponse

def acwing_oauth_callback(request):
    code = request.GET.get('code', '')
    state = request.GET.get('state', '')
    return JsonResponse({
        'code': code,
        'state': state
    })  # ✅
```

### 问题 3: 页面空白

**现象**：
- 登录成功，token 保存
- 但页面一片空白

**原因分析**：
- Vuex 模块化后，状态访问路径变更
- `MainView.vue` 使用旧路径

**错误代码**：
```vue
<CalendarGrid v-if="$store.state.router_name === 'calendar'" />
```

**正确代码**：
```vue
<CalendarGrid v-if="$store.state.router.router_name === 'calendar'" />
```

### 问题 4: EventList 空白

**现象**：
- 显示 3 个空事件卡片
- 新创建的事件不显示

**原因分析**：
- `mapState` 访问路径错误
- `events` 数组为空

**错误代码**：
```javascript
computed: {
  ...mapState(['events', 'loading'])  // ❌ events 是 undefined
}
```

**正确代码**：
```javascript
computed: {
  ...mapState({
    events: state => state.events.events,      // ✅ 正确路径
    loading: state => state.events.loading
  })
}
```

### 问题 5: 重装不重新授权

**现象**：
- 卸载重装应用
- 直接使用旧 token
- 不触发授权流程

**原因分析**：
- 只检查 token 是否存在
- 没有验证 token 有效性

**解决方案**：
```javascript
async checkAndLogin() {
  const token = localStorage.getItem('access_token')
  
  if (!token) {
    this.requestAcWingLogin()
    return
  }
  
  // 验证 token 有效性（关键！）
  try {
    const response = await fetch('.../api/auth/me/', {
      headers: { 'Authorization': `Bearer ${token}` }
    })
    
    if (response.ok) {
      this.createApp()  // Token 有效
    } else {
      localStorage.clear()
      this.requestAcWingLogin()  // Token 无效，重新授权
    }
  } catch (error) {
    localStorage.clear()
    this.requestAcWingLogin()
  }
}
```

---

## 📊 开发统计

### 代码变更

| 指标 | 数量 |
|-----|------|
| **新增文件** | 4 个 |
| **修改文件** | 10+ 个 |
| **新增代码** | 800+ 行 |
| **Git 提交** | 5 次 |
| **解决 Bug** | 5 个 |

### 文件清单

**后端新增**：
- `backend/api/models.py` - 添加 `AcWingUser` 模型
- `backend/api/views/auth.py` - 添加 `acwing_login` 接口
- `backend/api/views/oauth_callback.py` - 添加回调处理
- `backend/calendar_backend/settings.py` - 环境变量配置

**前端新增/修改**：
- `acapp_frontend/src/main.js` - OAuth2 授权流程
- `acapp_frontend/src/store/index.js` - Store 模块化
- `acapp_frontend/src/store/modules/user.js` - 用户模块
- `acapp_frontend/src/store/modules/events.js` - 事件模块
- `acapp_frontend/src/store/modules/router.js` - 路由模块
- `acapp_frontend/src/views/MainView.vue` - 修复状态访问
- `acapp_frontend/src/components/EventList.vue` - 修复状态访问
- `acapp_frontend/src/components/CalendarGrid.vue` - 修复状态访问

---

## ✅ 功能测试

### 测试清单

| 测试项 | 状态 | 说明 |
|--------|------|------|
| 首次打开触发授权 | ✅ | 无 token 时自动授权 |
| 用户授权成功 | ✅ | 正确跳转回应用 |
| Token 保存 | ✅ | localStorage 存储 |
| 用户信息显示 | ✅ | 显示 AcWing 用户名 |
| 事件列表加载 | ✅ | 正确显示已有事件 |
| 创建事件 | ✅ | 新事件正确保存 |
| 删除事件 | ✅ | 删除后列表更新 |
| Token 失效处理 | ✅ | 自动重新授权 |
| 重装应用 | ✅ | 验证 token 并重新授权 |

### 测试流程

**场景 1：首次使用**
```
1. 打开 AcApp
2. ❌ 无 token
3. 🔐 触发 AcWing 授权
4. ✅ 用户点击授权
5. 📥 收到 code
6. 🔑 后端生成 JWT token
7. 💾 前端保存 token
8. 🚀 创建应用
9. 📋 加载事件列表
```

**场景 2：再次打开**
```
1. 打开 AcApp
2. ✅ 有 token
3. 🔍 验证 token 有效性
4. ✅ Token 有效
5. 🚀 直接创建应用
6. 📋 加载事件列表
```

**场景 3：Token 失效**
```
1. 打开 AcApp
2. ✅ 有 token
3. 🔍 验证 token 有效性
4. ❌ Token 无效（401）
5. 🗑️ 清除旧 token
6. 🔐 重新触发授权
7. ... 重复场景 1
```

---

## 💡 技术亮点

### 1. Token 有效性验证

**为什么重要？**
- 避免使用过期 token
- 提升用户体验（自动重新授权）
- 保证数据安全

**实现方式**：
```javascript
// 1. 检查 token 存在
const token = localStorage.getItem('access_token')

// 2. 验证 token 有效性
const response = await fetch('/api/auth/me/', {
  headers: { 'Authorization': `Bearer ${token}` }
})

// 3. 根据结果决定
if (response.ok) {
  // Token 有效，继续
} else {
  // Token 无效，重新授权
}
```

### 2. Vuex 模块化

**优势**：
- ✅ 按功能拆分，易于维护
- ✅ 避免命名冲突
- ✅ 支持大型应用

**对比**：

| 方案 | 优点 | 缺点 |
|-----|------|------|
| 单一 Store | 简单 | 难以维护 |
| 模块化 Store | 清晰、可扩展 | 稍复杂 |

### 3. 环境变量管理

**最佳实践**：
```python
# ❌ 硬编码（不安全）
ACWING_SECRET = "abcd1234"

# ✅ 环境变量（安全）
ACWING_SECRET = os.environ.get('ACWING_SECRET', '')
```

**优势**：
- ✅ 安全（Secret 不提交到 Git）
- ✅ 灵活（不同环境不同配置）
- ✅ 规范（符合 12-Factor App）

---

## 🎓 学习收获

### 1. OAuth2 授权码模式

**理解了完整流程**：
```
客户端 → 授权服务器 → 用户授权 → 回调 → 换取 token → 访问资源
```

**关键概念**：
- `code`：授权码（一次性使用）
- `access_token`：访问令牌（获取用户信息）
- `state`：防止 CSRF 攻击

### 2. AcWingOS 平台特性

**与标准 OAuth2 的区别**：
- ✅ `redirect_uri` 必须返回 JSON
- ✅ 回调函数接收 `{code, state}` 对象
- ✅ 适合 AcApp 单页应用场景

### 3. Vuex 最佳实践

**模块化设计**：
- 按功能拆分（user、events、router）
- 使用 `mapState`、`mapMutations` 简化代码
- 保持向后兼容（可选 `namespaced`）

### 4. Token 生命周期管理

**完整流程**：
```
生成 → 存储 → 使用 → 验证 → 刷新 → 失效 → 重新授权
```

### 5. 调试技巧

**使用 console.log 追踪流程**：
```javascript
console.log('🔐 开始授权...')
console.log('📥 收到回调:', resp)
console.log('✅ 登录成功:', user)
console.log('🚀 创建应用...')
```

**效果**：清晰看到每一步的执行情况

---

## 🚀 下一步计划

### Day 13 规划

**高优先级**：
1. ⭐⭐⭐⭐⭐ QQ 一键登录（Web 端）
2. ⭐⭐⭐⭐ 地图功能集成（高德地图 API）
3. ⭐⭐⭐ Android 端云同步（Retrofit）

**中优先级**：
4. ⭐⭐⭐ AI 语音助手（科大讯飞）
5. ⭐⭐ 日历订阅功能实现
6. ⭐⭐ 准备演示材料

**技术选型**：
- QQ 登录：QQ 互联 OAuth2
- 地图：高德地图 Web API
- Android 网络：Retrofit + OkHttp
- AI 语音：科大讯飞 SDK

---

## 🎊 总结

### 今日成就

**完成了 AcApp 端的完整登录体系！** 🎉

**技术成果**：
- ✅ AcWing OAuth2 一键登录
- ✅ JWT Token 认证集成
- ✅ Vuex Store 模块化
- ✅ Token 有效性验证
- ✅ 完整的授权流程测试

**解决的难点**：
- ✅ CORS 跨域问题
- ✅ JSON vs HTML 响应
- ✅ Vuex 状态访问路径
- ✅ Token 生命周期管理

**学习收获**：
- ✅ OAuth2 实战经验
- ✅ AcWingOS 平台特性
- ✅ Vuex 模块化最佳实践
- ✅ Token 验证机制

### 项目进度

```
✅ Day 1-8:  Android 核心功能
✅ Day 9:    Django 后端 + Vue3 Web 端
✅ Day 10:   AcWing 平台集成
✅ Day 11:   用户认证 + UI优化 + 功能规划
✅ Day 12:   AcWing OAuth2 登录

总体进度：105% 🎯
（超出原计划）
```

---

**工作时长**: ~3 小时  
**代码行数**: 800+ 行  
**Git 提交**: 5 次  
**解决问题**: 5 个  

**Day 12 完美收官！AcApp 端用户体验大幅提升！** 💪🚀


