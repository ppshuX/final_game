# 📅 Day 11: 用户认证系统 + 三端优化 + 功能规划

> **日期**：2025年11月6日  
> **用时**：全天（约8小时）  
> **难度**：⭐⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 今日目标

### 核心任务
1. ✅ 实现完整的 JWT 用户认证系统
2. ✅ AcWing 端 UI 优化（适配小容器）
3. ✅ Web 端登录注册页面
4. ✅ Event 模型重构（时间段支持）
5. ✅ 项目结构优化
6. ✅ 详细功能规划文档（3500+ 行）

### 完成情况

**进度**：100% ✅  
**Git 提交**：25+ 次  
**新增代码**：2500+ 行  
**规划文档**：5 个（3500+ 行）  
**数据库迁移**：2 次

---

## 🔐 JWT 用户认证系统

### 1. 技术选型

**为什么选择 JWT？**

| 方案 | 优点 | 缺点 | 选择 |
|-----|------|------|------|
| **Session** | 服务端可控 | 需要存储、不适合分布式 | ❌ |
| **JWT** | 无状态、分布式友好 | Token 泄漏风险 | ✅ |
| **OAuth2** | 标准协议 | 复杂度高 | ❌ |

**JWT 适合的原因**：
- ✅ 三客户端架构，无状态更灵活
- ✅ 支持跨域（CORS）
- ✅ 适合移动端和 SPA
- ✅ Token 刷新机制成熟

### 2. 安装配置

#### 安装依赖

```bash
pip install djangorestframework-simplejwt==5.2.2
```

**版本选择**：
- `5.2.2` 是稳定版本
- 兼容 Django 4.2 LTS
- 避免新版本的兼容性问题

#### Django 配置

```python
# backend/calendar_backend/settings.py

INSTALLED_APPS = [
    # ...
    'rest_framework',
    'rest_framework_simplejwt',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
}

from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=5),      # 短期 Token
    'REFRESH_TOKEN_LIFETIME': timedelta(days=15),       # 长期 Token
    'ROTATE_REFRESH_TOKENS': True,                      # 刷新时轮换
    'BLACKLIST_AFTER_ROTATION': True,                   # 旧 Token 加入黑名单
    'AUTH_HEADER_TYPES': ('Bearer',),
}
```

**Token 生命周期设计**：
- **Access Token**: 5 分钟
  - 短生命周期，提高安全性
  - 泄漏后影响小
- **Refresh Token**: 15 天
  - 长生命周期，用户体验好
  - 类似"记住我"功能

### 3. API 端点实现

#### 用户注册

```python
# backend/api/views.py

from rest_framework import status
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from django.contrib.auth.models import User

@api_view(['POST'])
@permission_classes([AllowAny])  # 允许匿名访问
def register(request):
    """用户注册"""
    username = request.data.get('username')
    password = request.data.get('password')
    email = request.data.get('email', '')
    
    # 验证
    if not username or not password:
        return Response(
            {'error': 'Username and password are required'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    if User.objects.filter(username=username).exists():
        return Response(
            {'error': 'Username already exists'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    # 创建用户
    user = User.objects.create_user(
        username=username,
        password=password,
        email=email
    )
    
    return Response({
        'id': user.id,
        'username': user.username,
        'email': user.email,
    }, status=status.HTTP_201_CREATED)
```

#### 用户登录

```python
from rest_framework_simplejwt.tokens import RefreshToken

@api_view(['POST'])
@permission_classes([AllowAny])
def login(request):
    """用户登录"""
    username = request.data.get('username')
    password = request.data.get('password')
    
    user = authenticate(username=username, password=password)
    
    if user is None:
        return Response(
            {'error': 'Invalid credentials'},
            status=status.HTTP_401_UNAUTHORIZED
        )
    
    # 生成 Token
    refresh = RefreshToken.for_user(user)
    
    return Response({
        'access': str(refresh.access_token),
        'refresh': str(refresh),
        'user': {
            'id': user.id,
            'username': user.username,
            'email': user.email,
        }
    })
```

#### 获取当前用户

```python
from rest_framework.decorators import authentication_classes
from rest_framework_simplejwt.authentication import JWTAuthentication

@api_view(['GET'])
@authentication_classes([JWTAuthentication])
def me(request):
    """获取当前登录用户信息"""
    user = request.user
    return Response({
        'id': user.id,
        'username': user.username,
        'email': user.email,
    })
```

#### URL 路由

```python
# backend/api/urls.py

urlpatterns = [
    # JWT Token
    path('auth/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('auth/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    
    # 自定义认证
    path('auth/register/', register, name='register'),
    path('auth/login/', login, name='login'),
    path('auth/me/', me, name='me'),
    
    # Event API（需要认证）
    path('', include(router.urls)),
]
```

### 4. API 端点清单

| HTTP 方法 | 端点 | 功能 | 权限 |
|----------|------|------|------|
| `POST` | `/api/auth/register/` | 用户注册 | 匿名 |
| `POST` | `/api/auth/login/` | 用户登录 | 匿名 |
| `POST` | `/api/auth/token/refresh/` | 刷新 Token | 匿名 |
| `GET` | `/api/auth/me/` | 获取当前用户 | 认证 |
| `GET` | `/api/events/` | 获取日程列表 | 只读或认证 |
| `POST` | `/api/events/` | 创建日程 | 认证 |
| `PUT` | `/api/events/:id/` | 更新日程 | 认证 |
| `DELETE` | `/api/events/:id/` | 删除日程 | 认证 |

---

## 🌐 Web 端登录注册页面

### 1. LoginView 组件

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
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username" placeholder="请输入用户名" />
        </el-form-item>
        
        <el-form-item v-if="!isLogin" label="邮箱" prop="email">
          <el-input v-model="form.email" placeholder="请输入邮箱" />
        </el-form-item>
        
        <el-form-item label="密码" prop="password">
          <el-input
            v-model="form.password"
            type="password"
            placeholder="请输入密码"
            show-password
          />
        </el-form-item>
        
        <el-form-item v-if="!isLogin" label="确认密码" prop="confirmPassword">
          <el-input
            v-model="form.confirmPassword"
            type="password"
            placeholder="请再次输入密码"
            show-password
          />
        </el-form-item>
        
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

const form = reactive({
  username: '',
  email: '',
  password: '',
  confirmPassword: ''
})

const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 3, max: 20, message: '长度在 3 到 20 个字符', trigger: 'blur' }
  ],
  email: [
    { type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码至少 6 个字符', trigger: 'blur' }
  ],
  confirmPassword: [
    {
      validator: (rule, value, callback) => {
        if (value !== form.password) {
          callback(new Error('两次输入密码不一致'))
        } else {
          callback()
        }
      },
      trigger: 'blur'
    }
  ]
}

const handleSubmit = async () => {
  // 表单验证
  await formRef.value.validate()
  
  loading.value = true
  
  try {
    if (isLogin.value) {
      // 登录
      const { data } = await api.login(form.username, form.password)
      
      // 保存 Token
      localStorage.setItem('access_token', data.access)
      localStorage.setItem('refresh_token', data.refresh)
      localStorage.setItem('user', JSON.stringify(data.user))
      
      ElMessage.success('登录成功')
      router.push('/')
    } else {
      // 注册
      await api.register(form.username, form.password, form.email)
      
      ElMessage.success('注册成功，请登录')
      isLogin.value = true
      form.password = ''
      form.confirmPassword = ''
    }
  } catch (error) {
    ElMessage.error(error.response?.data?.error || '操作失败')
  } finally {
    loading.value = false
  }
}

const toggleMode = () => {
  isLogin.value = !isLogin.value
  form.password = ''
  form.confirmPassword = ''
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

.toggle-link {
  text-align: center;
  margin-top: 20px;
}
</style>
```

### 2. Axios 拦截器（Token 自动刷新）

```javascript
// web/calendar_web/src/api/index.js

import axios from 'axios'
import { ElMessage } from 'element-plus'
import router from '@/router'

const api = axios.create({
  baseURL: 'https://app7626.acapp.acwing.com.cn/api',
  timeout: 5000,
})

// 请求拦截器：自动添加 Token
api.interceptors.request.use(
  config => {
    const token = localStorage.getItem('access_token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  error => Promise.reject(error)
)

// 响应拦截器：Token 自动刷新
let isRefreshing = false  // 是否正在刷新
let requests = []         // 请求队列

api.interceptors.response.use(
  response => response,
  async error => {
    const originalRequest = error.config
    
    // 如果是 401 错误，尝试刷新 Token
    if (error.response?.status === 401 && !originalRequest._retry) {
      if (isRefreshing) {
        // 如果正在刷新，将请求加入队列
        return new Promise(resolve => {
          requests.push(token => {
            originalRequest.headers.Authorization = `Bearer ${token}`
            resolve(api(originalRequest))
          })
        })
      }
      
      originalRequest._retry = true
      isRefreshing = true
      
      try {
        const refreshToken = localStorage.getItem('refresh_token')
        
        if (!refreshToken) {
          throw new Error('No refresh token')
        }
        
        // 刷新 Token
        const { data } = await axios.post(
          'https://app7626.acapp.acwing.com.cn/api/auth/token/refresh/',
          { refresh: refreshToken }
        )
        
        // 保存新 Token
        const newAccessToken = data.access
        localStorage.setItem('access_token', newAccessToken)
        
        // 更新请求头
        api.defaults.headers.common.Authorization = `Bearer ${newAccessToken}`
        originalRequest.headers.Authorization = `Bearer ${newAccessToken}`
        
        // 重试队列中的请求
        requests.forEach(cb => cb(newAccessToken))
        requests = []
        
        // 重试原请求
        return api(originalRequest)
      } catch (refreshError) {
        // 刷新失败，跳转到登录页
        localStorage.clear()
        router.push('/login')
        ElMessage.error('登录已过期，请重新登录')
        return Promise.reject(refreshError)
      } finally {
        isRefreshing = false
      }
    }
    
    return Promise.reject(error)
  }
)

export default {
  // 认证相关
  register: (username, password, email) =>
    api.post('/auth/register/', { username, password, email }),
  
  login: (username, password) =>
    api.post('/auth/login/', { username, password }),
  
  getCurrentUser: () => api.get('/auth/me/'),
  
  // Event 相关
  getEvents: () => api.get('/events/'),
  createEvent: data => api.post('/events/', data),
  updateEvent: (id, data) => api.put(`/events/${id}/`, data),
  deleteEvent: id => api.delete(`/events/${id}/`),
  
  // 农历相关
  getLunarDate: (year, month, day) =>
    api.get('/lunar/', { params: { year, month, day } }),
}
```

**Token 自动刷新机制亮点**：
1. ✅ **请求队列管理** - 避免并发刷新
2. ✅ **自动重试** - 刷新后重试原请求
3. ✅ **无感刷新** - 用户无感知
4. ✅ **失败降级** - 刷新失败跳转登录

### 3. NavBar 显示用户信息

```vue
<!-- web/calendar_web/src/components/NavBar.vue -->

<template>
  <nav class="navbar">
    <div class="navbar-brand">
      📅 Kotlin Calendar
    </div>
    
    <div class="navbar-user" v-if="user">
      <el-dropdown>
        <span class="user-info">
          <el-avatar :size="32">{{ user.username[0].toUpperCase() }}</el-avatar>
          <span class="username">{{ user.username }}</span>
        </span>
        <template #dropdown>
          <el-dropdown-menu>
            <el-dropdown-item @click="handleLogout">
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

const router = useRouter()
const user = ref(null)

onMounted(() => {
  const userStr = localStorage.getItem('user')
  if (userStr) {
    user.value = JSON.parse(userStr)
  }
})

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
```

---

## 📊 Event 模型重构

### 1. 字段变更

**旧模型**：
```python
class Event(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    date_time = models.DateTimeField()  # 单一时间点
    reminder_minutes = models.IntegerField(default=0)
```

**新模型**：
```python
class Event(models.Model):
    # 用户关联
    user = models.ForeignKey(
        User,
        on_delete=models.CASCADE,
        related_name='events'
    )
    
    # 基本信息
    title = models.CharField(max_length=200, verbose_name="标题")
    description = models.TextField(blank=True, verbose_name="描述")
    location = models.CharField(max_length=500, blank=True, verbose_name="地点")
    
    # 时间信息
    start_time = models.DateTimeField(verbose_name="开始时间")
    end_time = models.DateTimeField(null=True, blank=True, verbose_name="结束时间")
    
    # 提醒设置
    reminder_minutes = models.IntegerField(
        default=0,
        choices=[
            (0, '无提醒'),
            (5, '5分钟前'),
            (15, '15分钟前'),
            (30, '30分钟前'),
            (60, '1小时前'),
            (1440, '1天前'),
            (10080, '1周前'),
        ],
        verbose_name="提醒时间"
    )
    
    # 元数据
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = 'events'
        ordering = ['-start_time']
        indexes = [
            models.Index(fields=['user', 'start_time']),
        ]
```

**变更理由**：

| 变更 | 原因 |
|-----|------|
| `date_time` → `start_time` | 语义更清晰 |
| 新增 `end_time` | 支持时间段事件（会议、旅行）|
| 新增 `location` | 支持地点功能 |
| 新增 `user` 外键 | 多用户支持 |
| 新增索引 | 优化查询性能 |

### 2. 数据库迁移

```bash
# 生成迁移文件
python manage.py makemigrations

# 执行迁移
python manage.py migrate
```

**迁移文件**：
```python
# backend/api/migrations/0003_event_user_location_times.py

from django.db import migrations, models
import django.db.models.deletion

class Migration(migrations.Migration):
    dependencies = [
        ('auth', '0012_alter_user_first_name_max_length'),
        ('api', '0002_event_reminder'),
    ]
    
    operations = [
        migrations.RenameField(
            model_name='event',
            old_name='date_time',
            new_name='start_time',
        ),
        migrations.AddField(
            model_name='event',
            name='end_time',
            field=models.DateTimeField(blank=True, null=True),
        ),
        migrations.AddField(
            model_name='event',
            name='location',
            field=models.CharField(blank=True, max_length=500),
        ),
        migrations.AddField(
            model_name='event',
            name='user',
            field=models.ForeignKey(
                default=1,
                on_delete=django.db.models.deletion.CASCADE,
                related_name='events',
                to='auth.user'
            ),
            preserve_default=False,
        ),
        migrations.AddIndex(
            model_name='event',
            index=models.Index(
                fields=['user', 'start_time'],
                name='events_user_start_idx'
            ),
        ),
    ]
```

### 3. Serializer 更新

```python
# backend/api/serializers.py

class EventSerializer(serializers.ModelSerializer):
    class Meta:
        model = Event
        fields = [
            'id',
            'title',
            'description',
            'location',
            'start_time',
            'end_time',
            'reminder_minutes',
            'created_at',
            'updated_at',
        ]
        read_only_fields = ['created_at', 'updated_at']
    
    def create(self, validated_data):
        # 自动关联当前用户
        validated_data['user'] = self.context['request'].user
        return super().create(validated_data)
```

---

## 🎨 AcWing 端 UI 优化

### 1. 优化背景

**问题**：AcWing 平台容器尺寸为 **630x521px**（非常小）

**挑战**：
- 原日历网格过大（70px/格）
- 表单字段过多（占用空间）
- 信息卡片冗余（TodayCard）

**目标**：紧凑化布局，适配小容器

### 2. 日历网格优化

**优化前**：
```css
.calendar-cell {
  width: 70px;
  height: 70px;
  font-size: 16px;
}
```

**优化后**：
```css
.calendar-cell {
  width: 48px;    /* 缩小30% */
  height: 48px;   /* 缩小30% */
  font-size: 13px; /* 缩小字体 */
}

.calendar-cell .event-dots {
  display: flex;
  justify-content: center;
  gap: 2px;
  margin-top: 2px;
}

.event-dot {
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: #409eff;
}
```

**效果**：
- ✅ 日历从 490x420 → 336x294
- ✅ 留出更多空间给工具栏和列表

### 3. 移除冗余组件

**删除**：
- ❌ TodayCard.vue（今日信息卡片）

**原因**：
- 占用空间大（80px 高度）
- 信息可移到顶部显示

**替代方案**：
```vue
<!-- CalendarHeader.vue -->
<div class="header-info">
  <span class="current-date">2025年11月6日 星期三</span>
  <span class="lunar-date">农历九月十六</span>
</div>
```

### 4. 日期点击弹窗

**功能**：点击日期格子 → 显示该日事件 + 快速添加按钮

```vue
<!-- CalendarGridView.vue -->
<template>
  <div class="calendar-grid">
    <div
      v-for="date in dates"
      :key="date.dateStr"
      class="calendar-cell"
      :class="{ 'has-events': hasEvents(date) }"
      @click="handleDateClick(date)"
    >
      <div class="date-number">{{ date.day }}</div>
      <div class="event-dots">
        <span
          v-for="i in Math.min(getEventCount(date), 3)"
          :key="i"
          class="event-dot"
        ></span>
      </div>
    </div>
    
    <!-- 日期详情弹窗 -->
    <el-dialog
      v-model="showDateDialog"
      :title="selectedDateTitle"
      width="90%"
    >
      <div class="date-events">
        <div v-if="dateEvents.length === 0" class="no-events">
          该日暂无事件
        </div>
        <div
          v-for="event in dateEvents"
          :key="event.id"
          class="event-item"
          @click="viewEventDetail(event)"
        >
          <div class="event-time">
            {{ formatTime(event.start_time) }}
          </div>
          <div class="event-title">{{ event.title }}</div>
        </div>
      </div>
      
      <template #footer>
        <el-button type="primary" @click="addEventForDate">
          + 添加事件
        </el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
const handleDateClick = (date) => {
  selectedDate.value = date
  showDateDialog.value = true
  
  // 筛选该日事件
  dateEvents.value = events.value.filter(event => {
    const eventDate = new Date(event.start_time)
    return (
      eventDate.getFullYear() === date.year &&
      eventDate.getMonth() === date.month &&
      eventDate.getDate() === date.day
    )
  })
}
</script>
```

### 5. 表单紧凑化

**AddEventForm 优化**：

```vue
<!-- 优化前：字段间距大，占用高度 600px+ -->
<el-form label-width="100px" :label-position="'left'">
  <el-form-item label="标题">
    <el-input v-model="form.title" />
  </el-form-item>
  <!-- 间距 20px -->
  <el-form-item label="描述">
    <el-input v-model="form.description" type="textarea" :rows="3" />
  </el-form-item>
  <!-- ... -->
</el-form>

<!-- 优化后：紧凑布局，占用高度 450px -->
<el-form label-width="80px" size="small" :label-position="'left'">
  <el-form-item label="标题">
    <el-input v-model="form.title" placeholder="请输入标题" />
  </el-form-item>
  
  <el-form-item label="开始时间">
    <el-date-picker
      v-model="form.start_time"
      type="datetime"
      placeholder="选择开始时间"
      style="width: 100%"
    />
  </el-form-item>
  
  <el-form-item label="结束时间">
    <div class="end-time-wrapper">
      <el-checkbox v-model="hasEndTime">设置结束时间</el-checkbox>
      <el-date-picker
        v-if="hasEndTime"
        v-model="form.end_time"
        type="datetime"
        placeholder="选择结束时间"
        style="width: 100%"
      />
    </div>
  </el-form-item>
  
  <el-form-item label="地点">
    <el-input v-model="form.location" placeholder="请输入地点（可选）" />
  </el-form-item>
  
  <el-form-item label="提醒">
    <el-select v-model="form.reminder_minutes" style="width: 100%">
      <el-option label="无提醒" :value="0" />
      <el-option label="5分钟前" :value="5" />
      <el-option label="15分钟前" :value="15" />
      <el-option label="30分钟前" :value="30" />
      <el-option label="1小时前" :value="60" />
      <el-option label="1天前" :value="1440" />
      <el-option label="1周前" :value="10080" />
    </el-select>
  </el-form-item>
</el-form>
```

**优化效果**：
- ✅ 高度从 600px+ → 450px
- ✅ 字段间距缩小
- ✅ 所有组件 size="small"
- ✅ end_time 可选（勾选框控制）

---

## 📁 项目结构优化

### 1. 组件分类

**优化前**：所有组件平铺在 `components/`

**优化后**：按功能分类

```
web/calendar_web/src/
├── components/
│   ├── account/              # 账户相关
│   │   └── (未来扩展)
│   └── calendar/             # 日历相关
│       ├── CalendarGrid.vue
│       ├── CalendarHeader.vue
│       ├── CalendarGridView.vue
│       ├── ToolBar.vue
│       ├── EventList.vue
│       ├── EventDetail.vue
│       └── AddEventForm.vue
├── views/
│   ├── account/              # 账户视图
│   │   └── LoginView.vue
│   └── CalendarView.vue      # 日历视图
├── api/
│   └── index.js
└── router/
    └── index.js
```

### 2. 文档集中

**优化前**：文档散落在各个目录

**优化后**：统一移到 `docs/`

```
KotlinCalendar/
├── docs/                                # 集中文档（不提交 Git）
│   ├── CALENDAR_SHARING_PLAN.md        # 日历共享规划（1214行）
│   ├── MAP_INTEGRATION_PLAN.md         # 地图集成规划（1172行）
│   ├── AI_ASSISTANT_PLAN.md            # AI 助手规划
│   ├── FINAL_ARCHITECTURE.md           # 最终架构
│   └── FEATURES_SUMMARY.md             # 功能总结
├── tc_camp_projects/kotlin_calendar/
│   ├── README.md
│   ├── overview.md
│   └── day01~day11.md
└── (其他代码目录)
```

### 3. .gitignore 优化

```gitignore
# 文档（本地化，不提交）
docs/

# Android 源码
adapp/*
!adapp/README.md

# AcWing 源码
acapp_frontend/

# Web node_modules
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

**优势**：
- ✅ 仓库更轻量（只保留必要文件）
- ✅ 文档本地管理（敏感信息不泄漏）
- ✅ 构建产物和源码分离

---

## 📚 详细功能规划文档

### 1. 日历共享规划（CALENDAR_SHARING_PLAN.md）

**篇幅**：1214 行

**核心内容**：

#### 功能 1：公开日历订阅
```
用户案例：
- 订阅学校课程表（50门课自动同步）
- 订阅体育赛事（NBA/英超/中超）
- 订阅假期日历（法定假日）
- 订阅生日提醒（班级通讯录）
```

#### 功能 2：选择性同步（创新）
```
痛点：订阅50门课，但只上5门
传统方案：全部同步（干扰）
我们的方案：选择性同步

实现：
1. 订阅源返回所有课程
2. 用户勾选需要的课程
3. 只同步勾选的课程
4. 源更新时，只更新勾选的课程

优势：
✅ 减少干扰
✅ 提升效率
✅ 行业首创
```

#### 功能 3：共享事件
```
场景：
- 发送会议邀请
- 分享生日聚会
- 组织团队活动

实现：
1. 生成分享链接
2. 接收者点击链接
3. 自动添加到日历
4. 实时同步变更
```

#### 市场分析
```
目标用户：7500万
- 大学生：4000万
- 上班族：3000万
- 其他：500万

竞品分析：
- Google Calendar：功能全，但慢
- Outlook Calendar：企业向
- 滴答清单：无日历订阅
- 我们的优势：选择性同步
```

### 2. 地图集成规划（MAP_INTEGRATION_PLAN.md）

**篇幅**：1172 行

**核心内容**：

#### 功能 1：地图导航
```
实现方案：
1. 用户输入地点
2. 调用高德地图 API
3. 获取坐标
4. 生成导航链接
5. 点击跳转高德/百度地图
```

#### 功能 2：智能出发提醒（亮点）
```
传统方案：提前1小时提醒
我们的方案：实时路况计算

实现流程：
1. 事件前2小时，开始监控
2. 每15分钟查询实时路况
3. 计算当前路况下的通勤时间
4. 当"通勤时间 + 缓冲10分钟 = 剩余时间"时提醒

示例：
- 会议：9:00 AM
- 当前：7:30 AM
- 实时路况：需要1小时20分钟
- 系统计算：7:30 + 1:20 + 0:10 = 9:00
- 提醒：请立即出发！
```

#### 功能 3：地图视图
```
在地图上显示所有事件：
- 按地点聚合事件
- 点击地点查看事件列表
- 支持筛选（今天/本周/本月）
```

### 3. AI 助手规划（AI_ASSISTANT_PLAN.md）

**核心功能**：
- 语音创建事件
- 智能时间建议
- 冲突检测
- 自动分类

### 4. 最终架构（FINAL_ARCHITECTURE.md）

**架构演进**：
```
v1.0: Android 单机版
v2.0: 三客户端 + 云同步
v3.0: 用户认证 + 权限管理
v4.0: 日历订阅 + 地图集成（规划中）
v5.0: AI 助手（未来）
```

### 5. 功能总结（FEATURES_SUMMARY.md）

**功能清单**：
- ✅ 已完成：15 项
- ⏳ 开发中：0 项
- 📋 规划中：12 项

---

## 💎 技术亮点

### 1. Token 自动刷新机制

**创新点**：
- ✅ 请求队列管理（避免并发刷新）
- ✅ 无感刷新（用户无感知）
- ✅ 失败降级（自动跳转登录）

**代码量**：50+ 行（精简高效）

### 2. end_time 可选设计

**传统方案**：
- 方案 A：强制填写 end_time（用户体验差）
- 方案 B：默认 +1 小时（不灵活）

**我们的方案**：
```vue
<el-checkbox v-model="hasEndTime">设置结束时间</el-checkbox>
<el-date-picker v-if="hasEndTime" v-model="form.end_time" />
```

**优势**：
- ✅ 支持时间点事件（生日、纪念日）
- ✅ 支持时段事件（会议、旅行）
- ✅ 用户自由选择

### 3. 灵活的权限设计

**API 权限**：
```python
# 只读或认证
'DEFAULT_PERMISSION_CLASSES': [
    'rest_framework.permissions.IsAuthenticatedOrReadOnly',
]
```

**效果**：
- ✅ 未登录可查看公开日历
- ✅ 登录后可增删改查
- ✅ 引导用户登录

---

## 🐛 解决的问题

### 问题 1: AcWing 端界面被遮挡

**现象**：日历网格太大，工具栏被遮挡

**解决**：
1. 日历网格缩小 30%（70px → 48px）
2. 移除 TodayCard（节省 80px 高度）
3. 表单紧凑化（600px → 450px）

**效果**：✅ 所有内容完整显示

### 问题 2: Django 版本不兼容

**报错**：
```
django.core.exceptions.ImproperlyConfigured:
Application labels aren't unique, duplicates: auth
```

**原因**：Django 5.0 与 simplejwt 5.3 不兼容

**解决**：
```bash
# 降级到 LTS 版本
pip install Django==4.2.16
pip install djangorestframework-simplejwt==5.2.2
```

### 问题 3: 数据库迁移冲突

**现象**：多次修改模型，迁移文件冲突

**解决**：
```bash
# 删除旧数据库和迁移文件
rm backend/db.sqlite3
rm backend/api/migrations/000*.py

# 重新迁移
python manage.py makemigrations
python manage.py migrate

# 创建超级用户
python manage.py createsuperuser
```

### 问题 4: 字段名不匹配

**现象**：前端发送 `date_time`，后端接收 `start_time`

**解决**：统一字段名
- ✅ 后端：`start_time`, `end_time`
- ✅ 前端：`start_time`, `end_time`
- ✅ 序列化器：自动映射

### 问题 5: Git 连接超时

**现象**：`git push` 经常超时

**解决**：
```bash
# 增加超时时间
git config --global http.postBuffer 524288000

# 使用 SSH 代替 HTTPS（可选）
git remote set-url origin git@github.com:username/repo.git
```

---

## 💡 重要决策

### 架构决策

**决策 1：Android 本地优先**
- ✅ Room 数据库离线可用
- ✅ 不强制登录
- ✅ 提供本地提醒功能

**决策 2：Web/AcWing 云端优先**
- ✅ JWT 认证
- ✅ 云端存储
- ✅ 多设备同步

**决策 3：提醒功能暂缓**
- ⏸️ Android 端已实现（AlarmManager）
- ⏸️ Web/AcWing 端暂不实现（复杂度高）
- ✅ 专注差异化功能（日历订阅、地图集成）

### 产品决策

**决策 1：end_time 可选**
- ✅ 符合用户习惯
- ✅ 灵活性高

**决策 2：未登录只读**
- ✅ 降低使用门槛
- ✅ 引导用户登录

**决策 3：文档本地化**
- ✅ 规划文档不提交 Git（3500+ 行太大）
- ✅ 本地管理（敏感信息不泄漏）

---

## 📊 工作量统计

### 代码统计

| 指标 | 数量 |
|-----|------|
| **Git 提交** | 25+ 次 |
| **新增代码** | 2500+ 行 |
| **新增组件** | 1 个（LoginView）|
| **优化组件** | 8 个 |
| **数据库迁移** | 2 次 |
| **构建次数** | 15+ 次 |

### 文档统计

| 文档 | 行数 | 说明 |
|-----|------|------|
| CALENDAR_SHARING_PLAN.md | 1214 | 日历共享规划 |
| MAP_INTEGRATION_PLAN.md | 1172 | 地图集成规划 |
| AI_ASSISTANT_PLAN.md | 400+ | AI 助手规划 |
| FINAL_ARCHITECTURE.md | 350+ | 最终架构 |
| FEATURES_SUMMARY.md | 400+ | 功能总结 |
| **合计** | **3500+** | 5 个规划文档 |

---

## 🌟 今日成就

### 从"能用的日历"升级到"有商业潜力的产品"

**技术维度**：
- ✅ JWT 认证体系成熟
- ✅ Token 自动刷新机制
- ✅ 三端 UI 优化完成
- ✅ 数据模型重构（支持时间段）

**产品维度**：
- ✅ 用户系统完善
- ✅ 功能规划清晰
- ✅ 商业模式可行
- ✅ 市场定位明确

**文档维度**：
- ✅ 3500+ 行规划文档
- ✅ 为未来 6 个月指明方向
- ✅ 可作为产品提案

---

## 🎯 Day 11 核心价值

### 1. 技术成熟度：⭐⭐⭐⭐⭐

**完整的用户认证系统**：
- 注册/登录
- Token 刷新
- 权限管理

**优秀的 UI 适配**：
- 适配 630x521 小容器
- 紧凑化布局
- 良好的交互体验

### 2. 产品化程度：⭐⭐⭐⭐⭐

**清晰的功能规划**：
- 日历共享（选择性同步创新）
- 地图集成（智能出发提醒）
- AI 助手（未来愿景）

**可行的商业模式**：
- 免费版（基础功能）
- VIP 版（高级功能）
- 企业版（定制服务）

### 3. 工程化水平：⭐⭐⭐⭐⭐

**模块化架构**：
- 组件分类清晰
- 文档集中管理
- .gitignore 优化

**规范化开发**：
- 数据库迁移
- API 接口统一
- 代码质量高

---

## 🎊 总结

### 今天完成了从"作业"到"产品"的升级！

**成就**：
- ✅ 实现完整的用户认证系统
- ✅ 优化三端 UI（尤其是 AcWing 小容器）
- ✅ 重构数据模型（支持时间段）
- ✅ 编写 3500+ 行规划文档
- ✅ 明确未来 6 个月开发方向

**这是一个里程碑式的进展！** 🚀

### Day 11 亮点

1. **JWT 认证体系** - Token 自动刷新机制
2. **UI 紧凑化** - 完美适配小容器
3. **创新功能规划** - 选择性同步、智能出发提醒
4. **商业化思维** - 清晰的商业模式和市场定位

---

**工作时长**: ~8 小时  
**代码行数**: 2500+ 行  
**文档行数**: 3500+ 行  
**Git 提交**: 25+ 次  
**解决问题**: 5+ 个  

**Day 11 完美收官！三客户端架构 + 用户认证 + 功能规划全部完成！** 💪🔥

