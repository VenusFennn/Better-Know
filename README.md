# Better Know · Solution Design

Better Know 是一个同时支持 Codex 和 Claude Code 的只读解决方案设计插件。它把前端、后端和跨端协调能力作为三个独立 Skill 打包，帮助模型在实施前基于事实、契约、上下游影响、SSOT、故障反证和测试映射形成可实施、可验证且风险清晰的完整方案。

插件只输出解决方案，不修改源码、测试、配置、数据或外部系统状态，也不执行安装依赖、数据库迁移、提交、发布或部署。

## 包含的技能

| Skill | 适用场景 | Codex 调用 | Claude Code 调用 |
|---|---|---|---|
| `frontend-solution-design` | 以前端为主要变更对象的 Bug、新功能、修改和优化 | `$frontend-solution-design` | `/better-know:frontend-solution-design` |
| `backend-solution-design` | 以后端为主要变更对象的 Bug、新功能、修改和优化 | `$backend-solution-design` | `/better-know:backend-solution-design` |
| `fullstack-solution-design` | 同时涉及前端和后端，需要统一契约、发布顺序和联合验证 | `$fullstack-solution-design` | `/better-know:fullstack-solution-design` |

纯前端和纯后端任务分别使用对应领域技能。跨端任务由 `fullstack-solution-design` 建立共享事实基线，组织两个领域技能独立分析，再收敛跨端契约、职责、混合版本、发布、回滚和测试，不直接拼接两份子方案。

## 核心方法

- 固定五步框架，但根据任务风险调整分析深度；
- 双向扫描上游生产者、中间转换、状态、副作用和下游消费者；
- 区分已验证事实、推断、工作假设和业务歧义；
- 明确领域不变量、外部契约、权威来源和派生表示；
- 前置定义约束、非目标和可验证验收标准；
- 建立“需求 → 方案 → 验收 → 测试”覆盖关系；
- 通过边界、故障、兼容和混合版本推演主动推翻方案；
- 只输出一个明确推荐，不把相反方案留给实施者临场选择。

## 输出格式与动态范围

三个 Skill 都固定保留七个顶层章节：

1. 结论；
2. 事实与根因；
3. 双向影响面；
4. 约束、非目标和验收标准；
5. 详细解决方案；
6. 测试与验证方案；
7. 风险与待确认事项。

二级及以下内容根据实际任务动态调整：

- 纯前端任务不会生成后端影响、后端方案、后端测试、数据迁移或混合版本小节；
- 纯后端任务不会生成前端影响、前端方案或前端测试小节；
- 真正跨端任务才展开前端方案、后端方案、跨端契约、联合发布和端到端验证；
- 迁移、Feature Flag、灰度、回滚和混合版本只在对应风险真实存在时出现；
- 不使用空二级标题或批量“无”“不适用”占位填充格式。

## 插件结构

```text
Better-Know/
├── .agents/plugins/marketplace.json
├── .claude-plugin/marketplace.json
└── plugins/better-know/
    ├── .codex-plugin/plugin.json
    ├── .claude-plugin/plugin.json
    ├── skills/
    │   ├── frontend-solution-design/
    │   ├── backend-solution-design/
    │   └── fullstack-solution-design/
    ├── sources.lock.json
    ├── SHA256SUMS
    ├── VERSION
    ├── CHANGELOG.md
    └── LICENSE
```

这是一个 Skills-only 插件，不包含 MCP Server、Hook、自定义 UI 或需要认证的外部连接。

## Codex 安装

添加 GitHub Marketplace：

```bash
codex plugin marketplace add VenusFennn/Better-Know --ref main
```

安装插件：

```bash
codex plugin add better-know@better-know
```

检查安装：

```bash
codex plugin list --json
```

安装后在新任务中显式调用任一技能，例如：

```text
使用 $fullstack-solution-design 只读分析这个跨前后端需求，并仅输出统一解决方案。
```

## Claude Code 安装

添加 GitHub Marketplace：

```bash
claude plugin marketplace add VenusFennn/Better-Know
```

安装到用户级：

```bash
claude plugin install better-know@better-know
```

安装到项目级：

```bash
claude plugin install better-know@better-know --scope project
```

检查安装：

```bash
claude plugin list
```

安装后使用插件命名空间调用，例如：

```text
/better-know:fullstack-solution-design
```

## 完整性验证

每个 Skill 都包含独立的：

- `VERSION`；
- `SHA256SUMS`；
- 方法论哈希；
- 内容覆盖清单；
- 技能评测用例。

插件根目录还包含插件级 `SHA256SUMS` 和 `sources.lock.json`。`sources.lock.json` 记录前端、后端源仓库的基础提交、插件内版本和打包修改，避免来源漂移。

安装或升级后应验证：

- 三个 `SKILL.md` 均能被发现；
- Skill Frontmatter 和插件清单可解析；
- Skill 级与插件级 `SHA256SUMS` 全部通过；
- 跨端任务只输出一份统一方案；
- 显式调用前后项目和业务状态保持不变。

## 版本与升级

插件使用语义化版本。任何技能内容变化都会同步提升插件版本，因为 Codex 和 Claude Code 都可能按插件版本缓存安装内容。

升级前应比较当前版本和新版本；不要直接覆盖用户自行修改的 Skill。升级后重新验证插件清单、三个技能及全部哈希。

## 来源

- [Frontend-Solution-Design](https://github.com/VenusFennn/Frontend-Solution-Design)
- [Backend-Solution-Design](https://github.com/VenusFennn/Backend-Solution-Design)

插件内快照的具体基础提交和打包差异见 `plugins/better-know/sources.lock.json`。

## License

[MIT License](LICENSE)
