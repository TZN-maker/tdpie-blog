# TDPie Tech 博客迁移说明

本文件记录当前仓库从 Vercel 迁移/交接到 Codex 工作区时需要的信息。这里的“Codex”指 OpenAI Codex coding agent/工作区：它可以读取、修改、构建、验证和打包项目，但不是 Vercel、Netlify 或 Cloudflare Pages 那样的静态网站托管平台。域名绑定、HTTPS、CDN 和线上托管仍需要由实际托管平台完成。

## 当前项目

| 项目 | 当前值 |
| --- | --- |
| 站点名称 | TDPie Tech |
| 域名 | `www.tdpie.tech` |
| GitHub 仓库 | `https://github.com/TZN-maker/tdpie-blog` |
| 静态网站生成器 | Hugo |
| 本地已验证 Hugo | `v0.163.1+extended` |
| 原 Vercel 指定 Hugo | `0.146.0` |
| 主题 | `PaperMod`，已包含在 `themes/PaperMod/` |
| 构建命令 | `hugo --gc --minify` |
| 输出目录 | `public/` |

## 目录结构

```text
.
├── hugo.toml
├── vercel.json
├── archetypes/
├── assets/css/extended/custom.css
├── content/
│   ├── posts/
│   ├── about.md
│   ├── admin/_index.md
│   ├── categories/_index.md
│   ├── search.md
│   └── tags/_index.md
├── layouts/
├── static/
│   ├── admin/
│   └── images/
└── themes/PaperMod/
```

## 内容核对

当前 `content/posts/` 中有 10 篇文章：

| 分类 | 文章数 | URL |
| --- | ---: | --- |
| 半导体技术精帖 | 1 | `/categories/半导体技术精帖/` |
| 第三代半导体 | 6 | `/categories/第三代半导体/` |
| 人工智能 | 3 | `/categories/人工智能/` |

静态图片位于 `static/images/`，当前包含 11 个文件，其中包括 `banner.jpg`、`post1.jpg` 和 `post2.svg` 到 `post10.svg`。

## Hugo 配置要点

`hugo.toml` 当前关键配置：

```toml
baseURL = 'https://www.tdpie.tech/'
languageCode = 'zh-cn'
defaultContentLanguage = 'zh-cn'
title = 'TDPie Tech'
theme = 'PaperMod'

[taxonomies]
  tag = 'tags'
  category = 'categories'
```

主导航：

```toml
[[menus.main]]
  name = '首页'
  url = '/'

[[menus.main]]
  name = '半导体技术精帖'
  url = '/categories/半导体技术精帖/'

[[menus.main]]
  name = '第三代半导体'
  url = '/categories/第三代半导体/'

[[menus.main]]
  name = '人工智能'
  url = '/categories/人工智能/'

[[menus.main]]
  name = '关于我'
  url = '/about/'
```

## Decap CMS

CMS 入口：

- 管理入口：`/admin/`
- 配置文件：`static/admin/config.yml`
- 媒体目录：`static/images`
- 公开路径：`/images`

当前后端配置：

```yaml
backend:
  name: github
  repo: TZN-maker/tdpie-blog
  branch: main
  base_url: https://api.netlify.com
  auth_endpoint: auth
```

注意：CMS 目前仍依赖 GitHub 仓库和 Netlify OAuth 风格的认证端点。迁移托管平台时，需要重新验证 `/admin/` 登录、发文、上传图片和提交到 `main` 分支是否正常。

## Vercel 配置现状

当前 `vercel.json` 只有 Hugo 版本环境配置：

```json
{
  "build": {
    "env": {
      "HUGO_VERSION": "0.146.0"
    }
  }
}
```

如果继续使用 Vercel，建议在平台项目设置中确认：

- Framework Preset: Hugo
- Build Command: `hugo --gc --minify`
- Output Directory: `public`
- Environment Variable: `HUGO_VERSION=0.146.0`

## Codex 可完成的迁移工作

Codex 工作区中可以完成：

1. 拉取/整理源码。
2. 修改 Hugo、CMS、主题和样式配置。
3. 运行 `hugo --gc --minify` 并检查输出。
4. 生成 `public/` 静态产物。
5. 打包源码或静态产物。
6. 准备提交、PR、迁移说明和部署清单。

Codex 工作区外仍需要完成：

1. 在实际托管平台创建站点。
2. 配置构建命令和输出目录。
3. 配置域名 `www.tdpie.tech` 的 DNS。
4. 配置 HTTPS/CDN。
5. 验证 Decap CMS 的 GitHub OAuth 登录流程。

## 推荐迁移路径

### 路径 A：源码部署

适合 Netlify、Cloudflare Pages、GitHub Pages 自定义 Actions 或其他支持 Hugo 的静态托管平台。

配置：

```text
Build command: hugo --gc --minify
Publish/output directory: public
Hugo version: 0.146.0
```

优点：以后改文章或配置后，平台可以自动构建。

### 路径 B：静态产物部署

在 Codex/本地运行：

```powershell
hugo --gc --minify
```

然后把 `public/` 作为纯静态网站上传到托管平台。

优点：平台不需要安装 Hugo；缺点是每次内容更新都需要重新构建并上传。

## 已验证结果

在当前工作区运行：

```powershell
hugo --gc --minify
```

构建成功，输出到 `public/`。本地使用 Hugo `v0.163.1+extended` 构建时有 3 条 Hugo 废弃警告，主要与 Hugo `0.158.0` 之后的语言字段变更有关：

- `languageCode` 未来建议改为 `locale`
- `.Language.LanguageDirection` 未来建议改为 `.Language.Direction`
- `.Language.LanguageCode` 未来建议改为 `.Language.Locale`

这些警告不阻止当前构建；如果目标托管平台使用 `HUGO_VERSION=0.146.0`，预计不会触发同一组新版警告。

## 上线前检查清单

- [ ] 确认目标托管平台。
- [ ] 设置构建命令 `hugo --gc --minify`。
- [ ] 设置输出目录 `public`。
- [ ] 设置 Hugo 版本 `0.146.0`，或确认目标版本下构建无误。
- [ ] 绑定 `www.tdpie.tech`。
- [ ] 检查首页、文章页、分类页、标签页、搜索页和 404 页。
- [ ] 访问 `/admin/` 并验证 CMS 登录。
- [ ] 用 CMS 新建草稿并确认能提交到 GitHub。
- [ ] 上传一张图片并确认路径写入 `static/images`。
