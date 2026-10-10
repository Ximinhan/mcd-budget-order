# 麦当劳 MCP 集成说明（MCP_INTEGRATION）

## 一、使用的 MCP Server

| 项目 | 说明 |
|---|---|
| Server | 麦当劳官方 MCP Server（mcd-mcp） |
| 接入方式 | 腾讯 WorkBuddy 连接器（内置托管接入，用户在连接器菜单授权启用） |
| 能力范围 | 配送地址管理、门店查询、优惠券、餐品查询、计价、下单、订单跟踪、团餐促销 |

## 二、使用的 Tool 清单（共 15 个）

### 地址与门店

| Tool | 用途 | 关键入参 |
|---|---|---|
| `delivery-query-addresses` | 查询用户已保存的配送地址列表 | 无 |
| `delivery-create-address` | 新建配送地址（6 项必填） | city, contactName, gender, phone, address, addressDetail |
| `delivery-query-stores` | 按地址查可配送门店 | addressId, beType(2/6) |
| `query-nearby-stores` | 按位置搜可自提/得来速门店 | searchType, beType(1/5), city, keyword |

### 优惠券

| Tool | 用途 |
|---|---|
| `auto-bind-coupons` | 一键领取"麦麦省"当前全部可领优惠券 |
| `available-coupons` | 查询当前可领取的券列表（领取前预览） |
| `query-store-coupons` | 查指定门店+订单类型可用券 |

### 餐品与计价

| Tool | 用途 | 关键入参 |
|---|---|---|
| `query-meals` | 查门店在售餐品（价格、productCode） | storeCode, orderType, beType |
| `query-meal-detail` | 查餐品组成与特调选项 | + code |
| `calculate-price` | 含券计价（返回价格单位为"分"） | items[]（含 couponId/couponCode） |
| `create-order` | 创建订单，返回支付链接 | 外送必传 addressId；到店必传 takeWayCode |

### 订单与辅助

| Tool | 用途 |
|---|---|
| `query-order` | 查询订单状态（支付完成/失败后跟踪） |
| `order-list` | 查询历史订单 |
| `query-promotions` | 团餐场景查满减/满折规则 |
| `now-time-info` | 获取当前时间（营业时段判断） |

## 三、核心调用流程

本项目的主要创新在于**对工具调用顺序的编排**与**业务规则的定义**（确认关卡、预算红线、省钱策略），完整工作流定义见 [skill/SKILL.md](skill/SKILL.md)。

```
用户输入："用 100 元订 2 人份麦当劳，我在漕河泾"
    │
    ├─ ① now-time-info                      # 营业时段判断
    ├─ ② query-nearby-stores (beType=1)     # 最近自提门店 → 就近堂食建议
    │       └─ 门店步行 ≤1km → 建议到店自取，省配送费，用户选择就餐方式
    ├─ ③ delivery-query-addresses /         # 外送场景：确认或新建配送地址
    │   delivery-create-address             # （须用户确认地址后继续）
    ├─ ④ delivery-query-stores (beType=2)   # 按地址查可配送门店，用户确认门店
    ├─ ⑤ auto-bind-coupons                  # 一键领券
    │   query-store-coupons                 # 核对门店可用券
    ├─ ⑥ query-meals                        # 在售餐品 → 按人均预算档位配餐
    │       └─ ★ 方案确认（第一道用户确认）
    ├─ ⑦ calculate-price（带券计价）          # 实付总额 ≤ 预算红线
    │       └─ ★ 价格确认（第二道用户确认）
    ├─ ⑧ create-order                       # 创建订单 → 输出支付链接（不自动支付）
    └─ ⑨ query-order                        # 支付后跟踪订单状态
```

### 关键业务规则（本项目的编排创新点）

1. **付费双重确认**：create-order 前必须经过"方案确认"与"价格确认"两道用户显式确认。
2. **预算红线**：以 calculate-price 实付总额（含配送费/餐盒费，分转元）判定预算，超线自动回到配餐环节调整，绝不硬下单。
3. **就近堂食建议**：最近门店步行 ≤1 公里 / 15 分钟时主动建议到店自取，省配送费 → 同预算多吃一份。
4. **beType 切换防错**：外送↔到店切换后 storeCode/beCode 不通用，必须重新查门店。
5. **特调项 key 规则**：modification 中选中项用 selectedKey，未选项 unselectedKey 非空时也必须传参。

## 四、业务价值

| 用户痛点 | 本项目的解法 |
|---|---|
| "就 30 块钱能吃什么"——人工凑单费时 | 按人均预算档位自动配餐，秒级出方案 |
| 券太多不知道怎么用 | 一键领券 + 门店券核对 + 配餐时自动用券 |
| 外送配送费侵蚀预算 | 就近堂食建议，把配送费省成一份小食 |
| AI 下单怕误操作 | 双重确认 + 预算红线 + 不自动支付，安全可控 |
| 到店/外送信息来回切换 | 一条对话内完成方式决策、门店、配餐、下单全流程 |
