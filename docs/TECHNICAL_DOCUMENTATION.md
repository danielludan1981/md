# Doocs Markdown Editor - 技术文档

## 项目概述

Doocs Markdown Editor 是一款高度简洁的微信 Markdown 编辑器，支持 Markdown 语法、自定义主题样式、内容管理、多图床、AI 助手等特性。项目采用 Monorepo 架构，基于 Vue 3 + TypeScript 构建。

---

## 目录

1. [项目架构](#项目架构)
2. [技术栈](#技术栈)
3. [包结构](#包结构)
4. [核心模块详解](#核心模块详解)
5. [数据流](#数据流)
6. [主题系统](#主题系统)
7. [渲染流程](#渲染流程)
8. [存储系统](#存储系统)
9. [图床集成](#图床集成)
10. [扩展机制](#扩展机制)

---

## 项目架构

```
md/
├── apps/                          # 应用程序
│   ├── web/                       # Web 主应用 (Vue 3 + Vite)
│   ├── vscode/                    # VS Code 扩展
│   └── utools/                    # uTools 插件
├── packages/                      # 共享包
│   ├── core/                      # 核心渲染引擎
│   ├── shared/                    # 共享工具、类型、配置
│   ├── config/                    # 共享配置 (TSConfig)
│   └── md-cli/                    # CLI 工具
├── patches/                       # 补丁文件
└── package.json                   # 根配置
```

### Monorepo 管理

- **包管理器**: pnpm
- **工作区协议**: `workspace:*`

---

## 技术栈

### 前端框架
- **Vue 3**: 渐进式 JavaScript 框架 (v3.5.27)
- **TypeScript**: 类型安全的 JavaScript 超集 (~5.9.3)
- **Vite**: 下一代前端构建工具 (v7.3.1)

### 编辑器
- **CodeMirror 6**: 现代化代码编辑器
  - `@codemirror/state`: 编辑器状态管理
  - `@codemirror/view`: 编辑器视图
  - `@codemirror/lang-markdown`: Markdown 语言支持

### Markdown 处理
- **marked**: Markdown 解析器 (v17.0.1)
- **highlight.js**: 代码语法高亮 (v11.11.1)
- **mermaid**: 图表绘制 (v11.12.2)
- **KaTeX**: 数学公式渲染

### UI 组件
- **Tailwind CSS**: 实用优先的 CSS 框架 (v4.1.18)
- **Radix Vue**: 无障碍 UI 组件原语
- **Lucide Vue**: 图标库

### 状态管理
- **Pinia**: Vue 官方状态管理库 (v3.0.4)
- **VueUse**: Vue 组合式函数库 (v14.2.0)

### 构建工具
- **WXT**: Web 扩展工具包 (浏览器扩展)
- **Wrangler**: Cloudflare Workers CLI

---

## 包结构

### @md/core (核心渲染引擎)

位置: `packages/core/`

```
core/src/
├── extensions/           # Markdown 扩展
│   ├── alert.ts         # GFM 警告块
│   ├── footnotes.ts     # 脚注
│   ├── infographic.ts   # AntV 信息图
│   ├── katex.ts         # 数学公式
│   ├── markup.ts        # 标记语法
│   ├── mermaid.ts       # Mermaid 图表
│   ├── plantuml.ts      # PlantUML
│   ├── ruby.ts          # 注音符号
│   ├── slider.ts        # 轮播图
│   └── toc.ts           # 目录生成
├── renderer/            # 渲染器
│   └── renderer-impl.ts # 渲染器实现
├── theme/               # 主题系统
│   ├── cssProcessor.ts  # CSS 处理器
│   ├── cssScopeWrapper.ts # CSS 作用域包装
│   ├── cssVariables.ts  # CSS 变量
│   ├── selectorMapping.ts # 选择器映射
│   ├── themeApplicator.ts # 主题应用器
│   ├── themeExporter.ts   # 主题导出
│   └── themeInjector.ts   # 主题注入
└── utils/               # 工具函数
    ├── basicHelpers.ts
    ├── languages.ts     # 语言支持
    └── markdownHelpers.ts
```

**主要导出**:
- `initRenderer()`: 初始化 Markdown 渲染器
- `applyTheme()`: 应用主题配置
- 各种 Markdown 扩展

### @md/shared (共享模块)

位置: `packages/shared/`

```
shared/src/
├── configs/             # 配置
│   ├── ai-service-options.ts  # AI 服务选项
│   ├── api.ts                 # API 配置
│   ├── prefix.ts              # 存储前缀
│   ├── shortcut-key.ts        # 快捷键
│   ├── store.ts               # 存储配置
│   ├── style.ts               # 样式配置
│   └── theme.ts               # 主题配置
├── constants/           # 常量
│   ├── ai-config.ts
│   └── index.ts
├── editor/              # 编辑器相关
│   ├── basicSetup.ts    # 基础设置
│   ├── css.ts           # CSS 编辑
│   ├── format.ts        # 格式化
│   ├── index.ts
│   ├── javascript.ts    # JavaScript 编辑
│   ├── markdown.ts      # Markdown 编辑
│   └── themes.ts        # 编辑器主题
├── types/               # TypeScript 类型
│   ├── ai-services-types.ts
│   ├── common.ts
│   ├── renderer-types.ts
│   └── template.ts
└── utils/               # 工具函数
    ├── basicHelpers.ts
    ├── fetch.ts
    ├── fileHelpers.ts
    ├── index.ts
    └── tokenTools.ts
```

### @md/web (Web 应用)

位置: `apps/web/`

```
web/src/
├── components/          # 组件
│   ├── ai/             # AI 相关组件
│   ├── editor/         # 编辑器组件
│   └── ui/             # UI 组件 (shadcn-vue)
├── composables/         # 组合式函数
│   ├── useEditorFormat.ts
│   ├── useFolderFileSync.ts
│   └── useImageUploader.ts
├── entrypoints/         # 入口点
│   └── popup/          # 浏览器扩展弹窗
├── stores/              # Pinia Stores
│   ├── aiConfig.ts     # AI 配置
│   ├── aiImageConfig.ts # AI 图片配置
│   ├── cssEditor.ts    # CSS 编辑器
│   ├── editor.ts       # 编辑器状态
│   ├── export.ts       # 导出功能
│   ├── folderSource.ts # 文件夹源
│   ├── post.ts         # 文章管理
│   ├── quickCommands.ts # 快捷命令
│   ├── render.ts       # 渲染状态
│   ├── template.ts     # 模板
│   ├── theme.ts        # 主题配置
│   └── ui.ts           # UI 状态
├── utils/               # 工具函数
│   ├── clipboard.ts    # 剪贴板
│   ├── file.ts         # 文件上传
│   ├── index.ts        # 通用工具
│   ├── setup-components.ts
│   └── storage.ts      # 存储管理
└── views/               # 页面视图
    └── CodemirrorEditor.vue
```

---

## 核心模块详解

### 1. 渲染系统 (@md/core/renderer)

**文件**: `packages/core/src/renderer/renderer-impl.ts`

渲染系统负责将 Markdown 文本转换为 HTML，核心功能包括：

#### 初始化渲染器

```typescript
import { initRenderer } from '@md/core'

const renderer = initRenderer({
  citeStatus: true,        // 启用引用链接
  legend: 'alt-title',     // 图注格式
  countStatus: true,       // 显示字数统计
  isMacCodeBlock: true,    // Mac 风格代码块
  isShowLineNumber: false, // 显示行号
  themeMode: 'light',      // 主题模式
})
```

#### 渲染流程

1. **Front Matter 解析**: 使用 `front-matter` 解析 YAML 前置数据
2. **Markdown 解析**: 使用 `marked` 解析 Markdown
3. **自定义渲染**: 通过自定义 Renderer 生成带 CSS 类的 HTML
4. **扩展处理**: 应用各种 marked 扩展 (Katex、Mermaid、Alert 等)
5. **后处理**: 添加阅读时间、脚注等

#### 关键方法

| 方法 | 说明 |
|------|------|
| `reset(newOpts)` | 重置渲染器配置 |
| `setOptions(newOpts)` | 更新配置 |
| `parseFrontMatterAndContent(md)` | 解析 Front Matter |
| `buildReadingTime(rt)` | 生成阅读时间 HTML |
| `buildFootnotes()` | 生成脚注 HTML |
| `createContainer(html)` | 创建容器 |

### 2. 主题系统 (@md/core/theme)

**文件**: `packages/core/src/theme/`

主题系统采用 CSS 变量 + 动态注入的方式，支持：

- 多主题切换
- 自定义 CSS
- 响应式变量

#### 核心组件

1. **themeInjector.ts**: 将主题 CSS 注入到 `<style id="md-theme">` 标签
2. **cssProcessor.ts**: 使用 PostCSS 处理 CSS
3. **cssVariables.ts**: 管理 CSS 变量
4. **themeApplicator.ts**: 应用主题配置

#### 使用方式

```typescript
import { applyTheme } from '@md/core'

await applyTheme({
  themeName: 'default',
  customCSS: '/* 自定义 CSS */',
  variables: {
    primaryColor: '#0F4C81',
    fontFamily: '-apple-system, ...',
    fontSize: '16px',
    isUseIndent: false,
    isUseJustify: false,
    headingStyles: { h1: 'border-left', h2: 'color-only' },
  },
})
```

### 3. 文章管理 Store (apps/web/src/stores/post.ts)

文章管理使用 Pinia Store，支持：

- 多文章管理
- 历史记录 (每 30 秒自动保存)
- 文章树形结构 (父子关系)

```typescript
interface Post {
  id: string
  title: string
  content: string
  history: { datetime: string; content: string }[]
  createDatetime: Date
  updateDatetime: Date
  parentId?: string | null
  collapsed?: boolean
}
```

### 4. 编辑器 Store (apps/web/src/stores/editor.ts)

管理 CodeMirror 编辑器实例：

```typescript
const editorStore = useEditorStore()

// 获取/设置内容
const content = editorStore.getContent()
editorStore.importContent('# New Content')

// 格式化
await editorStore.formatContent()

// 选中文本操作
const selection = editorStore.getSelection()
editorStore.replaceSelection('replacement')
editorStore.insertAtCursor('inserted text')
```

### 5. 渲染 Store (apps/web/src/stores/render.ts)

管理 Markdown 渲染：

```typescript
const renderStore = useRenderStore()

// 初始化渲染器
renderStore.initRendererInstance({
  isMacCodeBlock: true,
  isShowLineNumber: false,
})

// 渲染 Markdown
const html = renderStore.render(markdownContent)

// 访问输出和统计
const { output, readingTime, titleList } = storeToRefs(renderStore)
```

---

## 数据流

```
┌─────────────────────────────────────────────────────────────┐
│                         用户操作                             │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    CodeMirror 编辑器                         │
│              (apps/web/src/views/CodemirrorEditor.vue)      │
└────────────────────┬────────────────────────────────────────┘
                     │ 输入/粘贴/拖拽
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    Editor Store                              │
│              (apps/web/src/stores/editor.ts)                │
└────────────────────┬────────────────────────────────────────┘
                     │ 内容变更 (300ms 防抖)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    Render Store                              │
│              (apps/web/src/stores/render.ts)                │
└────────────────────┬────────────────────────────────────────┘
                     │ 调用 render()
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              @md/core Renderer (initRenderer)               │
│              (packages/core/src/renderer/renderer-impl.ts)  │
└────────────────────┬────────────────────────────────────────┘
                     │ marked.parse()
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              Marked Extensions                               │
│  - markedAlert, markedMermaid, MDKatex, markedFootnotes...  │
└────────────────────┬────────────────────────────────────────┘
                     │ HTML 输出
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    预览区域 (#output)                        │
│              (apps/web/src/views/CodemirrorEditor.vue)      │
└─────────────────────────────────────────────────────────────┘
```

---

## 主题系统

### 主题配置结构

```typescript
// packages/shared/src/configs/style.ts
interface StyleConfig {
  theme: ThemeName              // 主题名称
  fontFamily: string            // 字体
  fontSize: string              // 字体大小
  primaryColor: string          // 主题色
  codeBlockTheme: string        // 代码块主题 URL
  legend: string                // 图注格式
  isMacCodeBlock: boolean       // Mac 代码块样式
  isShowLineNumber: boolean     // 显示行号
  isCiteStatus: boolean         // 引用链接
  isCountStatus: boolean        // 字数统计
  headingStyles: HeadingStyles  // 标题样式
}
```

### CSS 变量

```css
:root {
  --md-primary-color: #0F4C81;
  --md-font-family: -apple-system, ...;
  --md-font-size: 16px;
  --blockquote-background: #f7f7f7;
  --foreground: hsl(var(--foreground));
}
```

### 标题样式选项

- `default`: 默认样式
- `color-only`: 仅主题色文字
- `border-bottom`: 底部边框
- `border-left`: 左侧边框
- `custom`: 自定义 CSS

---

## 渲染流程

### 1. Markdown 解析流程

```typescript
// 1. 解析 Front Matter
const { yamlData, markdownContent, readingTime } =
  renderer.parseFrontMatterAndContent(rawContent)

// 2. 使用 marked 解析
marked.use({ renderer: customRenderer })
marked.use(markedAlert())
marked.use(MDKatex({ nonStandard: true }, true))
marked.use(markedMermaid())
// ... 其他扩展

const html = marked.parse(markdownContent)

// 3. 后处理
const finalHtml = postProcessHtml(html, readingTime, renderer)
```

### 2. 自定义 Renderer

```typescript
const renderer: RendererObject = {
  heading({ tokens, depth }) {
    const text = this.parser.parseInline(tokens)
    return styledContent(`h${depth}`, text)
  },

  code({ text, lang }) {
    const highlighted = highlightAndFormatCode(text, lang, hljs, showLineNumber)
    return `<pre class="hljs code__pre">${macCodeSvg}<code>${highlighted}</code></pre>`
  },

  image({ href, title, text }) {
    return `<figure><img src="${href}" alt="${text}"/><figcaption>${text}</figcaption></figure>`
  },

  // ... 其他方法
}
```

---

## 存储系统

**文件**: `apps/web/src/utils/storage.ts`

### StorageEngine 接口

```typescript
interface StorageEngine {
  get(key: string): Promise<string | null>
  set(key: string, value: string): Promise<void>
  remove(key: string): Promise<void>
  has(key: string): Promise<boolean>
  clear(): Promise<void>
  keys(): Promise<string[]>
}
```

### 存储引擎实现

1. **LocalStorageEngine**: 使用 localStorage
2. **RestfulStorageEngine**: 使用 RESTful API

### 响应式存储

```typescript
// 创建响应式引用，自动同步到存储
const theme = store.reactive<ThemeName>('md__theme', 'default')
const posts = store.reactive<Post[]>('md__posts', [])

// 修改自动保存
theme.value = 'new-theme' // 自动保存到 localStorage
```

---

## 图床集成

**文件**: `apps/web/src/utils/file.ts`

支持 13 种图床服务：

| 图床 | 配置参数 | 说明 |
|------|----------|------|
| 默认 (GitHub) | 无需配置 | 使用默认 GitHub 仓库 |
| GitHub | Repo, Token | 自定义 GitHub 仓库 |
| 阿里云 OSS | AccessKey, Bucket, Region | 阿里云对象存储 |
| 腾讯云 COS | SecretId, SecretKey, Bucket | 腾讯云对象存储 |
| 七牛云 | AccessKey, SecretKey, Bucket | 七牛云 Kodo |
| MinIO | Endpoint, Port, Bucket | 自建 MinIO |
| S3 协议 | Endpoint, Region, Bucket | AWS S3 兼容 |
| 公众号 | appID, appsecret | 微信公众号素材 |
| Cloudflare R2 | AccountId, AccessKey | R2 对象存储 |
| 又拍云 | Bucket, Operator | 又拍云存储 |
| Telegram | Bot Token, Chat ID | Telegram 图床 |
| Cloudinary | Cloud Name, API Key | Cloudinary CDN |
| 自定义 | 自定义代码 | 自定义上传逻辑 |

### 使用示例

```typescript
import { fileUpload } from '@/utils/file'

// 上传文件
const imageUrl = await fileUpload(base64Content, file)

// 获取当前图床配置
const imgHost = await store.get('imgHost') || 'default'
const config = await store.get(`${imgHost}Config`)
```

---

## 扩展机制

### Markdown 扩展示例

```typescript
// packages/core/src/extensions/alert.ts
import { marked } from 'marked'

export function markedAlert(options = {}) {
  return {
    tokenizer: {
      // 自定义词法分析
    },
    renderer: {
      // 自定义渲染
    }
  }
}
```

### 扩展示例：Ruby 注音

```typescript
// packages/core/src/extensions/ruby.ts
// 支持 [文字]{注音} 和 [文字]^(注音) 语法
export function markedRuby() {
  return {
    tokenizer: {
      ruby: function(src: string) {
        const match = src.match(/^\[(.+?)\]\{(.*?)\}/) ||
                      src.match(/^\[(.+?)\]\^\((.*?)\)/)
        if (match) {
          return {
            type: 'ruby',
            raw: match[0],
            text: match[1],
            ruby: match[2],
          }
        }
      }
    },
    renderer: {
      ruby: function(token) {
        return `<ruby>${token.text}<rt>${token.ruby}</rt></ruby>`
      }
    }
  }
}
```

---

## 开发指南

### 安装依赖

```bash
# 使用正确的 Node 版本
nvm i && nvm use

# 安装依赖
pnpm i
```

### 开发命令

```bash
# 启动开发服务器
pnpm web dev
# 访问 http://localhost:5173/md/

# 构建
pnpm web build

# 构建 (根目录部署)
pnpm web build:h5-netlify

# 类型检查
pnpm type-check

# 代码检查
pnpm lint
```

### 添加新图床

1. 在 `apps/web/src/utils/file.ts` 添加上传函数
2. 在配置界面添加对应配置表单
3. 在 `fileUpload` 函数中添加 case 分支

### 添加新主题

1. 在 `packages/shared/src/configs/theme.ts` 添加主题配置
2. 在 `packages/core/src/theme/` 添加主题 CSS 处理逻辑

---

## 性能优化

1. **防抖处理**: 编辑器输入 300ms 防抖
2. **按需加载**: 语言高亮按需加载
3. **虚拟滚动**: 大文档使用虚拟滚动
4. **图片压缩**: 支持上传前图片压缩
5. **代码分割**: 按路由和组件分割代码

---

## 浏览器支持

- Chrome (推荐)
- Firefox
- Safari
- Edge

---

## 许可证

ISC License

---

*文档版本: 2.1.0*
*最后更新: 2026-02-08*
