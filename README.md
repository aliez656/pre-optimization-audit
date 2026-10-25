# pre-optimization-audit

> 一个通用的 **Agent Skill**：当用户要求「深入优化」时，**先做四道前置审计，再动手改**。
> 不是 DSH 专属——任何支持 `SKILL.md` 约定的 agent 都能用（DSH / Claude Code / Cursor / Codex …）。

---

## 它解决什么问题

大多数「优化一下」的请求，模型会**直接改写**——把话说得更漂亮、结构排得更整齐。但真正值钱的优化通常不在措辞层，而在：

- 这个题目本身该不该这么做
- 用户给的是**一手材料**还是**三手转述**
- 改完凭什么说它**变好了**（不是"看起来更好"）

这个 skill 强制在动手前回答四个问题，把优化从「改写」拉回「判断」。

---

## 四道审计

| 代号 | 问题 | 产物 |
|---|---|---|
| **A** | 专家会做哪些用户没要求的事？ | 指出该质疑的前提、反常/矛盾/负结果、是否是"换材料做的模仿性研究" |
| **B** | 原始素材是第几手？ | 判定一手/二手/三手；列出一手素材应是什么；指出转述中最可能丢失的三类信息 |
| **C** | 用哪些**相互独立**的方法验证？ | 至少两种，不能是"再读一遍""再想一次" |
| **D** | 除了用户和 agent，什么能证明正确？ | 至少一个**落在对话之外**、不受双方偏好影响的证据源；找不到就标注"只能视为提案" |

然后才是 **Phase 1：优化**——方向必须由审计结论驱动，产出要附**证据锚点**；如果审计结论和用户的修改建议冲突，**先指出冲突并给选项**，不静默服从。

---

## 安装

### 通用位置（推荐，多数 agent 都读）

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/pre-optimization-audit.git \
  ~/.agents/skills/pre-optimization-audit
```

> `~/.agents/skills/` 是 Agent Skills 的通用用户级目录。DSH 原生读它；Claude Code / Cursor 等项目级也可以放到各自目录。

### DSH

```powershell
# 用户级（所有工作区可见）
git clone https://github.com/<YOUR_GITHUB_USERNAME>/pre-optimization-audit.git `
  "$env:USERPROFILE\.agents\skills\pre-optimization-audit"
```

或工作区级：`<你的项目>\.dsh\skills\pre-optimization-audit\`

DSH 是**热加载**——放进去即生效，不用重启。

### Claude Code

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/pre-optimization-audit.git \
  ~/.claude/skills/pre-optimization-audit
```

### 其他宿主

把整个目录放到该宿主的 skills 根下，目录名保持 `pre-optimization-audit`，其中必须有 `SKILL.md`。

---

## 用法

满足触发条件时自动生效（宿主的 description 匹配机制决定），也可以直接点名：

```
用 pre-optimization-audit 审一下这段，然后深入优化
```

### 触发的说法（必须带方向限定）

| 说法 | 触发 |
|---|---|
| 「**深入**优化」 | ✅ |
| 「在**这个方向**优化」 | ✅ |
| 「**深入**改进 / 再改一版」 | ✅ |
| 「按**这个方向**重做」 | ✅ |
| 「优化一下」 | ❌ 直接改 |
| 「改进一下」「提升一下」 | ❌ 直接改 |

> 设计如此：只有**带方向限定**的优化才值得花审计成本。泛泛的「优化一下」直接改，不打扰。

---

## 输出格式

```
AUDIT
  A. …（3–5 句）
  B. …（3–5 句）
  C. …（3–5 句）
  D. …（3–5 句）

REVISION
  …（优化后的正文）
  证据锚点：<这一版哪个判断来自哪条审计结论>
```

**如果审计发现题目本身要重写**，只输出 `REWRITE PROPOSAL`，不出 REVISION。

---

## 设计取舍（诚实说明）

**它不保证效果**。这个 skill 的价值在于**强制暴露判断过程**，不是保证审计结论一定正确。

已知局限：

- 四道题的回答质量**取决于模型自身的领域知识**——它不知道的领域，A 题会答得很空
- 「独立验证方法」在纯文本任务里很难真正独立（C 题最容易流于形式）
- D 题的"外部证据源"在纯写作/纯分析任务里经常**找不到**——这时它会如实标注"只能视为提案"，而不是编一个
- 审计会增加 **30–60% 的 token 开销**，所以触发条件刻意收窄了

**不适合**：从零生成新内容、纯格式调整、用户已明确指定改哪一句的场景。

---

## 文件结构

```
pre-optimization-audit/
├── SKILL.md      # 技能本体（agent 读这个）
├── README.md     # 人读这个
└── LICENSE       # MIT
```

---

## License

MIT — 见 [LICENSE](LICENSE)。
