# Team Agent 协作项目

## 项目概述

本项目使用 4 个 Claude Code Agent 协作开发，角色分别为：产品经理(PM)、开发者(Dev)、测试工程师(QA)、E2E 测试工程师(E2E)。各角色的详细职责见 `.claude/skills/team/` 下的对应文件。

## 目录所有权（严格遵守）

| 角色 | 可写目录 | 可读目录 |
|------|---------|---------|
| PM   | `pm/` | 全部 |
| Dev  | `dev/` | 全部 |
| QA   | `qa/tests/components/`、`qa/tests/api/`、`qa/tests/lib/`、`qa/tests/setup.ts`、`qa/reports/` | 全部 |
| E2E  | `qa/tests/e2e/`、`qa/reports/` | 全部 |

禁止写入其他角色的目录，修改意见通过消息通知对方。

## 协作协议

### 阶段流转

```
需求阶段 → 设计阶段 → 编码阶段 → 测试阶段（并行） → 验收阶段
   PM        Dev        Dev      QA + E2E          PM
```

### 消息格式

```
[类型] 需求就绪 | 设计提交 | 代码完成 | Bug报告 | Bug修复 | 验收请求 | 验收通过 | 验收驳回 | 需求澄清 | E2E Bug报告 | E2E通过
[关联] REQ-XXX
[内容] 具体说明
[文件] 相关文件路径（可选）
```

### 循环终止条件

1. QA 单元测试全部通过 + E2E 测试全部通过
2. PM 验收通过（需 QA 和 E2E 两方都通过才可验收）
3. PM 发出 broadcast 关闭任务

超过 3 轮 Bug修复-回归 仍未通过，QA 或 E2E 应 message PM 请求介入决策。

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
