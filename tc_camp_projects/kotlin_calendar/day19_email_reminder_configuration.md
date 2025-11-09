# 📧 Day 19: 邮件提醒功能完整配置与测试

> **日期**：2025年11月9日（周六）  
> **用时**：约4小时  
> **难度**：⭐⭐⭐⭐  
> **状态**：✅ 完成

---

## 🎯 今日目标

### 核心任务
1. ✅ 优化日历 UI（移动端适配）
2. ✅ 配置邮件服务（Gmail → 163）
3. ✅ 修复安全问题（密码泄露处理）
4. ✅ Django 配置修复（EMAIL_USE_SSL）
5. ✅ 完整测试验证（手动 + 自动）
6. ✅ Gravatar 头像配置

### 完成情况

**进度**：100% ✅  
**Git 提交**：4 次  
**解决问题**：4 个关键问题  
**邮件测试**：✅ 全部通过

---

## 📋 完整工作流程

### 上午：日历 UI 优化

#### 问题分析

**用户反馈**：
- 日历在移动端显示不完整
- 内容被裁剪，体验不佳
- 希望参考"游戏地图缩放逻辑"

#### 尝试方案

**方案 1：移植游戏地图算法**

```javascript
// 参考游戏地图的等比例缩放
update_size() {
    let width = window.innerWidth;
    let height = window.innerHeight;
    let scale = Math.min(width / 16, height / 9);
    // ...
}
```

**结果**：与 FullCalendar 内部布局冲突 ❌

**方案 2：FullCalendar 原生方案**

```javascript
// 使用 FullCalendar 的响应式配置
calendarOptions.value = {
    aspectRatio: window.innerWidth < 768 ? 1 : 1.8,
    handleWindowResize: true,
    windowResizeDelay: 100
}
```

**结果**：完美适配 ✅

#### 相关文件

- `web_frontend/src/composables/useCalendarEvents.js`
- `web_frontend/src/views/CalendarView.vue`
- `web_frontend/src/styles/calendar.css`

---

### 中午：Gmail 配置尝试

#### 背景

**为什么切换到 Gmail**：
- QQ 邮箱发件人显示 QQ 号（如 `2064747320@qq.com`）
- 用户体验不佳
- 希望使用 `Ralendar <...@gmail.com>` 作为发件人

#### 配置过程

**1. 注册 Gmail 账号**
```
邮箱：wenxiaolv8@gmail.com
```

**2. 启用两步验证**
- 登录 Google Account
- Security → 2-Step Verification
- 设置完成

**3. 生成 App Password**
```
原始密码：ycmm omun syrb veqt（16位，无空格）
```

**4. 更新配置**
```bash
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=wenxiaolv8@gmail.com
EMAIL_HOST_PASSWORD=ycmmomunsyrbveqt
DEFAULT_FROM_EMAIL=Ralendar <wenxiaolv8@gmail.com>
```

#### ⚠️ 密码泄露事件 #1

**发生了什么**：
- 将 Gmail App Password 写入 `ENV_TEMPLATE_FOR_PRODUCTION.txt`
- 提交到 GitHub 公开仓库（commit `76cb4c4`）
- GitGuardian 检测到高危密码暴露

**GitGuardian 告警**：
```
Alert: Google API Key exposed
Severity: High
Secret: ycmmomunsyrbveqt
Commit: 76cb4c4
```

**立即响应**：
1. 登录 Google Account
2. App Passwords → 撤销旧密码
3. 生成新密码
4. 更新本地配置

**教训**：
> **永远不要将真实凭证提交到 Git，即使在模板文件中！**

---

### 下午：安全修复与配置重构

#### 问题分析

**根本原因**：
- `ENV_TEMPLATE_FOR_PRODUCTION.txt` 同时具有两个角色：
  1. Git 中的公开模板（应该用占位符）
  2. 本地的配置文件（包含真实凭证）
- 这种混淆导致真实凭证被提交

#### 解决方案：双文件系统

**文件架构**：

| 文件 | 用途 | Git 状态 | 内容类型 |
|-----|------|---------|---------|
| `ENV_TEMPLATE_FOR_PRODUCTION.txt` | 公开模板 | ✅ 在 Git 中 | 占位符 |
| `ENV_PRODUCTION_READY_TO_COPY.txt` | 本地配置 | ❌ 不在 Git 中 | 真实凭证 |

**实施步骤**：

**1. 创建本地配置文件**
```bash
# backend/ENV_PRODUCTION_READY_TO_COPY.txt
# 包含所有真实凭证
EMAIL_HOST=smtp.gmail.com
EMAIL_HOST_USER=wenxiaolv8@gmail.com
EMAIL_HOST_PASSWORD=<新密码>
```

**2. 更新 .gitignore**
```gitignore
# Local configuration with real credentials
ENV_PRODUCTION_READY_TO_COPY.txt
*.local.txt
*.secret.txt
```

**3. 恢复模板文件**
```bash
# ENV_TEMPLATE_FOR_PRODUCTION.txt
EMAIL_HOST=smtp.your-provider.com
EMAIL_HOST_USER=your_email@example.com
EMAIL_HOST_PASSWORD=your_password_here
```

**4. 提交修复**
```bash
git add .
git commit -m "security: remove exposed credentials from template"
git push
```

**Commit**: `418cb68`

#### ⚠️ 密码泄露事件 #2

**又发生了什么**：
- 在修复 Gmail 密码时，QQ 邮箱 SMTP 授权码也被提交
- GitGuardian 再次告警

**GitGuardian 告警**：
```
Alert: SMTP Password exposed
Severity: High
Secret: zwcqgzukwkfyeaja
Commit: 418cb68
```

**决策**：
- 不再使用 QQ 邮箱
- 切换到国内邮箱服务（163）
- QQ 授权码已暴露，不再安全

---

### 下午：Gmail 连接失败

#### 问题现象

**手动测试 SMTP 连接**：
```python
import socket
s = socket.socket()
s.settimeout(10)
s.connect(('smtp.gmail.com', 587))
# KeyboardInterrupt (超时，用户 Ctrl+C 中断)
```

**Django Shell 测试**：
```python
from django.core.mail import send_mail
send_mail(
    subject='测试',
    message='内容',
    from_email='wenxiaolv8@gmail.com',
    recipient_list=['2064747320@qq.com']
)
# 长时间无响应...
```

#### 根本原因

**网络层面的问题**：
- 服务器位置：`app7626.acapp.acwing.com.cn`（国内）
- 目标服务器：`smtp.gmail.com`（海外）
- 国内服务器访问 Gmail SMTP 被限制或超时

**无法解决**：
- 不是代码问题，是网络问题
- 无法通过配置修复

#### 决策

**切换到国内邮箱服务 - 163 邮箱**

**优势**：
- ✅ smtp.163.com 连接速度快
- ✅ 稳定可靠
- ✅ 支持自定义发件人名称
- ✅ 国内服务器访问无障碍

---

### 下午：163 邮箱配置

#### 申请邮箱和授权码

**1. 注册邮箱**
```
邮箱：roamio_ralendar@163.com
用途：Ralendar 日程提醒专用
```

**2. 开启 SMTP 服务**
- 登录 163 邮箱
- 设置 → POP3/SMTP/IMAP
- 开启 IMAP/SMTP 服务
- 需要手机验证

**3. 获取授权码**
```
授权码：MWhM934vyBrYQGVU
说明：这不是登录密码，是 SMTP 专用授权码
```

#### SMTP 参数确认

**163 邮箱 SMTP 配置**：

| 参数 | 值 | 说明 |
|-----|---|------|
| 服务器 | smtp.163.com | 网易邮箱 SMTP 服务器 |
| 端口 | **465** | SSL 加密端口 |
| 加密方式 | **SSL/TLS** | 不是 STARTTLS |
| 用户名 | roamio_ralendar@163.com | 完整邮箱地址 |
| 密码 | MWhM934vyBrYQGVU | 授权码（非登录密码）|

**与 Gmail/QQ 的区别**：

| 邮箱 | 端口 | 加密方式 | Django 配置 |
|-----|------|---------|------------|
| Gmail | 587 | STARTTLS | `EMAIL_USE_TLS=True` |
| QQ | 587 | STARTTLS | `EMAIL_USE_TLS=True` |
| **163** | **465** | **SSL/TLS** | `EMAIL_USE_SSL=True` |

#### Django 配置缺陷发现

**问题**：
```python
# 在 Django Shell 中测试
from django.conf import settings
print(settings.EMAIL_USE_SSL)
# 输出：False

# 但 .env 文件中设置的是
EMAIL_USE_SSL=True
```

**排查**：
```python
# backend/calendar_backend/settings.py

# ✅ 有这个
EMAIL_USE_TLS = os.environ.get('EMAIL_USE_TLS', 'False') == 'True'

# ❌ 没有这个！
EMAIL_USE_SSL = ???
```

**发现**：
- `settings.py` 中**完全没有** `EMAIL_USE_SSL` 的配置
- Django 使用默认值 `False`
- 导致 465 端口无法使用 SSL 连接

#### 修复 settings.py

**添加配置**：
```python
# backend/calendar_backend/settings.py

# Email Configuration
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = os.environ.get('EMAIL_HOST', 'smtp.163.com')
EMAIL_PORT = int(os.environ.get('EMAIL_PORT', 465))
EMAIL_USE_TLS = os.environ.get('EMAIL_USE_TLS', 'False') == 'True'
EMAIL_USE_SSL = os.environ.get('EMAIL_USE_SSL', 'False') == 'True'  # ← 新增！
EMAIL_HOST_USER = os.environ.get('EMAIL_HOST_USER', '')
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_HOST_PASSWORD', '')
DEFAULT_FROM_EMAIL = os.environ.get('DEFAULT_FROM_EMAIL', 'Ralendar <roamio_ralendar@163.com>')
```

**提交**：
```bash
git add backend/calendar_backend/settings.py
git commit -m "fix: add EMAIL_USE_SSL support in settings.py"
git push
```

**Commit**: `bc3d8a4`

#### 为什么之前没发现这个 Bug？

**之前的配置**：
- QQ 邮箱：587 端口 + TLS
- Gmail：587 端口 + TLS
- 都用 `EMAIL_USE_TLS`，没问题

**163 邮箱暴露问题**：
- 163 推荐：465 端口 + SSL
- 需要 `EMAIL_USE_SSL=True`
- 才发现这个配置缺失

---

### 下午：服务器部署

#### 部署步骤

**1. 拉取最新代码**
```bash
cd ~/kotlin_calendar
git pull
```

**2. 更新 .env 文件**
```bash
vim backend/.env

# 更新为 163 配置
EMAIL_HOST=smtp.163.com
EMAIL_PORT=465
EMAIL_USE_SSL=True
EMAIL_USE_TLS=False
EMAIL_HOST_USER=roamio_ralendar@163.com
EMAIL_HOST_PASSWORD=MWhM934vyBrYQGVU
DEFAULT_FROM_EMAIL=Ralendar <roamio_ralendar@163.com>
```

**3. 优雅重启 Django**
```bash
pkill -HUP uwsgi
```

**效果**：
- 发送 HUP 信号给 uWSGI master 进程
- 重新加载 Python 代码和配置
- 不中断现有请求
- 平滑切换 worker 进程

**4. 重启 Celery**
```bash
pkill -f "celery"

# 启动 Celery Worker
cd ~/kotlin_calendar/backend
python3 -m celery -A calendar_backend worker --loglevel=info &

# 启动 Celery Beat
python3 -m celery -A calendar_backend beat --loglevel=info &
```

**5. 验证服务**
```bash
ps aux | grep celery
# 应该看到 2 个进程（worker + beat）

ps aux | grep uwsgi
# 应该看到多个 worker 进程
```

---

### 傍晚：邮件功能测试

#### 测试 1：手动发送测试

**Django Shell**：
```python
python3 manage.py shell

from django.core.mail import send_mail
from django.conf import settings

# 验证配置
print(f"EMAIL_HOST: {settings.EMAIL_HOST}")
print(f"EMAIL_PORT: {settings.EMAIL_PORT}")
print(f"EMAIL_USE_SSL: {settings.EMAIL_USE_SSL}")
print(f"EMAIL_USE_TLS: {settings.EMAIL_USE_TLS}")
print(f"EMAIL_HOST_USER: {settings.EMAIL_HOST_USER}")
print(f"DEFAULT_FROM_EMAIL: {settings.DEFAULT_FROM_EMAIL}")

# 输出：
# EMAIL_HOST: smtp.163.com
# EMAIL_PORT: 465
# EMAIL_USE_SSL: True
# EMAIL_USE_TLS: False
# EMAIL_HOST_USER: roamio_ralendar@163.com
# DEFAULT_FROM_EMAIL: Ralendar <roamio_ralendar@163.com>

# 发送测试邮件
send_mail(
    subject='🧪 163 邮箱测试',
    message='这是一封测试邮件，验证 163 SMTP SSL 配置。',
    from_email=settings.DEFAULT_FROM_EMAIL,
    recipient_list=['2064747320@qq.com'],
    fail_silently=False,
)

# 输出：1 (表示成功发送)
```

**✅ 测试结果**：
- 发送成功
- QQ 邮箱收到邮件
- 发件人显示：`Ralendar <roamio_ralendar@163.com>`

#### 测试 2：Celery 自动提醒测试

**创建测试事件**：
```
标题：📧 163 邮箱自动提醒测试
时间：18:18（3 分钟后）
提醒：提前 1 分钟（即 18:17 发送）
```

**Celery Beat 调度**：
```bash
# Celery Beat 每分钟执行一次
# celery.py 配置
beat_schedule = {
    'check-upcoming-reminders': {
        'task': 'api.tasks.check_and_send_reminders',
        'schedule': crontab(minute='*/1'),  # 每分钟
    },
}
```

**监控日志**：
```bash
tail -f celery_worker.log

# 18:16 - Beat 调度任务
[2025-11-09 18:16:00] Task api.tasks.check_and_send_reminders received

# 18:16 - 检查，还没到提醒时间
[2025-11-09 18:16:01] No reminders to send

# 18:17 - Beat 再次调度
[2025-11-09 18:17:00] Task api.tasks.check_and_send_reminders received

# 18:17 - 找到需要提醒的事件
[2025-11-09 18:17:01] Found 1 event(s) to remind

# 18:17 - 发送邮件任务
[2025-11-09 18:17:01] Task api.tasks.send_event_reminder_email[1] received

# 18:17 - 发送成功
[2025-11-09 18:17:03] Email sent successfully
```

**✅ 测试结果**：
- Celery Beat 正常调度
- 时间判断准确
- 邮件发送成功
- 事件标记为已通知（`notification_sent=True`）

#### 测试 3：邮件内容验证

**QQ 邮箱收到**：

**邮件头**：
```
发件人：Ralendar <roamio_ralendar@163.com>
收件人：2064747320@qq.com
主题：📅 日程提醒：📧 163 邮箱自动提醒测试
时间：2025-11-09 18:17
```

**邮件正文**（HTML）：
```html
<!DOCTYPE html>
<html>
<head>
    <style>
        body { font-family: Arial, sans-serif; }
        .header { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
        .logo { width: 120px; }
        .title { font-size: 24px; color: white; }
        .event-card { border-left: 4px solid #667eea; }
    </style>
</head>
<body>
    <div class="header">
        <img src="https://..." class="logo" alt="Ralendar">
        <h1 class="title">📅 日程提醒</h1>
    </div>
    <div class="event-card">
        <h2>📧 163 邮箱自动提醒测试</h2>
        <p>⏰ 开始时间：2025-11-09 18:18</p>
        <p>📍 地点：无</p>
        <p>📝 描述：测试 Celery 自动发送邮件</p>
    </div>
    <div class="footer">
        <p>此邮件由 Ralendar 自动发送</p>
    </div>
</body>
</html>
```

**✅ 验证结果**：
- Logo 显示正常
- HTML 样式正确
- 事件信息完整
- 用户体验良好

---

### 晚上：Gravatar 头像配置

#### 问题

**现象**：
- QQ 邮箱显示的发件人头像：绿色圆圈，白色字母 "Ra"
- 不是 Ralendar 的 Logo
- 品牌形象不统一

#### 原因分析

**为什么不是 163 邮箱设置的头像**：
- 163 邮箱的头像设置只在网易系统内生效
- 其他邮件客户端（QQ 邮箱、Gmail 等）不会读取
- 每个邮件客户端都有自己的头像逻辑

**邮件头像的标准做法**：
- 使用 **Gravatar**（Globally Recognized Avatar）
- 全球通用的头像服务
- 所有主流邮件客户端都支持

#### Gravatar 工作原理

**流程**：
```
1. 邮件客户端看到发件人邮箱：roamio_ralendar@163.com
    ↓
2. 计算邮箱地址的 MD5 哈希值
    ↓
3. 查询 Gravatar API
   GET https://www.gravatar.com/avatar/<md5_hash>
    ↓
4. 如果注册了 Gravatar：
   - 返回自定义头像
   否则：
   - 返回默认头像（字母、图案等）
```

**示例**：
```python
import hashlib

email = "roamio_ralendar@163.com"
email_hash = hashlib.md5(email.lower().strip().encode()).hexdigest()
# 输出：a1b2c3d4e5f6...

gravatar_url = f"https://www.gravatar.com/avatar/{email_hash}"
# 邮件客户端会请求这个 URL
```

#### 配置步骤

**1. 注册 Gravatar 账号**
```
网站：https://en.gravatar.com/
邮箱：roamio_ralendar@163.com
密码：（设置安全密码）
```

**2. 验证邮箱**
- Gravatar 发送验证邮件到 163 邮箱
- 点击验证链接
- 账号激活成功

**3. 上传头像**
- 选择 Ralendar Logo 图片
- 裁剪为正方形（推荐 200x200px）
- 选择分级：G（适合所有人）
- 确认上传

**4. 填写个人资料**
```
名称：Ralendar
组织：Roamio
地点：Yunnan, China
网站：https://app7626.acapp.acwing.com.cn/
简介：智能日历应用，Roamio 生态旗下产品
```

**5. 设置为主头像**
- Gravatar 支持多个邮箱和多个头像
- 将 Ralendar Logo 设置为 `roamio_ralendar@163.com` 的头像

#### 生效时间

**Gravatar CDN 同步**：
- 全球 CDN 需要同步时间
- 预计：**24-48 小时**
- 48 小时后发送测试邮件验证

#### 验证计划

**48 小时后（11月11日）**：
```bash
# 发送测试邮件
python3 manage.py shell

from django.core.mail import send_mail
send_mail(
    subject='🖼️ Gravatar 头像测试',
    message='验证 Gravatar 头像是否生效',
    from_email='Ralendar <roamio_ralendar@163.com>',
    recipient_list=['2064747320@qq.com']
)
```

**检查**：
- QQ 邮箱是否显示 Ralendar Logo
- Gmail 是否显示
- Outlook 是否显示

---

## 🐛 问题汇总与解决

### 问题 1：密码泄露到 GitHub

**现象**：
- GitGuardian 连续 2 次检测到密码暴露
- Gmail App Password
- QQ 邮箱 SMTP 授权码

**根本原因**：
- 误将真实凭证写入 Git 仓库的文件
- 混淆了"模板文件"和"配置文件"

**解决方案**：

**1. 立即响应**
```bash
# 撤销泄露的密码
# Gmail: 登录 Google Account → App Passwords → 撤销
# QQ: 决定不再使用（切换到 163）
```

**2. 建立双文件系统**
```
ENV_TEMPLATE_FOR_PRODUCTION.txt  (Git 中，占位符)
ENV_PRODUCTION_READY_TO_COPY.txt (本地，真实凭证)
```

**3. 更新 .gitignore**
```gitignore
ENV_PRODUCTION_READY_TO_COPY.txt
*.local.txt
*.secret.txt
```

**4. 修复 Git 历史**
```bash
# 选项 A：接受历史泄露（密码已失效）
# 选项 B：使用 BFG Repo-Cleaner 清理历史
#         需要强制推送，影响协作者
```

**教训**：
> **真实凭证绝不能提交到 Git，即使在注释、备用方案中也不行！**

**最佳实践**：
```bash
# 模板文件（Git 中）
EMAIL_HOST_PASSWORD=your_password_here

# 本地配置（不在 Git 中）
EMAIL_HOST_PASSWORD=MWhM934vyBrYQGVU
```

---

### 问题 2：Gmail SMTP 连接超时

**现象**：
```python
socket.connect(('smtp.gmail.com', 587))
# 长时间无响应，最终 KeyboardInterrupt
```

**排查过程**：

**1. 测试网络连接**
```bash
ping smtp.gmail.com
# 丢包率高或无响应

telnet smtp.gmail.com 587
# 无法连接
```

**2. 测试 Django 发送**
```python
from django.core.mail import send_mail
send_mail(...)
# 卡住，无响应
```

**3. 查看 Celery 日志**
```
Task received: send_event_reminder_email
(然后就没有后续日志了)
```

**根本原因**：
- 服务器位于国内（app7626.acapp.acwing.com.cn）
- Gmail SMTP 服务器在海外
- 国内服务器访问 Gmail 被限制或极慢

**解决方案**：
- 切换到国内邮箱服务（163 邮箱）
- `smtp.163.com` 连接速度快，稳定可靠

**教训**：
- 服务器地理位置很重要
- 国内服务器 → 国内邮箱
- 海外服务器 → Gmail/Outlook

---

### 问题 3：Django 缺少 EMAIL_USE_SSL 配置

**现象**：
```python
# .env 文件
EMAIL_USE_SSL=True

# Django Shell
print(settings.EMAIL_USE_SSL)
# 输出：False （不是预期的 True）
```

**排查过程**：

**1. 检查 .env 文件**
```bash
cat backend/.env | grep EMAIL_USE_SSL
# EMAIL_USE_SSL=True  ✅ 正确
```

**2. 检查 settings.py**
```python
# backend/calendar_backend/settings.py

EMAIL_USE_TLS = os.environ.get('EMAIL_USE_TLS', 'False') == 'True'  ✅
EMAIL_USE_SSL = ???  # ❌ 根本没有这行！
```

**3. 查看 Django 源码**
```python
# django/core/mail/backends/smtp.py
if self.use_ssl:
    connection = smtplib.SMTP_SSL(...)  # 465 端口
else:
    connection = smtplib.SMTP(...)      # 587 端口
```

**根本原因**：
- `settings.py` 中**完全没有** `EMAIL_USE_SSL` 的配置
- Django 使用默认值 `False`
- 导致 465 端口无法正确使用 SSL

**解决方案**：
```python
# 添加配置
EMAIL_USE_SSL = os.environ.get('EMAIL_USE_SSL', 'False') == 'True'
```

**为什么之前没发现**：
- 之前用的 QQ/Gmail 都是 587 + TLS
- 163 推荐 465 + SSL，才暴露问题

**教训**：
- 配置要完整覆盖所有场景
- 不能假设"某个配置不会用到"

---

### 问题 4：Celery 任务无后续日志

**现象**：
```
[18:15:00] Task api.tasks.send_event_reminder_email received
(之后就没有日志了，不知道成功还是失败)
```

**可能原因**：

**原因 1：SMTP 连接超时**
- 任务卡在 `send_mail()` 调用
- 没有设置超时，无限等待
- Gmail 连接问题时就是这样

**原因 2：任务代码有异常但被静默**
```python
try:
    send_mail(...)
except Exception as e:
    pass  # 异常被吞掉，没有日志
```

**原因 3：print() 输出被缓冲**
```python
print("Sending email...")
# 在 Celery 中，print 可能被缓冲，看不到输出
```

**解决方案**：

**1. 使用 Python logging**
```python
import logging
logger = logging.getLogger(__name__)

logger.info("Sending email...")
logger.error("Failed:", exc_info=True)
```

**2. 设置 SMTP 超时**
```python
# Django 不支持原生超时，需要手动设置
import socket
socket.setdefaulttimeout(10)  # 10 秒超时
```

**3. 添加异常处理和日志**
```python
try:
    send_mail(...)
    logger.info("✅ Email sent successfully")
except Exception as e:
    logger.error(f"❌ Email failed: {e}", exc_info=True)
    raise
```

---

## 📊 代码变更统计

### Git 提交记录

| Commit | 说明 | 文件变更 |
|--------|------|---------|
| `76cb4c4` | 切换到 Gmail 配置 | ENV_TEMPLATE（后被撤销）|
| `418cb68` | 移除泄露的 Gmail 凭证 | ENV_TEMPLATE 恢复占位符 |
| `84e4fd0` | .gitignore 添加本地配置 | .gitignore |
| `bc3d8a4` | 添加 EMAIL_USE_SSL 支持 | settings.py |

### 文件变更汇总

**新增文件**：
- `backend/ENV_PRODUCTION_READY_TO_COPY.txt` （本地，不在 Git）

**修改文件**：
- `backend/ENV_TEMPLATE_FOR_PRODUCTION.txt` - 恢复占位符
- `backend/calendar_backend/settings.py` - 添加 `EMAIL_USE_SSL`
- `.gitignore` - 添加本地配置文件规则

**代码行数**：
- 新增：约 50 行
- 修改：约 30 行
- 删除：约 20 行（密码）

---

## 🎯 技术要点总结

### 1. 邮件服务器端口与加密

**三种常见配置**：

| 端口 | 加密方式 | Django 配置 | 适用场景 |
|-----|---------|------------|----------|
| 25 | 无/STARTTLS | `EMAIL_USE_TLS=True` | 传统端口，部分禁用 |
| 587 | STARTTLS | `EMAIL_USE_TLS=True` | Gmail、QQ 推荐 |
| 465 | SSL/TLS | `EMAIL_USE_SSL=True` | 163、Outlook 推荐 |

**STARTTLS vs SSL/TLS**：
```
STARTTLS（587）:
  1. 明文连接
  2. 发送 STARTTLS 命令
  3. 升级到 TLS 加密
  
SSL/TLS（465）:
  1. 直接建立 SSL/TLS 连接
  2. 全程加密
```

### 2. Django 环境变量布尔值读取

**错误方式** ❌：
```python
EMAIL_USE_SSL = os.environ.get('EMAIL_USE_SSL', False)
# 问题：任何非空字符串都是 True
# 'False' → True（错误！）
# '0' → True（错误！）
```

**正确方式** ✅：
```python
EMAIL_USE_SSL = os.environ.get('EMAIL_USE_SSL', 'False') == 'True'
# 'True' → True
# 'False' → False
# '' → False
# 未设置 → False
```

### 3. uWSGI 优雅重启

**为什么需要优雅重启**：
- 更新代码后需要重启 Django
- 但不能中断正在处理的请求
- 不能造成服务短暂不可用

**方法**：
```bash
# 方式 1：发送 HUP 信号
pkill -HUP uwsgi

# 方式 2：直接 kill
kill -HUP $(cat /tmp/uwsgi.pid)

# 方式 3：uwsgi 命令
uwsgi --reload /tmp/uwsgi.pid
```

**效果**：
1. Master 进程收到 HUP 信号
2. 重新加载配置和代码
3. 启动新的 worker 进程
4. 等待旧 worker 处理完当前请求
5. 关闭旧 worker
6. 平滑切换，无请求丢失

### 4. Gravatar 头像机制

**完整流程**：
```python
import hashlib

def get_gravatar_url(email, size=200):
    """获取 Gravatar 头像 URL"""
    # 1. 邮箱地址预处理
    email = email.lower().strip()
    
    # 2. 计算 MD5 哈希
    email_hash = hashlib.md5(email.encode()).hexdigest()
    
    # 3. 构造 URL
    url = f"https://www.gravatar.com/avatar/{email_hash}?s={size}"
    
    # 4. 可选参数
    # d=404: 未注册时返回 404
    # d=mp: 未注册时返回默认头像（Mystery Person）
    # d=identicon: 未注册时返回几何图案
    # d=https://...: 未注册时返回自定义图片
    
    return url

# 示例
email = "roamio_ralendar@163.com"
gravatar_url = get_gravatar_url(email)
# https://www.gravatar.com/avatar/a1b2c3d4...?s=200
```

**邮件客户端支持**：
- ✅ Gmail
- ✅ Outlook
- ✅ Apple Mail
- ✅ Thunderbird
- ✅ QQ 邮箱（部分支持）

---

## 💡 经验总结

### 成功经验

**1. 问题定位方法论**

```
逐层测试：
  配置文件 → 环境变量 → Django settings
  ↓
  手动连接 → Django shell → Celery 任务
  ↓
  本地测试 → 服务器测试
```

**2. 安全响应流程**

```
发现泄露：
  ↓
立即撤销：撤销泄露的密码
  ↓
生成新密码：重新生成
  ↓
修复配置：建立正确的架构
  ↓
提交修复：推送修复代码
```

**3. 服务选型考虑**

```
地理位置：
  国内服务器 → 国内邮箱（163、QQ）
  海外服务器 → Gmail、Outlook

功能需求：
  自定义发件人 → Gmail、163
  高送达率 → 企业邮箱
  
成本考虑：
  免费 → Gmail、163
  付费 → SendGrid、AWS SES
```

### 改进空间

**1. Git 历史清理**

**当前状态**：
- 泄露的密码仍在 Git 历史中
- 虽然已失效，但不美观

**可选方案**：
```bash
# 方案 A：使用 BFG Repo-Cleaner
git clone --mirror https://github.com/user/repo.git
java -jar bfg.jar --replace-text passwords.txt repo.git
cd repo.git
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force

# 方案 B：使用 git filter-branch
git filter-branch --tree-filter 'sed -i "s/password123/REDACTED/g" ENV_TEMPLATE' HEAD
git push --force

# ⚠️ 注意：强制推送会影响所有协作者
```

**2. 配置管理工具**

**当前方案**：`.env` 文件

**更好的方案**：
```bash
# 方案 A：direnv
# 自动加载 .envrc 文件
cd ~/kotlin_calendar
# 自动加载环境变量

# 方案 B：AWS Secrets Manager
# 云端密钥管理
aws secretsmanager get-secret-value --secret-id ralendar/email

# 方案 C：HashiCorp Vault
# 企业级密钥管理
vault kv get secret/ralendar/email
```

**3. 邮件发送监控**

**当前状态**：
- 只有 Celery 日志
- 没有统计和监控

**改进方案**：
```python
# 添加邮件发送统计
class EmailSendLog(models.Model):
    event = models.ForeignKey(Event)
    recipient = models.EmailField()
    sent_at = models.DateTimeField(auto_now_add=True)
    success = models.BooleanField()
    error_message = models.TextField(blank=True)

# 定期统计
today_total = EmailSendLog.objects.filter(sent_at__date=today).count()
today_success = EmailSendLog.objects.filter(sent_at__date=today, success=True).count()
success_rate = today_success / today_total * 100
```

---

## 📈 下一步计划

### 短期（1-3 天）

**1. 通知 Roamio 团队** ⭐⭐⭐⭐⭐
```
主题：Ralendar 邮件提醒功能上线通知

内容：
- 邮件提醒功能已完成并测试通过
- 发件人：Ralendar <roamio_ralendar@163.com>
- 提醒时机：事件开始前 N 分钟
- API 使用说明（附文档）
- Gravatar 头像配置（48小时后生效）
```

**2. 验证 Gravatar 头像** ⭐⭐⭐
```
时间：11月11日（48小时后）
操作：发送测试邮件
检查：QQ 邮箱、Gmail、Outlook 头像显示
```

**3. 监控邮件发送** ⭐⭐⭐⭐
```
- 查看 Celery 日志
- 统计发送成功率
- 检查是否有异常
```

### 中期（1-2 周）

**1. 优化邮件模板** ⭐⭐⭐
```
- 根据用户反馈调整样式
- 添加"取消提醒"链接
- 支持自定义模板
```

**2. 添加监控和统计** ⭐⭐⭐
```
- 邮件发送日志
- 成功率统计
- 失败原因分析
```

**3. 考虑企业邮箱** ⭐⭐
```
- 如果需要更专业的形象
- 自定义域名：noreply@ralendar.com
- 成本：约 50 元/年/邮箱
```

### 长期（1-3 个月）

**1. 多邮箱提供商支持** ⭐⭐
```
- 163 作为主要
- Gmail 作为备用
- 自动切换机制
```

**2. 邮件队列优化** ⭐⭐⭐
```
- 批量发送
- 发送速率限制（防止被限制）
- 重试机制优化
```

---

## 📝 待办事项

### 立即完成
- [ ] 通知 Roamio 团队邮件功能上线
- [ ] 编写邮件提醒使用文档
- [ ] 整理 API 调用示例

### 2天后
- [ ] 验证 Gravatar 头像是否生效
- [ ] 发送测试邮件到多个邮箱

### 本周内
- [ ] 监控一周的邮件发送日志
- [ ] 统计发送成功率
- [ ] 收集用户反馈

### 可选
- [ ] 重新生成 QQ 邮箱授权码（如需备用）
- [ ] 清理 Git 历史中的泄露密码
- [ ] 添加邮件发送统计功能

---

## 🎊 总结

### 今日成果

**✅ 核心成果**：
1. 邮件提醒功能完全正常
2. 163 邮箱配置成功
3. Celery 自动化流程稳定运行
4. 安全配置得到改善
5. Gravatar 头像已配置（待生效）

**✅ 技术成果**：
1. 掌握了邮件服务器端口和加密配置
2. 理解了 Django 环境变量的正确读取方式
3. 学会了 uWSGI 优雅重启
4. 了解了 Gravatar 头像机制

**✅ 安全成果**：
1. 建立了双文件系统（模板 + 本地配置）
2. 更新了 .gitignore
3. 撤销了泄露的密码
4. 提升了安全意识

### 遇到的挑战

**❌ 密码泄露**：
- 2 次密码泄露到 GitHub
- GitGuardian 检测到
- 立即撤销并修复

**❌ Gmail 连接失败**：
- 国内服务器访问限制
- 无法通过代码解决
- 切换到 163 邮箱

**❌ Django 配置缺陷**：
- 缺少 EMAIL_USE_SSL 配置
- 163 邮箱才暴露问题
- 添加配置后解决

### 学到的经验

**1. 安全第一**
> 真实凭证绝不能提交到 Git，即使在注释中也不行！

**2. 服务选型要合理**
> 国内服务器用国内邮箱，海外服务器用 Gmail

**3. 配置要完整**
> 不能假设"某个配置不会用到"，要覆盖所有场景

**4. 问题定位要系统**
> 逐层测试：配置 → 连接 → 发送 → 自动化

### 今日高光时刻

**🎉 第一封自动提醒邮件成功发送！**

```
发件人：Ralendar <roamio_ralendar@163.com>
主题：📅 日程提醒：📧 163 邮箱自动提醒测试
时间：2025-11-09 18:17

看到这封邮件的那一刻，所有的努力都值得了！
```

---

**工作时长**：4 小时  
**Git 提交**：4 次  
**解决问题**：4 个关键问题  
**测试通过**：✅ 全部通过  

**Day 19 完美收官！邮件提醒功能上线！** 💪📧✨

---

**相关 Commit**：
- `76cb4c4` - 切换到 Gmail（后撤销）
- `418cb68` - 移除泄露凭证
- `84e4fd0` - 更新 .gitignore
- `bc3d8a4` - 添加 EMAIL_USE_SSL 支持

