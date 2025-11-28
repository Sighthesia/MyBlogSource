# Technology Stack

## Core Technologies

### Hugo Static Site Generator
- **Minimum Version**: 0.87.0
- **Current Testing Version**: 0.142.0 (from theme netlify.toml)
- **Purpose**: Static site generation from Markdown content
- **Key Features Used**:
  - CJK language support (`hasCJKLanguage = true`)
  - Emoji support (`enableEmoji = true`)
  - Multi-output formats (HTML, RSS, JSON)
  - Goldmark Markdown renderer
  - Built-in taxonomies (tags, categories, authors, series)

### Blowfish Theme
- **Version**: 2.91.0
- **Author**: Nuno Coração (based on Congo by James Panther)
- **Repository**: https://github.com/nunocoracao/blowfish
- **License**: MIT
- **Installation Method**: Git submodule
- **Key Features**:
  - Tailwind CSS 4.1.14+ based styling
  - Multiple layout options (background, hero, card, profile)
  - Built-in dark mode support
  - Search functionality
  - Code copy feature
  - Math equation support (KaTeX)
  - Multiple hero styles for articles

## Frontend Technologies

### CSS Framework
- **Tailwind CSS**: 4.1.14+ (via @tailwindcss/cli)
- **Custom CSS**: `assets/css/custom.css` (currently empty, all styling via Tailwind)
- **CSS Variables**: Blowfish theme provides extensive CSS variable system
  - Color system: `--color-primary-*`, `--color-neutral-*`, etc.
  - Responsive utilities
  - Light/dark mode support via `.dark` class

### JavaScript
- **Waline Client**: v3 (comment system)
  - CDN: https://unpkg.com/@waline/client@v3
  - Dynamic module import for client-side loading
- **jQuery**: Bundled with theme for specific features
- **Zoom.js**: Image zoom functionality
- **Theme JavaScript**: Built-in features (search, dark mode toggle, etc.)

## Markdown Processing

### Goldmark Renderer
- **Configuration Location**: `config/_default/markup.toml`
- **Settings**:
  - `unsafe = true` - Allows raw HTML in Markdown
  - `noClasses = false` - Uses CSS classes for code highlighting
  - Auto-heading IDs enabled
  - Smart typography disabled (for Chinese content)

### Math Support
- **Inline**: `\(...\)` delimiter
- **Block**: `\[...\]` and `$$...$$` delimiters
- **Renderer**: KaTeX (client-side)

### Code Highlighting
- **Method**: CSS classes (not inline styles)
- **Supported**: All major languages
- **Copy Button**: Enabled via `enableCodeCopy = true`

## Third-Party Services

### Comment System - Waline
- **Version**: v3
- **Server URL**: https://sighthesia-s-blog.vercel.app/
- **Features Used**:
  - Comment threading
  - Page view counter
  - Markdown support in comments
  - Emoji support
  - Dark mode support
- **Integration**: Custom partials in `layouts/partials/`

### Deployment - GitHub Pages
- **URL**: https://sighthesia.github.io/
- **Method**: Static files in `public/` directory
- **Branch**: main
- **Auto-deployment**: Enabled

## Development Tools

### Package Management
- **npm/npx**: For Tailwind CSS CLI
- **Hugo Modules**: Not currently used (module.toml is empty)
- **Git Submodules**: For theme management

### Build Process
- **Development**: `hugo server -D` (includes drafts)
- **Production**: `hugo` (builds to `public/`)
- **Theme Development**: Tailwind watch mode available via npm scripts

## Language and Localization

### Primary Language
- **Code**: zh-cn (Simplified Chinese)
- **Timezone**: +08:00 (China Standard Time)
- **Date Format**: ISO 8601
- **CJK Support**: Enabled via `hasCJKLanguage = true`

### i18n
- **Location**: `i18n/` directory (currently uses theme defaults)
- **Language Files**: zh-cn.yaml (from theme)
- **Menu Localization**: `config/_default/menus.zh-cn.toml`
- **Language Config**: `config/_default/languages.zh-cn.toml`

## File Formats

### Configuration
- **Format**: TOML (not YAML)
- **Location**: `config/_default/*.toml`
- **Separation Strategy**: Split by concern (hugo, params, menus, languages, markup, module)

### Content
- **Format**: Markdown with TOML frontmatter
- **Frontmatter Delimiter**: `---` (despite using TOML syntax inside)
- **File Extension**: `.md`
- **Character Encoding**: UTF-8

### Assets
- **Images**: PNG, JPG, WEBP
- **Icons**: Favicon formats (32x32, etc.)
- **Fonts**: Default system fonts (no custom fonts)

## Browser Compatibility

### Target Browsers
- Modern browsers with ES6+ support
- Light and dark color scheme support
- CSS Grid and Flexbox support

### Progressive Enhancement
- Core content accessible without JavaScript
- Comment system requires JavaScript
- Dark mode toggle requires JavaScript
- Search requires JavaScript

## Performance Optimizations

### Image Handling
- **Default**: Image optimization enabled
- **Configuration**: `disableImageOptimization = false`
- **Markdown Images**: Automatic conversion via theme

### Asset Fingerprinting
- **Algorithm**: sha512
- **Purpose**: Cache busting for CSS/JS files

### Output Optimization
- **Minification**: Enabled in production builds
- **RSS**: Generated for posts
- **JSON**: Search index generation
- **Sitemap**: Auto-generated with daily update frequency

## Security Considerations

### Content Security
- **Raw HTML**: Allowed via `unsafe = true` in Goldmark
- **User Input**: Only in comments (handled by Waline server)
- **XSS Protection**: Handled by Waline comment system

### HTTPS
- **GitHub Pages**: Automatic HTTPS
- **Comment Server**: HTTPS via Vercel

## Version Constraints

### Strict Requirements
- Hugo >= 0.87.0
- Blowfish theme = 2.91.0 (locked via git submodule)

### Flexible Dependencies
- Tailwind CSS: 4.x (managed by theme)
- Waline: v3.x (loaded from CDN, version not locked)
- Browser JavaScript: ES6+ features

## Upgrade Considerations

### When Upgrading Hugo
1. Check Blowfish theme compatibility
2. Test with `hugo server -D`
3. Verify CJK language support still works
4. Check Goldmark rendering behavior
5. Validate all existing posts render correctly

### When Upgrading Blowfish Theme
1. Review theme changelog for breaking changes
2. Check if custom partials need updates
3. Verify CSS variable names haven't changed
4. Test Waline integration still works
5. Validate all layout configurations in params.toml

### When Upgrading Waline
1. Check if CSS variables have changed
2. Verify pageview counter API compatibility
3. Test dark mode styling
4. Check if new features require configuration changes
