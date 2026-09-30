# 完整案例：ask-matt-smart

这是一个完整的 skill-adapter 案例，展示了如何把 `/dbs` 的机制适配到 Matt skills。

## 对标分析

### 原 skill：/dbs

**位置**：`~/.reasonix/skills/dbs/SKILL.md`

**核心机制**：
1. **版本检查**：每次执行前检查更新
2. **模式判断**：新手入门 / 任务编排 / 编号执行 / 隐藏款目录
3. **发现候选**：运行 `scripts/list-official-skills.py`
4. **五维筛选**：任务结果、输入匹配、阶段匹配、边界匹配、可执行性
5. **单/组合判断**：1 个主 Skill + 最多 2 个辅助 Skill
6. **输出**：推荐 + 理由 + 可直接发送的提示词

**关键组件**：
- `scripts/list-official-skills.py` - 发现正式 Skill
- `scripts/check-update.sh` - 版本检查
- `scripts/numbered-prompts.py` - 编号执行
- `references/composition-contract.md` - 组合契约

**成功要素**：
- 从粗糙需求出发，不要求用户提前判断
- 生成可直接发送的提示词
- 说明推荐理由
- 支持单 skill 和组合

---

## 目标领域：Matt skills

**范围**：24 个 Matt Pocock 工程 skills

**特点**：
- 专注工程任务（构思/编码/测试/审查/重构）
- 有完整的工作流（idea → ship）
- 有主流程、上车点、独立工具
- 已有静态文档 `/ask-matt`

**差异点**：

| 特性 | /dbs | Matt skills |
|------|------|-------------|
| 版本检查 | ✅ 需要 | ❌ 不需要 |
| 编号执行 | ✅ 支持 `/dbs 782` | ❌ 不需要 |
| 隐藏款目录 | ✅ 需要 | ❌ 不需要 |
| 流程查询 | ❌ 无 | ✅ 需要（指向 `/ask-matt`） |
| 任务编排 | ✅ | ✅ |
| 新手引导 | ✅ | ✅ |

---

## 适配决策

### 保留的通用逻辑

1. **任务编排**：
   - 五维筛选（任务结果、输入匹配、阶段匹配、边界匹配、可执行性）
   - 单 skill 或组合判断
   - 输出模板（推荐 + 理由 + 提示词）

2. **新手引导**：
   - 说明使用方式
   - 引导用户说出任务

3. **空对话引导**：
   - 当前对话没有任务时的引导

### 删除的模式

1. **版本检查**：Matt skills 不需要版本管理
2. **编号执行**：Matt skills 没有编号系统
3. **隐藏款目录查询**：不适用

### 新增的模式

1. **流程查询**：指向 `/ask-matt` 静态文档

---

## 实现细节

### 1. SKILL.md 适配

**frontmatter**：
```yaml
name: ask-matt-smart
description: Matt Pocock 工程工具箱智能路由。根据当前任务自动推荐最合适的 Matt skill、给出理由并生成可直接发送的提示词。
```

**模式判断**：
```markdown
- 用户输入 `/ask-matt-smart 新手入门` → 模式 A（新手教程）
- 用户输入 `/ask-matt-smart 完整流程` → 模式 C（流程查询）
- 当前对话包含工程任务 → 模式 B（任务编排）
- 当前对话无任务 → 模式 B 的空对话引导
```

**候选发现**（保留逻辑）：
```bash
python3 "<本 Skill 目录>/scripts/list-matt-skills.py"
```

**五维筛选**（保留逻辑）：
```markdown
| 判断 | 要回答的问题 |
| --- | --- |
| 任务结果 | 它是否直接产生最终交付或必要中间判断？ |
| 输入匹配 | 用户已有材料是否属于它处理的输入？ |
| 阶段匹配 | 它是否处理当前卡住的阶段？ |
| 边界匹配 | 它有没有明确排除当前任务或角色？ |
| 可执行性 | 当前权限、文件、证据和工具是否足够开始？ |
```

**常见场景判断**（适配到 Matt 领域）：
```markdown
- 模糊想法 + 在工作目录 → grill-with-docs
- 模糊想法 + 无工作目录 → grill-me
- 想法清晰 + 单会话 → implement
- 想法清晰 + 多会话 → to-spec → to-tickets → implement
- 待分类 issues → triage
- 疑难 bug → diagnosing-bugs
- 代码审查 → code-review
- 老代码重构 → improve-codebase-architecture
- 交接上下文 → handoff
```

### 2. 发现脚本

**文件**：`scripts/list-matt-skills.py`

**固定清单**：
```python
MATT_SKILLS = {
    "grill-with-docs",
    "grill-me",
    "grilling",
    "implement",
    "tdd",
    "code-review",
    "to-spec",
    "to-tickets",
    "triage",
    "diagnosing-bugs",
    "wayfinder",
    "improve-codebase-architecture",
    "codebase-design",
    "domain-modeling",
    "prototype",
    "handoff",
    "research",
    "to-questionnaire",
    "wizard",
    "wait-what",
    "teach",
    "writing-for-agents",
    "retro",
    "pr",
    "setup-matt-pocock-skills",
    "resolving-merge-conflicts"
}
```

**输出格式**：
```
name|description|path
```

---

## 验证过程

### 等级 1：结构校验 ✅

**工具**：`validate_skill_project.py`

**修复**：
- 占位符：将花括号改为方括号
- 绝对路径：改为波浪号

**结果**：
```
WARNING：Skill 根目录存在非标准条目：README.md
WARNING：SKILL.md 共 321 行，建议检查渐进式披露
校验通过：~/.agents/skills/ask-matt-smart（2 个提醒）
```

### 等级 2：行为冒烟 ✅

**测试样本**（8 个）：
1. 待分类 issues → `/triage` ✅
2. 模糊想法 + 项目目录 → `/grill-with-docs` ✅
3. 疑难 bug → `/diagnosing-bugs` ✅
4. 代码审查 → `/code-review` ✅
5. 清晰需求 + 单会话 → `/implement` ✅
6. 非工程任务 → 正确识别不匹配 ✅
7. 新手入门 → 欢迎语 + 使用方式 ✅
8. 流程查询 → 指向 `/ask-matt` ✅

**通过率**：100%

### 等级 3：留出/回归 ✅

**留出样本**（2 个，执行者未提前查看）：
9. 老代码模块重构 → `/improve-codebase-architecture` ✅
10. 交接上下文 → `/handoff` ✅

**回归检查**：
- 样本 1-3 重新验证 ✅
- 核心能力无退化 ✅
- 边界判断无松动 ✅

### 等级 4：安装交付 ⏸️

用户选择暂不发布

---

## 文件清单

```
ask-matt-smart/
├── SKILL.md                          # 321 行，主入口
├── README.md                         # 安装和测试指南
├── VERIFICATION.md                   # 完整验证报告
├── LEVEL-3-VERIFICATION.md           # 等级 3 验证详情
├── DELIVERY-REPORT.md                # 最终交付报告
├── test-samples.md                   # 11 个测试样本
└── scripts/
    └── list-matt-skills.py           # 发现 24 个 Matt skills
```

---

## 关键学习

### 1. 保留什么

**通用判断逻辑**：
- 五维筛选框架
- 单/组合判断规则
- 输出模板

**这些是跨领域的**，适配时保持不变。

### 2. 适配什么

**领域特定内容**：
- 候选清单（从 dbs 系列 → Matt 系列）
- 常见场景判断（从商业内容 → 工程任务）
- 特殊模式（删除版本检查、编号执行，新增流程查询）

### 3. 如何验证

**分级验证**：
- 等级 1：结构（静态检查）
- 等级 2：冒烟（主要任务完成）
- 等级 3：留出/回归（未看预期答案 + 无退化）
- 等级 4：安装（真实环境验证）

**渐进式**：先确保结构正确，再验证行为，最后确保质量。

---

## 适配检查清单

在适配过程中，逐项检查：

### 理解原 skill

- [ ] 读取原 SKILL.md
- [ ] 识别核心机制（至少 3 个）
- [ ] 列出关键组件（脚本/引用）
- [ ] 总结成功要素

### 分析目标领域

- [ ] 确定覆盖范围（多少个 skills）
- [ ] 识别领域特点
- [ ] 列出差异点（至少 3 个）
- [ ] 确定保留/删除/新增的模式

### 创建文件

- [ ] 创建目录结构
- [ ] 适配 SKILL.md（frontmatter + 主体）
- [ ] 创建发现脚本
- [ ] 创建 README.md

### 验证

- [ ] 结构校验通过
- [ ] 设计 6-8 个测试样本
- [ ] 行为冒烟通过（至少 1 个主要任务）
- [ ] 设计 2 个留出样本
- [ ] 留出/回归通过

### 交付

- [ ] 生成 VERIFICATION.md
- [ ] 生成 LEVEL-3-VERIFICATION.md
- [ ] 生成 DELIVERY-REPORT.md
- [ ] 确认本地可用

---

**总结**：这个案例展示了完整的适配流程，从对标分析到验证交付，所有关键步骤都有记录。
