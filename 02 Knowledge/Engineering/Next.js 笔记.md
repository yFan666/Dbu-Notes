# Next.js 笔记

> 来源：Second Brain（00 Inbox/Next.md）
> 整合日期：2026-06-18

---

## 核心定位

**Next.js 不是普通 React 前端框架，它同时包含"前端渲染能力"和"一部分服务端能力"。**

> 但前提是：数据库访问代码必须运行在服务端环境里，不能放到浏览器端组件里。

## 为什么浏览器不能直连数据库

- 数据库账号密码会暴露
- 数据库通常不允许公网浏览器直连
- 浏览器没有稳定的数据库驱动运行环境
- 权限、安全、查询控制都不可控

Next.js 有服务端运行部分：

```text
浏览器 -> 请求页面 -> Next.js 服务端 -> 查询数据库 -> 渲染 HTML/返回数据 -> 浏览器展示
```

## 哪些地方可以连接数据库

**可以访问数据库：**
- Server Components
- Route Handlers
- Server Actions
- 服务端工具函数
- 构建时数据生成逻辑

**不能访问数据库：**
- Client Components
- useEffect 里的浏览器代码
- onClick / onChange 这类浏览器事件代码
- 任何带 `"use client"` 的组件内部直接调用数据库

> 组件顶部写了 `'use client'` 就是客户端组件，运行在浏览器里，不能直接访问数据库。

## Route Handler

Next.js 也可以写 API。App Router 里叫 Route Handlers，用 `route.ts` 定义请求处理函数，支持 GET、POST、PUT、PATCH、DELETE 等方法。

```ts
// app/api/posts/route.ts
import { getAllPosts } from '@/server/post-service'

export async function GET() {
  const posts = await getAllPosts()
  return Response.json(posts)
}
```

这就类似后端接口。所以 Next.js 里有两种读取数据方式：

```text
页面自己在服务端查数据：
Server Component -> Database

提供 API 给客户端调用：
Client Component -> Route Handler -> Database
```

## Next.js 和 NestJS 的关系

Next.js 可以做一部分后端，但它不是 NestJS 那种完整后端框架。

| | Next.js | NestJS |
|---|---|---|
| 适合 | 页面渲染、SEO、前台展示、简单 API、表单提交、登录集成、轻量数据库查询、内容站和中小型全栈应用 | 复杂 API、复杂权限、后台管理、多模块服务、队列任务、定时任务、微服务、大型业务后端、清晰的 Controller/Service/Module 分层 |
