# GitHub Pages 部署配置指南

## ✅ 已完成的配置

1. **GitHub Actions 工作流** - `.github/workflows/update-ai-news.yml`
   - 每 2 小时自动更新新闻数据
   - 自动构建并部署到 GitHub Pages
   - 支持手动触发 (`workflow_dispatch`)

2. **Vite 配置** - `web/vite.config.ts`
   - 已配置 `GITHUB_PAGES` 环境变量支持
   - Base path 设置为 `/ai-news-aggregator/`

3. **构建脚本** - 工作流中已包含数据文件复制

---

## 🔧 需要手动配置

### 1. 启用 GitHub Pages

访问：**https://github.com/niumanniu/ai-news-aggregator/settings/pages**

**Build and deployment** 设置：
- **Source**: 选择 `GitHub Actions`
- 其他保持默认

### 2. 配置环境变量（可选）

如果需要自定义 RSS 源，添加 GitHub Secret：

访问：**https://github.com/niumanniu/ai-news-aggregator/settings/secrets/actions**

添加：
- **Name**: `FOLLOW_OPML_B64`
- **Value**: OPML 文件的 Base64 编码

生成 Base64 命令：
```bash
base64 -i feeds/follow.opml
```

### 3. 推送代码到 GitHub

```bash
cd /tmp/ai-news-aggregator

# 提交配置更改
git add -A
git commit -m "chore: configure GitHub Pages deployment"

# 推送到 GitHub
git push origin main
```

---

## 🚀 首次部署

推送代码后，手动触发部署：

1. 访问：**https://github.com/niumanniu/ai-news-aggregator/actions**
2. 点击左侧 **"Update AI News Snapshot"**
3. 点击 **"Run workflow"** 按钮
4. 选择 `main` 分支
5. 点击 **"Run workflow"**

等待约 3-5 分钟，部署完成后访问：

**https://niumanniu.github.io/ai-news-aggregator/**

---

## 📊 部署流程

```
┌─────────────────────────────────────────────────────────────┐
│              GitHub Actions Workflow                         │
│         触发：每 2 小时 或 手动触发                            │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  1. Checkout 代码                                            │
│  2. Setup Node.js 20 + pnpm                                 │
│  3. pnpm install                                            │
│  4. pnpm fetch (抓取最新新闻)                                │
│  5. git commit & push (更新 data 目录)                       │
│  6. Generate OPML JSON                                      │
│  7. cd web && pnpm build                                    │
│  8. cp data/*.json dist/data/                               │
│  9. Upload artifact                                         │
│  10. Deploy to GitHub Pages                                 │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  部署完成                                                    │
│  URL: https://niumanniu.github.io/ai-news-aggregator/       │
└─────────────────────────────────────────────────────────────┘
```

---

## 🧪 本地测试

### 1. 安装依赖
```bash
cd /tmp/ai-news-aggregator
pnpm install
```

### 2. 运行 fetch 脚本
```bash
pnpm fetch
```

### 3. 构建 web
```bash
cd web
pnpm install
GITHUB_PAGES=true pnpm build
```

### 4. 预览
```bash
pnpm preview
```

---

## ⚠️ 注意事项

1. **仓库名称**：GitHub Pages URL 与仓库名称一致
   - 仓库：`niumanniu/ai-news-aggregator`
   - URL：`https://niumanniu.github.io/ai-news-aggregator/`

2. **分支**：默认部署 `main` 分支

3. **更新频率**：Cron 设置为每 2 小时 (`0 */2 * * *`)
   - 可修改 `.github/workflows/update-ai-news.yml` 中的 schedule

4. **数据文件**：以下文件会自动更新：
   - `data/latest-24h.json`
   - `data/latest-7d.json`
   - `data/source-status.json`
   - `data/archive.json`
   - `data/title-zh-cache.json`

---

## 🔗 相关链接

| 页面 | URL |
|------|-----|
| **仓库** | https://github.com/niumanniu/ai-news-aggregator |
| **Actions** | https://github.com/niumanniu/ai-news-aggregator/actions |
| **Pages 设置** | https://github.com/niumanniu/ai-news-aggregator/settings/pages |
| **Secrets** | https://github.com/niumanniu/ai-news-aggregator/settings/secrets/actions |

---

**配置时间**: 2026-03-06 22:42 GMT+8
