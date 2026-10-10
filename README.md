# 麦麦预算管家 mcd-budget-order 🍔💰

> 有限预算内聪明吃麦当劳的 AI Skill——基于麦当劳 MCP（Model Context Protocol）构建，运行于腾讯 WorkBuddy 智能体平台。

一句话：**你出预算，它负责让每一块钱都吃到嘴里。**

## 这是什么

`mcd-budget-order` 是一个 WorkBuddy Skill，把麦当劳 MCP 的 15 个工具编排成一条完整的「预算内点单」工作流。你说一句"帮我用 100 元订 2 人份的麦当劳"，它会：

1. **就近门店查询**——根据你的位置找到最近的麦当劳
2. **堂食/外送智能建议**——门店步行 1 公里内主动推荐到店自取，帮你省下约 8–9 元配送费
3. **一键领券**——自动领取"麦麦省"全部可领优惠券，并核对门店可用券
4. **预算内配餐**——按人均预算档位（20/30/40/50 元档）智能搭配，套餐优先、用券优先
5. **双重确认后下单**——方案确认 + 价格确认两道关卡，绝不误下单；创建订单后引导支付
6. **支付跟踪**——支付完成后自动查询订单状态

## 目标用户

- 预算敏感的打工人：午饭就 30 块，也想吃得体面
- 帮团队订餐的组织者：按人头和预算快速出方案
- 麦当劳 MCP / AI Agent 开发者：作为 MCP 工具编排的参考实现
- 家长：给孩子定的零花钱额度内完成点单，不多不少

## 安装方法

### 前置条件

- [腾讯 WorkBuddy](https://www.workbuddy.cn) 客户端，并在连接器中启用**麦当劳 MCP**
- （可选）GitHub CLI / git，用于获取本仓库

### 安装 Skill

```bash
# 克隆仓库
git clone https://github.com/Ximinhan/mcd-budget-order.git

# 将 skill 目录复制到 WorkBuddy 用户级 skills 目录
mkdir -p ~/.workbuddy/skills
cp -r mcd-budget-order/skill ~/.workbuddy/skills/mcd-budget-order
```

重启 WorkBuddy 会话后，skill 即可被自动触发。

## 使用示例

```
你：帮我用 100 元订一份 2 人份的麦当劳

AI：好的。请问送到哪个地址？（或告诉我你的大致位置，
    附近有门店的话可以帮你省下配送费）

你：上海徐汇区漕河泾开发区

AI：最近的门店是「麦当劳漕河泾店」，步行约 600 米 / 8 分钟。
    建议到店自取——省下 9 元配送费，预算能多点一份麦乐鸡。
    您选到店自取，还是外卖送到家？

你：到店自取吧

AI：已自动领取 5 张"麦麦省"优惠券。按人均 50 元预算，
    推荐搭配：① 巨无霸套餐（券后 33 元）×2 ……
    实付合计 78 元，含餐盒费。确认后帮您下单？
```

更多对话细节与工作流定义见 [`skill/SKILL.md`](skill/SKILL.md)，MCP 工具编排详见 [`MCP_INTEGRATION.md`](MCP_INTEGRATION.md)。

## 项目结构

```
mcd-budget-order/
├── README.md                  # 本文件
├── CONTEST_DECLARATION.md     # 参赛声明（原创性 / 合规性 / 敏感信息）
├── MCP_INTEGRATION.md         # 麦当劳 MCP 集成说明
├── mcp-config.example.json    # 脱敏 MCP 配置示例
├── workbuddy.md               # WorkBuddy 创作上下文（专项奖励材料）
├── LICENSE
└── skill/
    ├── SKILL.md               # Skill 主文档：工作流、确认关卡、易错点
    └── references/
        └── mcd-tools.md       # 麦当劳 MCP 15 个工具速查手册
```

## 参赛信息

本项目参加 **麦当劳程序员创意开发大赛**（2026.10.09 – 2026.10.25）。

- 诚实声明：本项目由作者韩工主导，在腾讯 WorkBuddy 智能体辅助下完成开发，详见 [workbuddy.md](workbuddy.md) 与 [CONTEST_DECLARATION.md](CONTEST_DECLARATION.md)。

## License

[MIT](LICENSE)
