---
name: team
description: "一键创建 PM+Dev+QA 协作团队。使用方式：/team <需求描述> 或 /team init <需求描述>（init 会清空旧数据重新开始）"
---
# Team Agent 协作项目

## 项目概述

本项目使用 3 个 Claude Code Agent 协作开发，角色分别为：产品经理(PM)、开发者(Dev)、测试工程师(QA)。

## 目录所有权（严格遵守，避免文件冲突）

| 角色 | 可写目录 | 可读目录 |
|------|---------|---------|
| PM   | `docs/requirements/`, `docs/acceptance/` | 全部 |
| Dev  | `src/`, `docs/design/` | 全部 |
| QA   | `tests/`, `docs/test-reports/` | 全部 |

**规则：不要写入其他角色的目录。** 如果需要对其他角色的产出提出修改意见，通过消息通知对方，由对方自己修改。

## 协作协议

### 阶段流转

```
需求阶段 → 设计阶段 → 编码阶段 → 测试阶段 → 验收阶段
   PM        Dev        Dev        QA         PM
```

### 消息驱动的反馈循环

每个角色在以下时机必须主动发消息：

**PM Agent：**
- 需求文档完成后 → message Dev："需求已就绪：docs/requirements/REQ-XXX.md，请开始设计"
- 验收不通过 → message Dev："验收问题：[具体问题]，请修复"
- 验收通过 → broadcast："REQ-XXX 验收通过，任务关闭"

**Dev Agent：**
- 设计方案完成后 → message PM："设计方案已提交：docs/design/REQ-XXX-design.md，请审阅"
- 编码完成后 → message QA："代码已完成，入口文件：src/XXX，请开始测试"
- Bug 修复后 → message QA："Bug 已修复：[修复说明]，请回归测试"
- 需要需求澄清时 → message PM："需求疑问：[具体问题]"

**QA Agent：**
- 测试计划完成后 → message Dev："测试计划已就绪：docs/test-reports/REQ-XXX-plan.md"
- 发现 Bug → message Dev："发现 Bug：[问题描述、复现步骤、期望行为]"
- 全部测试通过 → message PM："REQ-XXX 全部测试通过，请验收。报告：docs/test-reports/REQ-XXX-report.md"
- 回归测试通过 → message PM："回归测试通过，请验收"

### 消息格式

发送消息时使用以下结构化格式，方便对方解析：

```
[类型] 需求就绪 | 设计提交 | 代码完成 | Bug报告 | Bug修复 | 验收请求 | 验收通过 | 验收驳回 | 需求澄清
[关联] REQ-XXX
[内容] 具体说明
[文件] 相关文件路径（可选）
```

### 循环终止条件

一个需求的生命周期在以下条件下结束：
1. QA 全部测试通过
2. PM 验收通过
3. PM 发出 broadcast 关闭任务

如果循环超过 3 轮 Bug修复-回归 仍未通过，QA 应 message PM 请求介入决策。

## 文件命名规范

- 需求文档：`docs/requirements/REQ-{序号}-{简短描述}.md`
- 设计文档：`docs/design/REQ-{序号}-design.md`
- 测试计划：`docs/test-reports/REQ-{序号}-test-plan.md`
- 测试报告：`docs/test-reports/REQ-{序号}-test-report.md`
- 验收记录：`docs/acceptance/REQ-{序号}-acceptance.md`

## 技术栈

（根据具体项目填写，以下为示例）
- 语言：TypeScript
- 运行时：Node.js
- 测试框架：Vitest
- 包管理：npm

## 质量标准

- Dev：代码必须通过 lint，函数需有 JSDoc 注释
- QA：单元测试覆盖率 > 80%，必须包含边界用例
- PM：需求文档必须包含用户故事、验收标准、优先级
