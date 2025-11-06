# 前端项目创建与 NPM 发布精简教程

本教程将带你从零开始创建一个前端项目，并将其发布到 NPM 仓库。

## 第一步：创建项目

### 使用 Vite 创建项目

Vite 是一个现代化的前端构建工具，它提供了极快的开发体验。

```bash
# 创建项目
npm create vite@latest

# 或者使用 yarn
# yarn create vite
```

### 填写项目信息

执行命令后，会出现以下提示：

```
✔ Project name: ... (输入项目名称，例如：my-awesome-project)
✔ Select a framework: › - Use arrow-keys to return to the options and press Enter to select
  - Vanilla
  - Vue
  - React
  - Preact
  - Lit
  - Svelte
```

1. **输入项目名称**：输入你想要的英文项目名称（使用短横线命名法）
2. **选择框架**：选择 `Vue` 或 `React`，推荐使用 `Vue 3`

> 💡 **提示**：项目名称将成为你的 NPM 包名，确保名称唯一且有意义

### 进入项目目录并安装依赖

```bash
cd your-project-name
npm install
```

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

---

## 第三步：配置 package.json

### 配置 package.json 文件

在 `package.json` 中添加或修改以下配置：

```json
{
  "name": "your-package-name", // 包名
  "version": "1.0.0", // 版本号
  "description": "你的项目描述", // 项目描述
  "type": "module", // 指定模块类型
  "main": "./dist/index.js", // 入口文件
  "module": "./dist/index.js", // 模块文件
  "types": "./dist/index.d.ts", // 类型定义文件
  "files": ["dist"], // 发布时包含的文件
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "type-check": "vue-tsc --noEmit",
    // 使用tsup 构建脚本
    "ts-build": "tsup src/index.ts --format cjs,esm --dts --minify --tsconfig tsconfig.app.json"
  },
  "keywords": ["vue", "typescript", "library"], // 项目关键词
  "author": "你的姓名 <your@email.com>", // 作者信息
  "license": "MIT", // 许可证
  "repository": {
    // 仓库信息
    "type": "git",
    "url": "https://github.com/yourusername/your-repo.git"
  }
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

## 第四步：配置 tsup （可选）

### 创建基础配置

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
| ----------- | ------------------ | ------------------------ |
| `entry`     | 入口文件           | `['src/index.ts']`       |
| `outDir`    | 输出目录           | `'dist'`                 |
| `dts`       | 生成类型声明       | `true`                   |
| `sourcemap` | 生成 source map    | `true`                   |
| `clean`     | 构建前清理输出目录 | `true`                   |
| `minify`    | 压缩方式           | `'esbuild'` 或 `false`   |
| `splitting` | 代码分割           | `true` 或 `false`        |
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
```

> 🔐 **安全提示**：
>
> - 建议使用 NPM 身份验证令牌而不是密码
> - 可以使用 `npm token create` 创建令牌

### 注册 NPM 账户

如果还没有 NPM 账户，请访问 [https://www.npmjs.com](https://www.npmjs.com) 注册：

1. 点击 "Sign Up"
2. 输入用户名、密码和邮箱
3. 验证邮箱地址
4. 完成注册

---

## 第六步：发布项目

### 1. 构建项目

```bash
# 构建项目
npm run build

# 或者构建库
npm run build:lib
```

### 2. 发布到 NPM

```bash
# 发布
npm publish
```

### 3. 首次发布

如果是首次发布包名，包名可能是新的，会直接成功。

### 4. 遇到包名冲突

如果报错 `403 Forbidden - PUT https://registry.npmjs.org/... - Package name already exists`：

**解决方案**：

1. **更换包名**：在 `package.json` 中修改 `name` 字段
2. **认领包名**：如果你认为这个包名应该属于你，联系 NPM 支持

## ⚖️ 作用域和非作用域对比总结

| 特性             | 非作用域包                    | 作用域包                                                          |
| ---------------- | ----------------------------- | ----------------------------------------------------------------- |
| **包名格式**     | `package-name`                | `@org/package-name`                                               |
| **命名空间**     | 全局唯一                      | 组织/用户内部唯一                                                 |
| **冲突风险**     | 高                            | 低                                                                |
| **发布权限**     | 任何人都可发布                | 需要组织权限                                                      |
| **私有包**       | ❌ 不支持                     | ✅ 支持（付费）                                                   |
| **发布命令**     | `npm publish`                 | `npm publish --access public` / `npm publish --access restricted` |
| **private 属性** | 不可发布为私有                | ✅ 可以设置私有                                                   |
| **安装方式**     | `npm install package`         | `npm install @org/package`                                        |
| **使用示例**     | `import lodash from 'lodash'` | `import component from '@org/component'`                          |

---

### 5. 更新版本

发布新版本时，需要更新版本号：

```bash
# 1. 更新 package.json 中的版本号
# 或使用命令自动更新

# 补丁更新 (1.0.0 -> 1.0.1)
npm version patch

# 次要更新 (1.0.0 -> 1.1.0)
npm version minor

# 主要更新 (1.0.0 -> 2.0.0)
npm version major

# 2. 重新构建
npm run build

# 3. 重新发布
npm publish
```

### 6. 设置包为私有

如果不想公开包，设置 `private: true`：

```json
{
  "private": true
}
```

> 🚀 **发布成功标志**：控制台会显示类似：
>
> ```
> + your-package-name@1.0.0
> ```

---

## 常见问题解决

### Q1: 权限被拒绝 (403)

**错误信息**：

```
403 Forbidden - PUT https://registry.npmjs.org/... - You do not have permission to publish "xxx"
```

**解决方案**：

- 检查是否已登录正确的账户：`npm whoami`
- 检查包名是否已被使用
- 如果是组织包，可能需要权限

### Q2: 找不到模块

**错误信息**：

```
Cannot find module 'xxx'
```

**解决方案**：

- 确保所有依赖已安装：`npm install`
- 检查 `tsconfig.json` 中的路径配置
- 检查 `package.json` 中的 `files` 字段

### Q3: TypeScript 编译错误

**错误信息**：

```
Type error: 'xxx' is not assignable to type 'yyy'
```

**解决方案**：

- 检查类型定义
- 修复 `strict` 模式下的类型问题
- 使用类型断言或 `any` 类型（不推荐）

### Q4: 构建产物为空

**解决方案**：

- 检查 `vite.config.ts` 配置
- 确保入口文件存在
- 检查 `package.json` 中的 `files` 配置

---

## 🎉 完成

恭喜！你已经学会了如何创建项目并发布到 NPM。现在你可以：

- ✨ 创建自己的 NPM 包
- 🔄 维护和更新版本
- 📦 在其他项目中引用你的包

**引用方式**：

```bash
npm install your-package-name
```

祝你在开源路上越走越远！🚀

---

> 💝 如果本教程对你有帮助，欢迎给个 Star！
