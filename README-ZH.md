# Next.js Boilerplate — 独立开发者专用

一个 Next.js 启动套件，帮助你快速实现创意，`1 小时开始赚钱`。

![Landing Page](public/landingFeatures.png)

### 主要功能

- ☀️ 免费
- 👁️ [落地页](https://nextjsboilerplate-blue.vercel.app/)
- 🔑 [Google SSO](https://nextjsboilerplate-blue.vercel.app/login) (NextAuth)
- 💰 Stripe 支付
- 📂 Postgres + Prisma
- 📈 Google Analytics
- 📱 响应式设计
- 📧 Mailgun
- 📦 SEO ⏳
- 📝 博客 ⏳
- 📚 文档 ⏳
- 🔑 客服：用户模拟登录 (impersonation)
- 🫂 客服（Chatwoot 或 Chaty）⏳
- 🍾 推荐计划 ⏳
- 🛠️ 可定制

⏳: 即将推出

### 为什么选择这个 Next.js 启动套件

作为独立开发者，你需要专注于产品本身，而不是花时间配置 Stripe、数据库、认证等繁琐的集成工作。这个 Next.js 启动套件旨在帮助你跳过这些步骤，在一天内构建并发布你的产品。

根据该项目灵感来源 Marc Lou 的计算，这套方案可节省 **22 小时**：

- 4 小时设置邮件
- 6 小时设计落地页
- 4 小时处理 Stripe webhook
- 2 小时配置 SEO 标签
- 1 小时申请 Google OAuth
- 3 小时配置 DNS 记录
- 2 小时编写受保护的 API 路由
- ∞ 小时调研...

### 定价

本套件永久 **免费**。

如果你想支持该项目，可以[请我喝杯咖啡 ☕️](https://buymeacoffee.com/guillim)。

**服务商费用**

运行该项目应该花费 `0 美元`。我们的理念是：你可以在不破产的情况下测试 10 个产品！

因此我们只选择了具有良好免费套餐的服务商（详见下方[技术栈](#技术栈)）。

## Star 历史

[![Star History Chart](https://api.star-history.com/svg?repos=guillim/nextjs-boilerplate&type=Date)](https://www.star-history.com/#guillim/nextjs-boilerplate&Date)

## 快速开始

配置环境变量：
_将 [.env.example](./.env.example) 复制为 [.env](.env) 并填写变量_

#### 开发

安装依赖并启动项目：

```bash
pnpm install
npx prisma migrate dev --name init
pnpm run dev
```

打开 [http://localhost:3000/](http://localhost:3000/) 查看结果。

#### 生产环境

生产环境推荐使用 Vercel（见下文）。

```bash
pnpm run build
pnpm run start
```

## 技术栈

全程使用 TypeScript，Next.js App Router 模式。

- 数据库：PostgreSQL，托管在 [Neon](https://neon.tech/)（可替换）
- 前端：React + TailwindCSS
- 托管：[Vercel](https://vercel.com/)（可替换）
- 自动部署：git push 自动触发

访问 [Vercel](https://vercel.com/signup) 注册账号，选择免费 Hobby 计划。关联 GitHub 账号即可。

- **数据库：Neon**

不选用 Vercel PostgreSQL 因为它不是免费的。Neon 是很好的替代品，而且免费。访问 [Neon](https://neon.tech/) 注册账号，选择免费计划。

使用 [Prisma](https://www.prisma.io/docs/getting-started/setup-prisma/start-from-scratch/relational-databases-typescript-postgresql) 连接数据库，确保类型安全。

更多信息请参阅 [prisma/README.md](prisma/README.md)。

- **落地页**

启动项目后，可在 [localhost:3000](http://localhost:3000/) 访问落地页。
自定义落地页请编辑 `(landing-page)` 目录。

![Landing Page](public/landing.png)

- **认证**

已集成 Google Auth。使用前请参考此[指南](https://authjs.dev/getting-started/authentication/oauth)在 [Google Cloud Platform](https://console.cloud.google.com/apis/credentials) 创建项目。

建议阅读 [NextAuth](https://next-auth.js.org/getting-started/introduction) 了解其他认证方式（Google、Twitter、GitHub...）。

![SSO](public/sso.png)

**模拟登录 (Impersonation)**：为提供更好的客服支持，你可以模拟登录为任意用户。在 **.env** 中设置 `NEXT_PUBLIC_ADMIN_USER_ID=你的用户ID`，然后访问 [/login/impersonate](/login/impersonate) 即可。

- **支付：Stripe**

我们使用 Stripe 处理支付。请在 [Stripe](https://stripe.com/) 注册账号。
为简化设计，Stripe 与公司（Company）关联而非直接与用户关联。每个用户注册时自动创建一家公司。

配置请参考 [Stripe 教程](https://medium.com/@rakeshdhariwal61/integrating-stripe-payment-gateway-in-next-js-14-a-step-by-step-guide-1bd17d164c2c)。测试请使用 [Stripe 测试卡号](https://docs.stripe.com/testing)。

![billing section](public/billing-screenshot.png)

<details>
<summary>一键支付按钮</summary>

集成 Stripe 支付按钮非常简单，只需添加以下代码（修改为正确的 priceId）：

```react
<SubscribeComponent 
        priceId="price_1Q6U4ZP9VWutz4pQA1UC2ilX" 
        price="10" 
        description="基础套餐" />
```

该组件已包含在 [billing](/billing) 页面中。

</details>

<details>
<summary>客户门户</summary>

无需担心发票和订阅管理。Stripe 提供了[客户门户](https://stripe.com/docs/billing/subscriptions/customer-portal)帮你完成这些工作。

用户可以通过邮箱登录，门户地址类似：[https://billing.stripe.com](https://billing.stripe.com/p/session/test_YWNjdF8xR3kxaUZBbHF2S3B4SkN1LF9SOEN5QTN0aGVrNFpWTHExWWNMaW1EWnE5Y29tOE1o0100dW7QNfxX)

客户门户已集成在 [billing](/billing) 页面中。

</details>
<br/>

- **分析：Google Analytics**

我们使用 Google Analytics 追踪用户。请在 [Google Analytics](https://analytics.google.com/) 注册账号，然后在 [.env](./.env) 中添加你的 ID：

```markdown
# Google Analytics
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=G-xxxxxxx
```

- **邮件：Mailgun**

Mailgun 每天提供 100 封免费邮件，这是我们调研中最大的免费方案。在[这里](https://signup.mailgun.com/new/signup?plan_name=dev_free&currency=USD)注册账号。
- 在 [API Key 管理页面](https://app.mailgun.com/settings/api_security) 创建 API Key
- 添加到 [.env](./.env) 文件
- 根据需求调整代码，确保将 'mail.mydomain.com' 替换为你 Mailgun 中配置的域名：

```ts
// 在 pages/api 路由中：
import { mailgunClientGlobal } from '@/infra/mailgun';
const mg = await mailgunClientGlobal
await mg.mailgun?.messages.create(
  'mail.mydomain.com',
  {...mg.getDefaultValues(), 
    from: '兴奋的用户 <mailgun@mail.mydomain.com>',
    to: ['contact@mydomain.uk'] }
);
```

注意：如果你的邮件服务器不在欧洲，可能需要注释掉 [mailgun.ts](./infra/mailgun.ts) 中的 `url: 'https://api.eu.mailgun.net'` 这一行（该设置仅适用于欧盟服务器）。

- **IDE：VSCode**

推荐使用 VSCode 开发本项目。已配置保存时自动 ESLint 修复。

- **代码托管：GitHub**

请参考此[指南](https://help.github.com/en/github/getting-started-with-github/create-a-repo)将代码托管到 GitHub。

### 致谢

本项目基于 Next.js（App Router）启动模板。
定制化指南请参考 Next.js 官网[教程](https://nextjs.org/learn)。

### 许可证

MIT
