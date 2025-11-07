# 📅 Day 14: QQ OAuth2 一键登录 + 用户个人中心 + JWT 核心问题修复

> **日期**：2025年11月7日  
> **用时**：约8小时  
> **难度**：⭐⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 重要突破

### 核心问题修复 ⭐⭐⭐⭐⭐

**修复了影响全局的 JWT 认证问题！**

**问题**：所有需要认证的 API 返回 403 Forbidden  
**原因**：`DEFAULT_AUTHENTICATION_CLASSES` 被注释  
**影响**：用户信息获取、导航栏头像、个人中心全部失效  
**修复后**：所有认证功能恢复正常 ✅

---

## 🎯 今日目标

### 核心任务
1. ✅ 实现 Web 端 QQ OAuth2 一键登录
2. ✅ 开发用户个人中心页面
3. ✅ 修复 JWT 认证核心问题（关键！）
4. ✅ 模型模块化重构（提升可维护性）
5. ✅ 代码清理优化（删除调试日志）
6. ✅ 项目结构优化
7. ✅ 处理数据库迁移问题

### 完成情况

**进度**：100% ✅  
**Git 提交**：25+ 次  
**新增文件**：11 个  
**删除文件**：4 个  
**修改文件**：20+ 个  
**代码清理**：35+ 处调试输出  
**解决问题**：8 个关键问题  
**新增 API**：8 个端点

---

## 🔐 QQ OAuth2 授权流程

### 1. 完整流程图

```
┌─────────────────────────────────────────────────────────────┐
│              QQ OAuth2 Authorization Flow                    │
└─────────────────────────────────────────────────────────────┘

[1] 用户访问登录页面
        ↓
[2] 点击 "QQ 登录" 按钮
        ↓
[3] 构建授权 URL
    https://graph.qq.com/oauth2.0/authorize
    ?response_type=code
    &client_id=102814915
    &redirect_uri=https://app7626.acapp.acwing.com.cn/qq/callback
    &state=random_string
    &scope=get_user_info
        ↓
[4] 跳转到 QQ 授权页面
        ↓
[5] 用户在 QQ 授权
        ↓
[6] QQ 重定向到 redirect_uri
    携带参数: code, state
        ↓
[7] 前端回调页面 QQCallback.vue
    显示加载动画
        ↓
[8] 调用后端 /api/auth/qq/login/
    POST {code, state}
        ↓
[9] 后端三步获取用户信息：
    Step 1: code → access_token
    Step 2: access_token → openid
    Step 3: access_token + openid → 用户信息
        ↓
[10] 后端创建/更新 QQUser
     生成 JWT token
        ↓
[11] 返回 JWT token 和用户信息
        ↓
[12] 前端保存 token 到 localStorage
        ↓
[13] 跳转到日历页面（强制刷新）
```

### 2. QQ API 特殊处理

#### QQ 与 AcWing OAuth2 的区别

| 特性 | AcWing | QQ | 难度 |
|-----|--------|-----|------|
| **授权端点** | AcWing API | graph.qq.com | 相同 |
| **Token 格式** | JSON | **URL 参数** | ⭐⭐⭐⭐ |
| **OpenID 格式** | JSON | **JSONP** | ⭐⭐⭐⭐⭐ |
| **用户信息格式** | JSON | JSON | 相同 |
| **步骤数** | 2 步 | **3 步** | ⭐⭐⭐⭐ |

**QQ OAuth2 更复杂！**

---

## 🔧 后端实现

### 1. QQUser 模型

```python
# backend/api/models/user.py

from django.db import models
from django.contrib.auth.models import User

class QQUser(models.Model):
    """QQ 用户信息"""
    user = models.OneToOneField(
        User,
        on_delete=models.CASCADE,
        related_name='qq_profile'
    )
    openid = models.CharField(max_length=100, unique=True, verbose_name="QQ OpenID")
    access_token = models.CharField(max_length=200, blank=True, verbose_name="访问令牌")
    refresh_token = models.CharField(max_length=200, blank=True, verbose_name="刷新令牌")
    photo_url = models.URLField(blank=True, verbose_name="头像URL")
    nickname = models.CharField(max_length=100, blank=True, verbose_name="昵称")
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = 'qq_users'
        verbose_name = 'QQ 用户'
        verbose_name_plural = 'QQ 用户'
    
    def __str__(self):
        return f"{self.nickname} (OpenID: {self.openid})"
```

**设计要点**：
- ✅ OneToOne 关联 Django User
- ✅ `openid` 唯一标识 QQ 用户
- ✅ 存储 access_token 和 refresh_token（可选）
- ✅ 存储昵称和头像

### 2. 模型模块化重构

#### 重构前

```
backend/api/models.py  (103 行，所有模型混在一起)
```

#### 重构后

```
backend/api/models/
    __init__.py         (15 行，统一导入)
    user.py            (54 行，用户相关模型)
    event.py           (33 行，事件相关模型)
    calendar.py        (35 行，日历相关模型)
```

#### models/__init__.py

```python
# backend/api/models/__init__.py

from .user import AcWingUser, QQUser
from .event import Event
from .calendar import PublicCalendar

__all__ = [
    'AcWingUser',
    'QQUser',
    'Event',
    'PublicCalendar',
]
```

**优势**：
- ✅ 代码结构更清晰
- ✅ 每个文件职责单一
- ✅ 易于维护和扩展
- ✅ 符合 Django 最佳实践

### 3. QQ 登录接口实现

```python
# backend/api/views/auth.py

import requests
import urllib.parse
import re
import json
from django.conf import settings
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from rest_framework import status
from rest_framework_simplejwt.tokens import RefreshToken
from django.contrib.auth.models import User
from api.models import QQUser

@api_view(['POST'])
@permission_classes([AllowAny])
def qq_login(request):
    """
    QQ 一键登录
    
    QQ OAuth2 三步流程：
    1. code → access_token
    2. access_token → openid
    3. access_token + openid → 用户信息
    """
    code = request.data.get('code')
    
    if not code:
        return Response(
            {'error': 'Code is required'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    try:
        # Step 1: 用 code 换取 access_token
        token_url = 'https://graph.qq.com/oauth2.0/token'
        token_params = {
            'grant_type': 'authorization_code',
            'client_id': settings.QQ_APPID,
            'client_secret': settings.QQ_APPKEY,
            'code': code,
            'redirect_uri': settings.QQ_REDIRECT_URI
        }
        
        token_response = requests.get(token_url, params=token_params, timeout=10)
        token_text = token_response.text
        
        # QQ 返回格式：access_token=xxx&expires_in=7776000&refresh_token=xxx
        # 需要手动解析（不是 JSON！）
        token_dict = urllib.parse.parse_qs(token_text)
        
        if 'access_token' not in token_dict:
            return Response(
                {'error': f'Failed to get access_token: {token_text}'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        access_token = token_dict['access_token'][0]
        refresh_token = token_dict.get('refresh_token', [''])[0]
        
        # Step 2: 用 access_token 获取 openid
        openid_url = f'https://graph.qq.com/oauth2.0/me?access_token={access_token}'
        
        openid_response = requests.get(openid_url, timeout=10)
        openid_text = openid_response.text
        
        # QQ 返回格式：callback( {"client_id":"YOUR_APPID","openid":"YOUR_OPENID"} );
        # JSONP 格式，需要正则提取（不是标准 JSON！）
        match = re.search(r'callback\(\s*(\{.*?\})\s*\)', openid_text)
        
        if not match:
            return Response(
                {'error': f'Failed to parse openid: {openid_text}'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        openid_data = json.loads(match.group(1))
        openid = openid_data.get('openid')
        
        if not openid:
            return Response(
                {'error': 'OpenID not found'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        # Step 3: 用 access_token + openid 获取用户信息
        userinfo_url = 'https://graph.qq.com/user/get_user_info'
        userinfo_params = {
            'access_token': access_token,
            'oauth_consumer_key': settings.QQ_APPID,
            'openid': openid
        }
        
        userinfo_response = requests.get(userinfo_url, params=userinfo_params, timeout=10)
        userinfo = userinfo_response.json()
        
        if userinfo.get('ret') != 0:
            return Response(
                {'error': f"QQ error: {userinfo.get('msg')}"},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        nickname = userinfo.get('nickname')
        # QQ 头像优先使用大图
        photo_url = userinfo.get('figureurl_qq_2') or userinfo.get('figureurl_qq_1', '')
        
        # 创建或更新用户
        qq_user = QQUser.objects.filter(openid=openid).first()
        
        if qq_user:
            # 更新用户信息
            qq_user.nickname = nickname
            qq_user.photo_url = photo_url
            qq_user.access_token = access_token
            qq_user.refresh_token = refresh_token
            qq_user.save()
            
            user = qq_user.user
            
            # 更新 Django User 的用户名（避免冲突）
            if user.username != nickname:
                if not User.objects.filter(username=nickname).exclude(id=user.id).exists():
                    user.username = nickname
                    user.save()
        else:
            # 创建新用户
            django_user = User.objects.create_user(
                username=f'qq_{openid}',
                password=None
            )
            
            qq_user = QQUser.objects.create(
                user=django_user,
                openid=openid,
                nickname=nickname,
                photo_url=photo_url,
                access_token=access_token,
                refresh_token=refresh_token
            )
        
        # 生成 JWT token
        refresh = RefreshToken.for_user(qq_user.user)
        
        return Response({
            'access': str(refresh.access_token),
            'refresh': str(refresh),
            'user': {
                'id': qq_user.user.id,
                'username': qq_user.nickname,
                'photo': qq_user.photo_url
            }
        })
        
    except requests.exceptions.Timeout:
        return Response(
            {'error': 'Request timeout'},
            status=status.HTTP_504_GATEWAY_TIMEOUT
        )
    except Exception as e:
        return Response(
            {'error': f'Internal error: {str(e)}'},
            status=status.HTTP_500_INTERNAL_SERVER_ERROR
        )
```

**关键点**：
1. ✅ 三步流程完整实现
2. ✅ URL 参数解析（urllib.parse）
3. ✅ JSONP 格式解析（正则表达式）
4. ✅ 超时处理（10 秒）
5. ✅ 错误处理完善

### 4. 环境变量配置

```python
# backend/calendar_backend/settings.py

# QQ OAuth2 配置
QQ_APPID = os.environ.get('QQ_APPID', '')
QQ_APPKEY = os.environ.get('QQ_APPKEY', '')
QQ_REDIRECT_URI = os.environ.get('QQ_REDIRECT_URI', 'https://app7626.acapp.acwing.com.cn/qq/callback')

if not QQ_APPID or not QQ_APPKEY:
    print('Warning: QQ OAuth2 credentials not configured')
```

```bash
# backend/.env

QQ_APPID=102814915
QQ_APPKEY=your_app_key_here
QQ_REDIRECT_URI=https://app7626.acapp.acwing.com.cn/qq/callback
```

---

## 🌐 前端实现

### 1. QQ 登录按钮（LoginView）

```vue
<!-- web/calendar_web/src/views/account/LoginView.vue -->

<template>
  <!-- ... 其他代码 -->
  
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
    
    <!-- QQ 登录 -->
    <el-button
      class="oauth-button qq"
      @click="handleQQLogin"
    >
      <img
        src="https://app7626.acapp.acwing.com.cn/static/images/qq_login.png"
        alt="QQ"
        class="oauth-icon"
      />
      QQ {{ isLogin ? '登录' : '注册' }}
    </el-button>
  </div>
</template>

<script setup>
/**
 * QQ 登录
 */
const handleQQLogin = () => {
  const appid = '102814915'
  const redirect_uri = encodeURIComponent(`${window.location.origin}/qq/callback`)
  const state = Math.random().toString(36).substring(2)
  const scope = 'get_user_info'
  
  // 保存 state 用于验证
  localStorage.setItem('qq_state', state)
  
  // 跳转到 QQ 授权页面
  const authUrl = `https://graph.qq.com/oauth2.0/authorize?response_type=code&client_id=${appid}&redirect_uri=${redirect_uri}&state=${state}&scope=${scope}`
  
  window.location.href = authUrl
}
</script>

<style scoped>
.oauth-button.qq {
  background: linear-gradient(135deg, #12B7F5, #0B8CDC);
  color: white;
  border: none;
}

.oauth-button.qq:hover {
  background: linear-gradient(135deg, #0B8CDC, #0868A8);
}
</style>
```

### 2. QQ 回调页面

```vue
<!-- web/calendar_web/src/views/account/QQCallback.vue -->

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

const route = useRoute()
const router = useRouter()

const loading = ref(true)
const error = ref('')

onMounted(async () => {
  const code = route.query.code
  const state = route.query.state
  const savedState = localStorage.getItem('qq_state')
  
  // 验证 state
  if (state !== savedState) {
    error.value = '安全验证失败，请重试'
    loading.value = false
    return
  }
  
  if (!code) {
    error.value = '授权失败：未获取到授权码'
    loading.value = false
    return
  }
  
  try {
    const response = await fetch('https://app7626.acapp.acwing.com.cn/api/auth/qq/login/', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ code, state })
    })
    
    const data = await response.json()
    
    if (response.ok) {
      // 保存 token
      localStorage.setItem('access_token', data.access)
      localStorage.setItem('refresh_token', data.refresh)
      localStorage.setItem('user', JSON.stringify(data.user))
      
      // 清除 state
      localStorage.removeItem('qq_state')
      
      ElMessage.success('登录成功！')
      
      loading.value = false
      
      // 跳转到日历页面
      setTimeout(() => {
        window.location.href = '/calendar'
      }, 1000)
    } else {
      throw new Error(data.error || '登录失败')
    }
  } catch (err) {
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

### 3. UserSerializer 更新

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
        
        优先级：QQ > AcWing > 默认
        """
        try:
            # 优先返回 QQ 头像
            if hasattr(obj, 'qq_profile'):
                return obj.qq_profile.photo_url
            # 其次返回 AcWing 头像
            if hasattr(obj, 'acwing'):
                return obj.acwing.photo
        except:
            pass
        
        # 返回默认头像
        return 'https://www.acwing.com/media/user/profile/photo/default.png'
```

---

## 🧹 代码清理

### 1. 删除调试日志

#### 后端清理（20+ 处）

```python
# ❌ 删除前
logger.info(f"[AcWing Login] Received code: {code}")
logger.info(f"[AcWing Login] AppID: {settings.ACWING_APPID}")
logger.info(f"[AcWing Login] Token response: {data}")
logger.info(f"[AcWing Login] User info response: {userinfo}")
# ... 更多调试日志

# ✅ 删除后
# 只保留关键错误日志
logger.error(f"[AcWing Login] Token error: {data.get('errmsg')}")
logger.error(f"[AcWing Login] Internal error: {str(e)}")
```

#### 前端清理（15+ 处）

```javascript
// ❌ 删除前
console.log('AcWing callback:', { code, state })
console.log('Login success:', data.user.username)
console.log('✅ Token 有效，用户:', user)
console.log('🔐 开始 AcWing OAuth2 授权流程...')
// ... 更多调试输出

// ✅ 删除后
// 只保留关键错误日志
console.error('Login error:', err)
console.error('❌ Token 验证失败:', error)
```

### 2. 删除临时文件

```bash
# 删除的文件
- images/AcWing_logo.png  (移到 backend/static/images/)
- images/QQ_logo.png      (移到 backend/static/images/)
- images/qq_login.png     (移到 backend/static/images/)
- temp_debug.log
```

### 3. 优化注释

```python
# ❌ 过度注释
# 这个函数用于获取用户信息
# 参数：obj - 用户对象
# 返回值：用户头像 URL
def get_photo(self, obj):
    # 首先检查用户是否有 QQ 账号
    if hasattr(obj, 'qq_profile'):
        # 返回 QQ 头像
        return obj.qq_profile.photo_url
    # 如果没有 QQ 账号，检查 AcWing
    if hasattr(obj, 'acwing'):
        # 返回 AcWing 头像
        return obj.acwing.photo
    # 如果都没有，返回默认头像
    return 'default.png'

# ✅ 恰当注释
def get_photo(self, obj):
    """获取用户头像（优先级：QQ > AcWing > 默认）"""
    try:
        if hasattr(obj, 'qq_profile'):
            return obj.qq_profile.photo_url
        if hasattr(obj, 'acwing'):
            return obj.acwing.photo
    except:
        pass
    return 'default.png'
```

---

## 👤 用户个人中心实现

### 1. 功能设计

#### 核心功能模块

```
用户个人中心
├── 用户信息展示
│   ├── 头像（120x120 圆形）
│   ├── 用户名
│   ├── 邮箱
│   └── 加入时间
├── 用户统计信息
│   ├── 📅 总日程数
│   ├── 📆 今日日程数
│   └── ⏰ 即将到来的日程数
├── 第三方账号绑定
│   ├── AcWing 绑定状态
│   ├── QQ 绑定状态
│   └── 解绑功能（智能保护）
├── 个人信息编辑
│   ├── 修改用户名
│   └── 修改邮箱
└── 修改密码
    └── 仅普通账号可用
```

### 2. 后端 API 实现

#### 用户统计 API

```python
# backend/api/views/user.py

from rest_framework.decorators import api_view, authentication_classes
from rest_framework_simplejwt.authentication import JWTAuthentication
from rest_framework.response import Response
from django.utils import timezone
from datetime import timedelta

@api_view(['GET'])
@authentication_classes([JWTAuthentication])
def user_stats(request):
    """获取用户统计信息"""
    user = request.user
    
    # 总日程数
    total_events = user.events.count()
    
    # 今日日程数
    today = timezone.now().date()
    today_events = user.events.filter(
        start_time__date=today
    ).count()
    
    # 即将到来的日程数（未来7天）
    next_week = today + timedelta(days=7)
    upcoming_events = user.events.filter(
        start_time__date__range=(today, next_week)
    ).count()
    
    return Response({
        'total_events': total_events,
        'today_events': today_events,
        'upcoming_events': upcoming_events
    })
```

#### 绑定状态 API

```python
@api_view(['GET'])
@authentication_classes([JWTAuthentication])
def user_bindings(request):
    """获取用户第三方账号绑定状态"""
    user = request.user
    
    return Response({
        'has_acwing': hasattr(user, 'acwing'),
        'has_qq': hasattr(user, 'qq_profile'),
        'has_password': user.has_usable_password(),
        'acwing_username': user.acwing.username if hasattr(user, 'acwing') else None,
        'qq_nickname': user.qq_profile.nickname if hasattr(user, 'qq_profile') else None
    })
```

#### 更新个人信息 API

```python
@api_view(['PATCH'])
@authentication_classes([JWTAuthentication])
def update_profile(request):
    """更新用户个人信息"""
    user = request.user
    
    username = request.data.get('username')
    email = request.data.get('email')
    
    # 更新用户名（检查重复）
    if username and username != user.username:
        if User.objects.filter(username=username).exclude(id=user.id).exists():
            return Response(
                {'error': '用户名已被使用'},
                status=status.HTTP_400_BAD_REQUEST
            )
        user.username = username
    
    # 更新邮箱
    if email is not None:
        user.email = email
    
    user.save()
    
    return Response({
        'id': user.id,
        'username': user.username,
        'email': user.email
    })
```

#### 修改密码 API

```python
@api_view(['POST'])
@authentication_classes([JWTAuthentication])
def change_password(request):
    """修改密码"""
    user = request.user
    
    # 检查是否是 OAuth 用户
    if not user.has_usable_password():
        return Response(
            {'error': 'OAuth 账号无密码，无需修改'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    old_password = request.data.get('old_password')
    new_password = request.data.get('new_password')
    
    # 验证旧密码
    if not user.check_password(old_password):
        return Response(
            {'error': '旧密码错误'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    # 设置新密码
    user.set_password(new_password)
    user.save()
    
    return Response({'message': '密码修改成功，请重新登录'})
```

#### 解绑账号 API

```python
@api_view(['DELETE'])
@authentication_classes([JWTAuthentication])
def unbind_acwing(request):
    """解绑 AcWing 账号"""
    user = request.user
    
    # 检查是否至少有一种登录方式
    has_password = user.has_usable_password()
    has_qq = hasattr(user, 'qq_profile')
    
    if not has_password and not has_qq:
        return Response(
            {'error': '至少保留一种登录方式'},
            status=status.HTTP_400_BAD_REQUEST
        )
    
    # 删除 AcWing 绑定
    if hasattr(user, 'acwing'):
        user.acwing.delete()
    
    return Response({'message': 'AcWing 账号已解绑'})
```

### 3. 前端个人中心页面

```vue
<!-- web/calendar_web/src/views/account/ProfileView.vue -->

<template>
  <div class="profile-container">
    <NavBar />
    
    <div class="profile-content">
      <!-- 用户信息卡片 -->
      <el-card class="user-card">
        <div class="user-header">
          <el-avatar :src="user?.photo" :size="120">
            {{ user?.username[0].toUpperCase() }}
          </el-avatar>
          <div class="user-info">
            <h2>{{ user?.username }}</h2>
            <p>{{ user?.email || '未设置邮箱' }}</p>
            <p class="join-date">加入时间：{{ formatDate(user?.created_at) }}</p>
          </div>
        </div>
      </el-card>
      
      <!-- 统计信息卡片 -->
      <el-card class="stats-card">
        <template #header>
          <h3>我的统计</h3>
        </template>
        <div class="stats-grid">
          <div class="stat-item">
            <div class="stat-icon">📅</div>
            <div class="stat-value">{{ stats.total_events }}</div>
            <div class="stat-label">总日程数</div>
          </div>
          <div class="stat-item">
            <div class="stat-icon">📆</div>
            <div class="stat-value">{{ stats.today_events }}</div>
            <div class="stat-label">今日日程</div>
          </div>
          <div class="stat-item">
            <div class="stat-icon">⏰</div>
            <div class="stat-value">{{ stats.upcoming_events }}</div>
            <div class="stat-label">即将到来</div>
          </div>
        </div>
      </el-card>
      
      <!-- 第三方账号绑定 -->
      <el-card class="bindings-card">
        <template #header>
          <h3>第三方账号</h3>
        </template>
        <div class="binding-list">
          <!-- AcWing -->
          <div class="binding-item">
            <div class="binding-info">
              <img
                src="https://app7626.acapp.acwing.com.cn/static/images/AcWing_logo.png"
                class="binding-icon"
              />
              <div>
                <div class="binding-name">AcWing</div>
                <div class="binding-status" v-if="bindings.has_acwing">
                  ✅ 已绑定：{{ bindings.acwing_username }}
                </div>
                <div class="binding-status" v-else>
                  ⚪ 未绑定
                </div>
              </div>
            </div>
            <el-button
              v-if="bindings.has_acwing"
              size="small"
              :disabled="!canUnbind"
              @click="unbindAcWing"
            >
              解绑
            </el-button>
          </div>
          
          <!-- QQ -->
          <div class="binding-item">
            <div class="binding-info">
              <img
                src="https://app7626.acapp.acwing.com.cn/static/images/qq_login.png"
                class="binding-icon"
              />
              <div>
                <div class="binding-name">QQ</div>
                <div class="binding-status" v-if="bindings.has_qq">
                  ✅ 已绑定：{{ bindings.qq_nickname }}
                </div>
                <div class="binding-status" v-else>
                  ⚪ 未绑定
                </div>
              </div>
            </div>
            <el-button
              v-if="bindings.has_qq"
              size="small"
              :disabled="!canUnbind"
              @click="unbindQQ"
            >
              解绑
            </el-button>
          </div>
        </div>
        <el-alert
          v-if="!canUnbind"
          type="warning"
          :closable="false"
          show-icon
          style="margin-top: 16px"
        >
          至少保留一种登录方式
        </el-alert>
      </el-card>
      
      <!-- 编辑个人信息 -->
      <el-card class="edit-card">
        <template #header>
          <h3>编辑资料</h3>
        </template>
        <el-form label-width="100px">
          <el-form-item label="用户名">
            <el-input v-model="editForm.username" />
          </el-form-item>
          <el-form-item label="邮箱">
            <el-input v-model="editForm.email" />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="saveProfile">
              保存修改
            </el-button>
          </el-form-item>
        </el-form>
      </el-card>
      
      <!-- 修改密码（仅普通账号） -->
      <el-card class="password-card" v-if="bindings.has_password">
        <template #header>
          <h3>修改密码</h3>
        </template>
        <el-form label-width="100px">
          <el-form-item label="旧密码">
            <el-input v-model="passwordForm.old_password" type="password" />
          </el-form-item>
          <el-form-item label="新密码">
            <el-input v-model="passwordForm.new_password" type="password" />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="changePassword">
              修改密码
            </el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage, ElMessageBox } from 'element-plus'
import api from '@/api'
import NavBar from '@/components/NavBar.vue'

const router = useRouter()

const user = ref(null)
const stats = reactive({
  total_events: 0,
  today_events: 0,
  upcoming_events: 0
})
const bindings = reactive({
  has_acwing: false,
  has_qq: false,
  has_password: false,
  acwing_username: '',
  qq_nickname: ''
})

const editForm = reactive({
  username: '',
  email: ''
})

const passwordForm = reactive({
  old_password: '',
  new_password: ''
})

// 是否可以解绑（至少保留一种登录方式）
const canUnbind = computed(() => {
  const count = [
    bindings.has_acwing,
    bindings.has_qq,
    bindings.has_password
  ].filter(Boolean).length
  
  return count > 1
})

// 加载用户信息
const loadUserInfo = async () => {
  try {
    const { data } = await api.getCurrentUser()
    user.value = data
    editForm.username = data.username
    editForm.email = data.email
  } catch (error) {
    ElMessage.error('加载用户信息失败')
  }
}

// 加载统计信息
const loadStats = async () => {
  try {
    const { data } = await api.getUserStats()
    Object.assign(stats, data)
  } catch (error) {
    ElMessage.error('加载统计信息失败')
  }
}

// 加载绑定状态
const loadBindings = async () => {
  try {
    const { data } = await api.getUserBindings()
    Object.assign(bindings, data)
  } catch (error) {
    ElMessage.error('加载绑定状态失败')
  }
}

// 保存个人信息
const saveProfile = async () => {
  try {
    await api.updateProfile(editForm)
    ElMessage.success('保存成功')
    loadUserInfo()
  } catch (error) {
    ElMessage.error(error.response?.data?.error || '保存失败')
  }
}

// 修改密码
const changePassword = async () => {
  if (!passwordForm.old_password || !passwordForm.new_password) {
    ElMessage.warning('请填写完整')
    return
  }
  
  if (passwordForm.new_password.length < 6) {
    ElMessage.warning('新密码至少 6 个字符')
    return
  }
  
  try {
    await api.changePassword(passwordForm)
    ElMessage.success('密码修改成功，请重新登录')
    
    // 清除 token，跳转登录
    setTimeout(() => {
      localStorage.clear()
      router.push('/login')
    }, 1500)
  } catch (error) {
    ElMessage.error(error.response?.data?.error || '修改失败')
  }
}

// 解绑 AcWing
const unbindAcWing = async () => {
  await ElMessageBox.confirm('确定要解绑 AcWing 账号吗？', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  })
  
  try {
    await api.unbindAcWing()
    ElMessage.success('解绑成功')
    loadBindings()
  } catch (error) {
    ElMessage.error(error.response?.data?.error || '解绑失败')
  }
}

// 解绑 QQ
const unbindQQ = async () => {
  await ElMessageBox.confirm('确定要解绑 QQ 账号吗？', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  })
  
  try {
    await api.unbindQQ()
    ElMessage.success('解绑成功')
    loadBindings()
  } catch (error) {
    ElMessage.error(error.response?.data?.error || '解绑失败')
  }
}

onMounted(() => {
  loadUserInfo()
  loadStats()
  loadBindings()
})
</script>

<style scoped>
.profile-container {
  min-height: 100vh;
  background: #f5f5f5;
}

.profile-content {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.user-card {
  margin-bottom: 20px;
}

.user-header {
  display: flex;
  align-items: center;
  gap: 30px;
}

.user-info h2 {
  margin: 0 0 10px 0;
}

.user-info p {
  margin: 5px 0;
  color: #666;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

.stat-item {
  text-align: center;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 8px;
  transition: transform 0.2s;
}

.stat-item:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.stat-icon {
  font-size: 32px;
  margin-bottom: 10px;
}

.stat-value {
  font-size: 28px;
  font-weight: bold;
  color: #409eff;
  margin-bottom: 5px;
}

.stat-label {
  color: #666;
  font-size: 14px;
}

.binding-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.binding-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  background: #f9f9f9;
  border-radius: 8px;
}

.binding-info {
  display: flex;
  align-items: center;
  gap: 16px;
}

.binding-icon {
  width: 32px;
  height: 32px;
  object-fit: contain;
}

.binding-name {
  font-weight: bold;
  margin-bottom: 4px;
}

.binding-status {
  font-size: 14px;
  color: #666;
}
</style>
```

### 4. 导航栏添加入口

```vue
<!-- web/calendar_web/src/components/NavBar.vue -->

<el-dropdown-menu>
  <el-dropdown-item disabled>
    <!-- 用户信息 -->
  </el-dropdown-item>
  <el-dropdown-item command="profile">
    <el-icon><User /></el-icon>
    个人中心
  </el-dropdown-item>
  <el-dropdown-item divided command="logout">
    <el-icon><SwitchButton /></el-icon>
    退出登录
  </el-dropdown-item>
</el-dropdown-menu>

<script setup>
const handleCommand = (command) => {
  if (command === 'profile') {
    router.push('/profile')
  } else if (command === 'logout') {
    handleLogout()
  }
}
</script>
```

---

## 🔥 JWT 认证核心问题修复

### 问题描述（重大 Bug！）

**现象**：
```
GET /api/auth/me/ 403 (Forbidden)
{
  "detail": "身份认证信息未提供。"
}
```

**影响范围**：
- ❌ 导航栏无法显示用户信息
- ❌ 个人中心无法访问
- ❌ 所有需要认证的 API 全部失效

### 问题追踪过程（耗时3小时）

#### 第1步：检查 Token

```javascript
// 前端检查
const token = localStorage.getItem('access_token')
console.log('Token:', token)  // ✅ Token 存在
```

#### 第2步：检查请求头

```javascript
// Chrome DevTools Network
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
// ✅ Authorization header 正确发送
```

#### 第3步：检查 Nginx 配置

```nginx
location /api/ {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:8000;
    
    # 确保传递 Authorization header
    uwsgi_param HTTP_AUTHORIZATION $http_authorization;
}
```

**结果**：✅ Nginx 配置正确

#### 第4步：检查 uWSGI 配置

```ini
[uwsgi]
socket = 127.0.0.1:8000
module = calendar_backend.wsgi:application
buffer-size = 65536
```

**结果**：✅ uWSGI 配置正确

#### 第5步：检查 CORS 配置

```python
CORS_ALLOWED_ORIGINS = [
    'https://www.acwing.com',
    'https://app7626.acapp.acwing.com.cn',
]

CORS_ALLOW_HEADERS = [
    'authorization',  # ✅ 允许 Authorization header
    'content-type',
]
```

**结果**：✅ CORS 配置正确

#### 第6步：检查 DRF 配置（发现问题！）

```python
# backend/calendar_backend/settings.py

REST_FRAMEWORK = {
    # ❌ 认证类被注释掉了！
    # 'DEFAULT_AUTHENTICATION_CLASSES': [
    #     'rest_framework_simplejwt.authentication.JWTAuthentication',
    # ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
}
```

**问题根源**：认证类被注释，DRF 无法识别 JWT Token！

### 解决方案

```python
# 启用 JWT 认证
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',  # ✅ 启用
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
}
```

**修复后**：
```
GET /api/auth/me/ 200 OK
{
  "id": 1,
  "username": "张三",
  "email": "test@example.com",
  "photo": "https://..."
}
```

**所有功能恢复正常！** ✅

---

## 🐛 其他问题解决记录

### 问题 1: 数据库迁移依赖错误

**报错**：
```
django.db.migrations.exceptions.NodeNotFoundError:
Migration api.0005_acwinguser dependencies reference nonexistent parent node ('api', '0004_merge_20251107_0811')
```

**原因分析**：
- 本地迁移历史与服务器不一致
- 本地有 `0004_merge` 迁移，服务器没有

**解决方案**：
```python
# 修改 0005_acwinguser.py 的依赖
dependencies = [
    ('api', '0002_event_reminder_minutes_alter_event_end_time_and_more'),
]
```

### 问题 2: 表已存在错误

**报错**：
```
django.db.utils.OperationalError: table "api_acwinguser" already exists
```

**原因分析**：
- 表在数据库中已存在
- 但迁移记录未同步

**解决方案**：
```bash
# 使用 fake migration
python3 manage.py migrate api 0005_acwinguser --fake
```

### 问题 3: Git 合并冲突（db.sqlite3）

**报错**：
```
error: Your local changes to the following files would be overwritten by merge:
        backend/db.sqlite3
```

**原因分析**：
- 数据库文件不应该提交到 Git
- 但已经被跟踪了

**解决方案**：
```bash
# 1. 暂存本地修改
git stash

# 2. 更新 .gitignore
echo "*.sqlite3" >> backend/.gitignore
echo "db.sqlite3" >> backend/.gitignore

# 3. 从 Git 中移除（但保留本地文件）
git rm --cached backend/db.sqlite3

# 4. 提交
git commit -m "chore: Remove db.sqlite3 from Git tracking"

# 5. 恢复本地修改
git stash pop
```

---

## 📊 开发统计

### 代码变更

| 指标 | 数量 |
|-----|------|
| **新增文件** | 11 个 |
| **删除文件** | 4 个 |
| **修改文件** | 20+ 个 |
| **Git 提交** | 25+ 次 |
| **代码清理** | 35+ 处 |
| **模型重构** | 1 → 4 文件 |
| **新增 API** | 8 个端点 |

### 时间分布

| 任务 | 用时 |
|-----|------|
| QQ OAuth2 实现 | 1.5h |
| 用户个人中心 | 2h |
| JWT 问题修复 | 3h |
| 模型重构 | 0.5h |
| 代码清理 | 0.5h |
| 问题修复 | 0.5h |
| **总计** | **8h** |

---

## 💡 技术亮点

### 1. QQ API 特殊格式处理

#### URL 参数解析

```python
# QQ 返回格式（不是 JSON！）
"access_token=xxx&expires_in=7776000&refresh_token=xxx"

# 解析方法
import urllib.parse
token_dict = urllib.parse.parse_qs(token_text)
access_token = token_dict['access_token'][0]
```

**为什么不用 JSON？**
- QQ 的 Token 接口返回的是 URL 参数格式
- 历史原因，保持向后兼容

#### JSONP 格式解析

```python
# QQ 返回格式（JSONP）
'callback( {"client_id":"YOUR_APPID","openid":"YOUR_OPENID"} );'

# 解析方法
import re
match = re.search(r'callback\(\s*(\{.*?\})\s*\)', openid_text)
openid_data = json.loads(match.group(1))
```

**为什么用 JSONP？**
- 跨域请求解决方案
- 早期 Web 开发的标准做法

### 2. 模型模块化最佳实践

**单一职责原则**：
```
user.py      - 用户相关（AcWingUser, QQUser）
event.py     - 事件相关（Event）
calendar.py  - 日历相关（PublicCalendar）
```

**统一导入接口**：
```python
# 其他文件导入时不需要关心具体结构
from api.models import QQUser, Event, PublicCalendar
```

### 3. 代码清理原则

**保留的日志**：
- ✅ 错误日志（logger.error）
- ✅ 关键操作日志（用户创建、更新）
- ✅ 安全相关日志（授权失败）

**删除的日志**：
- ❌ 调试日志（logger.info with debug info）
- ❌ 变量值打印（console.log(data)）
- ❌ 流程追踪日志（每一步的 log）

---

## 🎓 学习收获

### 1. QQ OAuth2 特殊处理

**关键点**：
- URL 参数解析（urllib.parse）
- JSONP 格式解析（正则表达式）
- 三步流程（比 AcWing 多一步）

### 2. Django 模型组织

**最佳实践**：
- 按功能拆分模块
- 使用 `__init__.py` 统一导入
- 保持向后兼容

### 3. 代码清理技巧

**原则**：
- 只保留必要的日志
- 删除调试代码
- 优化注释

### 4. 数据库迁移管理

**技巧**：
- 检查迁移依赖
- 使用 `--fake` 同步记录
- `.gitignore` 数据库文件

### 5. Git 最佳实践

**规范**：
- 数据库文件不提交
- 二进制文件用 `.gitattributes`
- 敏感信息用 `.env`

---

## 📁 项目结构优化对比

### 优化前

```
backend/api/
  ├── models.py              (103 行，混乱)
  ├── views.py               (300+ 行，混乱)
  └── serializers.py

web_frontend/src/
  └── views/account/
      ├── LoginView.vue
      └── AcWingCallback.vue
```

### 优化后

```
backend/api/
  ├── models/                ⭐ 模块化
  │   ├── __init__.py
  │   ├── user.py           (54 行)
  │   ├── event.py          (33 行)
  │   └── calendar.py       (35 行)
  ├── views/                 ✅ 已模块化
  │   ├── __init__.py
  │   ├── auth.py
  │   ├── events.py
  │   ├── calendars.py
  │   ├── lunar.py
  │   └── oauth_callback.py
  ├── serializers.py         (优化注释)
  └── urls.py

web_frontend/src/
  └── views/account/
      ├── LoginView.vue      (支持 AcWing + QQ)
      ├── AcWingCallback.vue
      └── QQCallback.vue     ⭐ 新增
```

---

## ✅ 功能测试

### 测试清单

| 测试项 | 状态 | 说明 |
|--------|------|------|
| QQ 登录按钮显示 | ✅ | 带图标和渐变 |
| 点击跳转到 QQ 授权 | ✅ | URL 正确 |
| 授权成功回调 | ✅ | 显示加载动画 |
| Token 保存 | ✅ | localStorage 存储 |
| 用户信息显示 | ✅ | 昵称和头像 |
| 导航栏更新 | ✅ | 强制刷新页面 |
| AcWing 登录（复测）| ✅ | 功能正常 |
| 代码清理 | ✅ | 无调试日志 |
| 模型重构 | ✅ | 导入正常 |

### 已支持的登录方式

```
✅ 传统登录: 用户名 + 密码
✅ AcWing (Web): OAuth2 一键登录
✅ AcWing (AcApp): OAuth2 一键登录
✅ QQ (Web): OAuth2 一键登录
```

**三种 OAuth2 登录全部完成！** 🎉

---

## 🚀 Day 15 详细规划

### 核心任务 1: 项目品牌化 ⭐⭐⭐⭐⭐

**预计耗时**: 1-2 小时  
**难度**: 简单

**实现内容**:
1. **项目重命名**
   - KotlinCalendar → Ralendar
   - 更新所有页面标题
   - 更新 README 和文档
   - 更新 package.json

2. **Logo 设计更新**
   - 使用 Roamio 风格的图标
   - 统一配色方案
   - 更新 favicon

3. **品牌文案**
   - "Ralendar - Roamio 旗下的智能日历"
   - 统一 slogan
   - 关于页面

### 核心任务 2: API 文档编写 ⭐⭐⭐⭐

**预计耗时**: 2-3 小时  
**难度**: 中等

**实现内容**:
1. **API 接口清单**
   - 列出所有 REST API
   - 请求/响应格式
   - 认证要求

2. **使用 Swagger/OpenAPI**
   ```bash
   pip install drf-yasg
   ```
   - 自动生成 API 文档
   - 在线测试接口
   - 导出 API 定义

3. **数据库设计文档**
   - ER 图
   - 表结构说明
   - 字段定义

### 功能任务 1: 地图功能集成 ⭐⭐⭐⭐⭐

**预计耗时**: 4-5 小时  
**难度**: 中高

**实现内容**:
1. **申请高德地图 API Key**
2. **后端地理编码**
   - Event 模型添加 latitude/longitude 字段
   - 地址 → 坐标转换接口
3. **前端地图展示**
   - 安装 `@amap/amap-jsapi-loader`
   - 事件详情显示地图
   - 可点击选择位置
4. **地图视图**
   - 创建独立的地图页面
   - 显示所有有位置的事件
   - 点击标记查看详情

### 功能任务 2: 前端通知提醒 ⭐⭐⭐

**预计耗时**: 2-3 小时  
**难度**: 中等

**实现内容**:
1. **Web Push Notifications**
   - 请求通知权限
   - 定时检查即将到来的事件
   - 发送桌面通知
2. **提示音**
   - 添加提示音文件
   - 用户设置是否播放

### 融合准备: Roamio 对接方案设计 ⭐⭐⭐⭐

**预计耗时**: 1-2 小时  
**难度**: 中等

**实现内容**:
1. **设计数据同步方案**
   - 用户表如何共享
   - 事件数据如何互通
   - API 鉴权统一

2. **组件封装规划**
   - Ralendar 作为独立组件
   - props 接口设计
   - 事件回调设计

3. **集成方式选择**
   - 方案 A：iframe 嵌入（简单）
   - 方案 B：npm 包组件（专业）
   - 方案 C：微前端架构（复杂）

---

## 🌟 Roamio × Ralendar 融合展望

### 短期目标（Day 15-20）

1. ✅ 完成 Ralendar 核心功能
2. ✅ 编写完整 API 文档
3. ✅ 品牌化和视觉统一
4. ⏳ QQ 登录审核通过

### 中期目标（融合准备）

1. 设计数据同步架构
2. 创建 Ralendar 组件库
3. 统一认证服务
4. API Gateway 设计

### 长期目标（生态建设）

1. Roamio（旅行规划）+ Ralendar（时间管理）
2. 未来扩展：Rote（笔记）、Rapture（照片）
3. 建立完整的 Roamio 产品矩阵

---

## 🎊 总结

### 今日成就（Day 14 = 突破性的一天！）

**这是最重要的一天！修复了影响全局的 JWT 认证问题！** 🔥

**三大核心成就**：

1. **✅ QQ OAuth2 一键登录**
   - 处理 QQ 特殊格式（URL 参数 + JSONP）
   - 三步流程完整实现
   - 用户头像和昵称显示

2. **✅ 用户个人中心**
   - 用户信息展示
   - 统计信息（总日程/今日/即将到来）
   - 第三方账号绑定管理
   - 个人信息编辑
   - 修改密码功能
   - 智能保护（至少保留一种登录方式）

3. **✅ JWT 认证核心问题修复（最重要！）**
   - 修复了影响全局的 403 问题
   - 恢复了所有认证功能
   - 系统性的问题追踪（3小时）

**技术成果**：
- ✅ QQ OAuth2 完整实现
- ✅ 8 个新 API 端点
- ✅ 模型模块化重构
- ✅ 代码清理（35+ 处）
- ✅ 项目结构优化

**解决的难点（8个）**：
- ✅ JWT 认证未启用（核心问题）
- ✅ QQ API 特殊格式处理
- ✅ 数据库迁移依赖问题
- ✅ Git 数据库文件冲突
- ✅ 用户名冲突处理
- ✅ 解绑账号智能保护
- ✅ OAuth 回调页面刷新
- ✅ 导航栏状态更新

**学习收获**：
- ✅ QQ OAuth2 三步流程
- ✅ URL 参数和 JSONP 解析
- ✅ Django 模型模块化
- ✅ 系统性问题追踪思维
- ✅ 代码清理最佳实践
- ✅ 数据库迁移管理
- ✅ 用户中心功能设计

### 项目质量提升

**代码质量**：⭐⭐⭐⭐⭐
- 模块化结构清晰
- 无调试日志
- 注释恰当
- 错误处理完善

**可维护性**：⭐⭐⭐⭐⭐
- 每个文件职责单一
- 易于扩展
- 符合最佳实践

**用户体验**：⭐⭐⭐⭐⭐
- 四种登录方式（传统 + AcWing双端 + QQ）
- 完整的用户中心
- 智能的账号保护
- 快速便捷

**系统稳定性**：⭐⭐⭐⭐⭐
- JWT 认证正常工作
- 所有功能恢复
- 无已知 Bug

### 项目进度

```
✅ Day 1-8:  Android 核心功能
✅ Day 9:    Django 后端 + Vue3 Web 端
✅ Day 10:   AcWing 平台集成
✅ Day 11:   用户认证 + UI优化 + 功能规划
✅ Day 12:   AcWing OAuth2（AcApp端）
✅ Day 13:   AcWing OAuth2（Web端）
✅ Day 14:   QQ OAuth2 + 用户中心 + JWT修复 ⭐

总体进度：127% 🎯
（持续超出原计划）
```

### 项目成熟度评估

| 模块 | 完成度 | 评级 |
|-----|-------|------|
| 基础架构 | 100% | ⭐⭐⭐⭐⭐ |
| 用户认证 | 95% | ⭐⭐⭐⭐⭐ |
| 用户中心 | 90% | ⭐⭐⭐⭐⭐ |
| 日历功能 | 85% | ⭐⭐⭐⭐ |
| 多端适配 | 70% | ⭐⭐⭐⭐ |
| 地图功能 | 0% | 待开发 |
| AI 助手 | 0% | 待开发 |

**整体完成度：约 70%（核心功能完善）**

---

**工作时长**: ~8 小时  
**代码行数**: 1500+ 行（新增）  
**代码清理**: 35+ 处  
**Git 提交**: 25+ 次  
**解决问题**: 8 个  
**新增 API**: 8 个  

**Day 14 是突破性的一天！修复了 JWT 核心问题，完成了用户中心，实现了 QQ 登录！** 💪🚀🔥

**为 Roamio 融合做好了准备！明天开始品牌化和生态建设！** 🌟

