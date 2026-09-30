# skill-adapter

把一个成功 skill 的机制适配到新领域。对标驱动，分级验证，完整交付。

## 一句话理解

> 我想做一个类似 `/dbs` 的 Matt 版智能路由

→ skill-adapter 帮你完成从对标分析到验证交付的全流程。

---

## 使用场景

✅ **适用**：
- "我想做一个类似 X 的 Y"
- "参考 Z 的机制，做一个新工具"
- "把 A skill 适配到我们的场景"

❌ **不适用**：
- 从零创造全新 skill（用 `/dbs-skill-maker`）
- 简单功能扩展（直接修改原 skill）

---

## 快速开始

```bash
/skill-adapter
```

然后说出你的对标对象和目标领域：

```
我想做一个类似 /dbs 的 Matt 版智能路由
```

skill-adapter 会自动：
1. 分析原 skill 核心机制
2. 识别目标领域特殊性
3. 生成适配后的 Skill 文件
4. 执行分级验证（等级 1-3）
5. 生成交付报告

---

## 完整案例

参考 [`references/case-ask-matt-smart.md`](references/case-ask-matt-smart.md)

**输入**：
> 对标 `/dbs`，覆盖 24 个 Matt skills

**输出**：
- `ask-matt-smart` skill
- 11 个测试样本 100% 通过
- 等级 3 验证完成
- 可本地使用

---

## 执行流程

### 阶段 1：澄清对标与目标
- 读取原 skill 的 SKILL.md
- 分析核心机制（如五维筛选）
- 识别目标领域差异
- 确定保留/删除/新增的模式

### 阶段 2：生成适配后的 Skill
- 创建目录结构
- 适配 SKILL.md（保留通用逻辑，改变领域内容）
- 创建发现脚本
- 创建 README.md

### 阶段 3：分级验证
- **等级 1**：结构校验（frontmatter、脚本、路径）
- **等级 2**：行为冒烟（6-8 个测试样本）
- **等级 3**：留出/回归（2 个留出样本 + 无退化）
- **等级 4**：安装交付（GitHub + npx，可选）

### 阶段 4：生成交付报告
- VERIFICATION.md
- DELIVERY-REPORT.md
- 本地安装说明

---

## 交付物

```
<new-skill-name>/
├── SKILL.md                          # 主入口
├── README.md                         # 安装指南
├── VERIFICATION.md                   # 完整验证报告
├── DELIVERY-REPORT.md                # 最终交付报告
├── test-samples.md                   # 测试样本
└── scripts/
    └── discover-script.py            # 发现脚本
```

---

## 核心能力

1. **对标驱动**：从成功案例学习，不是从零设计
2. **保留通用逻辑**：识别跨领域的判断框架
3. **适配领域差异**：调整候选、场景、特殊模式
4. **分级验证**：结构 → 冒烟 → 留出/回归 → 安装
5. **完整交付**：文件 + 测试 + 报告

---

## 适配 vs 创建

| 场景 | 用什么 |
|------|--------|
| 已有成功案例，做新领域版本 | `/skill-adapter` ✅ |
| 从零创造全新 skill | `/dbs-skill-maker` |
| 简单功能扩展 | 直接修改原 skill |

---

## 典型时间

- 阶段 1（澄清）：10-15 分钟
- 阶段 2（生成）：15-20 分钟
- 阶段 3（验证）：20-30 分钟
- 阶段 4（交付）：5-10 分钟

**总计**：50-75 分钟完成等级 3 验证

---

## 参考文档

- [快速开始](references/quick-start.md) - 最少信息开始适配
- [完整案例](references/case-ask-matt-smart.md) - ask-matt-smart 的完整流程

---

## License

MIT
