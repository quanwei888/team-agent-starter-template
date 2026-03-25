# Team Agent Starter Template

Claude Code 多 Agent 协作开发的项目模板集合。通过 `/team` 命令一键拉起多角色团队（PM / Dev / QA / E2E），按需求→设计→编码→测试→验收的流程自动协作。

## 模板列表

| 模板 | 团队 | 技术栈 |
|------|------|--------|
| `nextjs_fullstack_template` | PM + Dev + QA + E2E（4 人） | Next.js App Router, shadcn/ui, Prisma, Vitest, Playwright |
| `python_cli_template` | PM + Dev + QA（3 人） | Python 3.11+, Click, SQLite, pytest, ruff |

## 快速开始

### 1. 复制模板为你的新项目

```bash
# Next.js 全栈项目
cp -r nextjs_fullstack_template your-project-name

# Python CLI 项目
cp -r python_cli_template your-project-name
```

### 2. 启动团队

进入项目目录，打开 Claude Code，输入：

```
/team 你的需求描述
```

PM 会自动拆分需求、分发给 Dev 编码、QA 测试，最终完成验收。
