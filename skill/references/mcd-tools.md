# 麦当劳 MCP 工具速查（mcp__mcd-mcp__*）

> 价格字段单位均为"分"，展示给用户前除以 100。

## 地址

### delivery-query-addresses
查询用户已保存的配送地址列表。无入参。返回 addressId、联系人、城市、地址等。

### delivery-create-address
新建配送地址。全部必填，不得用占位值：
- `city`（如"上海市"）、`contactName`、`gender`（先生/女士）
- `phone`（11 位纯数字，1 开头）
- `address`（小区/大厦）、`addressDetail`（门牌号）

## 门店

### delivery-query-stores
外送场景按地址查可配送门店。入参：`addressId`（来自 delivery-query-addresses）、`beType`（2=麦乐送，6=团餐）。返回 storeCode、beCode、门店名、距离、配送费。

### query-nearby-stores
到店场景查门店。入参：`searchType`（2=按位置搜，1=收藏）、`beType`（1=自提，5=得来速）；searchType=2 时还需 `city` + `keyword`。beType=5 返回 beCode，后续必传；beType=1 无 beCode。返回 storeCode、距离、reservationTimeOptions（预约用）。

## 优惠券

### auto-bind-coupons
一键领取麦麦省当前全部可领优惠券。无入参。配餐前先调它。

### available-coupons
查询当前可领取的券列表（领取前预览用）。无入参。

### query-store-coupons
查指定门店+订单类型可用券。入参：`storeCode`、`orderType`、`beType`，beCode 按 SKILL.md 对照表传；预约场景传 `reservationDate`（yyyy-MM-dd HH:mm）。

## 餐品

### query-meals
查门店在售餐品列表（含价格、productCode）。入参：`storeCode`、`orderType`、`beType`（+ beCode 按对照表）；预约场景传 `reservationDate`。

### query-meal-detail
查餐品组成/特调选项。入参同上 + `code`（餐品编码）。注意：
- `supportModify=true` 标注【可特调】。
- 特调组内：选中项 key=selectedKey；未选项 unselectedKey 非空时也必须传入（key=unselectedKey）。
- 不主动展开特调列表。

## 计价与下单

### calculate-price
计价（含优惠）。入参：`storeCode`、`orderType`、`beType`（+ beCode）、`items[]`：
- `productCode`、`quantity`（≥1）
- `couponId` / `couponCode`（用券时）
- `modification`（特调，见上）
- `roundList`（套餐子项，来自 query-meal-detail）
可选：`needTableware`、`gmServiceCode`（团餐）、`withOrder`（随单购）。

返回：各商品实付、优惠明细、总价（分）；到店场景返回 `takeWayList[]`（下单取 takeWayCode 用）。

### create-order
创建订单。入参：`storeCode`、`orderType`、`beType`（+ beCode 按对照表）：
- 外送/团餐：必传 `addressId`；可传 `remark`（≤50 字）、`needTableware`
- 到店/得来速：必传 `takeWayCode`（来自 calculate-price）
- `items[]`：与 calculate-price 一致（含券、特调）
- 团餐必传 `gmServiceCode`

返回支付链接。**不自动支付**，引导用户扫码/APP 支付。

## 订单

### query-order
查订单详情/状态。入参：`orderId`。用户说"支付完成/失败"后调它同步状态。

### order-list
查历史订单（非商城订单）。无入参。

## 团餐专用

### query-meal-assistance
团餐场景查助餐服务，返回 gmServiceCode（calculate-price/create-order 必传）。

### query-promotions
查门店团餐满减/满折规则。入参固定：`storeCode`、`orderType=2`、`beType=6`（+ beCode）。promotionType：31=满额减，33=订单折扣；ruleCategory：40=满减，30=满折。据此优化搭配方案。

## 时间

### now-time-info
获取服务器当前时间（时间戳、格式化时间）。用于营业时段判断与预约场景。
