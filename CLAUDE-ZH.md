# CLAUDE-ZH.md

本文档为 Claude Code (claude.ai/code) 在此仓库中工作提供指导。

## 常用命令

```bash
pnpm install                     # 安装依赖
pnpm run dev                     # 启动开发服务器 localhost:3000
pnpm run build                   # 生产构建（执行 prisma generate + next build）
pnpm run start                   # 启动生产服务器
pnpm run lint                    # ESLint 检查（next/core-web-vitals + next/typescript）
npx prisma migrate dev --name init  # 初始化数据库表
npx prisma migrate dev           # 应用数据库 schema 变更
npx prisma generate              # 重新生成 Prisma 客户端代码
```

## 项目架构

### 目录结构

- **`app/`** — Next.js App Router 页面和布局
  - `(landing-page)/` — 公开的营销落地页，路由 `/`（包含 Hero、特性、定价等组件）
  - `login/` — 登录页面（Google OAuth）
  - `dashboard/` — 受保护的仪表盘（`(overview)/` 为默认视图）
  - `billing/` — Stripe 订阅管理页面
  - `account/` — 用户账户页面
  - `api/[...nextauth]/` — Auth.js 路由处理器
  - `api/payment/checkout_sessions/` — Stripe 结账会话创建
  - `api/payment/webhook/` — Stripe webhook 处理
  - `api/impersonate/` — 管理员模拟登录接口
  - `ui/` — 共享 UI 组件（登录表单、Stripe 订阅按钮、骨架屏、侧边栏导航等）
  - `lib/` — 工具函数和占位数据

- **`domain/`** — Clean Architecture 领域层
  - `user/` — 用户实体、仓库（Prisma 实现）、用例（GetUser、CreateUser、UpdateUser）
  - `company/` — 公司实体、仓库、用例（CreateCompany、RegisterTransaction）
  - 模式：Entity → Port（接口）→ Repository（实现 Port）→ Use-Case（调用 Repository）

- **`infra/`** — 基础设施 / 外部服务封装
  - `prisma.ts` — PrismaClient 单例
  - `stripe.ts` — Stripe.js 客户端加载器
  - `mailgun.ts` — Mailgun 客户端封装（默认使用 EU 端点）
  - `googleAnalytics.tsx` — Google Analytics 脚本组件
  - `googleTagManager.tsx` — GTM 脚本组件
  - `providerDetector.ts` — 运行时检测各服务是否已配置（基于环境变量）；每个封装类在初始化前都会检查 `providersList`

- **`prisma/`** — Prisma schema 和迁移文件。数据模型：User、Company、Account、Session、VerificationToken、PaymentTransaction

### 认证流程

使用 NextAuth v5（beta）+ JWT 会话策略。认证 Provider：Google OAuth + Credentials（用于管理员模拟登录）。用户注册时（`trigger === 'signUp'`），`auth.config.ts` 的回调会自动为新用户创建 Company。`middleware.ts` 保护除 `/`（落地页）、`/api/*` 和静态资源外的所有路由——未认证请求会重定向到 `/login`。管理员模拟登录：设置 `NEXT_PUBLIC_ADMIN_USER_ID` 环境变量后，管理员可通过 `/api/impersonate` 以任意用户身份登录。

### 支付流程

1. 客户端：`<SubscribeComponent>` 加载 Stripe.js，然后向 `/api/payment/checkout_sessions` POST 请求传递 `priceId`
2. 结账 API 路由创建包含用户/公司元数据的 Stripe Checkout Session
3. 支付成功或取消后，用户通过查询参数重定向回 `/billing`
4. Stripe webhook（`/api/payment/webhook`）监听 `checkout.session.completed` 事件，通过 `RegisterTransaction` 用例记录交易

### 路径别名

`@/*` 映射到项目根目录（在 `tsconfig.json` 中通过 baseUrl + paths 配置）。

### 环境变量

将 `.env.example` 复制为 `.env` 并填写必要变量：`AUTH_SECRET`、`DATABASE_URL`、`AUTH_GOOGLE_ID`、`AUTH_GOOGLE_SECRET`、Stripe 密钥、Google Analytics ID、Mailgun API Key、Google Tag Manager ID，以及用于模拟登录的 `NEXT_PUBLIC_ADMIN_USER_ID`。
