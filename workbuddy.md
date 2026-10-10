# WorkBuddy 创作上下文（WorkBuddy Conversation Context）

> 本文件为「麦当劳程序员创意开发大赛」WorkBuddy 专项奖励材料，记录本项目在腾讯 WorkBuddy 平台上的真实创作过程。

## 一、平台信息

- **平台**：腾讯 WorkBuddy 智能体平台
- **创作账号**：韩工（WorkBuddy 用户）
- **创作日期**：2026 年 10 月 10 日
- **参与角色**：WorkBuddy 智能体「小微」作为 AI 开发助手，与用户（韩工）通过多轮对话协作完成

## 二、创作过程实录（对话上下文摘要）

### 第 1 轮：需求定义

用户向 WorkBuddy 提出需求：

> "请帮我创建一个可以实现「利用有限预算进行麦当劳点单并下单」的 skill。用户会输入一个预算金额以及就餐人数和地址……根据用户的可配送地址寻找距离最近的一家麦当劳，查找到门店后查询门店的优惠券并领取，然后在预算范围内帮用户选择餐品，用户确认后放进购物车完成下单。"

WorkBuddy 读取麦当劳 MCP 全部 15 个工具的接口定义，通过 skill-creator 流程完成：
- 设计 8 步工作流（收集要素 → 地址确认 → 门店确认 → 领券 → 预算内配餐 → 计价确认 → 下单 → 支付跟踪）
- 定义安全铁律：付费双重确认、地址确认关卡、预算红线（实付含配送费 ≤ 预算）、价格单位"分"转"元"
- 梳理 beType/orderType/beCode 对照表与特调项传参规则等易错点
- 产出 `skill/SKILL.md`（主工作流）与 `skill/references/mcd-tools.md`（15 工具速查）

### 第 2 轮：堂食建议迭代

用户提出：

> "也不一定要配送，如果距离比较近可以告知用户可以去堂食以及需要的步行距离。"

WorkBuddy 据此迭代：
- 新增「就近堂食建议」：最近门店步行 ≤1 公里 / 15 分钟时主动推荐到店自取，给出步行距离与时长，说明省下的配送费可换成一份小食
- 到店场景用户位置仅用于搜门店，无需创建配送地址，降低使用门槛
- 新增 beType 切换防错规则（外送↔到店的 storeCode/beCode 不通用）

### 第 3 轮：参赛项目化

用户提出参加麦当劳程序员创意开发大赛，WorkBuddy 将 skill 整理为开源参赛项目，补齐 README.md、CONTEST_DECLARATION.md、MCP_INTEGRATION.md、mcp-config.example.json 与本文件。

## 三、与 WorkBuddy 能力的结合

- **Skill 机制**：本项目即一个标准 WorkBuddy Skill（含 YAML frontmatter 与渐进式披露结构），存放于 `~/.workbuddy/skills/` 后可被 WorkBuddy 在对话中自动触发。
- **MCP 连接器**：麦当劳 MCP 通过 WorkBuddy 连接器接入，凭据由平台托管，对话内直接调用。
- **多轮确认交互**：利用 WorkBuddy 的对话能力实现"方案确认 + 价格确认"双重付费关卡。

## 四、真实性声明

以上创作过程真实发生在腾讯 WorkBuddy 平台，本项目为在 WorkBuddy 中真实开发并使用的智能体 Skill。
