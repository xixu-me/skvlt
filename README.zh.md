# skvlt

**_[English](./README.md)_**

这是一个围绕单个文件 [Skills Vault](https://github.com/xixu-me/skills-vault) 的 Agent Skills 清单 [`skvlt.yaml`](./skvlt.yaml) 组织的存储库。

它适合希望获得一份经过审阅、可移植的已批准 skills 的来源与 skills 的名称快照的用户。

## 存储库内容

- [`skvlt.yaml`](./skvlt.yaml)：唯一事实来源的清单文件
- [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md)：面向智能体的清单变更决策策略，用于处理新增、替换、拒绝或需要进一步请人类确认的情况

> [!IMPORTANT]
> 一般情况下，清单变更通过智能体按照策略提出和应用。

## 清单概览

这份清单记录了：

- 已批准的上游 skills 的来源
- 每个来源下包含的 skills 的名称
- `total_sources`、`total_skills`、每个来源的 `count` 等统计字段
- 当前的清单作用域 `global`

精简示例：

```yaml
total_sources: 21
total_skills: 137
scope: "global"

sources:
  "openai/skills":
    count: 27
    skills:
      - "openai-docs"
      - "slides"
      - "sentry"
```

目前，这份清单覆盖了较广的实际开发场景，从通用工程工作流到 AI、云平台、安全、测试、文档、设计、部署与研究型任务都有覆盖。

它尤其适合：

- 面向广泛现代开发场景工作的开发者
- 希望在多个领域共享一份经过审阅的 skills 基线的团队
- 使用 Skills Vault 且不想手动逐个拼装来源的用户

## 配合 Skills Vault 使用

本存储库是 Skills Vault 推荐的一份受维护的清单来源。

推荐工作流：

1. [fork 本存储库](https://github.com/xixu-me/skvlt/fork)并克隆。
2. 通过 Skills Vault 执行恢复流程。

示例：

```bash
git clone https://github.com/xixu-me/skvlt.git
cd skvlt
bunx skvlt restore --all
```

本存储库本身不是独立安装器。它的作用是为 Skills Vault 工具链提供一份持续维护的清单。

> [!IMPORTANT]
> 本存储库只能配合 Skills Vault 使用。不支持脱离 Skills Vault 的独立使用方式，也不将临时手工使用视为正式工作流。

## 格式化

本存储库现在包含一套基于 Prettier 的轻量格式化工具链，用于统一 Markdown 和 YAML 文件格式。

本地常用命令：

```bash
bun install
bun run format:check
bun run format
```

GitHub Actions 工作流：

- `Format Check`：在 `pull_request` 和推送到 `main` 时校验格式
- `Format Fix`：手动触发 `workflow_dispatch` 后，对所选分支执行格式化；只有存在变更时才会自动提交 `chore: apply formatting`

## 变更策略

由于这份清单面向的是全局 skills 集合，而不是单个项目，所以这里对变更采取保守策略。

高层规则包括：

- 优先做小而可审计的变更
- 相比新增来源，更优先在已信任的来源块内增补 skills 条目
- 默认拒绝绑定特定存储库或特定运行时的 skills 条目，除非有明确允许
- 对重叠判断不清晰、会改变流程形态或涉及来源级变更的情况，通常需要进一步请人类确认

完整决策模型见 [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md)。

## 如何通过智能体变更清单

一般情况下，应当通过智能体提出清单变更请求，而不是直接手改 [`skvlt.yaml`](./skvlt.yaml)。

推荐流程：

1. [fork 本存储库](https://github.com/xixu-me/skvlt/fork)并克隆。
2. 使智能体可以访问克隆的存储库，并在其中提出清单变更请求。
3. 说明候选的 skills 条目或来源、希望做什么调整。
4. 智能体自动依据 [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md) 评估请求；如果变更成立，再由它准备补丁。
5. 在接受变更前，先审阅智能体给出的理由和 diff。
6. 提交变更。

如果变更存在歧义、影响面较大，或者涉及来源级调整，智能体通常会先请人类确认，而不是自动落地。

## 许可证

采用 MIT 许可证。详见 [`LICENSE`](./LICENSE)。
