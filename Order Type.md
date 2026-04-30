Exchange Order

## Order Type

- Market 市价

- Limit 限价

- TWAP time-weighted average price 时间加权平均价格

- BLOCK 大宗交易

- VWAP volume-weighted average price 成交量加权平均价格

- STOP_LIMIT 止损限价单

### Stop_Limit

A stop-limit order is a conditional trade over a set time frame that combines the features of stop with those of a limit order and is used to mitigate risk. It is related to other order types, including limit orders (an order to either buy or sell a specified number of shares at a given price or better) and stop-on-quote orders (an order to either buy or sell a security after its price has surpassed a specified point).

## 1. 举例说明

**买入方向**

如果您在市场交易价格为 10 时提交了一份止损触发价为 15，订单价格为 16 的买入止损限价单，该订单会在标的当前市价达到 15 或以上时，自动以限价单提交至上游，则订单有可能以 16 或更优的价格成交。

**卖出方向**

假设您持有一只标的，成本价为 10。为了避免该股票在未来大幅下滑，用户在市价为 20 时提交了一份止损触发价为 15，订单价格为 14 的卖出止损限价单。如果此时股票交易价格降至 15 或以下时，自动以限价单提交至上游，则订单有可能以 14 或更优的价格成交。
