# 🌏 Roamio 生态系统 API 文档

> **版本**: v1.0.0  
> **更新日期**: 2025-11-07  
> **适用项目**: Roamio + Ralendar

---

## 📖 文档说明

本文档是 **Roamio 生态系统**的统一 API 文档，涵盖：
- **Roamio**：旅行规划与内容分享平台
- **Ralendar**：智能日历与时间管理工具

两个项目共享用户认证体系，支持数据互通和功能联动。

---

## 🏗️ 生态架构

```
统一用户认证系统 (JWT + OAuth2)
        │
    ┌───┴───┐
Roamio API  Ralendar API
(旅行规划)  (日历管理)
```

### 核心设计理念

1. **独立开发，统一部署** - 各项目独立开发，共享认证体系
2. **API 优先** - RESTful 设计，便于跨端调用
3. **数据互通** - 预留接口，支持未来数据联动
4. **可扩展性** - 模块化设计，易于添加新产品

---

## 🔐 认证体系（通用）

### JWT Token 认证

**认证方式**：Bearer Token  
**Header 格式**：`Authorization: Bearer <access_token>`

**Token 生命周期**：
- Access Token：5 分钟
- Refresh Token：15 天

### 核心端点

- `POST /api/v1/token/` (Roamio) 或 `POST /api/auth/login/` (Ralendar) - 登录获取Token
- `POST /api/v1/token/refresh/` - 刷新Token
- `GET /api/v1/auth/me/` - 获取当前用户信息

---

## 🌏 Roamio API

**Base URL**: `https://roamio.com/api/v1/`

### 1. 认证接口 (Auth)

- `POST /api/v1/auth/register/` - 用户注册
- `POST /api/v1/auth/login/` - 用户登录
- `GET /api/v1/auth/me/` - 获取当前用户
- `POST /api/v1/auth/send_verification_code/` - 发送邮箱验证码
- `POST /api/v1/auth/verify_code/` - 验证邮箱验证码
- `GET /api/v1/auth/qq_login_url/` - 获取QQ登录URL
- `POST /api/v1/auth/qq_callback/` - QQ登录回调
- `POST /api/v1/auth/qq_bind_existing/` - 绑定QQ账号
- `DELETE /api/v1/auth/qq_unbind/` - 解绑QQ
- `POST /api/v1/auth/reset_password/` - 重置密码

### 2. 用户接口 (Users)

- `GET /api/v1/users/` - 获取用户列表（分页）
- `GET /api/v1/users/{id}/` - 获取用户详情
- `PATCH /api/v1/users/{id}/profile/` - 更新个人资料
- `POST /api/v1/users/{id}/avatar/` - 上传头像

### 3. 旅行计划接口 (Trips)

- `GET /api/v1/trips/` - 获取旅行列表（支持筛选：visibility, author, status）
- `GET /api/v1/trips/{slug}/` - 获取旅行详情
- `POST /api/v1/trip-plans/` - 创建旅行计划
- `PATCH /api/v1/trip-plans/{slug}/` - 更新旅行计划
- `DELETE /api/v1/trip-plans/{slug}/` - 删除旅行计划
- `POST /api/v1/trips/{slug}/like/` - 点赞旅行
- `POST /api/v1/trips/{slug}/checkin/` - 打卡旅行
- `GET /api/v1/trips/{slug}/stats/` - 获取旅行统计

### 4. 评论接口 (Comments)

- `GET /api/v1/comments/` - 获取评论列表（支持筛选：page_filter, parent_id）
- `POST /api/v1/comments/` - 发表评论
- `POST /api/v1/comments/{id}/like/` - 点赞评论
- `DELETE /api/v1/comments/{id}/` - 删除评论

---

## 📅 Ralendar API

**Base URL**: `https://app7626.acapp.acwing.com.cn/api/`

### 1. 认证接口 (Auth)

- `POST /api/auth/register/` - 用户注册
- `POST /api/auth/login/` - 用户登录
- `GET /api/auth/me/` - 获取当前用户
- `POST /api/auth/acwing/login/` - AcWing一键登录
- `GET /api/oauth2/receive_code/` - AcWing回调端点
- `POST /api/auth/qq/login/` - QQ一键登录

### 2. 用户中心接口 (User)

- `GET /api/user/stats/` - 获取用户统计
- `GET /api/user/bindings/` - 获取绑定状态
- `PATCH /api/user/profile/` - 更新个人信息
- `POST /api/user/change-password/` - 修改密码
- `DELETE /api/user/unbind/acwing/` - 解绑AcWing
- `DELETE /api/user/unbind/qq/` - 解绑QQ

### 3. 日程接口 (Events)

- `GET /api/events/` - 获取日程列表（支持日期范围筛选）
- `GET /api/events/{id}/` - 获取单个日程
- `POST /api/events/` - 创建日程
- `PUT/PATCH /api/events/{id}/` - 更新日程
- `DELETE /api/events/{id}/` - 删除日程

### 4. 公开日历接口 (Calendars)

- `GET /api/calendars/` - 获取公开日历列表
- `POST /api/calendars/` - 创建公开日历
- `POST /api/calendars/{id}/subscribe/` - 订阅公开日历

### 5. 农历接口 (Lunar)

- `GET /api/lunar/` - 阳历转农历（参数：year, month, day）

---

## 🔗 生态融合接口

### 1. 旅行 → 日程同步

**端点**: `POST /api/v1/trips/{slug}/sync-to-calendar/`  
**权限**: 作者  
**功能**: 将旅行计划中的行程同步到Ralendar日历

### 2. 日程 → 旅行关联

**端点**: `GET /api/events/{id}/related_trip/`  
**功能**: 获取日程关联的旅行信息

---

## 📊 数据模型关系

### Roamio 核心模型
- `User` - 用户
- `UserProfile` - 用户资料
- `SocialAccount` - 第三方账号绑定
- `Trip` - 旅行计划
- `Comment` - 评论

### Ralendar 核心模型
- `User` - 用户（可与Roamio共享）
- `AcWingUser` - AcWing账号绑定
- `QQUser` - QQ账号绑定
- `Event` - 日程事件
- `PublicCalendar` - 公开日历

### 融合扩展
- `Event.related_trip_slug` - 关联旅行计划
- `Trip.calendar_events_ids` - 关联日程ID列表

---

## 🌟 API 设计原则

1. **RESTful 设计** - 使用标准HTTP方法（GET, POST, PUT, PATCH, DELETE）
2. **统一响应格式** - 成功返回数据，失败返回错误信息
3. **分页支持** - 列表接口默认分页（20条/页，最大100条/页）
4. **时间格式** - 使用ISO 8601格式（`2025-11-07T14:00:00Z`）
5. **版本控制** - URL版本化（`/api/v1/`）

---

## 🔧 技术实现指南

### 跨项目API调用

**场景**：Roamio调用Ralendar API

```python
import requests

class RalendarClient:
    def __init__(self, user_token=None):
        self.base_url = settings.RALENDAR_API_BASE
        self.token = user_token
    
    def create_event(self, event_data):
        headers = {'Authorization': f'Bearer {self.token}'}
        response = requests.post(
            f'{self.base_url}/events/',
            json=event_data,
            headers=headers
        )
        return response.json()
```

### 共享认证实现

**方案A：共享数据库（推荐）**
- 两个项目使用相同的数据库和`SECRET_KEY`
- JWT Token可在两个项目中互通

**方案B：API互调验证**
- 通过中间件验证来自另一个项目的Token
- 调用对方API验证Token有效性

---

## 📝 最佳实践

### 错误处理

统一错误格式：
```json
{
  "error": "错误描述",
  "error_code": "INVALID_INPUT"
}
```

常见错误码：
- `INVALID_INPUT` - 输入无效
- `UNAUTHORIZED` - 未认证
- `FORBIDDEN` - 无权限
- `NOT_FOUND` - 资源不存在
- `RATE_LIMIT_EXCEEDED` - 超过频率限制

### 性能优化

- **分页**：所有列表接口必须分页（默认20条/页）
- **缓存**：使用Redis缓存热点数据
- **查询优化**：使用`select_related`和`prefetch_related`

### 安全考虑

- **CORS配置**：限制允许的源
- **频率限制**：匿名用户100请求/小时，已认证用户1000请求/小时
- **SQL注入防护**：使用Django ORM（自动转义）
- **XSS防护**：用户输入内容转义

---

## 🧪 API 测试

### 使用 Postman/Insomnia

环境变量：
```
ROAMIO_BASE_URL = https://roamio.com
RALENDAR_BASE_URL = https://app7626.acapp.acwing.com.cn
ACCESS_TOKEN = (登录后获取)
```

测试流程：
1. 注册/登录获取Token
2. 使用Token调用需要认证的API
3. 测试跨项目调用（如旅行同步到日历）

---

## 🌟 未来扩展接口（预留）

- `GET /api/v1/recommendations/trips/` - 智能推荐
- `POST /api/events/{id}/geocode/` - 地址转坐标
- `POST /api/ai/create-event/` - AI助手创建日程
- `GET /api/analytics/user-insights/` - 用户行为分析

---

**最后更新**: 2025-11-07  
**文档版本**: v1.0.0  
**维护**: Roamio Team
