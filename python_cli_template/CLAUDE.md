# Team Agent 协作项目

## 项目概述

本项目使用 3 个 Claude Code Agent 协作开发，角色分别为：产品经理(PM)、开发者(Dev)、测试工程师(QA)。各角色的详细职责见 `.claude/skills/team/` 下的对应文件。

## 目录所有权（严格遵守）

| 角色 | 可写目录 | 可读目录 |
|------|---------|---------|
| PM   | `pm/` | 全部 |
| Dev  | `dev/` | 全部 |
| QA   | `qa/` | 全部 |

禁止写入其他角色的目录，修改意见通过消息通知对方。

## 协作协议

### 阶段流转

```
用户需求 → 需求拆分 → [设计 → 编码 → 测试 → 验收] × N个子需求 → 反思迭代 → 完成
              PM        Dev    Dev     QA     PM               PM
```

### 消息格式

```
[类型] 需求就绪 | 设计提交 | 设计通过 | 代码完成 | Bug报告 | Bug修复 | 测试通过 | 验收通过 | 验收驳回 | 需求澄清 | 反思迭代
[关联] REQ-XXX
[内容] 具体说明
[文件] 相关文件路径（可选）
```

### 循环终止条件

单个需求：QA 全部测试通过 + PM 验收通过。

整个项目：所有需求验收通过 + PM 反思后无新增需求 + PM broadcast 关闭项目。

超过 3 轮 Bug修复-回归 仍未通过，QA 应 message PM 请求介入决策。

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
│       ├── lib/            # 工具函数、数据库操作等
│       └── models/         # 数据模型 (dataclass / Pydantic)
└── qa/                    # QA 产出物
    ├── reports/           # 测试计划 & 测试报告
    └── tests/             # 测试代码
        ├── conftest.py    # pytest 全局 fixtures
        ├── unit/          # 单元测试
        ├── integration/   # 集成测试（模块间交互）
        └── e2e/           # E2E 测试（完整 CLI 流程）
```
