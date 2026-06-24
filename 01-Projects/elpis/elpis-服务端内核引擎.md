---
type: project
status: doing
area: 项目
review: weekly
created: 2026-06-17
updated: 2026-06-23
---

# elpis 服务端内核引擎

> 项目：elpis 服务端内核（Node.js + Koa）
> 来源：整合自 Second Brain（开发笔记）与 Write Your Future（系列博客开篇）
> 整合日期：2026-06-17

---

## 一、为什么先做服务端内核引擎

在正式开发业务能力之前，服务端需要先有一个稳定的基础层。这个基础层是项目的"内核引擎"，负责承载后续的路由、插件、业务模块、配置、日志、错误处理等能力。

如果一开始直接堆业务代码，短期能跑起来，但模块变多后容易出现结构混乱、职责不清、扩展困难的问题。

因此第一步先把服务端启动流程和核心结构搭起来，让后续功能都基于同一套规则接入。

## 二、内核引擎的职责边界

内核引擎不直接包含业务逻辑，更像一个基础运行容器，负责：

- 应用启动：统一创建和启动服务。
- 配置管理：端口、环境变量、运行模式等。
- 中间件注册：统一加载日志、错误处理、跨域、解析器等。
- 路由挂载：为业务模块提供统一入口。
- 异常处理：集中处理接口错误，保证返回格式一致。
- 扩展机制：为模块化 / 插件化能力预留空间。

## 三、技术选型

- **Node.js**：轻量服务端，生态成熟，和前端技术栈更易统一。
- **Koa**：中间件模型清晰，框架本身较轻、不强绑定目录结构，适合逐步封装启动流程。
- **ghooks**：用于 Git 提交阶段的规范校验（`commit-msg` 校验 + `pre-commit` 跑 lint）。

```javascript
// package.json
"config": {
  "ghooks": {
    "commit-msg": "validate-commit-msg",
    "pre-commit": "npm run lint"
  }
}
```

## 四、项目初始化结构

最小可运行结构：

```text
elpis-server
├── package.json
├── src
│   ├── app.js              # 应用入口，启动服务
│   ├── core
│   │   └── engine.js       # 内核引擎核心：创建 Koa 实例、注册中间件、挂载路由
│   ├── config
│   │   └── index.js        # 基础配置管理
│   ├── middleware          # 统一存放中间件
│   └── routes
│       └── index.js        # 路由入口
└── README.md
```

启动流程收敛成一个 `Engine` 类：

```javascript
class Engine {
  constructor(options = {}) {
    this.options = options;
    this.app = null;
  }

  init() {
    this.createApp();        // 创建 Koa 实例
    this.registerMiddleware(); // 注册全局中间件
    this.registerRoutes();   // 挂载路由
    return this;
  }

  listen(port) {
    // 启动服务
  }
}
```

应用启动流程被收敛到内核中，后续增加日志、错误处理或业务模块，都围绕这个核心对象扩展。

---

## 五、Loader 机制（核心）

### 作用

项目启动时，把约定目录的文件加载到 Koa `app` 实例上。避免手动维护大量 `middleware`、`router`、`service` 等入口文件。

> loader 的核心思想：把重复工作变成约定式加载方式。

### 两类 Loader

**1. 对象树型**：把文件系统目录映射成 `app` 上的对象树。
```
app.controller.user.info
app.service.user.info
app.middlewares.auth.jwt
```

核心思想 —— **把文件系统映射成 JS 对象树**：
- `glob` 扫描文件
- `path.relative` 获取路径
- `split` 切割层级
- 引用类型动态构建对象树

最终形成 `app.middlewares.xxx.xxx`。

**2. 注册 / 合并型**：不一定形成对象树，把文件内容注册到某个运行机制里。
```
app.use(router.routes())
app.config = Object.assign({}, defaultConfig, envConfig)
```

### 加载顺序

`elpic-core/index.js` 按顺序加载：

```text
middleware -> router-schema -> controller -> service -> config -> extend -> router
```

前面的 loader 把能力挂到 `app` 上，最后 `router-loader` 再注册路由。这样路由文件里就可以使用已准备好的 `app.middlewares`、`app.controller`、`app.service`、`app.config` 等能力。

**执行流程**：
1. 初始化 Koa 实例
2. 初始化基础路径（`process.cwd()`）、业务路径
3. 初始化环境配置（生产 / 开发）
4. 按上面顺序加载各 loader

### 对象树构建关键

```javascript
temp = temp[names[i]]
```

这不是复制对象，而是让指针进入对象树下一层，最终始终在同一棵对象树上写入数据。步骤：
1. `glob` 扫描约定目录下的 `.js` 文件
2. `path` 截取相对路径并去掉后缀
3. `_` / `-` 转驼峰命名
4. `split(sep)` 拆层级
5. 临时引用指针逐层创建对象
6. 最后一层挂载模块结果

### 7 个 Loader 对照表

| Loader | 输入 | 输出 | 实现关键 |
|---|---|---|---|
| middleware | `app/middlewares` | `app.middlewares` | 扫描目录，构建对象树，挂载 `require(file)(app)` |
| controller | `app/controller` | `app.controller` | 类似 middleware，但最后 `new ControllerModule()` |
| service | `app/service` | `app.service` | 类似 middleware，直接挂载 `require(file)(app)` |
| router-schema | `app/router-schema` | `app.routerSchema` | 扫描 schema 文件，对象展开合并 |
| config | `config` | `app.config` | 读 default 配置，再按环境读 local/beta/prod 覆盖 |
| extend | `app/extend` | `app[name]` / `app.extend` | 扫描扩展文件，避免和 app 已有 key 冲突后挂载 |
| router | `app/router` | Koa 路由中间件 | 创建 `KoaRouter`，加载路由，`app.use` 注册 |

### 各 Loader 区别要点

- `middleware / controller / service` 共性是"文件系统 → JS 对象树"。
- `controller` 特殊点：最后挂载**实例**，不是函数返回值。
- `service / middleware` 接近：执行模块工厂函数后挂载返回值。
- `router-schema` 合并多个规则对象，不关心目录层级。
- `config` 是默认配置 + 环境配置覆盖。
- `router` 必须靠后加载，依赖前面 loader 准备好的能力。

---

## 六、开发完成总结（elpic-core）

- 完成日期：2026-05-31
- 提交：`82ad389 feat: complete elpic core loaders`

### 本次完成

- 补齐 `config / controller / service / extend / router` loader 基础实现。
- 整理 `middleware`、`router-schema` loader，纳入统一启动流程。
- 启动入口按序加载：middleware → router-schema → controller → service → config → extend → 全局 middleware → router。
- 增加项目级 `AGENTS.md`，统一 Prettier 配置和代码格式。

### 提交前修正的问题

- 修复 `.prettierrc` 末尾逗号，确保 JSON 可解析。
- 修复 router 兜底跳转，避免跳转到错误的模板字符串文本。
- 统一启动参数为 `homePage`，router 使用同一字段。
- 修复默认 local 环境下 `config.local.js` 不加载的问题。
- 修复 `app.middleware` 日志字段应为 `app.middlewares`。
- 修复 router 文件扫描 glob 表达式。

### 验证

- `node --check`：通过
- `.prettierrc` / `package.json` JSON 解析：通过
- `npm run lint`：通过
- `git diff --check`：通过

---

## 七、后续注意

- `controller / service / middleware` 对象树构建逻辑重复，后续可抽成公共工具。
- `config` 当前是浅合并，嵌套配置以后可能需要深合并。
- 当前没有真正测试用例，建议补一组最小启动和 loader 单测。
- `router-loader` 路径字段注意是否统一使用 `app.bussinessPath`。
- 全局 `app/middleware.js` 加载用 try/catch 兜底，后续可细化"文件不存在"和"文件内部报错"的区分。

---

## 知识关联

- 相关知识：[[Node.js Loader 机制]]
