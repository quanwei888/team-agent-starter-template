# Team Agent Starter Template

Claude Code 多 Agent 协作开发的项目模板集合。通过 `/team` 命令一键拉起多角色团队（PM / Dev / QA / E2E），按需求→设计→编码→测试→验收的流程自动协作。

## 模板列表

| 模板 | 团队 | 技术栈 |
|------|------|--------|
| `nextjs_fullstack_template` | PM + Dev + QA + E2E（4 人） | Next.js App Router, shadcn/ui, Prisma, Vitest, Playwright |
| `python_cli_template` | PM + Dev + QA（3 人） | Python 3.11+, Click, SQLite, pytest, ruff |

## 快速开始

### 1. 复制模板到你的项目

```bash
# Next.js 全栈项目
cp -r nextjs_fullstack_template/{.claude,CLAUDE.md} your-project/

# Python CLI 项目
cp -r python_cli_template/{.claude,CLAUDE.md} your-project/
```

### 2. 启动团队

在项目目录下打开 Claude Code，输入：

```
/team 你的需求描述
```

PM 会自动拆分需求、分发给 Dev 编码、QA 测试，最终完成验收。

## 目录结构

每个模板包含：

```
├── .claude/
│   ├── settings.json          # 环境变量与权限配置
│   └── skills/team/
│       ├── SKILL.md           # /team 命令入口（编排角色与任务依赖）
│       ├── pm.md              # PM 角色提示词
│       ├── dev.md             # Dev 角色提示词
│       ├── qa.md              # QA 角色提示词
│       └── e2e.md             # E2E 角色提示词（仅 Next.js 模板）
└── CLAUDE.md                  # 协作规则、目录所有权、消息协议
```

## 前置条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 已安装
- Agent Teams 功能已通过 `settings.json` 中的环境变量启用：
  ```json
  { "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
  ```

## 自定义

- **调整角色提示词**：编辑 `.claude/skills/team/` 下的 `pm.md`、`dev.md`、`qa.md` 等文件
- **修改协作流程**：编辑 `CLAUDE.md` 中的阶段流转和消息协议
- **增减角色**：编辑 `SKILL.md`，添加或移除队友定义和任务依赖
