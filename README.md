# B 站评论 + 图片抓取系统 v2.0

🏹 通过 Browser 自动化（Playwright + CDP）抓取 B 站视频评论区的评论和图片，生成美观的 HTML 报告。

**在线演示**: https://zoopools.github.io/bili-comments/

---

## ✨ 功能特性

- ✅ **Browser 自动化** - 模拟真人访问，绕过所有反爬机制
- ✅ **评论抓取** - 提取用户名、评论内容、点赞数、回复数
- ✅ **图片下载** - 自动下载评论中的图片（100% 原图）
- ✅ **图片过滤** - 智能过滤头像/装饰图，只保留评论图片
- ✅ **HTML 报告** - 紫色渐变背景 + 卡片布局 + 响应式设计
- ✅ **GitHub Pages** - 一键部署，永久在线访问
- ✅ **免费托管** - GitHub 免费提供 CDN 加速

---

## 🚀 快速开始

### 前置要求

- Python 3.9+
- Node.js
- Git
- Chrome 浏览器

### 安装依赖

```bash
# 安装 Playwright
pip install playwright
playwright install chromium

# 或使用 requirements.txt
pip install -r requirements.txt
```

### 启动 Chrome CDP

```bash
# macOS
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir=/tmp/chrome-debug

# Windows
chrome.exe --remote-debugging-port=9222 --user-data-dir=C:\tmp\chrome-debug

# Linux
google-chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-debug
```

**注意**: 保持 Chrome 运行，不要关闭

---

## 📖 使用方法

### 1. 修改视频链接

编辑 `bili_5comments.py`，第 56 行：

```python
await page.goto("https://www.bilibili.com/video/BV15QAUzXEtP", ...)
```

改为目标视频链接。

### 2. 运行抓取脚本

```bash
python3 bili_5comments.py
```

**输出示例**:
```
🔌 连接 CDP 浏览器...
✅ 连接成功
📥 打开 B 站视频...
⏳ 等待页面加载...
📜 滚动到评论区...
⏳ 等待评论加载...
🔍 提取带图评论...
✅ 找到 5 条带图评论
🚀 下载图片...
📊 总计：5 条评论，10 张图片
💾 保存到：/Users/xxx/.openclaw/temp/bili_5comments
```

### 3. 生成 HTML 报告

```bash
python3 generate_index.py
```

### 4. 部署到 GitHub Pages

```bash
# 初始化 Git
git init

# 创建 GitHub 仓库（浏览器操作）
# 访问 https://github.com/new
# 仓库名：bili-comments

# 关联远程仓库
git remote add origin https://github.com/Zoopools/bili-comments.git

# 推送代码
git add .
git commit -m "初始提交：B 站评论报告"
git branch -M main
git push -u origin main
```

### 5. 启用 GitHub Pages

1. 访问仓库 → Settings → Pages
2. Source 选择：main 分支
3. 点击 Save
4. 等待 1-2 分钟

**访问链接**: `https://zoopools.github.io/bili-comments/`

---

## 📁 文件结构

```
bili-comments/
├── README.md                 # 项目说明
├── requirements.txt          # Python 依赖
├── bili_5comments.py        # 评论抓取脚本（核心）
├── generate_index.py        # HTML 生成脚本
└── bili_images/             # 输出目录
    ├── index.html          # HTML 报告
    ├── comments.json       # 评论数据
    └── *.jpg               # 评论图片
```

---

## ⚙️ 配置说明

### 抓取参数（bili_5comments.py）

```python
# 评论数量限制（默认 10 条）
if (results.length >= 10) return;

# 每条评论图片数量（默认 2 张）
for j, img_url in enumerate(comment['images'][:2]):

# 滚动次数（默认 15 次）
for i in range(15):
    await page.evaluate("window.scrollBy(0, 200)")
```

### 图片过滤规则

```python
# 保留条件
if (src.includes('/bfs/new_dyn/') || src.includes('/bfs/archive/'))

# 过滤条件
!src.includes('/bfs/face/')      # 过滤头像
!src.includes('/bfs/garb/')      # 过滤装饰
!src.includes('/bfs/sycp/')      # 过滤小表情
```

---

## 📊 性能指标

| 指标 | 数值 |
|------|------|
| 连接时间 | ~1 秒 |
| 页面加载 | ~3 秒 |
| 滚动等待 | ~12 秒 |
| 评论加载 | ~5-10 秒 |
| **总耗时** | **~25-35 秒** |
| 图片画质 | 100% 原图 |
| 部署时间 | ~1 分钟 |
| 托管成本 | 免费 |

---

## 🔧 常见问题

### CDP 连接失败

**错误**: `Connection refused`

**解决**: 检查 Chrome 是否以调试模式启动，端口 9222 是否被占用

```bash
lsof -i :9222
```

### 页面加载失败

**错误**: 403 / 验证码

**解决**: 手动在 Chrome 中访问一次 B 站，保持登录态

### 评论提取为空

**原因**: B 站更新 DOM 结构

**解决**: 调整 JavaScript 选择器

```javascript
// 尝试多种选择器
const selectors = [
    '[class*="comment-item"]',
    '.comment-list .item',
    '[class*="reply-item"]'
];
```

### 图片 403

**现象**: 图片显示裂开

**解决**: HTML 中添加 `referrerpolicy="no-referrer"`

```html
<img referrerpolicy="no-referrer" src="...">
```

---

## 🎯 使用场景

- 📊 **舆情监控** - 新品发布/活动后收集用户反馈
- 🔍 **竞品分析** - 分析竞品视频评论区
- 📝 **内容创作** - UP 主收集观众建议
- 📈 **用户研究** - 获取真实用户评论数据
- 🎬 **热点追踪** - 监控舆论走向

---

## 📚 技术原理

### Browser 自动化 vs API 调用

| 维度 | Browser 自动化 | API 调用 |
|------|--------------|---------|
| 实现方式 | Playwright + CDP | requests + API |
| 登录状态 | 复用浏览器会话 | 需要 WBI 签名 |
| 反爬绕过 | ✅ 完全绕过 | ❌ 可能被限流 |
| 执行速度 | ~30 秒 | ~5 秒 |
| 稳定性 | ✅ 高 | ⚠️ 中 |

### 为什么选择 Browser 自动化？

1. **无需处理 WBI 签名** - B 站 API 需要复杂的签名算法
2. **利用现有登录态** - Chrome 中已登录，无需重新认证
3. **绕过所有反爬机制** - 模拟真人行为，无法检测
4. **图片防盗链自动解决** - 浏览器会话内直接下载

---

## 📝 更新日志

### v2.0 (2026-04-02)
- ✅ 修正为 Browser 自动化方案（Playwright + CDP）
- ✅ 修复图片过滤逻辑
- ✅ 优化滚动等待时间
- ✅ 添加常见问题文档

### v1.0 (2026-03-13)
- ✅ 初始版本
- ✅ 实现评论抓取 + 图片下载
- ✅ HTML 报告生成
- ✅ GitHub Pages 部署

---

## 📄 许可证

MIT License

---

## 👥 作者

**小猎（Hunter）** - 信息捕手 🏹

---

## 🔗 相关链接

- **在线演示**: https://zoopools.github.io/bili-comments/
- **B 站**: https://www.bilibili.com/
- **Playwright 文档**: https://playwright.dev/
- **GitHub Pages**: https://pages.github.com/

---

*🏹 专注信息获取和整理*
