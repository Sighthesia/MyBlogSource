# Coding Standards

## Content Creation Standards

### Frontmatter Standard

**Required Format**: TOML between `---` delimiters

```markdown
---
title: "文章标题"
date: 2025-11-28T22:06:23+08:00
draft: false
description: ""
tags: ["Linux", "美化"]
---
```

**Field Specifications**:

| Field         | Type     | Required      | Format                      | Example                        |
| ------------- | -------- | ------------- | --------------------------- | ------------------------------ |
| `title`       | String   | ✅ Yes         | Double quotes               | `"使用pacman更新时忽略软件包"` |
| `date`        | DateTime | ✅ Yes         | ISO 8601 + timezone         | `2025-11-28T22:06:23+08:00`    |
| `draft`       | Boolean  | ✅ Yes         | Lowercase                   | `false` (to publish)           |
| `description` | String   | ⚠️ Recommended | Double quotes, can be empty | `"简要说明文章内容"`           |
| `tags`        | Array    | ⚠️ Recommended | Array of strings            | `["Linux", "美化"]`            |

**Critical Rules**:
- ✅ Always use +08:00 timezone offset
- ✅ Set `draft: false` before publishing
- ✅ Use lowercase for boolean values
- ❌ Never use YAML format (`+++` delimiters)
- ❌ Never omit required fields

---

## Article Structure Standard

### Heading Hierarchy

```markdown
# Article Title (auto-generated from frontmatter, do not include in body)

## Main Section (Level 2)
Introduction or main content block

### Subsection (Level 3)
Details, methods, or steps

#### Minor Detail (Level 4)
Rarely used, for deep nesting only
```

**Rules**:
- Start article body with `##` (level 2 headings)
- Use `###` for subsections (方法说明, 步骤, 示例)
- Maximum nesting: 4 levels
- Never skip heading levels (no `##` → `####`)

### Common Section Patterns

**Technical Tutorial Pattern**:
```markdown
## 问题描述
Brief explanation of the problem

## 解决方案一
### 方法说明
Explanation of the approach

### 步骤
1. Step one
2. Step two

### 示例
Code or command examples

## 解决方案二
[Repeat structure]

## 后记
Personal notes, experiences, lessons learned

## 参考资料
- [Source Title](URL)
- [Documentation](URL)
```

**Warning/Note Pattern**:
```markdown
⚠️ 来自 [Source] 的提示：
> **警告**：
> Important warning text

💡 **提示**：Helpful information
```

---

## Writing Style Standards

### Language and Tone

**Primary Language**: Simplified Chinese (简体中文)
- Technical accuracy is paramount
- Conversational and approachable tone
- Personal experiences welcomed in 后记 sections

**Technical Terms**:
- Use English for proper nouns: Hugo, Blowfish, GitHub
- Use English for commands: `pacman`, `hugo server`
- Chinese translation with English in parentheses for complex terms on first use

**Examples from Existing Posts**:
- ✅ "使用 `xdg-mime` 命令将 `inode/directory`（文件夹）关联到 Dolphin"
- ✅ "在 Niri 或 Hyprland 这样轻量的桌面合成器上"
- ✅ "在命令行输入命令更新软件时暂时忽略某些包的更新"

### Emoji Usage

**Approved Usage**:
- ⚠️ Warnings and important notices
- 💡 Tips and helpful information
- 😊 Friendly notes or humor
- 🐧 Linux-related content
- ✅ Confirmations or correct examples
- ❌ Anti-patterns or incorrect examples

**Guidelines**:
- Use sparingly for emphasis
- Place at start of line or inline with text
- Never use as bullet points
- Avoid overuse (max 3-5 per article)

### Formatting Conventions

**Inline Code**: Use single backticks
```markdown
使用 `pacman -Syu` 更新系统
配置文件位于 `/etc/pacman.conf`
```

**Code Blocks**: Always specify language
```markdown
​```shell
paru -Syu --ignore nvidia-dkms
​```

​```toml
[options]
IgnorePkg = nvidia-dkms
​```

​```html
<div id="waline"></div>
​```
```

**Emphasis**:
- **Bold**: `**重要内容**` for important concepts
- *Italic*: `*强调内容*` for emphasis (rarely used in Chinese)
- ~~Strikethrough~~: `~~删除内容~~` for humorous asides

**Lists**:
```markdown
## Unordered Lists
- Item one
- Item two
  - Nested item (2 spaces or 1 tab indent)

## Ordered Lists
1. First step
2. Second step
   - Additional detail (indented)
```

---

## Code Block Standards

### Language Identifiers

**Always Specify**: Never use ``` without language

**Common Languages**:
- `shell` or `bash` or `fish` - Shell commands
- `toml` - Configuration files
- `yaml` - YAML configuration
- `html` - HTML code
- `css` - CSS styles
- `javascript` or `js` - JavaScript code
- `python` - Python code
- `markdown` - Markdown examples
- `text` - Plain text output

**Examples**:

```markdown
​```shell
# Fish shell command
hugo new content posts/新文章.md
​```

​```toml
# TOML configuration
[options]
hasCJKLanguage = true
​```

​```html
<!-- HTML partial -->
<div id="waline"></div>
​```
```

### Command Formatting

**Single Commands**:
```shell
hugo server -D
```

**Multiple Commands** (explain with comments):
```shell
# Update system except nvidia packages
paru -Syu --ignore nvidia-dkms,nvidia-utils

# Verify configuration
hugo server -D
```

**Long Commands** (use line continuation):
```shell
paru -Syu --ignore nvidia-dkms,nvidia-utils,\
opencl-nvidia,lib32-nvidia-utils
```

---

## Configuration File Standards

### TOML Files (config/_default/*.toml)

**Formatting**:
```toml
# -- Section Name --
# Brief explanation if needed

key = "value"
booleanKey = true
numberKey = 100

[section]
  nestedKey = "value"
  nestedBool = false
```

**Rules**:
- Use lowercase for boolean: `true`, `false`
- Quote all string values: `"value"`
- Use section comments: `# -- Section --`
- Preserve existing comment structure from theme
- 2-space indentation for nested keys

**Example**:
```toml
# -- Theme Options --
colorScheme = "blowfish"
defaultAppearance = "light"
autoSwitchAppearance = true

[homepage]
  layout = "background"
  showRecent = true
```

---

## HTML/CSS Standards

### Custom Layouts (layouts/partials/*.html)

**Structure**:
```html
<!-- Purpose: Brief description of this partial -->

<div class="descriptive-class">
  <!-- Content -->
</div>
```

**Rules**:
- Add HTML comments explaining purpose
- Use Tailwind CSS classes (avoid inline styles)
- Support light/dark modes
- Use Hugo template syntax for dynamic content

**Example**:
```html
<!-- Waline Comment System Container -->
<div id="waline"></div>
```

### CSS Standards (assets/css/custom.css)

**Variable Usage** (prefer Blowfish theme variables):
```css
:root {
  /* Map to Blowfish variables */
  --custom-color: rgb(var(--color-primary-500));
}

.dark {
  /* Dark mode overrides */
  --custom-color: rgb(var(--color-primary-400));
}
```

**Rules**:
- Use theme CSS variables instead of hardcoded colors
- Support both light and dark modes
- Comment purpose of custom styles
- Avoid !important unless absolutely necessary

---

## Markdown Extensions

### Blockquotes

**Standard Quote**:
```markdown
> 这是一段引用文本
```

**Warning/Alert Pattern**:
```markdown
⚠️ 来自 Arch Linux Wiki 的提示：
> **警告**：
> 在跳过软件包时要小心，因为部分升级不受支持
```

### Links

**Internal Links** (relative):
```markdown
[查看配置说明](../config/)
```

**External Links** (absolute):
```markdown
[Arch Wiki](https://wiki.archlinux.org/)
[Blowfish 文档](https://blowfish.page/docs/)
```

**Link Titles** (hover text):
```markdown
[Blowfish](https://blowfish.page/ "Blowfish Theme Documentation")
```

### Images

**From assets/ (Hugo processes)**:
```markdown
![描述文字](img/image-name.png)
```

**From static/ (direct copy)**:
```markdown
![描述文字](/img/image-name.png)
```

**With Alt Text** (always provide):
```markdown
![Dolphin 文件管理器界面截图](img/dolphin-screenshot.png)
```

---

## Git Commit Standards

### Commit Message Format

**Structure**:
```
<type>: <subject>

[optional body]

[optional footer]
```

**Types**:
- `content:` - New or updated blog post
- `config:` - Configuration changes
- `style:` - CSS/styling changes
- `layout:` - Layout/partial changes
- `fix:` - Bug fixes
- `chore:` - Maintenance tasks
- `docs:` - Documentation updates

**Examples**:
```
content: 添加 pacman 忽略软件包方法文章

config: 启用 Waline 评论系统

layout: 自定义 Waline 颜色变量映射

fix: 修正文章日期时区格式
```

---

## Testing Standards

### Pre-Publish Checklist

**For New Posts**:
- [ ] Frontmatter includes all required fields
- [ ] `draft: false` is set
- [ ] Date uses +08:00 timezone
- [ ] Tags are appropriate and consistent
- [ ] All code blocks have language identifiers
- [ ] Images load correctly
- [ ] Links are not broken
- [ ] Content renders correctly in both light and dark modes

**For Configuration Changes**:
- [ ] Test with `hugo server -D`
- [ ] Verify no build errors
- [ ] Check affected pages render correctly
- [ ] Validate TOML syntax
- [ ] Preserve existing comments

**For Layout Changes**:
- [ ] Test in light mode
- [ ] Test in dark mode
- [ ] Test responsive behavior
- [ ] Verify theme overrides work as expected
- [ ] Check console for JavaScript errors

### Testing Commands

```fish
# Development server with drafts
hugo server -D

# Production build test
hugo

# Check for broken links (if using tools)
hugo server & sleep 2 && wget --spider -r -nd -nv http://localhost:1313/
```

---

## Anti-Patterns to Avoid

### Content Anti-Patterns

❌ **Using YAML frontmatter format**:
```yaml
+++
title: "Title"
+++
```
✅ **Use TOML instead**:
```markdown
---
title: "Title"
---
```

❌ **Omitting language from code blocks**:
```
​```
command here
​```
```
✅ **Always specify language**:
```shell
command here
```

❌ **Incorrect date format**:
```toml
date: 2025-11-28
```
✅ **ISO 8601 with timezone**:
```toml
date: 2025-11-28T22:06:23+08:00
```

❌ **Publishing with draft flag**:
```toml
draft: true
```
✅ **Set to false before publishing**:
```toml
draft: false
```

### Configuration Anti-Patterns

❌ **Modifying theme files directly**:
```
themes/blowfish/layouts/partial/header.html
```
✅ **Create project-level override**:
```
layouts/partials/header.html
```

❌ **Hardcoding colors in CSS**:
```css
.custom {
  color: #3b82f6;
}
```
✅ **Use theme variables**:
```css
.custom {
  color: rgb(var(--color-primary-500));
}
```

❌ **Inconsistent configuration structure**:
```toml
# All settings in one file
```
✅ **Split by concern**:
```
config/_default/hugo.toml
config/_default/params.toml
config/_default/menus.zh-cn.toml
```

### Layout Anti-Patterns

❌ **Missing dark mode support**:
```css
:root {
  --color: #000;
}
```
✅ **Support both modes**:
```css
:root {
  --color: rgb(var(--color-neutral-900));
}
.dark {
  --color: rgb(var(--color-neutral-100));
}
```

❌ **Inline styles in HTML**:
```html
<div style="color: red;">
```
✅ **Use Tailwind classes or custom CSS**:
```html
<div class="text-red-500 dark:text-red-400">
```

---

## Quality Checklist

### Content Quality
- [ ] Title is descriptive and concise
- [ ] Content is well-structured with clear headings
- [ ] Technical instructions are accurate and complete
- [ ] Code examples are tested and work as documented
- [ ] Writing is clear and conversational
- [ ] Chinese grammar and punctuation are correct

### Technical Quality
- [ ] All frontmatter fields are correctly formatted
- [ ] Date uses correct timezone (+08:00)
- [ ] Tags are consistent with existing posts
- [ ] Code blocks have language identifiers
- [ ] No Hugo build warnings or errors
- [ ] Configuration follows TOML standards

### User Experience Quality
- [ ] Content renders correctly in light and dark modes
- [ ] Images have descriptive alt text
- [ ] Links are descriptive (not "click here")
- [ ] Navigation structure is logical
- [ ] Mobile-friendly layout
- [ ] Page loads quickly

### Maintenance Quality
- [ ] Configuration is well-commented
- [ ] Custom code includes explanatory comments
- [ ] Git commits are descriptive
- [ ] No hardcoded values that should be configurable
- [ ] Changes documented in appropriate files

---

## Reference Examples

### Complete Blog Post Example

```markdown
---
title: "在 Hyprland 中配置默认应用"
date: 2025-11-28T15:30:00+08:00
draft: false
description: "介绍如何在 Hyprland 窗口管理器中正确配置默认应用程序"
tags: ["Linux", "Hyprland", "配置"]
---

⚠️ **注意**：
> 本文适用于 Hyprland 0.40.0 及以上版本

## 问题背景

在 Hyprland 中使用 `xdg-open` 打开文件时，可能会调用错误的默认应用...

## 解决方案

### 方法说明

通过配置 XDG MIME 类型关联来指定默认应用程序。

### 步骤

1. 查询当前默认应用
```shell
xdg-mime query default text/plain
```

2. 设置新的默认应用
```shell
xdg-mime default org.kde.kate.desktop text/plain
```

### 验证

运行以下命令验证设置：
```shell
xdg-mime query default text/plain
```

应返回 `org.kde.kate.desktop`。

## 后记

这个方法不仅适用于 Hyprland，也适用于其他 Wayland 合成器 😊

## 参考资料

- [Arch Wiki - Default Applications](https://wiki.archlinux.org/title/Default_applications)
- [Hyprland Wiki](https://wiki.hyprland.org/)
```

---

**Remember**: These standards are derived from actual patterns in the existing codebase. Consistency with established conventions is more important than personal preferences.
