---
name: xiaohongshu-auto-reply
description: 小红书留学咨询自动回复。支持真实Chrome浏览器执行、智能浏览策略、Excel记录追踪。
metadata:
  default_prompt: reply_prompt_v3.txt
  excel_file: xiaohongshu_replies_log.xlsx
  excel_mode: APPEND
  execution_mode: IMMEDIATE
  planning_required: NO
  task_type: REPETITIVE_WORKFLOW
---

# ⚠️ CRITICAL: EXECUTION-FIRST SKILL

**THIS IS AN EXECUTION SKILL, NOT A PLANNING SKILL**

---

# 🔴🔴🔴 最重要的规则 - 智能浏览策略 🔴🔴🔴

**每次回复后：点击X关闭 → 继续同页面 → 滚动找更多 → 只有滚动2-3屏没有才F5**

```
❌ 错误：回复1 → F5 → 回复2 → F5 → 回复3 → F5...
✅ 正确：回复1 → 关闭X → 同页面回复2 → 关闭X → scroll_down → 回复3 → ... → 滚动2-3屏没有 → F5
```

**详见下方规则3**

---

# 🔴🔴🔴 第二重要的规则 - 节奏控制（防风控）🔴🔴🔴

**每次回复后必须执行两件事，不可跳过：**

```
1️⃣ 随机等待：每次回复后 timeout /t random(5-10) /nobreak
   - 每3个回复额外+15-30秒 | 每5个+30-60秒 | 每10个+2-3分钟

2️⃣ 模拟真人行为：生成random(1-100)
   - 1-10: 点赞非留学帖子 | 11-15: 收藏帖子 | 16-23: 滚动浏览 | 24-28: 看评论 | 29-100: 不触发
```

```
❌ 错误：回复→记录Excel→立刻下一个回复
✅ 正确：回复→记录Excel→等待5-10秒→随机模拟行为→下一个回复
```

**详见下方规则4步骤13-14 + MANDATORY_RULES.md Rule 9**

---

When the user requests to send N replies (e.g., "回复10个", "继续回复", "开始第二批"):
- DO NOT enter plan mode
- DO NOT create implementation plans
- IMMEDIATELY start executing the 12-step cycle
- Repeat N times until all replies are sent

**Why no planning needed:**
- The complete 12-step workflow is already defined
- All rules are explicit in MANDATORY_RULES.md
- This is a repetitive execution task, not a design task
- Each reply follows identical steps: F5 → screenshot → click → wait → find box → click box → type → screenshot → send → wait → return → Excel record

**Only enter plan mode if:**
- User explicitly asks to "plan" or "design" something new
- User wants to modify the skill itself
- User asks architectural questions about the skill

**For normal reply requests:** Execute immediately, no planning.

# Xiaohongshu Auto-Reply Skill

## Core Workflow

This skill automates Xiaohongshu study abroad consulting replies with **continuous iterative improvement** based on your feedback.

```
[Auto-select post] → [Send reply] → [Record to Excel]
    ↓
[You provide feedback: YES/NO]
    ↓
[AI analyzes patterns & your guidance]
    ↓
[Auto-optimize reply prompt]
    ↓
[Next cycle with improved strategy]
```

## 🔴 强制性规则 (MANDATORY RULES) - 必须遵守

### 规则1：真实Chrome连接（强制）
- **每一次回复都必须通过真实Chrome浏览器执行**
- ❌ 禁止虚拟数据、Python脚本、批量导入
- ❌ 禁止任何形式的偷懒（如：部分用脚本、跳过步骤）
- ❌ 禁止批量导入虚拟数据
- ✅ 完整执行：F5刷新 → 截图识别 → 点击帖子 → 输入回复 → 点击发送 → 返回记录

### 规则2：完整循环（强制）
- **完成指定数量的回复（如：10个就要做10个）**
- ❌ 不能提前停止，不能部分完成
- ✅ 进度跟踪：1/N → 2/N → ... → N/N ✅
- ✅ 必须完成指定的所有回复才能报告"完成"

### 规则3：智能浏览策略（左上角优先 + 同页扫描 + 滚动发现）⚠️ 关键规则
- **核心策略**：先浏览再回复，充分利用同一页面的所有帖子

**阶段A：首次加载**
1. F5刷新页面 → 截图识别当前可见的所有帖子（通常4-6个）
2. 从左上角开始，逐个判断是否为留学相关帖子
3. 选择最合适的帖子进行回复

**阶段B：回复后返回同页面（⚠️ 关键改进 - 不要每次都F5）**
4. 回复完成后，点击X关闭帖子弹窗（❌不要F5刷新）
5. 返回后页面仍在原位，继续检查同页面剩余的帖子
6. 如果同页面还有其他留学相关帖子，直接点击下一个回复

**阶段C：向下滚动发现更多帖子**
7. 当前可见区域没有合适帖子了 → 向下滚动一屏（scroll_down）
8. 截图识别新出现的帖子，继续从左到右检查和回复
9. 可以连续滚动2-3屏寻找合适帖子

**阶段D：刷新获取全新内容（最后手段）**
10. 只有在以下情况才F5刷新：
    - 已经滚动2-3屏都没找到合适的留学帖子
    - 页面加载异常或出错
    - 连续跳过了太多不相关帖子（超过10个）

**❌ 错误行为**：每次回复完就F5刷新
**✅ 正确行为**：回复→关闭X→同页面继续→滚动→滚动2-3屏没有→才F5

### 规则4：回复步骤不可省略（强制）
**每个回复的核心步骤都不能省略**：

```
========== 浏览策略（配合规则3）==========
步骤1: 页面准备（根据情况选择）
  - 首次/需要刷新时: F5刷新 → 等待2秒
  - 回复后继续浏览时: ❌不刷新，直接从当前位置继续
  - 滚动发现更多时: scroll_down → 等待1秒

步骤2: 截图扫描（必须）
  - 执行: screenshot
  - 目的: 识别当前可见的所有帖子
  - 记住: 哪些帖子已经看过/回复过/跳过了

步骤3: 选择目标帖子
  - 从当前可见帖子中选择最合适的（未回复过的）
  - 如果当前屏幕没有合适的:
    → 先scroll_down查看更多
    → 滚动2-3屏仍没有 → 才F5刷新

========== 单个回复流程 ==========
步骤4:  点击目标帖子 → 等待2秒详情页加载
步骤5:  找到评论输入框（⚠️ 必须用JS focus）
  - 执行: JS `document.querySelector('#content-textarea').click(); .focus();`
  - ❌ 不要直接点击页面上的输入框（可能点到子回复框）
  - ❌ 不要用find定位输入框再点击（可能焦点错位）
  - ✅ 必须用JS精确定位 #content-textarea 并 focus
步骤6:  点击输入框激活（JS focus后可跳过手动点击）
步骤7:  输入回复内容（type命令）
步骤8:  截图确认文本 ⚠️⚠️⚠️ 关键验证步骤
  - 执行: screenshot
  - ✅ 必须确认：输入框中显示了完整的回复文本
  - ❌ 如果看到"说点什么..."占位符 → 文本没进去，必须重试
  - ❌ 如果看到"这是一片荒地 点击评论" → 输入框未激活，必须重试
  - 🔄 重试方法: 重新JS focus → 重新type → 重新截图确认
步骤9:  点击发送按钮（⚠️ 推荐用find ref方式点击）
步骤10: 等待2秒发送确认 + 验证发送成功
  - ✅ 成功标志: 页面自动返回feed（标题变为"小红书 - 你的生活兴趣社区"）
  - ✅ 成功标志: 评论区出现自己刚发的评论
  - ❌ 失败标志: 仍在帖子详情页且输入框为空（可能发送失败）
  - ❌ 失败标志: 出现错误提示（如"评论发送失败"、"操作过于频繁"）
  - 🔄 发送失败恢复: 见下方"发送失败恢复流程"
步骤11: 返回到feed（⚠️点击X关闭，不要F5）
步骤12: 记录到Excel（⚠️ 只有确认发送成功后才记录）

========== ⚠️ 发送失败恢复流程（如需要）==========
如果步骤10验证发现发送失败：
  恢复1: 检查输入框是否还有文本
    - 有文本 → 直接重新点击发送按钮
    - 无文本 → 继续恢复2
  恢复2: 重新输入并发送
    a) JS focus输入框: document.querySelector('#content-textarea').click(); .focus();
    b) 重新type回复内容
    c) 截图确认文本确实在输入框中（⚠️ 不可跳过）
    d) 用find ref方式点击发送按钮
    e) 等待2秒，再次验证是否成功
  恢复3: 如果连续2次失败
    - 截图记录错误状态
    - 关闭帖子返回feed
    - 在Excel中标记该帖子为"发送失败"
    - 继续下一个帖子（不计入已完成数量）

常见失败原因及解决：
  | 现象 | 原因 | 解决 |
  |------|------|------|
  | 文本没进输入框 | 焦点在子回复框或其他元素 | 用JS focus #content-textarea |
  | 点发送无反应 | 按钮未正确定位 | 用find ref精确定位发送按钮 |
  | 发送后无变化 | 网络问题或平台限制 | 等待后重试，注意频率限制 |

========== 🔴 节奏控制（防风控，不可跳过）==========
步骤13: 随机等待（必须执行）
  - 基础等待：random(5-10)秒，用 Bash: timeout /t X /nobreak
  - 每3个回复后 (N % 3 == 0)：额外 random(15-30)秒
  - 每5个回复后 (N % 5 == 0)：额外 random(30-60)秒
  - 每10个回复后 (N % 10 == 0)：额外 random(120-180)秒

步骤14: 模拟真人浏览行为（必须执行）
  - 生成随机数(1-100)，根据结果执行：
  - 1-10 (10%): 点赞一个非留学帖子（点爱心图标）
  - 11-15 (5%): 收藏一个帖子（打开→收藏→关闭）
  - 16-23 (8%): 纯滚动浏览2-3屏（scroll_down + 等2-3秒）
  - 24-28 (5%): 打开一个帖子看评论后关闭（滚动评论区+等3-5秒）
  - 29-100 (72%): 不触发额外行为，直接继续
  - ⚠️ 触发任何行为后，累计计数器+1

========== 继续下一个回复 ==========
[返回步骤2: 截图扫描同页面剩余帖子]
[只有当同页面+滚动都没有合适帖子时，才回到步骤1 F5刷新]
[Session结束时: 记录模拟行为统计到 simulation_behavior_log.xlsx]
```

- ❌ 任何步骤都不能跳过或简化
- ❌ 不能提前假设操作成功，必须等待确认
- ⚠️ **关键**：步骤11后返回步骤2，不是返回步骤1

### 规则5：禁止偷懒（强制）
| 禁止行为 | 原因 |
|---------|------|
| Python脚本自动添加到Excel | 违反真实Chrome要求 |
| 部分Chrome+部分脚本混合 | 混合作弊 |
| 跳过某些回复 | 没有完成指定数量 |
| 简化步骤（如不截图直接点击） | 无法验证执行 |
| 批量导入虚拟数据 | 欺骗记录 |
| 提前报告"完成" | 未真实完成 |

### 规则6：验证机制（强制）
**每个回复必须验证**：
- ✅ 实际点击了帖子（有URL变化）
- ✅ 实际输入了文本（screenshot显示input有内容）
- ✅ 实际发送了（等待2秒后返回feed）
- ✅ 已记录到Excel（文件有新行+帖子链接）

### 规则7：Excel记录格式（强制）
**位置**：`{SKILL_DIR}/xiaohongshu_replies_log.xlsx`
- `{SKILL_DIR}` = 当前skill所在目录
- ⚠️ **重要**：所有回复都追加到这**同一个文件**，不要每次创建新文件
- 文件不存在时自动创建（带表头）

**必须包含的列**（顺序固定）：
```
序号 | 时间 | 帖子类型 | 帖子标题 | 帖子正文内容 | 帖子链接 | 回复内容 | 模型标注 | [可选: 帖子质量评分 | 反馈-帖子 | 反馈-回复 | 反馈原因 | 说明]
```

**强制要求**：
- ✅ 前8列必须完整填充（序号、时间、类型、标题、正文、**链接**、回复、模型）
- ✅ 每行必须真实数据（不能虚构）
- ✅ 帖子标题 = 从Chrome页面识别到的完整标题
- ✅ 帖子正文 = 从Chrome页面识别到的完整正文内容
- ✅ **帖子链接 = 点击进帖子后浏览器地址栏的完整URL**（包含所有参数，不能遗漏）
- ✅ 回复内容 = 与Chrome中实际发送的完全一致
- ✅ 时间戳必须准确（不能篡改）
- ✅ 每个回复完成后立即**追加**记录（不能最后一起补，不能创建新文件）
- ✅ 序号从文件现有最大序号+1开始（保持连续）
- ⚠️ 帖子质量评分为可选项（0-100分）
- ❌ 禁止删除或修改已记录的行
- ❌ 禁止使用脚本或虚拟数据填充
- ❌ 禁止创建新的Excel文件（必须追加到现有文件）

**违反任何规则的尝试都将被拒绝，并要求重新执行。真实优先。完整优先。用户优先。**

详见：`MANDATORY_RULES.md` 和 `SKILL_CONFIG.md`

---

## Key Features

### 1. Anthropomorphic Browsing
- Mimics natural user behavior: scroll for a period → refresh with F5 → focus on top-left post
- **Top-left post = highest priority** (most recent, highest quality)
- Creates realistic browsing patterns to avoid detection

### 2. Intelligent Post Selection
- Prioritize top-left post (core strategy from initial prompt)
- Identify study abroad consulting topics
- Track selection quality based on your YES/NO feedback

#### 不回复的帖子类型（直接跳过）

| 类型 | 判断标准 | 关键词 |
|------|----------|--------|
| **吐槽/情绪发泄类** | 用户在发泄情绪，不是寻求帮助 | 吐槽、奇葩、坑、骗、无语、服了、醉了、崩溃、心累 |
| **面经/经验分享类** | 用户在分享经验，不是求助 | 面经、经验分享、干货、攻略、我的申请经历、timeline、录取分享 |

**识别方法**：
1. 截图扫描时先判断帖子类型
2. 如果属于以上两类，标记为"跳过"并选择下一个帖子
3. Excel中不记录跳过的帖子（只记录实际回复的）

### 3. Automated Reply Sending
- Generate contextual, professional replies
- Vary response styles to avoid patterns
- Record all details to Excel

### 4. Feedback-Driven Learning
- Manual feedback: Mark each reply/post as **YES** ✅ or **NO** ❌
- Optional explanation: Tell AI why you marked NO and how to improve
- AI analyzes patterns across all feedback

### 5. Automatic Optimization
- **reply_prompt_vX.txt** updates based on:
  - User's explicit guidance (e.g., "NO - too long, be more concise")
  - Pattern analysis (e.g., "Top 10 YES replies average 120 chars")
  - Topic performance (e.g., "521/27fall posts → 89% accept rate")
- Version control: Track all iterations and improvements

### 6. Comprehensive Tracking
- Excel records: timestamp, post type, keywords, reply content, user feedback, explanation
- Metrics: Acceptance rate, post selection accuracy, reply length trends
- Optimization history: Every change and its rationale

## Advanced Optimizations (A-K)

### A. Post Quality Scoring (帖子质量评分)
- **Automatic scoring** (0-100) for each post before replying
- Three dimensions:
  - 留学相关性 (Study abroad relevance): 40 points
  - 回复有用性 (Reply usefulness): 35 points
  - 引流效果 (Engagement potential): 25 points
- **Smart filtering**: Only reply to posts scoring ≥60 (good) or ≥80 (excellent)
- **Result**: Prevents wasting replies on low-quality posts

### B. Multi-Select Feedback Reasons (反馈原因)
- **New Excel column**: "反馈原因" (Feedback Reason)
- Multi-select dropdown menu with predefined reasons:
  - For replies: 太长, 太短, 不够个性化, 语气不当, 话题无关, 没解决问题, 表情符号, 其他
  - For posts: 话题无关, 内容不清, 重复话题, 质量低, 其他
- **Benefit**: Structured feedback → more precise analysis
- **Real-time suggestions**: System suggests likely reasons when you mark NO

### C. Real-Time Feedback Suggestions (实时反馈建议)
- When you mark a reply as NO, system immediately suggests likely reasons
- Examples:
  - "这个回复160字，平均YES回复是120字。是否想说'太长'？"
  - "这个回复没有具体例子。是否想说'不够个性化'？"
- **Reduce friction**: No need to type explanation every time
- **Auto-learning**: Suggestions improve as system learns your patterns

### E. Model Performance Judgment (模型性能追踪)
- **Automatic tracking** of Claude vs Haiku performance
- After 5+ replies per model, calculate acceptance rates
- **Intelligent allocation**: Automatically allocate more replies to better-performing model
  - If Claude: 85%, Haiku: 70% → shift to 70% Claude / 30% Haiku
  - If performance gap closes → rebalance
- **Judgment criteria**: >10% difference = shift allocation; >15% = strong preference

### F. Auto Rollback Mechanism (自动回滚)
- Monitor acceptance rate across versions
- **If performance degrades** by >5%:
  - Alert: "⚠️ Version v3 (82%) is worse than v2 (87%). Consider rollback?"
  - Suggest rolling back to previous version
  - Provide option to blend versions
- **Safety feature**: Prevents bad optimizations from persisting

### G. User Intent Profiling (用户风格指南)
- **Extract implicit preferences** from your feedback patterns:
  - Tone preferences (formal vs casual, warm vs professional)
  - Length preferences (short vs detailed)
  - Specificity preferences (generic vs examples)
  - Structure preferences (what works for you)
- **Generate style guide**: Inject into prompt automatically
- **Example**: "User prefers formal + specific (based on 8 YES patterns)"
- **Continuous learning**: Preferences update as you provide more feedback

### H. Intelligent Pause Detection (智能暂停检测)
- Automatically detect when optimization has plateaued
- **Plateau criteria**: Last 2 versions both improved by <3%
- **Alert**: "Acceptance rate plateau detected: v3→v4 only +1%, v2→v3 only +2%"
- **Recommendation**: "Consider freezing current version for production use"
- **Prevents over-optimization**: Stop tweaking when returns diminish

### I. Visual Dashboard & Trend Analysis (可视化仪表板)
- **Real-time performance dashboard** (text + HTML)
- Displays:
  - Overall metrics: total replies, acceptance rate, post accuracy, avg quality score
  - Trend analysis: Acceptance rate over time (last 7 days)
  - Topic performance heatmap: Which topics perform best (🟢🟡🔴)
  - Model comparison: Claude vs Haiku side-by-side
  - Version progression: How each version improved
- **Export formats**: Text (terminal), HTML (browser), JSON (integration)

### J. Smart Browsing Logic (智能浏览逻辑)
- **Adaptive F5 refresh timing** based on recent post quality:
  - High quality posts (avg 80+): Scroll more, refresh less frequently
  - Medium quality posts (65-79): Normal refresh pattern
  - Low quality posts (<65): F5 more frequently
- **Adaptive scroll depth**: More scrolling if posts are good, less if bad
- **Self-learning**: System learns optimal scroll depth from your YES positions
- **Benefit**: Maximize good post discovery, minimize time on low-quality feed

### K. Error Recovery & Resilience (错误恢复机制)
- **Automatic retry** with exponential backoff (2s → 5s → 10s)
- **Bot detection** awareness:
  - Recognize: "操作过于频繁", "请稍后", verification requests
  - Auto-pause: Add adaptive delays when detection signals appear
  - Progressive delays: 60s → 5min → 10min → 30min
- **Page structure change detection**: Alert if page layout changes unexpectedly
- **Session resumption**: Auto-resume from last post on network errors
- **Resilience**: Gracefully handle and recover from common failures

### L. Soft Call-to-Action Integration (软引流集成)
- **Non-intrusive engagement strategy**: 低调、自然、不突兀的引流方式
- **Three engagement types**:
  1. **Professional exchange** (通用): "有更多疑问欢迎继续问哈" | "有其他问题可以再交流"
  2. **Professional guidance hint** (文书/材料类): "文书这块如果抓不准，可以找专业指导" | "申请材料如果需要优化，也很容易出彩"
  3. **Follow-up support** (需要跟进): "具体实施过程中有任何疑问，随时可以讨论" | "定期做复盘很重要，有需要可以深入交流"
- **Placement strategy**:
  - Last 1-2 sentences of each reply
  - Natural integration without breaking overall logic
  - Uses "additional note" or "as-you-go" approach
- **Key principles**:
  - 10-15 characters per engagement sentence
  - Preserves professionalism and credibility
  - Leaves space for further conversation
  - No hard selling, maintains expert positioning
- **Excel tracking**: Optional "引流类型" (CTA Type) column for analysis

### M. Reliable Like/Favorite via JavaScript (点赞收藏JS优化)
- **问题**: 截图识别点赞/收藏按钮经常失败，反复重试浪费大量tokens
- **方案**: 使用 `javascript_tool` 直接操作DOM，一次调用完成点赞+收藏
- **关键选择器**（实测验证）:
  - 帖子点赞: `.engage-bar-container .like-wrapper`（必须限定engage-bar，页面有28+个like-wrapper）
  - 帖子收藏: `.engage-bar-container .collect-wrapper`
  - 状态判断: SVG `<use href="#like">` = 未赞, `<use href="#liked">` = 已赞
- **三个坑**:
  1. `document.querySelector('.like-wrapper')` 选中评论区的赞，不是帖子的
  2. `like-active` class 不代表已点赞（始终存在），要用 SVG use href 判断
  3. `.like-icon` 是SVG无 `.click()`，必须点击父级 `.like-wrapper`
- **Token节省**: 每个帖子从 700-2000 tokens 降至 ~100 tokens（节省85-95%）
- **规则**: 见 MANDATORY_RULES.md Rule 9

### N. Close Post via JavaScript (关闭帖子JS优化)
- **问题**: 关闭帖子返回feed时截图找关闭按钮浪费tokens
- **方案**: `document.querySelector('.close-circle').click()` 一行JS直接关闭
- **选择器**: `.close-circle`（DIV元素，全局唯一，父级 `.note-detail-mask`）
- **Token节省**: 省去一次截图识别（~500 tokens）

## Detailed Workflow

### Phase 1: Running the Campaign

1. **Initialize**: Load current `reply_prompt.txt` and optimization history
2. **Browse**: Scroll discovery feed, refresh periodically with F5
3. **Select**: Always prioritize top-left post (best recency and quality)
4. **Capture URL**: After clicking into post detail page, capture the browser address bar URL (this is the post link)
5. **Reply**: Generate response using current prompt
6. **Record**: Add entry to Excel with all details **including the post link from step 4**
7. **Repeat**: Continue until you decide to stop

### Phase 2: You Provide Feedback

After each reply:
- **Mark post selection**: YES ✅ or NO ❌ (was this a good topic?)
- **Mark reply**: YES ✅ or NO ❌ (was this a good response?)
- **Optional guidance**: If marking NO, explain why and how to improve
  - Examples:
    - "NO - reply too long, keep under 100 chars"
    - "NO - not personal enough, add more specific examples"
    - "NO - topic not study abroad related"

**Key point**: You DON'T need to explain every NO, but when you do, I focus heavily on your guidance.

### Phase 3: Automatic Analysis & Optimization

**The system analyzes:**

1. **Pattern Recognition**
   - What post types get YES vs NO?
   - What reply characteristics correlate with YES?
   - Length, tone, examples, specificity, emotional tone?

2. **Your Explicit Guidance**
   - If you said "NO - too long", I prioritize shortening replies
   - If you said "this is perfect", I identify what made it work
   - Your explanations are treated as high-priority learning signals

3. **Initial Strategy Preservation**
   - Keep core logic: top-left prioritization, timed refreshes, browse behavior
   - Layer improvements on top without losing foundations
   - Track which optimizations improved accept rate

4. **Prompt Version Updates**
   - `reply_prompt_v1.txt` → `reply_prompt_v2.txt` (after feedback set 1)
   - Each version includes timestamp, changes made, metrics before/after
   - Keep all versions for reference and rollback if needed

### Phase 4: Next Campaign With Improvements

Run campaign with updated prompt, collect feedback, analyze again, improve further.

---

## Scripts & Resources

### Scripts (in `scripts/`)

- **`xiaohongshu_automation.py`** - Main automation loop
  - Anthropomorphic browsing with adaptive timing (J)
  - Top-left post selection with quality scoring (A)
  - Reply generation and sending
  - Excel recording with quality scores and feedback reasons (B)
  - PostQualityScorer class for filtering posts

- **`feedback_analyzer.py`** - Pattern analysis
  - Read feedback from Excel (including new feedback reasons column)
  - Calculate acceptance rates, topic performance
  - Identify reply characteristics (length, tone, structure)
  - Generate optimization recommendations
  - Support for multi-select feedback reasons

- **`prompt_updater.py`** - Automatic prompt optimization
  - Parse user feedback and explanations
  - Layer improvements on initial prompt
  - Version control and tracking
  - Generate optimization history
  - Incorporate user style guide (G)

- **`optimization_engine.py`** - Advanced optimization features (NEW)
  - RealtimeFeedbackSuggester: Generate suggestions for NO feedback (C)
  - ModelPerformanceTracker: Track and recommend models (E)
  - AutoRollbackMechanism: Detect and rollback degradations (F)
  - UserIntentProfiler: Extract and track user preferences (G)
  - PausePointDetector: Detect optimization plateaus (H)
  - SmartBrowsingLogic: Adaptive F5 and scroll timing (J)
  - ErrorRecoverySystem: Bot detection and error handling (K)

- **`dashboard_generator.py`** - Visualization and reporting (NEW)
  - DashboardGenerator: Generate performance dashboards
  - Text dashboard for terminal display
  - HTML dashboard for web view
  - Topic performance heatmap (I)
  - Model comparison visualizations
  - Trend analysis and forecasting
  - JSON export for integration

### References (in `references/`)

- **`initial_strategy.md`** - Your original prompt and core logic
- **`workflow.md`** - Complete step-by-step workflow
- **`feedback_tracking.md`** - Excel schema and metric definitions
- **`optimization_history.md`** - Record of all iterations and improvements

### Assets (in `assets/`)

- **`reply_prompt_template.txt`** - Template for prompt versions
- **`excel_template.xlsx`** - Excel structure with headers

---

## Excel Tracking Format

Each row contains (in order):
- **序号** (ID): Auto-increment (1, 2, 3...)
- **时间** (Timestamp): When reply was sent (accurate timestamp)
- **帖子类型** (Post Type): Topic category (e.g., "27fall planning", "GPA assessment")
- **帖子标题** (Post Title): Full title extracted from page
- **帖子正文内容** (Post Content): Full post body text extracted from page
- **帖子链接** (Post Link): Direct URL to the post (extracted from browser address bar)
- **回复内容** (Reply Content): Full reply text (≥80 characters)
- **模型标注** (Model Tag): Which model generated this (Claude / Haiku)
- **帖子质量评分** (Quality Score): 0-100 (calculated by PostQualityScorer)
- **你的反馈-帖子** (Your Feedback - Post): YES ✅ / NO ❌ (optional)
- **你的反馈-回复** (Your Feedback - Reply): YES ✅ / NO ❌ (optional)
- **反馈原因** (Feedback Reason): Multi-select reasons (optional)
- **说明** (Explanation): Optional "why NO" and improvement guidance (optional)

### Link Tracking Purpose
- **帖子链接** enables:
  - Direct access to original post for verification
  - Tracking post engagement (likes, comments, shares)
  - Building engagement metrics database
  - Analyzing link-to-conversion for future optimization
  - Audit trail for quality assurance

---

## Key Metrics & Reporting

After each feedback session, system generates report:

```
=== Campaign Summary ===
Total replies: 20
Acceptance rate: 85% (17/20)
Post selection accuracy: 90% (18/20)

=== Topic Performance ===
27fall/planning: 5 posts, 100% accept
HK3 application: 8 posts, 87% accept
Agency advice: 4 posts, 75% accept
(...)

=== Reply Characteristics - YES replies ===
Average length: 118 characters
Tone: Supportive + Specific advice
Structure: Problem recognition → 2-3 concrete recommendations

=== Improvements Made ===
v1 → v2: Reduced average length from 140 → 118 chars (+15% accept rate)
v2 → v3: Added more emotional support (+8% accept rate)
(...)
```

---

## Important Principles

1. **Top-left post prioritization is core** - This is your original insight; improvements are layered on top
2. **Your feedback is gold** - Explicit guidance outweighs pattern analysis
3. **Preserve successful foundations** - Don't abandon what works
4. **Version control everything** - Always know what changed and why
5. **Measure impact** - Track before/after for each optimization

---

## Getting Started

1. Read `references/initial_strategy.md` to understand core logic
2. Read `references/workflow.md` for step-by-step instructions
3. Run first campaign with initial prompt
4. Mark YES/NO on each post selection and reply
5. System generates optimization recommendations
6. Provide explicit guidance for anything marked NO
7. System updates prompt and runs next cycle
8. Review optimization history in `references/optimization_history.md`

---

## File Structure

```
xiaohongshu-auto-reply/
├── SKILL.md (this file - updated with A-N optimizations)
├── reply_prompt_optimized.txt (UPDATED - added soft CTA strategy for L)
├── scripts/
│   ├── xiaohongshu_automation.py (+ PostQualityScorer class for A, J, L)
│   ├── feedback_analyzer.py (+ multi-select feedback support for B)
│   ├── prompt_updater.py (+ user style guide for G)
│   ├── optimization_engine.py (NEW - C, E, F, G, H, J, K)
│   └── dashboard_generator.py (NEW - I)
├── references/
│   ├── initial_strategy.md
│   ├── workflow.md
│   ├── feedback_tracking.md (+ post quality scoring schema, feedback reasons, link tracking)
│   └── optimization_history.md
└── assets/
    ├── reply_prompt_template.txt
    └── excel_template.xlsx (UPDATED - added post link column + CTA type column for L)
```

## Quick Reference: Optimizations A-N

| Code | Feature | Purpose | Script |
|------|---------|---------|--------|
| **A** | Post Quality Scoring | Filter low-quality posts before replying | xiaohongshu_automation.py |
| **B** | Multi-Select Feedback | Structured feedback collection | feedback_tracking.md, feedback_analyzer.py |
| **C** | Real-Time Suggestions | Auto-suggest feedback reasons | optimization_engine.py |
| **E** | Model Performance | Track and allocate to better model | optimization_engine.py |
| **F** | Auto Rollback | Detect and recover from degradations | optimization_engine.py |
| **G** | User Intent Profile | Extract and apply user preferences | optimization_engine.py, prompt_updater.py |
| **H** | Pause Detection | Stop optimizing at plateau | optimization_engine.py |
| **I** | Visual Dashboard | Real-time performance visualization | dashboard_generator.py |
| **J** | Smart Browsing | Adaptive F5/scroll based on quality | xiaohongshu_automation.py, optimization_engine.py |
| **K** | Error Recovery | Bot detection and resilience | optimization_engine.py |
| **L** | Soft CTA Integration | Non-intrusive engagement & lead generation | reply_prompt_optimized.txt, xiaohongshu_automation.py |
| **M** | Reliable Like/Favorite | JS-based like/collect via engage-bar (节省85-95% tokens) | reply_prompt_v3.txt, MANDATORY_RULES.md |
| **N** | Close Post via JS | `.close-circle` 一行JS关闭帖子返回feed | reply_prompt_v3.txt, MANDATORY_RULES.md |
