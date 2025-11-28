# GitHub Copilot Instructions for Hugo Blowfish Blog

## Priority Guidelines

When generating code for this repository:

1. **Version Compatibility**: Always detect and respect the exact versions of Hugo, Blowfish theme, and dependencies used in this project
2. **Context Files**: Prioritize patterns and standards defined in the .github/copilot directory
3. **Codebase Patterns**: When context files don't provide specific guidance, scan the codebase for established patterns
4. **Architectural Consistency**: Maintain Hugo's standard directory structure and Blowfish theme conventions
5. **Code Quality**: Prioritize maintainability, consistency with existing content, and proper Chinese language support in all generated code

## Technology Version Detection

Before generating code, scan the codebase to identify:

1. **Hugo Version**: Minimum version **0.87.0** (detected from theme requirements)
   - Never use Hugo features beyond this version without verification
   - Respect `hasCJKLanguage = true` for Chinese content handling

2. **Blowfish Theme Version**: **2.91.0** (detected from themes/blowfish/package.json)
   - Follow theme-specific configuration patterns in config/_default/*.toml
   - Never override theme files directly - use project-level overrides

3. **Key Dependencies**:
   - Tailwind CSS 4.1.14+ (via @tailwindcss/cli)
   - Waline v3 (comment system, loaded from unpkg CDN)
   - Goldmark (Hugo's Markdown renderer with custom config)

## Context Files

Prioritize the following files in .github/copilot directory:

- **tech-stack.md**: Technology versions and framework details
- **folder-structure.md**: Project organization guidelines
- **coding-standards.md**: Content creation and styling standards

## Codebase Scanning Instructions

When context files don't provide specific guidance:

1. **For new blog posts**: Analyze existing files in `content/posts/` for:
   - Frontmatter field usage and format
   - Heading structure (## for main sections, ### for subsections)
   - Code block language identifiers (shell, toml, html, etc.)
   - Warning/note formatting patterns (blockquote + emoji)
   - Footer sections (后记, 参考资料)

2. **For configuration changes**: Check similar settings in:
   - `config/_default/hugo.toml` - Core Hugo settings
   - `config/_default/params.toml` - Blowfish theme parameters
   - `config/_default/languages.zh-cn.toml` - Chinese localization
   - `config/_default/menus.zh-cn.toml` - Navigation structure

3. **For layout customization**: Examine patterns in:
   - `layouts/partials/` - Theme overrides (comments, extend-head, etc.)
   - CSS variable mapping for Waline in extend-head.html
   - Never modify files in `themes/blowfish/` directly

## Hugo + Blowfish Specific Guidelines

### Content Creation

**Frontmatter Standard** (mandatory fields):
```yaml
---
title: "文章标题"
date: 2025-11-28T22:06:23+08:00  # ISO 8601 format with +08:00 timezone
draft: false                      # Must set to false to publish
description: ""                   # Optional but recommended for SEO
tags: ["Linux", "美化"]           # Array of strings, Chinese tags allowed
---
```

**Article Structure Pattern**:
1. Opening context or warning (if applicable, using blockquote + ⚠️)
2. Main content sections with `##` headings
3. Subsections with `###` headings (方法说明, 步骤, 示例)
4. Code blocks with explicit language tags: ```shell, ```toml, ```html, etc.
5. Optional `## 后记` section for personal notes
6. Optional `## 参考资料` section with links to sources

**Writing Style** (based on existing posts):
- Use conversational Chinese with technical accuracy
- Moderate emoji usage for emphasis (⚠️, 😊, 🐧)
- Strike-through for humorous asides: ~~text~~
- Clear step-by-step instructions for technical procedures
- Include command examples with fish shell syntax

### Configuration Management

**TOML File Conventions**:
- Use lowercase for boolean values: `true`, `false`
- Quote string values: `"value"`
- Group related settings with comments: `# -- Section Name --`
- Preserve existing comment structure from theme defaults

**Critical Settings**:
- `hasCJKLanguage = true` - Required for Chinese content
- `enableEmoji = true` - Required for emoji in content
- `defaultContentLanguage = "zh-cn"` - Primary language
- `theme = "blowfish"` - Theme identifier

### Theme Customization

**Layout Override Pattern**:
1. Create files in `layouts/partials/` to override theme defaults
2. Never modify `themes/blowfish/` directly
3. Use Hugo's lookup order: project layouts > theme layouts

**Waline Integration Pattern** (from extend-head.html):
- Map Waline CSS variables to Blowfish theme colors using `rgb(var(--color-*))`
- Support both light and dark modes with `.dark` selector
- Load Waline v3 client from CDN: `https://unpkg.com/@waline/client@v3`

**CSS Variable Mapping Example**:
```css
:root {
    --waline-theme-color: rgb(var(--color-primary-500));
    --waline-bg-color: rgb(var(--color-neutral-50));
}
.dark {
    --waline-bg-color: rgb(var(--color-neutral-900));
}
```

### Archetype Usage

**Creating New Posts** (fish shell):
```fish
hugo new content posts/新文章标题.md
```

**Archetype Pattern** (from archetypes/default.md):
- Title auto-generated from filename: `{{ replace .Name "-" " " | title }}`
- Date auto-populated: `{{ .Date }}` (ISO 8601 with timezone)
- Draft set to `true` by default (must manually change to `false`)
- Empty description field (fill in before publishing)

### Markdown Rendering Configuration

**Goldmark Settings** (from markup.toml):
- `unsafe = true` - Allows raw HTML in Markdown
- Math delimiters: `$$...$$` (block), `\(...\)` (inline)
- Code highlighting: Use CSS classes (not inline styles)
- TOC: Levels 2-4 (`startLevel = 2`, `endLevel = 4`)

**Code Block Requirements**:
- Always specify language: ```shell, ```toml, ```yaml, ```html, ```css, ```javascript
- Use 4-space indentation for code within lists
- Triple backticks for multi-line, single backticks for inline code

## Version Control Guidelines

**Commit Workflow**:
1. Create/edit content in `content/posts/`
2. Test locally: `hugo server -D` (includes drafts)
3. Set `draft: false` when ready to publish
4. Build: `hugo` (generates static files in `public/`)
5. Commit changes to main branch
6. GitHub Pages auto-deploys from `public/` directory

**File Organization**:
- Content: `content/posts/*.md` (one file per post)
- Assets: `assets/css/`, `assets/img/`, `assets/js/`
- Generated files: `public/` (Git tracked for GitHub Pages)
- Theme: `themes/blowfish/` (Git submodule, do not modify)

## Documentation Requirements

**For Blog Posts**:
- Write in clear, conversational Chinese
- Document complex technical procedures with step-by-step instructions
- Include warnings for critical operations using blockquote format
- Provide command examples with expected output when relevant
- Link to official documentation in 参考资料 section

**For Configuration Files**:
- Preserve existing comment structure from Blowfish defaults
- Add inline comments for non-obvious settings
- Use section dividers: `# -- Section Name --`

**For Custom Layouts/Partials**:
- Add HTML comments explaining override purpose
- Document CSS variable mappings
- Note light/dark mode considerations

## Project-Specific Best Practices

### Content Standards

1. **Language**: Primary language is Chinese (zh-cn), technical terms may be in English
2. **Date Format**: Always use ISO 8601 with +08:00 timezone
3. **Tags**: Use Chinese or English, maintain consistency with existing tags
4. **Emoji**: Use sparingly for emphasis (⚠️ for warnings, 😊 for friendly notes)

### Configuration Standards

1. **Never modify theme files**: All customizations via project-level overrides
2. **Test configuration changes**: Run `hugo server` before committing
3. **Preserve Blowfish conventions**: Follow theme's parameter naming and structure
4. **Comment critical settings**: Explain why non-default values are used

### Layout Customization Standards

1. **Waline color mapping**: Always sync with Blowfish theme variables
2. **Responsive design**: Test light and dark modes for custom CSS
3. **Partial naming**: Use descriptive names (extend-head, extend-footer, comments)
4. **CSS specificity**: Use theme variables instead of hardcoded colors

### Deployment Standards

1. **Draft workflow**: New posts start as `draft: true`
2. **Local preview**: Always test with `hugo server -D` before publishing
3. **GitHub Pages**: Public directory is tracked and deployed automatically
4. **Clean builds**: Run `hugo` without draft flag for production

## Anti-Patterns to Avoid

❌ **Never** modify files in `themes/blowfish/` directory
❌ **Never** commit with `draft: true` unless intentionally keeping unpublished
❌ **Never** use YAML frontmatter format (project uses TOML: `---` not `+++`)
❌ **Never** omit language identifier from code blocks
❌ **Never** use features from Hugo versions above 0.87.0 without verification
❌ **Never** hardcode colors in custom CSS (use Blowfish theme variables)
❌ **Never** forget `hasCJKLanguage = true` when working with Chinese content
❌ **Never** use incorrect date format (must be ISO 8601 with +08:00)

## Quick Reference

### Common Commands (fish shell)

```fish
# Create new post
hugo new content posts/文章标题.md

# Local development server (includes drafts)
hugo server -D

# Production build
hugo

# Check Hugo version
hugo version
```

### File Locations

- Blog posts: `content/posts/*.md`
- Configuration: `config/_default/*.toml`
- Custom layouts: `layouts/partials/`
- Custom CSS: `assets/css/custom.css`
- Images: `assets/img/`
- Theme: `themes/blowfish/` (read-only)

### Important Configuration Files

- `hugo.toml` - Core Hugo settings, theme selection
- `params.toml` - Blowfish theme configuration
- `languages.zh-cn.toml` - Chinese language settings
- `menus.zh-cn.toml` - Navigation menu structure
- `markup.toml` - Markdown rendering options

### Waline Comment System

- CDN: `https://unpkg.com/@waline/client@v3`
- Server: `https://sighthesia-s-blog.vercel.app/`
- Integration: `layouts/partials/extend-head.html` + `comments.html`
- Styling: CSS variables mapped to Blowfish theme colors

---

**Remember**: Consistency with existing patterns is the highest priority. When in doubt, examine similar files in the codebase and follow the established conventions.
