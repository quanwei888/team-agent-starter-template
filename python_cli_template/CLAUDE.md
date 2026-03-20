# Chespark - Team Agent 协作项目

## 项目概述

本项目使用 3 个 Claude Code Agent 协作开发，角色分别为：产品经理(PM)、开发者(Dev)、测试工程师(QA)。PM 负责将用户的大需求拆分为小需求并持续驱动迭代，Dev 负责设计和实现，QA 负责全部测试。

## 目录所有权（严格遵守，避免文件冲突）

| 角色 | 可写目录 | 可读目录 |
|------|---------|---------|
| PM   | `pm/` | 全部 |
| Dev  | `dev/` | 全部 |
| QA   | `qa/` | 全部 |

**规则：不要写入其他角色的目录。** 如果需要对其他角色的产出提出修改意见，通过消息通知对方，由对方自己修改。

## 协作协议

### 阶段流转

```
用户需求 → 需求拆分 → [设计 → 编码 → 测试 → 验收] × N个子需求 → 反思迭代 → 完成
              PM        Dev    Dev     QA     PM               PM
```

PM 是整个流程的驱动者，负责：
1. 将用户的大需求拆分为多个独立的小需求（REQ-001, REQ-002, ...）
2. 逐个分发给 Dev 实现，每个小需求走完 设计→编码→测试→验收 的完整流程
3. 所有小需求完成后，反思整体产出：还有哪些可以完善？遗漏了什么功能？用户体验是否流畅？
4. 如发现改进点，创建新的需求继续分发，直到产品达到满意状态

### 消息驱动的反馈循环

每个角色在以下时机必须主动发消息：

**PM Agent：**
- 需求拆分完成后 → message Dev："需求已就绪：pm/requirements/REQ-XXX.md，请开始设计"（逐个分发，前一个验收通过后再分发下一个）
- 验收不通过 → message Dev："验收问题：[具体问题]，请修复"
- 验收通过 → 检查是否还有待分发的需求，有则继续分发下一个
- 所有需求完成后 → 进入反思阶段，审视整体产出，如有改进点则创建新需求继续分发
- 全部完成 → broadcast："所有需求已完成，项目关闭"

**Dev Agent：**
- 设计方案完成后 → message PM："设计方案已提交：dev/design/REQ-XXX-design.md，请审阅"
- 编码完成后 → message QA："代码已完成，入口文件：dev/src/XXX，请开始测试"
- Bug 修复后 → message QA："Bug 已修复：[修复说明]，请回归测试"
- 需要需求澄清时 → message PM："需求疑问：[具体问题]"

**QA Agent：**
- 测试计划完成后 → message Dev："测试计划已就绪：qa/reports/REQ-XXX-plan.md"
- 发现 Bug → message Dev："发现 Bug：[问题描述、复现步骤、期望行为]"
- 全部测试通过 → message PM："全部测试通过，报告：qa/reports/REQ-XXX-test-report.md，请验收"
- 回归测试通过 → message PM："回归通过，请验收"

### 消息格式

发送消息时使用以下结构化格式，方便对方解析：

```
[类型] 需求就绪 | 设计提交 | 设计通过 | 代码完成 | Bug报告 | Bug修复 | 测试通过 | 验收通过 | 验收驳回 | 需求澄清 | 反思迭代
[关联] REQ-XXX
[内容] 具体说明
[文件] 相关文件路径（可选）
```

### 循环终止条件

单个需求的生命周期在以下条件下结束：
1. QA 全部测试通过
2. PM 验收通过

整个项目在以下条件下结束：
1. 所有拆分的需求都已验收通过
2. PM 反思后认为无需再新增需求
3. PM 发出 broadcast 关闭项目

如果循环超过 3 轮 Bug修复-回归 仍未通过，QA 应 message PM 请求介入决策。

## 文件命名规范

- 需求文档：`pm/requirements/REQ-{序号}-{简短描述}.md`
- 验收记录：`pm/acceptance/REQ-{序号}-acceptance.md`
- 反思记录：`pm/retrospective.md`
- 设计文档：`dev/design/REQ-{序号}-design.md`
- 测试计划：`qa/reports/REQ-{序号}-test-plan.md`
- 测试报告：`qa/reports/REQ-{序号}-test-report.md`

## 技术栈

- 语言：Python 3.11+
- CLI 框架：Click
- 配置管理：TOML (tomllib / tomli)
- 数据存储：SQLite (通过标准库 sqlite3)
- 日志：logging (标准库)
- 单元/集成测试：pytest + pytest-cov + pytest-mock
- E2E 测试：pytest + subprocess（测试真实 CLI 行为）
- 包管理：uv（或 pip + venv）
- 代码质量：ruff (lint + format)
- 类型检查：mypy

## 目录结构

```
├── pm/                    # PM 产出物
│   ├── requirements/      # 需求文档
│   ├── acceptance/        # 验收记录
│   └── retrospective.md   # 反思记录
├── dev/                   # Dev 产出物（同时也是 Python 项目根目录）
│   ├── pyproject.toml     # 项目配置 & 依赖
│   ├── design/            # 设计文档
│   └── src/               # 源代码
│       ├── __init__.py
│       ├── cli.py          # CLI 入口（Click group）
│       ├── commands/       # 子命令模块
│       │   ├── __init__.py
│       │   └── ...
│       ├── lib/            # 工具函数、数据库操作等
│       │   ├── __init__.py
│       │   ├── db.py       # SQLite 数据库操作
│       │   ├── config.py   # 配置加载
│       │   └── ...
│       └── models/         # 数据模型 (dataclass / Pydantic)
│           ├── __init__.py
│           └── ...
└── qa/                    # QA 产出物
    ├── reports/           # 测试计划 & 测试报告
    └── tests/             # 测试代码
        ├── conftest.py    # pytest 全局 fixtures
        ├── unit/          # 单元测试
        │   └── ...
        ├── integration/   # 集成测试（模块间交互）
        │   └── ...
        └── e2e/           # E2E 测试（完整 CLI 流程）
            └── ...
```

## 质量标准

- PM：需求文档必须包含用户故事、验收标准、优先级；拆分的需求粒度要小到单个 Dev+QA 循环可以完成；反思阶段要从用户体验、功能完整性、边界处理三个角度审视
- Dev：代码必须通过 ruff 检查（lint + format）；公开函数需有类型注解；只在逻辑不明显处加简单行注释，禁止冗余的 docstring；涉及外部依赖（如第三方 API、网络请求等）时，必须提供测试模式的 bypass 方案（如环境变量 `APP_ENV=test` 时使用 mock 数据或本地替代），确保 E2E 测试不被外部依赖阻塞
- QA：单元测试覆盖率 > 80%，必须包含边界用例；E2E 测试覆盖每个用户可用的 CLI 命令/流程；E2E 测试通过 `subprocess.run` 调用真实 CLI 命令，验证退出码、stdout/stderr 输出和副作用（文件、数据库变更等）；对于有外部依赖的流程，使用 `APP_ENV=test` 环境变量启用 bypass 模式
