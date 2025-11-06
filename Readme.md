# Vue 项目创建与 NPM 发布完整指南

本指南将带你从零开始创建一个 Vue 项目，使用 tsup 进行打包，并发布到 NPM 仓库。

---

## 📋 目录

1. [创建项目](#第一步-创建项目)
2. [安装 tsup 打包工具](#第二步安装-tsup-打包工具)
3. [配置 package.json](#第三步配置-packagejson)
4. [配置 tsconfig.json](#第四步配置-tsconfigjson)
5. [登录 NPM 账户](#第五步登录-npm-账户)
6. [发布项目](#第六步发布项目)
7. [常见问题解决](#常见问题解决)

---

## 第一步：创建项目

### 使用 Vite 创建项目

Vite 是 Vue 官方推荐的前端构建工具，提供了极快的开发体验。

```bash
# 创建项目
npm create vite@latest
```

### 填写项目信息

执行命令后，系统会提示：

```
✔ Project name: ... (输入项目名称，例如：my-vue-component-library)
✔ Select a framework: › - Use arrow-keys to return to the options and press Enter to select
  - Vue
  - React
  - JavaScript
  - TypeScript
```

1. **输入项目名称**：输入你想要的英文项目名称（推荐使用短横线命名法，如 `my-vue-lib`）
2. **选择框架**：选择 `Vue/react`
3. **选择变体**：选择 `TypeScript`（推荐）

### 进入项目目录并安装依赖

```bash
cd your-project-name

# 安装项目依赖
npm install

# 安装 Vue Router（如果需要）
# npm install vue-router@4
```

> 💡 **提示**：项目名称将成为你的 NPM 包名，确保名称唯一且有意义

---

## 第二步：安装 tsup 打包工具

### 什么是 tsup？

tsup 是由 Egor Egorov 开发的高性能 TypeScript 打包工具，特点：

- 🚀 极速打包（基于 esbuild）
- 📦 零配置，开箱即用
- 🔥 支持多格式输出（ESM、CJS、UMD）
- 📝 自动生成类型声明文件
- ⚙️ 支持 CSS 提取

### 安装 tsup 及相关依赖

```bash
# 安装 tsup
npm install -D tsup

# # 安装 Vue 相关类型定义
# npm install -D @types/node

# # 安装其他有用的工具
# npm install -D vue-tsc
```

### 验证安装

```bash
# 检查 tsup 版本
npx tsup -v
```

> ✅ **成功标志**：会显示 tsup 版本号，例如 `tsup v8.0.0`

### 升级 tsup（可选）

```bash
# 升级到最新版本
npm install -D tsup@latest
```

---

## 第三步：配置 package.json

### 打开 package.json 文件

```bash
# 在编辑器中打开
code package.json
```

### 完整配置示例

```json
{
  "name": "your-vue-component-library",
  "version": "1.0.0",
  "description": "一个优秀的 Vue 3 组件库",
  "type": "module",
  "main": "./dist/index.cjs.js",
  "module": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.js",
      "require": "./dist/index.cjs.js"
    },
    "./style.css": "./dist/style.css"
  },
  "files": ["dist"],
  "scripts": {
    "dev": "vite",
    "build": "tsup",
    "build:watch": "tsup --watch",
    "preview": "vite preview",
    "type-check": "vue-tsc --noEmit"
  },
  "keywords": ["vue3", "typescript", "component-library", "ui-components"],
  "author": "Your Name <your@email.com>",
  "license": "MIT",
  "peerDependencies": {
    "vue": "^3.0.0"
  },
  "dependencies": {},
  "devDependencies": {
    "@types/node": "^20.0.0",
    "tsup": "^8.0.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0",
    "vue": "^3.0.0",
    "vue-tsc": "^1.8.0"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/yourusername/your-repo.git"
  },
  "bugs": {
    "url": "https://github.com/yourusername/your-repo/issues"
  },
  "homepage": "https://github.com/yourusername/your-repo#readme"
}
```

### 关键配置项说明

| 字段               | 说明                             | 示例                       |
| ------------------ | -------------------------------- | -------------------------- |
| `name`             | NPM 包名                         | `my-vue-component-library` |
| `version`          | 版本号                           | `1.0.0`                    |
| `description`      | 包描述                           | `一个优秀的 Vue 3 组件库`  |
| `main`             | CommonJS 入口                    | `./dist/index.cjs.js`      |
| `module`           | ES Module 入口                   | `./dist/index.js`          |
| `types`            | TypeScript 类型文件              | `./dist/index.d.ts`        |
| `exports`          | 导出配置（支持多种模块系统）     | 详见配置                   |
| `files`            | 发布文件列表                     | `["dist"]`                 |
| `scripts`          | 可执行脚本                       | `{"build": "tsup"}`        |
| `keywords`         | 搜索关键词                       | `["vue3", "component"]`    |
| `peerDependencies` | 对等依赖（使用者需要安装的依赖） | `{"vue": "^3.0.0"}`        |

### 配置 exports 字段

`exports` 字段允许你定义不同的入口点：

```json
"exports": {
  ".": {
    "types": "./dist/index.d.ts",
    "import": "./dist/index.js",
    "require": "./dist/index.cjs.js"
  },
  "./style.css": "./dist/style.css",
  "./plugin": {
    "types": "./dist/plugin.d.ts",
    "import": "./dist/plugin.js"
  }
}
```

> ⚠️ **重要**：
>
> - `peerDependencies` 中列出使用者需要自己安装的依赖
> - 不要将 `vue` 放在 `dependencies` 中，会导致版本冲突
> - 使用 `tsup` 打包时，所有依赖会被自动处理

---

## 第四步：配置 tsconfig.json

### 创建基础配置

在项目根目录创建或修改 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment",

    /* Linting */
    "strict": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
  "exclude": ["node_modules", "dist", "**/*.test.ts", "**/*.spec.ts"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 创建 tsup 配置文件

在项目根目录创建 `tsup.config.ts`：

```ts
import { defineConfig } from 'tsup'

export default defineConfig({
  entry: ['src/index.ts'],
  outDir: 'dist',
  dts: true,
  sourcemap: true,
  clean: true,
  minify: 'esbuild',
  splitting: false,
  format: ['cjs', 'esm', 'iife'],
  external: ['vue'],
  treeshake: true,
  banner: {
    js: '/* 基于 tsup 构建 */'
  },
  onSuccess: 'npm run type-check'
})
```

### tsup 配置项说明

| 配置项      | 说明               | 示例值                   |
| ----------- | ------------------ | ------------------------ | ------ |
| `entry`     | 入口文件           | `['src/index.ts']`       |
| `outDir`    | 输出目录           | `'dist'`                 |
| `dts`       | 生成类型声明       | `true`                   |
| `sourcemap` | 生成 source map    | `true`                   |
| `clean`     | 构建前清理输出目录 | `true`                   |
| `minify`    | 压缩方式           | `'esbuild'               | false` |
| `splitting` | 代码分割           | `true                    | false` |
| `format`    | 输出格式           | `['cjs', 'esm', 'iife']` |
| `external`  | 外部依赖（不打包） | `['vue']`                |
| `treeshake` | 树摇优化           | `true`                   |
| `onSuccess` | 构建成功后的回调   | `'npm run type-check'`   |

### 构建库项目（带 CSS 提取）

如果你的组件库需要提取 CSS，创建 `tsup.config.ts`：

```ts
import { defineConfig } from 'tsup'
import { createHtmlPlugin } from 'vite-plugin-html'

export default defineConfig({
  entry: ['src/index.ts'],
  outDir: 'dist',
  dts: true,
  sourcemap: true,
  clean: true,
  minify: 'esbuild',
  splitting: true,
  format: ['cjs', 'esm'],
  external: ['vue'],
  treeshake: true,
  css: 'extract', // 提取 CSS
  banner: {
    js: '/* 基于 tsup 构建 */',
    css: '/* 组件样式 */'
  }
})
```

> 💡 **提示**：使用 `css: 'extract'` 可以将 CSS 提取到单独文件中

### 开发环境配置

创建 `tsconfig.build.json`（开发专用）：

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "noEmit": false,
    "outDir": "./dist"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts", "**/*.spec.ts"]
}
```

---

## 第五步：登录 NPM 账户

### 检查登录状态

```bash
# 查看当前登录用户
npm whoami
```

### 如果未登录，执行登录

```bash
npm login
```

### 提示输入信息

```
Username: your-username
Password: your-password
Email: (your-public-email)
OTP: xxxxxx  # 如果启用了双因素认证
```

### 使用身份验证令牌登录（推荐）

1. **创建令牌**：

   ```bash
   npm token create
   ```

2. **选择权限**：

   - `Read-only`：只读
   - `Automation`：自动化
   - `Full control`：完全控制

3. **使用令牌登录**：
   ```bash
   npm login --registry https://registry.npmjs.org/
   # 输入用户名和邮箱，密码使用生成的令牌
   ```

### 注册 NPM 账户

如果还没有 NPM 账户，请访问 [https://www.npmjs.com](https://www.npmjs.com)：

1. 点击 "Sign Up" 注册
2. 验证邮箱地址
3. 完成注册流程

> 🔐 **安全最佳实践**：
>
> - 使用身份验证令牌而非密码
> - 不要在代码中硬编码令牌
> - 定期轮换访问令牌

---

## 第六步：发布项目

### 1. 构建项目

```bash
# 执行构建
npm run build
```

### 2. 构建输出说明

成功构建后，`dist` 目录会包含：

```
dist/
├── index.cjs.js     # CommonJS 格式
├── index.js         # ES Module 格式
├── index.d.ts       # TypeScript 类型声明
├── index.iife.js    # UMD 格式（可选）
└── style.css        # 提取的 CSS（如果配置了）
```

### 3. 本地测试（可选）

在发布前，可以本地测试：

```bash
# 链接本地包
npm link

# 在其他项目中测试
cd other-project
npm link your-package-name
```

### 4. 发布到 NPM

```bash
# 发布包
npm publish
```

### 5. 发布结果

**成功标志**：

```
+ your-package-name@1.0.0
```

### 6. 包名冲突处理

如果报错 `403 Forbidden - PUT https://registry.npmjs.org/...`：

**解决方案**：

1. **更换包名**：

   ```json
   {
     "name": "your-new-package-name"
   }
   ```

2. **查看包名是否可用**：

   ```bash
   npm view your-package-name
   ```

3. **如果是组织包**：
   - 确保有发布权限
   - 使用 `@org/package` 格式

### 7. 更新版本

发布新版本时，需要遵循语义化版本规范：

```bash
# 方法一：使用 npm version 命令
npm version patch  # 1.0.0 -> 1.0.1
npm version minor  # 1.0.0 -> 1.1.0
npm version major  # 1.0.0 -> 2.0.0

# 方法二：手动修改 package.json
# 1. 编辑 version 字段
# 2. 重新构建
npm run build
# 3. 重新发布
npm publish
```

### 版本号规范

- **主版本号** (major)：不兼容的 API 修改
- **次版本号** (minor)：向下兼容的功能性新增
- **修订号** (patch)：向下兼容的问题修正

### 8. 标记版本（可选）

```bash
# 添加 git 标签
git tag v1.0.0
git push origin v1.0.0
```

### 9. 私有包设置

如果不想公开包：

```json
{
  "private": true,
  "publishConfig": {
    "access": "restricted"
  }
}
```

### 10. 验证发布

发布完成后，访问：

- NPM 包页面：`https://www.npmjs.com/package/your-package-name`
- GitHub 仓库（如果有）

---

## 常见问题解决

### Q1: 权限被拒绝 (403)

**错误信息**：

```
403 Forbidden - PUT https://registry.npmjs.org/... - You do not have permission
```

**解决方案**：

- 检查登录状态：`npm whoami`
- 检查包名是否已存在
- 如果是组织包，检查权限
- 清除缓存：`npm cache clean --force`

### Q2: 找不到模块

**错误信息**：

```
Cannot find module 'vue'
```

**解决方案**：

- 将 Vue 放在 `peerDependencies` 中
- 更新 tsup 配置：`external: ['vue']`
- 确保使用者安装了 Vue：`npm install vue`

### Q3: tsup 构建失败

**错误信息**：

```
Error: Missing files
```

**解决方案**：

- 检查入口文件路径：`entry: ['src/index.ts']`
- 确保入口文件存在
- 检查文件扩展名

### Q4: 类型声明未生成

**解决方案**：

- 确保 `tsup.config.ts` 中设置 `dts: true`
- 正确配置 TypeScript 路径别名
- 检查 Vue 组件的类型定义

### Q5: CSS 未提取

**解决方案**：

- 在 `tsup.config.ts` 中添加 `css: 'extract'`
- 或使用 `css: true` 内联 CSS
- 确保正确导入 CSS：`import './style.css'`

### Q6: 构建产物过大

**解决方案**：

- 启用 `treeshake: true`
- 配置 `external` 排除外部依赖
- 使用 `minify: 'esbuild'` 压缩
- 避免引入大型依赖

---

## ✅ 发布前检查清单

在发布前，确保以下项目已完成：

- [ ] 项目名称唯一且有描述性
- [ ] `package.json` 配置完整（name, version, exports, files）
- [ ] `tsup.config.ts` 配置正确
- [ ] `tsconfig.json` 配置完整
- [ ] Vue 已放在 `peerDependencies` 中
- [ ] TypeScript 编译无错误：`npm run build`
- [ ] 类型检查通过：`npm run type-check`
- [ ] 构建产物生成成功（检查 dist 目录）
- [ ] 已登录正确的 NPM 账户
- [ ] 版本号遵循语义化版本规范
- [ ] README.md 文件完整且有示例
- [ ] 设置了 `peerDependencies` 版本范围
- [ ] 清理了调试代码和 console.log

---

## 📚 参考资源

- [tsup 官方文档](https://tsup.egoist.dev/)
- [Vite 官方文档](https://vitejs.dev/)
- [Vue 3 官方文档](https://cn.vuejs.org/)
- [NPM 发布指南](https://docs.npmjs.com/packages-and-modules/contributing-packages-to-the-registry)
- [语义化版本规范](https://semver.org/lang/zh-CN/)
- [tsup 配置参考](https://tsup.egoist.dev/#configuration)

---

## 🎉 完成

恭喜！你已经学会了如何：

- ✅ 使用 Vite 创建 Vue 3 项目
- ✅ 配置 tsup 进行高性能打包
- ✅ 配置 package.json 和 tsconfig.json
- ✅ 发布包到 NPM
- ✅ 管理和更新版本

### 使用你的包

在其他项目中安装使用：

```bash
npm install your-package-name
```

在 Vue 项目中使用：

```typescript
import { yourComponent } from 'your-package-name'
import 'your-package-name/style.css' // 如果有样式文件
```

### 最佳实践

1. **保持更新**：定期更新依赖和修复问题
2. **版本管理**：遵循语义化版本规范
3. **文档完善**：提供清晰的 README 和示例
4. **类型安全**：确保类型声明正确
5. **测试充分**：添加单元测试和集成测试

祝你在开源道路上越走越远！🚀

---

> 💝 如果本指南对你有帮助，欢迎 Star 支持！
