# Wuli3 文档站

面向使用者的中文设计与入门文档，按快速入门、核心设计、模块能力、扩展契约、工程交付逐步展开。详细 API 示例仍可在仓库 `guide/` 查阅；本站围绕职责、流程和使用边界重新组织内容。

## 本地运行

需要 Node.js 24 和 npm；文档站不依赖 Java 服务或中间件。

```bash
cd wuli3-docs
npm ci
npm run docs:dev
```

生产构建与预览：

```bash
npm run docs:build
npm run docs:preview
```

静态 HTML 输出到 `build/docs/`。从仓库根目录也可以运行 `./gradlew :wuli3-docs:build` 触发构建；`check` 只做校验，不构建文档站。Gradle 的 `npmCi` 会按锁文件安装依赖。

稳定版 VitePress 1.6.4 的 Vite/esbuild 开发工具链仍有 npm audit 报告，当前没有稳定版范围内的完整修复；本站的 dev/preview 默认只监听 `127.0.0.1`，不要作为对外服务运行。Vercel 仅部署静态产物，不运行该开发服务器。后续跟进 VitePress 稳定版升级，不跨主版本强制覆盖内部 Vite。

## Vercel 发布

文档站发布到 https://wuli3.wulicode.com，由 Vercel 连接 `weiran-tech/wuli3` 仓库，项目根目录为 `wuli3-docs`，使用 Node.js 24。

`.github/workflows/docs-vercel.yml` 仅在上游 `weiran-tech/wuli3` 的 `main` 上收到文档或发布流程变更时部署，也可从 `main` 手动运行。创建或更新 PR、推送其他分支或 fork 的 `main` 均不触发部署。

仓库 Actions Secrets 需要配置拥有 Vercel 项目发布权限的 `VERCEL_TOKEN`。工作流固定团队和项目 ID，拉取生产配置后使用 Vercel CLI 构建并上传产物；Token 不应写入代码或日志。

`vercel.json` 完全关闭 Vercel Git 自动部署，由 GitHub Actions 作为唯一发布入口，避免 fork 的 `main` 命中分支白名单并在 PR 阶段出现部署授权提示。

首次通过 Actions 发布并验证成功后，工作流会断开项目的原生 Git 关联，避免 fork 沿用旧配置再次触发授权。后续发布继续通过固定项目 ID 和 Token 完成，不依赖 Git 关联。

Vercel 使用 `npm ci` 安装依赖、`npm run docs:build` 构建，发布 `build/docs/`。站点使用根路径 `/`，`cleanUrls` 支持直接访问不带 `.html` 的章节地址。GitHub Pages 工作流已移除。

## 内容与图示维护

- 正文在 `src/`；导航与全文搜索配置在 `src/.vitepress/config.ts`。
- 首页与章节优先使用文字、表格、SVG 架构图；适合分阶段解释的流程使用全局 `FlowDiagram` Vue 组件，支持点击和键盘选择。
- 模块页说明默认条件、应用职责和验收方式，代码只保留必要命令、关键配置及扩展点。
- 以当前源码为事实来源；旧 `guide/` 的示例可能落后于实现。不要把草案、接口声明或测试夹具描述为生产能力。
- 构建默认检查站内死链。修改导航和组件后，还需检查移动端、深色主题、搜索和深层章节的直接访问。
