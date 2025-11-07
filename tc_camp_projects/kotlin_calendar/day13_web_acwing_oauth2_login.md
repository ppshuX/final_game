# 📅 Day 13: Web 端 AcWing OAuth2 一键登录实现

> **日期**：2025年11月7日  
> **用时**：约6小时  
> **难度**：⭐⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 今日目标

### 核心任务
1. ✅ 实现 Web 端 AcWing OAuth2 一键登录
2. ✅ 添加用户头像显示功能
3. ✅ 配置静态文件服务
4. ✅ 环境变量管理（python-dotenv）
5. ✅ 解决 6 个关键技术问题

### 完成情况

**进度**：100% ✅  
**Git 提交**：15+ 次  
**解决的 Bug**：6 个  
**新增文件**：5 个  
**修改文件**：15+ 个  
**添加依赖**：2 个

---

## 🔐 Web 端 OAuth2 授权流程

### 1. 完整流程图

```
┌─────────────────────────────────────────────────────────────┐
│            Web 端 AcWing OAuth2 Authorization Flow           │
└─────────────────────────────────────────────────────────────┘

[1] 用户访问登录页面
        ↓
[2] 点击 "AcWing 登录" 按钮
        ↓
[3] 构建授权 URL
    https://www.acwing.com/third_party/api/oauth2/web/authorize/
    ?appid=7626
    &redirect_uri=https://app7626.acapp.acwing.com.cn/acwing/callback
    &scope=userinfo
    &state=random_string
        ↓
[4] 跳转到 AcWing 授权页面
        ↓
[5] 用户在 AcWing 授权
        ↓
[6] AcWing 重定向到 redirect_uri
    携带参数: code, state
        ↓
[7] 前端回调页面 AcWingCallback.vue
    显示加载动画
        ↓
[8] 调用后端 /api/auth/acwing/login/
    POST {code, state}
        ↓
[9] 后端用 code 换取 access_token 和 openid
        ↓
[10] 后端用 access_token 获取用户信息
        ↓
[11] 后端创建/更新用户，生成 JWT token
        ↓
[12] 返回 JWT token 和用户信息
        ↓
[13] 前端保存 token 到 localStorage
        ↓
[14] 使用 window.location 跳转到日历页面
    （强制刷新，更新导航栏）
```

### 2. 关键 API 端点

#### AcWing 授权端点

**Web 端授权**（与 AcApp 不同）：
```
GET https://www.acwing.com/third_party/api/oauth2/web/authorize/
```

**参数**：
- `appid`: AcWing 应用 ID（7626）
- `redirect_uri`: 回调地址（必须是完整的 HTTPS URL）
- `scope`: 权限范围（userinfo）
- `state`: 状态码（防止 CSRF 攻击）

**与 AcApp 端的区别**：
| 端 | 授权方式 | 回调处理 |
|---|---------|---------|
| **AcApp** | AcWingOS API | 回调函数接收 JSON |
| **Web** | URL 跳转 | 重定向带 query 参数 |

---

## 🔧 后端实现

### 1. 环境变量管理（重要改进）

#### 问题：ACWING_SECRET 为空

**错误现象**：
```python
[AcWing Login] AppID: 7626, Secret: 
[AcWing Login] Token response: {'errcode': '40002', 'errmsg': 'args invalid'}
```

**原因分析**：
- 使用 `os.environ.get()` 读取环境变量
- uwsgi 进程不会继承 shell 的环境变量
- `~/.bashrc` 中的 `export` 对 uwsgi 无效

**解决方案：使用 python-dotenv**

```bash
# 1. 安装依赖
pip install python-dotenv
```

```python
# 2. backend/calendar_backend/settings.py
from pathlib import Path
from dotenv import load_dotenv
import os

BASE_DIR = Path(__file__).resolve().parent.parent

# 加载 .env 文件（关键！）
load_dotenv(BASE_DIR / '.env')

# AcWing OAuth2 配置
ACWING_APPID = os.environ.get('ACWING_APPID', '7626')
ACWING_SECRET = os.environ.get('ACWING_SECRET', '')

if not ACWING_SECRET:
    raise ValueError('ACWING_SECRET environment variable is required')
```

```bash
# 3. backend/.env（不提交到 Git）
ACWING_APPID=7626
ACWING_SECRET=7030aff130bd41c9876413211fe406af
```

```gitignore
# 4. .gitignore
.env
*.env
!.env.example
```

**优势**：
- ✅ 配置文件化，不依赖 shell
- ✅ 开发/生产环境隔离
- ✅ 符合 12-Factor App 规范
- ✅ 敏感信息不提交 Git

### 2. 日志系统改进

#### 问题：uwsgi 日志不显示信息

**错误方式**：
```python
print(f"[AcWing Login] Code: {code}")  # ❌ 不会被 uwsgi 捕获
```

**正确方式**：
```python
import logging

logger = logging.getLogger(__name__)

logger.info(f"[AcWing Login] Received code: {code}")
logger.error(f"[AcWing Login] Error: {error}")
```

#### 问题：Unicode 编码错误

**错误日志**：
```
UnicodeEncodeError: 'ascii' codec can't encode characters in position 15-17
```

**原因**：uwsgi 默认 ASCII 编码，日志中有中文

**解决方案**：**所有日志使用英文**

```python
# ❌ 错误
logger.error(f"[AcWing 登录] 收到授权码: {code}")

# ✅ 正确
logger.error(f"[AcWing Login] Received code: {code}")
```

### 3. 完善的 acwing_login 接口

```python
# backend/api/views/auth.py

import logging
import requests
from django.conf import settings
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from rest_framework import status
from rest_framework_simplejwt.tokens import RefreshToken
from django.contrib.auth.models import User
from api.models import AcWingUser

logger = logging.getLogger(__name__)

@api_view(['POST'])
@permission_classes([AllowAny])
def acwing_login(request):
    """
    AcWing 一键登录（Web 和 AcApp 通用）
    """
    code = request.data.get('code')
    state = request.data.get('state')
    
    logger.info(f"[AcWing Login] Received code: {code[:10]}..., state: {state}")
    
    if not code:
        logger.error("[AcWing Login] Missing code parameter")
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
    
    logger.info(f"[AcWing Login] AppID: {settings.ACWING_APPID}, Secret: {settings.ACWING_SECRET[:10]}...")
    
    try:
        logger.info("[AcWing Login] Requesting access token...")
        response = requests.get(apply_access_token_url, params=params, timeout=10)
        data = response.json()
        
        logger.info(f"[AcWing Login] Token response: {data}")
        
        if 'errcode' in data:
            logger.error(f"[AcWing Login] Token error: {data.get('errmsg')}")
            return Response(
                {'error': f"AcWing error: {data.get('errmsg')}"},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        access_token = data.get('access_token')
        openid = data.get('openid')
        
        logger.info(f"[AcWing Login] Got access_token and openid: {openid}")
        
        # 2. 用 access_token 获取用户信息
        get_userinfo_url = 'https://www.acwing.com/third_party/api/meta/identity/getinfo/'
        params = {
            'access_token': access_token,
            'openid': openid
        }
        
        logger.info("[AcWing Login] Requesting user info...")
        response = requests.get(get_userinfo_url, params=params, timeout=10)
        userinfo = response.json()
        
        logger.info(f"[AcWing Login] User info response: {userinfo}")
        
        if 'errcode' in userinfo:
            logger.error(f"[AcWing Login] User info error: {userinfo.get('errmsg')}")
            return Response(
                {'error': f"AcWing error: {userinfo.get('errmsg')}"},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        username = userinfo.get('username')
        photo = userinfo.get('photo')
        
        logger.info(f"[AcWing Login] Got user info: username={username}, photo={photo[:50]}...")
        
        # 3. 创建或更新用户
        acwing_user, created = AcWingUser.objects.get_or_create(
            openid=openid,
            defaults={
                'username': username,
                'photo': photo
            }
        )
        
        if not created:
            logger.info(f"[AcWing Login] Existing user, updating info...")
            
            # 更新用户信息
            acwing_user.username = username
            acwing_user.photo = photo
            acwing_user.save()
            
            # 更新 Django User 的用户名（避免冲突）
            user = acwing_user.user
            if user.username != username:
                # 检查新用户名是否已被其他用户占用
                if not User.objects.filter(username=username).exclude(id=user.id).exists():
                    user.username = username
                    user.save()
                    logger.info(f"[AcWing Login] Updated Django user username to {username}")
                else:
                    logger.warning(f"[AcWing Login] Username {username} already taken, keeping old username")
        else:
            logger.info(f"[AcWing Login] New user, creating Django user...")
            
            # 创建 Django User（用户名加前缀避免冲突）
            django_user = User.objects.create_user(
                username=f'acwing_{openid}',
                password=None
            )
            acwing_user.user = django_user
            acwing_user.save()
            logger.info(f"[AcWing Login] Created Django user: {django_user.username}")
        
        # 4. 生成 JWT token
        refresh = RefreshToken.for_user(acwing_user.user)
        
        logger.info(f"[AcWing Login] Login successful for user: {username}")
        
        return Response({
            'access': str(refresh.access_token),
            'refresh': str(refresh),
            'user': {
                'id': acwing_user.user.id,
                'username': acwing_user.username,
                'photo': acwing_user.photo
            }
        })
        
    except requests.exceptions.Timeout:
        logger.error("[AcWing Login] Request timeout")
        return Response(
            {'error': 'Request timeout'},
            status=status.HTTP_504_GATEWAY_TIMEOUT
        )
    except Exception as e:
        logger.error(f"[AcWing Login] Internal error: {str(e)}")
        return Response(
            {'error': f'Internal error: {str(e)}'},
            status=status.HTTP_500_INTERNAL_SERVER_ERROR
        )
```

**改进点**：
1. ✅ 详细的英文日志（每一步都有记录）
2. ✅ 超时处理（10 秒超时）
3. ✅ 用户名冲突检查（`exclude(id=user.id)`）
4. ✅ 错误处理完善
5. ✅ 日志级别合理（info/error/warning）

### 4. UserSerializer 完善

```python
# backend/api/serializers.py

class UserSerializer(serializers.ModelSerializer):
    photo = serializers.SerializerMethodField()
    
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'photo']
        read_only_fields = ['id']
    
    def get_photo(self, obj):
        """
        获取用户头像
        
        优先返回 AcWing 头像，如果没有则返回默认头像
        """
        try:
            if hasattr(obj, 'acwing'):
                return obj.acwing.photo
        except:
            pass
        
        # 返回默认头像
        return 'https://www.acwing.com/media/user/profile/photo/default.png'
```

**亮点**：
- ✅ 使用 `SerializerMethodField` 动态计算字段
- ✅ 通过 `obj.acwing` 关联获取 AcWing 头像
- ✅ 提供默认头像作为 fallback

### 5. 静态文件配置

#### Django 配置

```python
# backend/calendar_backend/settings.py

# 静态文件
STATIC_URL = 'static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

```python
# backend/calendar_backend/urls.py

from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... API 路由
]

# 开发环境提供静态文件服务
if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL, document_root=BASE_DIR / 'static')
```

#### Nginx 配置

```nginx
# backend/nginx.conf

server {
    listen 443 ssl;
    server_name app7626.acapp.acwing.com.cn;
    
    # SSL 证书配置
    ssl_certificate cert/acapp.pem;
    ssl_certificate_key cert/acapp.key;
    
    # 静态文件（新增）
    location /static/ {
        alias /home/acs/kotlin_calendar/backend/static/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Web 端
    location / {
        root /home/acs/kotlin_calendar/web/calendar_web/dist;
        index index.html;
        try_files $uri $uri/ /index.html;
    }
    
    # AcApp 端
    location /acapp/ {
        alias /home/acs/kotlin_calendar/acapp/;
        add_header 'Access-Control-Allow-Origin' 'https://www.acwing.com' always;
    }
    
    # Django API
    location /api/ {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:8000;
    }
}
```

**关键配置**：
- `alias`: 指定静态文件实际路径
- `expires 30d`: 设置缓存时间 30 天
- `Cache-Control`: 添加缓存控制头

#### 静态文件目录结构

```
backend/static/
└── images/
    ├── AcWing_logo.png
    └── QQ_logo.png
```

#### .gitattributes 配置

```
# .gitattributes
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
```

**作用**：确保二进制文件不被 Git 文本处理

---

## 🌐 前端实现

### 1. LoginView 组件（登录/注册页面）

```vue
<!-- web/calendar_web/src/views/account/LoginView.vue -->

<template>
  <div class="login-container">
    <el-card class="login-card">
      <template #header>
        <div class="card-header">
          <h2>{{ isLogin ? '登录' : '注册' }}</h2>
        </div>
      </template>
      
      <el-form
        ref="formRef"
        :model="form"
        :rules="rules"
        label-width="80px"
      >
        <!-- 用户名、邮箱、密码表单字段 -->
        <!-- ... -->
        
        <el-form-item>
          <el-button
            type="primary"
            :loading="loading"
            @click="handleSubmit"
            style="width: 100%"
          >
            {{ isLogin ? '登录' : '注册' }}
          </el-button>
        </el-form-item>
      </el-form>
      
      <!-- 第三方登录（关键！）-->
      <div class="divider">
        <span>或使用第三方登录</span>
      </div>
      
      <div class="oauth-buttons">
        <!-- AcWing 登录 -->
        <el-button
          class="oauth-button acwing"
          @click="handleAcWingLogin"
        >
          <img
            src="https://app7626.acapp.acwing.com.cn/static/images/AcWing_logo.png"
            alt="AcWing"
            class="oauth-icon"
          />
          AcWing {{ isLogin ? '登录' : '注册' }}
        </el-button>
        
        <!-- QQ 登录（占位） -->
        <el-button
          class="oauth-button qq"
          @click="handleQQLogin"
        >
          <img
            src="https://app7626.acapp.acwing.com.cn/static/images/QQ_logo.png"
            alt="QQ"
            class="oauth-icon"
          />
          QQ {{ isLogin ? '登录' : '注册' }}
        </el-button>
      </div>
      
      <div class="toggle-link">
        <span>{{ isLogin ? '还没有账号？' : '已有账号？' }}</span>
        <el-link type="primary" @click="toggleMode">
          {{ isLogin ? '立即注册' : '立即登录' }}
        </el-link>
      </div>
    </el-card>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import api from '@/api'

const router = useRouter()
const isLogin = ref(true)
const loading = ref(false)

// ... 表单逻辑

/**
 * AcWing 登录
 */
const handleAcWingLogin = () => {
  const appid = '7626'
  const redirect_uri = encodeURIComponent('https://app7626.acapp.acwing.com.cn/acwing/callback')
  const scope = 'userinfo'
  const state = Math.random().toString(36).substring(7)
  
  // 跳转到 AcWing 授权页面
  const authUrl = `https://www.acwing.com/third_party/api/oauth2/web/authorize/?appid=${appid}&redirect_uri=${redirect_uri}&scope=${scope}&state=${state}`
  
  window.location.href = authUrl
}

/**
 * QQ 登录（待实现）
 */
const handleQQLogin = () => {
  ElMessage.info('QQ 登录功能开发中...')
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.login-card {
  width: 400px;
}

.divider {
  text-align: center;
  margin: 20px 0;
  color: #999;
  position: relative;
}

.divider::before,
.divider::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 40%;
  height: 1px;
  background: #ddd;
}

.divider::before {
  left: 0;
}

.divider::after {
  right: 0;
}

.oauth-buttons {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.oauth-button {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.oauth-button.acwing {
  background: linear-gradient(135deg, #42a5f5, #1976d2);
  color: white;
  border: none;
}

.oauth-button.qq {
  background: linear-gradient(135deg, #00c6ff, #0072ff);
  color: white;
  border: none;
}

.oauth-icon {
  width: 20px;
  height: 20px;
  object-fit: contain;
}

.toggle-link {
  text-align: center;
  margin-top: 20px;
}
</style>
```

**亮点**：
1. ✅ 登录和注册页面都有第三方登录
2. ✅ 使用实际图标（不是 emoji）
3. ✅ 图标来自后端静态文件服务
4. ✅ 渐变按钮样式美观

### 2. AcWingCallback 组件（回调页面）

```vue
<!-- web/calendar_web/src/views/account/AcWingCallback.vue -->

<template>
  <div class="callback-container">
    <el-card class="callback-card">
      <div v-if="loading" class="loading">
        <el-icon class="is-loading" :size="50">
          <Loading />
        </el-icon>
        <p>正在登录...</p>
      </div>
      
      <div v-else-if="error" class="error">
        <el-icon :size="50" color="#f56c6c">
          <CircleClose />
        </el-icon>
        <p>{{ error }}</p>
        <el-button type="primary" @click="goToLogin">
          返回登录
        </el-button>
      </div>
      
      <div v-else class="success">
        <el-icon :size="50" color="#67c23a">
          <CircleCheck />
        </el-icon>
        <p>登录成功！</p>
      </div>
    </el-card>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { Loading, CircleClose, CircleCheck } from '@element-plus/icons-vue'
import api from '@/api'

const route = useRoute()
const router = useRouter()

const loading = ref(true)
const error = ref('')

onMounted(async () => {
  // 获取 URL 参数
  const code = route.query.code
  const state = route.query.state
  
  console.log('AcWing callback:', { code, state })
  
  if (!code) {
    error.value = '授权失败：未获取到授权码'
    loading.value = false
    return
  }
  
  try {
    // 调用后端登录接口
    const response = await fetch('https://app7626.acapp.acwing.com.cn/api/auth/acwing/login/', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ code, state })
    })
    
    const data = await response.json()
    
    if (response.ok) {
      console.log('Login success:', data.user.username)
      
      // 保存 token
      localStorage.setItem('access_token', data.access)
      localStorage.setItem('refresh_token', data.refresh)
      localStorage.setItem('user', JSON.stringify(data.user))
      
      ElMessage.success('登录成功！')
      
      loading.value = false
      
      // 跳转到日历页面（使用 window.location 强制刷新）
      setTimeout(() => {
        window.location.href = '/calendar'
      }, 1000)
    } else {
      throw new Error(data.error || '登录失败')
    }
  } catch (err) {
    console.error('Login error:', err)
    error.value = err.message || '登录失败，请重试'
    loading.value = false
    ElMessage.error(error.value)
  }
})

const goToLogin = () => {
  router.push('/login')
}
</script>

<style scoped>
.callback-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.callback-card {
  width: 400px;
  text-align: center;
}

.loading, .error, .success {
  padding: 40px 20px;
}

.loading p, .error p, .success p {
  margin-top: 20px;
  font-size: 16px;
  color: #666;
}

.is-loading {
  animation: rotating 2s linear infinite;
}

@keyframes rotating {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
```

**亮点**：
1. ✅ 加载动画（旋转的 Loading 图标）
2. ✅ 成功状态显示
3. ✅ 错误处理和提示
4. ✅ 使用 `window.location` 强制刷新页面

### 3. NavBar 组件（显示用户信息）

```vue
<!-- web/calendar_web/src/components/NavBar.vue -->

<template>
  <nav class="navbar">
    <div class="navbar-brand">
      📅 Kotlin Calendar
    </div>
    
    <div class="navbar-user" v-if="user">
      <el-dropdown @command="handleCommand">
        <span class="user-info">
          <el-avatar :src="user.photo" :size="32">
            {{ user.username[0].toUpperCase() }}
          </el-avatar>
          <span class="username">{{ user.username }}</span>
          <el-icon class="el-icon--right">
            <arrow-down />
          </el-icon>
        </span>
        <template #dropdown>
          <el-dropdown-menu>
            <el-dropdown-item disabled>
              <div class="user-details">
                <el-avatar :src="user.photo" :size="48">
                  {{ user.username[0].toUpperCase() }}
                </el-avatar>
                <div class="user-text">
                  <div class="user-name">{{ user.username }}</div>
                  <div class="user-email">{{ user.email || 'AcWing 用户' }}</div>
                </div>
              </div>
            </el-dropdown-item>
            <el-dropdown-item divided command="logout">
              <el-icon><SwitchButton /></el-icon>
              退出登录
            </el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>
    </div>
    
    <div class="navbar-user" v-else>
      <el-button type="primary" size="small" @click="goToLogin">
        登录
      </el-button>
    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { ArrowDown, SwitchButton } from '@element-plus/icons-vue'

const router = useRouter()
const user = ref(null)

onMounted(() => {
  const userStr = localStorage.getItem('user')
  if (userStr) {
    user.value = JSON.parse(userStr)
    console.log('NavBar loaded user:', user.value)
  }
})

const handleCommand = (command) => {
  if (command === 'logout') {
    handleLogout()
  }
}

const handleLogout = () => {
  localStorage.clear()
  user.value = null
  ElMessage.success('已退出登录')
  router.push('/login')
}

const goToLogin = () => {
  router.push('/login')
}
</script>

<style scoped>
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  height: 60px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.navbar-brand {
  font-size: 20px;
  font-weight: bold;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
}

.username {
  color: white;
}

.user-details {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 8px 0;
}

.user-text {
  flex: 1;
}

.user-name {
  font-weight: bold;
  margin-bottom: 4px;
}

.user-email {
  font-size: 12px;
  color: #999;
}
</style>
```

**亮点**：
1. ✅ 显示用户头像（AcWing 头像）
2. ✅ 头像加载失败时显示首字母
3. ✅ 下拉菜单显示详细信息
4. ✅ 优雅的渐变导航栏

### 4. 路由配置

```javascript
// web/calendar_web/src/router/index.js

import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  {
    path: '/',
    redirect: '/calendar'
  },
  {
    path: '/calendar',
    name: 'Calendar',
    component: () => import('@/views/CalendarView.vue'),
    meta: { requiresAuth: true }
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import('@/views/account/LoginView.vue')
  },
  {
    path: '/acwing/callback',
    name: 'AcWingCallback',
    component: () => import('@/views/account/AcWingCallback.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

// 路由守卫
router.beforeEach((to, from, next) => {
  const token = localStorage.getItem('access_token')
  
  if (to.meta.requiresAuth && !token) {
    next('/login')
  } else {
    next()
  }
})

export default router
```

---

## 🐛 问题解决详解

### 问题 6: 登录成功但导航栏不更新

**现象**：
- AcWing 登录成功
- Token 保存成功
- 跳转到日历页面
- 但导航栏仍显示"登录"按钮

**原因分析**：
```javascript
// 使用 router.push('/calendar')
// Vue Router 只会切换组件，不会刷新页面
// NavBar 的 onMounted 不会重新执行
// 所以 user 状态不会更新
```

**解决方案**：

```javascript
// ❌ 错误方式
router.push('/calendar')  // 不会刷新页面

// ✅ 正确方式
window.location.href = '/calendar'  // 强制刷新页面
```

**trade-off**：
- 优点：简单直接，确保状态更新
- 缺点：整个页面重新加载（但对登录场景来说可接受）

**更好的方案**（可选）：
```javascript
// 使用 Pinia 或 Vuex 全局状态管理
// 登录成功后触发 action 更新用户状态
store.dispatch('fetchUser')
```

---

## 📊 开发统计

### 代码变更

| 指标 | 数量 |
|-----|------|
| **新增文件** | 5 个 |
| **修改文件** | 15+ 个 |
| **新增代码** | 1200+ 行 |
| **Git 提交** | 15+ 次 |
| **解决 Bug** | 6 个 |
| **添加依赖** | 2 个 |

### 时间分布

| 任务 | 用时 |
|-----|------|
| 环境变量配置 | 1.5h |
| 日志系统调试 | 1h |
| 静态文件配置 | 1h |
| 前端组件开发 | 2h |
| 问题排查修复 | 0.5h |
| **总计** | **6h** |

### 文件清单

**后端新增/修改**：
- `backend/.env` - 环境变量配置
- `backend/requirements.txt` - 添加 python-dotenv 和 requests
- `backend/calendar_backend/settings.py` - 加载环境变量
- `backend/api/views/auth.py` - 完善日志和错误处理
- `backend/api/serializers.py` - 添加 photo 字段
- `backend/nginx.conf` - 添加静态文件路径
- `backend/static/images/` - 存放 OAuth 图标

**前端新增/修改**：
- `web/calendar_web/src/views/account/LoginView.vue` - 添加第三方登录
- `web/calendar_web/src/views/account/AcWingCallback.vue` - 回调页面
- `web/calendar_web/src/components/NavBar.vue` - 显示用户头像
- `web/calendar_web/src/router/index.js` - 添加回调路由

**配置文件**：
- `.gitattributes` - 二进制文件配置

---

## ✅ 功能测试

### 测试清单

| 测试项 | 状态 | 说明 |
|--------|------|------|
| 登录页面显示第三方按钮 | ✅ | AcWing + QQ 图标 |
| 注册页面显示第三方按钮 | ✅ | 同登录页面 |
| 点击 AcWing 按钮跳转 | ✅ | 正确跳转到授权页面 |
| 授权成功回调 | ✅ | 正确返回并显示加载 |
| Token 保存 | ✅ | localStorage 存储 |
| 跳转到日历页面 | ✅ | 强制刷新页面 |
| 导航栏显示头像 | ✅ | AcWing 头像 |
| 导航栏显示用户名 | ✅ | AcWing 用户名 |
| 下拉菜单显示信息 | ✅ | 头像、用户名、邮箱 |
| 退出登录 | ✅ | 清除 token 并跳转 |
| 静态文件访问 | ✅ | 图标正常显示 |
| 用户名冲突处理 | ✅ | 不会报错 |
| 环境变量读取 | ✅ | Secret 正确加载 |
| 日志输出 | ✅ | uwsgi.log 显示英文日志 |

### 测试流程

**场景 1：首次 AcWing 登录**
```
1. 访问登录页面
2. 点击 "AcWing 登录"
3. 跳转到 AcWing 授权页面
4. 点击"授权"
5. 回调到 /acwing/callback
6. 显示加载动画
7. 后端创建用户
8. 返回 JWT token
9. 前端保存 token
10. 跳转到日历页面（强制刷新）
11. 导航栏显示用户头像和用户名 ✅
```

**场景 2：再次登录**
```
1. 访问登录页面
2. 点击 "AcWing 登录"
3. AcWing 自动授权（已授权过）
4. 直接回调
5. 后端更新用户信息
6. 返回 JWT token
7. 跳转到日历页面 ✅
```

---

## 💡 技术亮点

### 1. 环境变量管理最佳实践

**python-dotenv 的优势**：
```python
# 传统方式：依赖 shell 环境变量
export ACWING_SECRET="xxx"  # ❌ uwsgi 不继承

# 新方式：使用 .env 文件
# .env
ACWING_SECRET=xxx

# settings.py
from dotenv import load_dotenv
load_dotenv()  # ✅ uwsgi 也能读取
```

**符合 12-Factor App 规范**

### 2. 详细的日志系统

**分级日志**：
```python
logger.info("[AcWing Login] Normal flow...")
logger.warning("[AcWing Login] Potential issue...")
logger.error("[AcWing Login] Critical error...")
```

**便于调试**：
- 每一步都有日志
- 请求参数记录
- 响应内容记录
- 错误详细信息

### 3. 用户名冲突处理

**问题**：
```python
# 用户 A: username="张三", acwing_openid="123"
# 用户 B: username="李四", acwing_openid="456"

# 如果用户 B 改名为"张三"
# 更新时会报错：UNIQUE constraint failed
```

**解决**：
```python
if user.username != username:
    # 检查新用户名是否被其他用户占用
    if not User.objects.filter(username=username).exclude(id=user.id).exists():
        user.username = username
        user.save()
    else:
        # 用户名已被占用，保持旧用户名
        logger.warning(f"Username {username} already taken")
```

### 4. 静态文件缓存策略

```nginx
location /static/ {
    alias /path/to/static/;
    expires 30d;  # 缓存 30 天
    add_header Cache-Control "public, immutable";
}
```

**优势**：
- 减少服务器负载
- 提升加载速度
- `immutable` 表示文件不会变化

---

## 🎓 学习收获

### 1. uwsgi 日志系统

**关键点**：
- `print()` 不会被 uwsgi 捕获
- 必须使用 `logging` 模块
- 日志必须是英文（避免编码问题）

### 2. Django ORM 高级用法

**`exclude()` 方法**：
```python
# 查询除了自己以外的用户
User.objects.filter(username='张三').exclude(id=current_user.id)
```

**用途**：
- 避免更新时的 UNIQUE 约束冲突
- 检查其他用户是否占用某个值

### 3. Nginx 配置技巧

**`alias` vs `root`**：
```nginx
# root: 会拼接路径
location /static/ {
    root /home/acs/;  # 实际路径: /home/acs/static/
}

# alias: 替换路径
location /static/ {
    alias /home/acs/files/;  # 实际路径: /home/acs/files/
}
```

### 4. Vue 页面刷新策略

**`router.push()` vs `window.location`**：

| 方式 | 优点 | 缺点 | 适用场景 |
|-----|------|------|---------|
| `router.push()` | 快速，无刷新 | 不触发 onMounted | SPA 内部导航 |
| `window.location` | 强制刷新，状态更新 | 慢，重新加载 | 登录后需要刷新状态 |

### 5. OAuth2 Web 流程

**与 AcApp 的区别**：
- Web: URL 跳转，query 参数传递
- AcApp: JS API，回调函数接收

---

## 🚀 下一步计划

### Day 14 规划

**高优先级**：
1. ⭐⭐⭐⭐⭐ QQ 一键登录（Web 端）
2. ⭐⭐⭐⭐ 清理临时文件和冗余代码
3. ⭐⭐⭐ 完善文档和演示材料

**可选功能**：
4. ⭐⭐⭐ 地图功能集成
5. ⭐⭐ Android 端云同步
6. ⭐⭐ AI 语音助手

---

## 🎊 总结

### 今日成就

**完成了 Web 端的完整 AcWing 登录体系！** 🎉

**技术成果**：
- ✅ AcWing OAuth2 Web 端登录
- ✅ 用户头像显示功能
- ✅ 环境变量管理（python-dotenv）
- ✅ 静态文件服务配置
- ✅ 详细的日志系统

**解决的难点**：
- ✅ uwsgi 日志不显示（logging 模块）
- ✅ Unicode 编码错误（英文日志）
- ✅ 环境变量为空（.env 文件）
- ✅ 用户名冲突（exclude 检查）
- ✅ 静态文件无法访问（Nginx 配置）
- ✅ 导航栏不更新（window.location）

**学习收获**：
- ✅ uwsgi 日志系统
- ✅ python-dotenv 环境变量管理
- ✅ Nginx 静态文件服务
- ✅ Django ORM 高级查询
- ✅ Vue 页面刷新策略
- ✅ OAuth2 Web 授权流程

### 项目进度

```
✅ Day 1-8:  Android 核心功能
✅ Day 9:    Django 后端 + Vue3 Web 端
✅ Day 10:   AcWing 平台集成
✅ Day 11:   用户认证 + UI优化 + 功能规划
✅ Day 12:   AcWing OAuth2 登录（AcApp 端）
✅ Day 13:   AcWing OAuth2 登录（Web 端）

总体进度：118% 🎯
（持续超出原计划）
```

---

**工作时长**: ~6 小时  
**代码行数**: 1200+ 行  
**Git 提交**: 15+ 次  
**解决问题**: 6 个  

**Day 13 完美收官！Web 端 AcWing 登录让多端登录体系更完善！** 💪🚀

