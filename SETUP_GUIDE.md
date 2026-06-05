# 🚀 设置指南 - 全球科技新闻日报

一步步的详细设置说明

## 第一步：获取 NewsAPI 密钥

### 1.1 访问 NewsAPI
- 打开浏览器访问 https://newsapi.org/

### 1.2 注册账户
- 点击 "Sign Up" 按钮
- 填写邮箱和密码
- 验证邮箱

### 1.3 获取 API Key
- 登录后自动跳转到 Dashboard
- 在 Dashboard 中可以看到您的 API Key
- **复制 API Key**（形如 `abcd1234...`）

## 第二步：163 邮箱配置

### 2.1 获取授权码

**重要：不能使用邮箱密码，必须使用授权码！**

步骤：
1. 打开 https://mail.163.com/
2. 用您的163账号登录
3. 点击右上角 **设置** → **账户设置**
4. 在左侧菜单找到 **POP3/SMTP/IMAP**
5. 点击 **开启**
6. 按照提示使用手机号验证
7. 系统会生成一串授权码（通常是16位）
8. **复制授权码**

示例：
- 邮箱：`hanlin0982@163.com`
- 授权码：`abcd1234efgh5678`（示例，非真实）

## 第三步：配置 GitHub Secrets

### 3.1 访问仓库设置
1. 打开您的 GitHub 仓库：https://github.com/zhouhanlin-0982/daily-news
2. 点击 **Settings** 选项卡
3. 在左侧菜单找到 **Secrets and variables** → **Actions**

### 3.2 添加 Secrets

点击 **New repository secret**，按顺序添加以下4个 secret：

#### Secret 1: NEWSAPI_KEY
- **Name**: `NEWSAPI_KEY`
- **Value**: 粘贴第一步复制的 API Key
- 点击 **Add secret**

#### Secret 2: EMAIL_ADDRESS
- **Name**: `EMAIL_ADDRESS`
- **Value**: `hanlin0982@163.com`（您的163邮箱）
- 点击 **Add secret**

#### Secret 3: EMAIL_PASSWORD
- **Name**: `EMAIL_PASSWORD`
- **Value**: 粘贴第二步复制的授权码
- 点击 **Add secret**

#### Secret 4: RECIPIENT_EMAIL
- **Name**: `RECIPIENT_EMAIL`
- **Value**: `hanlin0982@163.com`（接收新闻的邮箱）
- 点击 **Add secret**

### 3.3 验证 Secrets 已添加

完成后，**Secrets** 页面应该显示这4个 secret：
```
✓ NEWSAPI_KEY
✓ EMAIL_ADDRESS
✓ EMAIL_PASSWORD
✓ RECIPIENT_EMAIL
```

## 第四步：启用 GitHub Actions

### 4.1 检查 Actions 设置
1. 在仓库 **Settings** 中
2. 找到 **Actions** → **General**
3. 在 "Actions permissions" 中选择 **Allow all actions and reusable workflows**
4. 点击 **Save**

## 第五步：测试

### 5.1 手动触发工作流

1. 打开仓库首页：https://github.com/zhouhanlin-0982/daily-news
2. 点击 **Actions** 选项卡
3. 在左侧找到 **Daily News Digest** 工作流
4. 点击右侧 **Run workflow** 按钮
5. 选择 **Run workflow**

### 5.2 监控执行

1. 工作流开始执行，您会看到黄色的运行状态
2. 等待 5-10 分钟完成
3. 若成功，会显示绿色的 ✓ 标记

### 5.3 检查邮件

1. 打开您的163邮箱
2. 刷新邮件列表
3. 查找来自 `EMAIL_ADDRESS` 的邮件，主题为 "📰 全球科技新闻日报"

如果收到邮件，**恭喜！** 系统已成功配置！

## 第六步：自动调度

系统已配置为每天早上9点（北京时间）自动运行。

### 查看调度时间
- 工作流文件位置：`.github/workflows/daily-news-digest.yml`
- 当前设置：`0 1 * * *` （UTC 1:00 AM = 北京时间 9:00 AM）

### 修改运行时间

如需修改运行时间：

1. 打开文件：`.github/workflows/daily-news-digest.yml`
2. 找到 `schedule:` 部分
3. 修改 cron 表达式：

```yaml
schedule:
  - cron: '0 1 * * *'  # 改成您想要的时间
```

**Cron 表达式格式说明：**
```
分(0-59) 小时(0-23) 日(1-31) 月(1-12) 周(0-7)
```

**常见示例：**
- `0 1 * * *` - 每天 1:00 AM UTC (9:00 AM 北京时间)
- `0 6 * * *` - 每天 6:00 AM UTC (2:00 PM 北京时间)
- `0 23 * * *` - 每天 11:00 PM UTC (7:00 AM 北京时间 次日)
- `0 */6 * * *` - 每6小时运行一次

## 故障排查

### 问题1：���件没有收到

**检查步骤：**

1. 确认 GitHub Actions 成功运行
   - 打开仓库 → Actions → 查看最新运行记录
   - 若显示红色 ✗，说明运行失败

2. 检查 Secrets 配置
   - 确保 EMAIL_PASSWORD 是授权码，不是邮箱密码
   - 确保所有4个 Secrets 都已添加

3. 查看邮箱垃圾箱
   - 邮件可能被分类为垃圾邮件

4. 查看运行日志
   - 点击 Actions 中的运行记录
   - 点击 "generate-and-send-digest" 任务
   - 查看完整日志找出错误

### 问题2：GitHub Actions 运行失败

**常见错误及解决：**

| 错误信息 | 原因 | 解决方案 |
|---------|------|--------|
| `NEWSAPI_KEY not set` | 未配置 API Key | 检查 Secrets 中的 NEWSAPI_KEY |
| `SMTP authentication failed` | 授权码错误 | 重新获取授权码并更新 EMAIL_PASSWORD Secret |
| `No articles collected` | NewsAPI 无返回结果 | 检查 API Key 的请求配额是否用尽 |
| `Connection timeout` | 网络问题 | 重新运行工作流 |

### 问题3：收到邮件但格式不对

- 检查邮箱客户端是否支持 HTML 格式
- 尝试在网页版163邮箱查看
- 查看原始邮件源代码

## 高级配置

### 自定义新闻类别

编辑 `scripts/news_processor.py`，修改 `CATEGORY_KEYWORDS` 字典：

```python
CATEGORY_KEYWORDS = {
    '你的类别': ['关键词1', '关键词2', '关键词3'],
    ...
}
```

### 修改发送新闻数量

编辑 `scripts/news_processor.py`，在 `rank_articles()` 函数中修改：

```python
def rank_articles(self, articles: List[Dict[str, Any]], top_n: int = 20):
    # 改成您想要的数量
```

### 使用 Google Translate API

如需更好的翻译质量：

1. 创建 Google Cloud 项目并启用 Translate API
2. 下载服务账户密钥（JSON格式）
3. 将密钥文件添加到仓库的 Secrets
4. 编辑 `scripts/translator.py` 使用 Google Translate

## 监控和维护

### 定期检查

- 每月查看 Actions 的运行历史，确保持续工作
- 监控 NewsAPI 的请求使用量
- 定期更新依赖包

### 查看每日报告

- 每日报告保存在 `data/daily_reports/report_YYYYMMDD.json`
- 您可以在 GitHub 仓库中查看历史报告

## 支持和帮助

遇到问题？

1. **查看工作流日志**
   - GitHub Actions → 查看运行记录 → 查看日志

2. **常见问题**
   - 参考本文档的 "故障排查" 部分

3. **寻求帮助**
   - 在仓库提交 Issue
   - 提供完整的错误信息和日志

## 下一步

✅ 配置完成！

现在您的新闻日报系统已经：
- ✅ 自动收集全球新闻
- ✅ 智能分类和翻译
- ✅ 每天早上9点发送到邮箱

享受每日精选的科技新闻吧！📰
