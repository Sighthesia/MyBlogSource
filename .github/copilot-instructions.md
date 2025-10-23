## AI 开发者指南（本仓库专用：Hugo + Blowfish）

本仓库使用 Hugo（静态站点生成器）和 Blowfish 主题构建一个单语言（zh-cn）博客。主题通过 Git 子模块引入；`public/` 是构建产物，切勿手动编辑。以下为 AI 代理要立刻能干活的要点与可执行示例。

## 一眼看懂架构
- Hugo 负责内容到静态页面的转换；主题（`themes/blowfish`）提供样式、短代码和模板。
- 配置集中在 `config/_default/*.toml`（站点、语言、主题参数、markup）。
- 内容：`content/`（文章与页面）；模板样板在 `archetypes/`。
- 静态资源：`static/`（图片、favicon 等）；生成产物：`public/`（自动生成，勿提交）。

## 关键文件（可直接跳转阅读）
- `config/_default/hugo.toml` — 站点级配置（例：`baseURL`, `defaultContentLanguage`, `outputs.home`）。
- `config/_default/params.toml` — Blowfish 主题参数（大量 UI/功能开关）。
- `config/_default/markup.toml` — Goldmark/KaTeX/代码高亮设置（`unsafe=true`）。
- `config/_default/menus.zh-cn.toml` — 站点菜单定义。
- `archetypes/default.md`、`archetypes/external.md` — 新文章模板（`draft: true`）。
- 主题参考文档：`themes/blowfish/exampleSite/content/docs/configuration/index.zh-cn.md`（详尽的主题参数说明）。

## 开发 & 构建（必会命令）
- 本地预览（包含草稿）：
  - hugo server -D
- 生产构建（生成 `public/`）：
  - hugo --minify
- 主题更新（若使用子模块）：
  - git submodule update --init --remote --recursive
- 若使用 Hugo 模块安装主题：
  - hugo mod get -u
- Blowfish 提供 CLI 帮助快速初始化：
  - npx blowfish-tools  （主题 README 提示可用）

提示：保证已安装 Hugo（Extended）、Go、Node.js（如果需要构建主题前端资源）。

## 主题、模板覆盖与资产
- 避免直接修改 `themes/blowfish/`；如需定制，请在项目根的 `layouts/` 中放置同名模板来覆盖（例如覆盖页眉部分：`layouts/partials/header.html`）。
- 主题使用 Tailwind/CSS 打包，主题下可能含 `package.json` 和构建脚本，必要时运行 `npm install`/`npm run build`（仅当你修改主题前端资产时）。

## 内容约定与示例
- 新文章：使用 `hugo new posts/your-post.md`（会用 `archetypes/default.md` 生成 Front Matter）。发布前把 `draft: false`。
- 资源：把图片放 `static/img/...`，正文引用用 `/img/xxx.jpg`。
- 示例文件：`content/posts/使用pcman及aur助手更新时忽略或暂时忽略软件包方法摘要.md`（示范中文 Front Matter、tags 与代码块）。

## 集成点与注意事项
- 多语言由 `config/_default/languages.<code>.toml` 管理；当前仓库以 `zh-cn` 为主。
- 若启用阅读量/点赞，需要配置 Firebase（查看 `params.toml` 中的 `firebase` 小节与主题文档）。
- Goldmark 已开启 `unsafe=true`，因此内容里允许 HTML；注意 XSS 风险（由人工或 CI 审核内容）。

## 快速引用（内部文档路径）
- 主题参数详解：`themes/blowfish/exampleSite/content/docs/configuration/index.zh-cn.md`
- 示例配置模板：`themes/blowfish/exampleSite/config/_default/`（可参考 exampleSite 的默认配置）

## 禁止与建议
- 禁止手动编辑：`public/`（构建产物）。
- 不建议直接改动 `themes/blowfish/`，优先通过 `config/_default/` 或在 `layouts/` 覆盖模板实现定制。

如果你想把 CI（GitHub Actions / Pages）或部署细节加到这份指南里，告诉我你使用的部署目标（GitHub Pages、Netlify、Vercel 等），我会把具体步骤与示例命令补充进来。