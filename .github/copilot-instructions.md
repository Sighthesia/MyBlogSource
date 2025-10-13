## AI 开发者指南（本仓库专用：Hugo + Blowfish）

本仓库是使用 Hugo 构建、Blowfish 主题驱动的静态博客。主题以 Git 子模块引入；`public/` 为构建产物，勿编辑。主要语言为简体中文（zh-cn）。

### 架构速览
- 站点生成器：Hugo（建议使用 Extended 版，≥ 0.87）。
- 主题：`themes/blowfish`（Git 子模块，见 `.gitmodules`）。
- 配置：`config/_default/*.toml`（站点、主题与多语言）。
- 内容：`content/`（Markdown + Front Matter）；模版样板在 `archetypes/`。
- 静态资源：`static/`（例如图片 `/img/...`）。
- 构建产物：`public/`（会被覆盖，勿提交手改）。

### 关键配置与行为
- `config/_default/hugo.toml`
  - `baseURL = "https://sighthesia.github.io/"`（GitHub Pages 友好）。
  - `defaultContentLanguage = "zh-cn"`，`hasCJKLanguage = true`。
  - 输出首页 `HTML/RSS/JSON`；站点地图 `sitemap.xml` 每日更新。
- `config/_default/languages.zh-cn.toml`
  - 站点标题、作者信息、社交链接、日期格式等中文配置。
- `config/_default/params.toml`
  - Blowfish 主题参数：外观、主页布局、文章元信息显示等（如 `showReadingTime=true`、`showHero=false`、`showTableOfContents=false`）。
- `config/_default/markup.toml`
  - Goldmark 允许 HTML（`unsafe=true`），KaTeX/Math 支持（`$$...$$`、`\[...\]`、`\(...\)`），代码高亮使用 CSS 类。
- `config/_default/menus.zh-cn.toml`
  - 头部与页脚菜单（中文）。

### 参考文档
- 主题配置文档：`themes/blowfish/exampleSite/content/docs/configuration/index.zh-cn.md`

### 本地开发与构建
- 预览：`hugo server -D`（含草稿）；线上构建默认忽略草稿（`buildDrafts=false`）。
- 生产构建：`hugo --minify`（生成至 `public/`）。
- 主题更新（子模块）：`git submodule update --init --remote --recursive` 后提交变更。

### 内容创作约定
- 新文章优先使用 Hugo 模板：
  - 常规文章：`archetypes/default.md`（初始 `draft: true`）。
  - 外链文章：`archetypes/external.md`（`externalUrl` + 仅在列表渲染）。
- 典型 Front Matter（发布需将 `draft` 置为 `false`）：
  ```yaml
  ---
  title: "标题"
  date: 2025-10-12T22:48:57+08:00
  draft: false
  description: ""
  tags: ["Linux"]
  ---
  ```
- 图片与资源：放入 `static/img/...`，正文中以 `/img/文件名` 引用。
- 数学公式：行间 `$$ ... $$` 或 `\[ ... \]`；行内 `\( ... \)`。
- 请使用 UTF-8 与 CJK 友好用法（本仓库默认 zh-cn）。

### 目录与修改边界
- 可以编辑：`content/`、`archetypes/`、`config/_default/`、`static/`。
- 不建议直接改：`themes/blowfish/`（优先通过配置或在 `layouts/` 覆盖模板）。
- 禁止手改：`public/`（构建产物，自动生成）。

### 实例参考
- 文章示例：`content/posts/使用pcman及aur助手更新时忽略或暂时忽略软件包方法摘要.md` 展示了中文标题、`tags`、代码块与提示语法。

### 常见坑位
- 基础 URL：本地预览与线上 `baseURL` 差异可能影响资源链接。
- 草稿状态：未发布文章需 `draft: false` 才会进入构建（或使用 `-D` 预览）。
- 多语言：当前仅配置了 zh-cn；如需新增语言，请参照主题 exampleSite 的多语言配置添加对应 `languages.<lang>.toml` 与菜单文件。

如需补充或澄清本说明中仍不明确的部分（如 CI/部署细节、主题覆盖策略），请在本文件追加要点或提出问题以共同完善。