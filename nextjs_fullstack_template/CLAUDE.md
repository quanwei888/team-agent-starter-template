# Chespark - Team Agent 协作项目

## 项目概述

本项目使用 4 个 Claude Code Agent 协作开发，角色分别为：产品经理(PM)、开发者(Dev)、测试工程师(QA)、E2E 测试工程师(E2E)。

## 目录所有权（严格遵守，避免文件冲突）

| 角色 | 可写目录 | 可读目录 |
|------|---------|---------|
| PM   | `pm/` | 全部 |
| Dev  | `dev/` | 全部 |
| QA   | `qa/tests/components/`、`qa/tests/api/`、`qa/tests/lib/`、`qa/tests/setup.ts`、`qa/reports/` | 全部 |
| E2E  | `qa/tests/e2e/`、`qa/reports/` | 全部 |

**规则：不要写入其他角色的目录。** 如果需要对其他角色的产出提出修改意见，通过消息通知对方，由对方自己修改。

## 协作协议

### 阶段流转

```
需求阶段 → 设计阶段 → 编码阶段 → 测试阶段（并行） → 验收阶段
   PM        Dev        Dev      QA + E2E          PM
```

### 消息驱动的反馈循环

每个角色在以下时机必须主动发消息：

**PM Agent：**
- 需求文档完成后 → message Dev："需求已就绪：pm/requirements/REQ-XXX.md，请开始设计"
- 验收不通过 → message Dev："验收问题：[具体问题]，请修复"
- 验收通过 → broadcast："REQ-XXX 验收通过，任务关闭"

**Dev Agent：**
- 设计方案完成后 → message PM："设计方案已提交：dev/design/REQ-XXX-design.md，请审阅"
- 编码完成后 → 同时 message QA 和 E2E："代码已完成，入口文件：dev/src/XXX，请开始测试"
- Bug 修复后 → message 报告 Bug 的角色（QA 或 E2E）："Bug 已修复：[修复说明]，请回归测试"
- 需要需求澄清时 → message PM："需求疑问：[具体问题]"

**QA Agent：**
- 测试计划完成后 → message Dev："测试计划已就绪：qa/reports/REQ-XXX-plan.md"
- 发现 Bug → message Dev："发现 Bug：[问题描述、复现步骤、期望行为]"
- 单元测试全部通过 → message PM："单元测试全部通过，报告：qa/reports/REQ-XXX-test-report.md，请验收"
- 回归测试通过 → message PM："单元测试回归通过，请验收"

**E2E Agent：**
- 发现 Bug → message Dev："发现 E2E Bug：[问题描述、复现步骤、期望行为]"
- E2E 测试全部通过 → message PM："E2E 测试全部通过，报告：qa/reports/REQ-XXX-e2e-report.md，请验收"
- 回归测试通过 → message PM："E2E 回归通过，请验收"

### 消息格式

发送消息时使用以下结构化格式，方便对方解析：

```
[类型] 需求就绪 | 设计提交 | 代码完成 | Bug报告 | Bug修复 | 验收请求 | 验收通过 | 验收驳回 | 需求澄清 | E2E Bug报告 | E2E通过
[关联] REQ-XXX
[内容] 具体说明
[文件] 相关文件路径（可选）
```

### 循环终止条件

一个需求的生命周期在以下条件下结束：
1. QA 单元测试全部通过
2. E2E 测试全部通过
3. PM 验收通过（需 QA 和 E2E 两方都通过才可验收）
4. PM 发出 broadcast 关闭任务

如果循环超过 3 轮 Bug修复-回归 仍未通过，QA 或 E2E 应 message PM 请求介入决策。

## 文件命名规范

- 需求文档：`pm/requirements/REQ-{序号}-{简短描述}.md`
- 验收记录：`pm/acceptance/REQ-{序号}-acceptance.md`
- 设计文档：`dev/design/REQ-{序号}-design.md`
- 测试计划：`qa/reports/REQ-{序号}-test-plan.md`
- 测试报告：`qa/reports/REQ-{序号}-test-report.md`
- E2E 测试报告：`qa/reports/REQ-{序号}-e2e-report.md`

## 技术栈

- 框架：Next.js (App Router) — 全栈
- UI 组件库：shadcn/ui + Tailwind CSS
- 语言：TypeScript
- ORM：Prisma
- 数据库：默认使用 SQLite（开发/测试零配置），生产环境按需切换为 PostgreSQL 等
- 单元测试：Vitest + Testing Library
- E2E 测试：Playwright
- 包管理：pnpm

## 目录结构

```
├── pm/                    # PM 产出物
│   ├── requirements/      # 需求文档
│   └── acceptance/        # 验收记录
├── dev/                   # Dev 产出物（同时也是 Next.js 项目根目录）
│   ├── package.json
│   ├── design/            # 设计文档
│   ├── prisma/            # Prisma schema & migrations
│   └── src/               # 源代码
│       ├── app/           # App Router 页面 & API Routes
│       │   ├── (routes)/  # 前端页面
│       │   └── api/       # Route Handlers (后端 API)
│       ├── components/    # React 组件
│       │   └── ui/        # shadcn/ui 组件
│       └── lib/           # 工具函数、数据库客户端等
└── qa/                    # QA 产出物
    ├── reports/           # 测试计划 & 测试报告
    └── tests/             # 测试代码
        ├── components/    # 组件测试
        ├── api/           # API Route 测试
        ├── lib/           # 工具函数测试
        └── e2e/           # Playwright E2E/RPA 测试
```

## 质量标准

- Dev：代码必须通过 ESLint；公开组件需有 Props 类型定义；禁止使用 Server Actions，所有数据交互通过 API Routes 完成；只在逻辑不明显处加简单行注释，禁止 JSDoc 等格式化注释；所有可交互元素必须添加 `data-testid` 属性，便于 E2E 测试定位；涉及外部依赖（如第三方 OAuth 登录、外部 API 回调等）时，必须提供测试模式的 bypass 方案（如 `NODE_ENV=test` 时提供 `/api/auth/test-login` 等测试专用端点，直接创建 session 跳过外部流程），确保 E2E 测试不被外部依赖阻塞
- QA：单元测试覆盖率 > 80%，必须包含边界用例；对于有外部依赖的流程（如第三方登录），真实的外部交互逻辑（如 OAuth 回调处理）通过单元测试覆盖
- E2E：每个用户可见的功能流程必须有对应的 Playwright E2E 测试；对于有外部依赖的流程（如第三方登录），使用 Dev 提供的测试 bypass 端点完成 E2E 测试；E2E 测试前需以 test 模式启动 Next.js 服务（`NODE_ENV=test pnpm --prefix dev start` 或 `NODE_ENV=test pnpm --prefix dev dev`），确保测试 bypass 端点可用，Playwright 配置中通过 `webServer` 字段自动管理服务启停
- PM：需求文档必须包含用户故事、验收标准、优先级
