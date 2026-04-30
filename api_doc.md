# api

###this file shows exchange's API standard returned data format
####*last_update_time:2019-01-08*
####*update by: yyy*

### Market Data Channel Formats:

```
Exchange|symbol|contract_type|data_type|param1|param2
e.g
orderbook:OKcoin|OKBBTC|spot|orderbook|20
trade:    OKcoin|OKBBTC|spot|trade
kline:    OKcoin|OKBBTC|spot|kline|5min|200


Currently param1 supports orderbook depth and kline range.
Param2 suports kline size and will suport aggregation type in the future.
```

### Market Data Formats:

##### MARKET_DATA.ORDERBOOK

    the format below apply to both REST and WS api

##### api note

    all the time data use format 'YYYYMMDDHHmmssSSS'
    you can transfer use moment().format("YYYYMMDDHHmmssSSS");

##### MARKET_DATA.ORDERBOOK

```javascript
function _on_request_to_get_orderbook(symbol) {
    let orderbook_market_data_event=
        {
            exchange: this.name,
            symbol: symbol,
            contract_type: CONTRACT_TYPE,
            data_type: MARKET_DATA.ORDERBOOK,
            metadata: metadata,
            update_type: ORDERBOOK_UPDATE_TYPE.SNAPSHOT,
            subscribed_orderbook_depth: this.api_config.orderbook_max_depth,
            timestamp: utils._util_get_human_readable_timestamp()
        };
    global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, orderbook_market_data_event);
}
```

    response.metadata format
    
    spot:
    metadata = {
        "bids" : [[price , amount]], 
        "asks" : [[price , amount]], 
        "timestamp": YYYYMMDDHHmmssSSS
    }
    
    future:
    metadata = {
        "bids" : [[price , amount in contracts, amount in coins]], 
        "asks" : [[price , amount in contracts, amount in coins]], 
        "timestamp": YYYYMMDDHHmmssSSS
    
        }
    
    orderbook note: 
    timestamp = metadata.timestamp
    update_type = quote_stream when exchange return quote data,else snapshot

##### MARKET_DATA.TRADE

```javascript
function _on_request_to_get_trade(symbol) {
     let price_market_data_event = {
         exchange: this.name,
         contract_type: CONTRACT_TYPE,
         symbol: symbol,
         data_type: MARKET_DATA.TRADE,
         metadata: metadata,
         timestamp: utils._util_get_human_readable_timestamp()
        };
     global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, price_market_data_event);                
}
```

    response.metadata format
    
    spot:
    metadata = {
        [[id, moment(timestamp).format("YYYYMMDDHHmmssSSS"), price, side, size]]
    }
    
    future:
    metadata = {
         [[id, moment(timestamp).format("YYYYMMDDHHmmssSSS"), price, side, size, size in coin]]
    }

##### MARKET_DATA.PRICE

```javascript
function _on_request_to_get_latest_price_via_rest(symbol, contract_type){
    let price_market_data_event={
            exchange: this.exchange,
            symbol: symbol,
            contract_type: contract_type,
            data_type: MARKET_DATA.PRICE,
            metadata:
                {
                    result: true,
                    last_price: 5514.11,
                    time: '20190114165048000'
                },
            timestamp: utils._util_get_human_readable_timestamp()
        };
        global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, myevent);   
}
```

##### MARKET_DATA.KLINE

- 订阅: `"Binance|BTCUSD|perp|kline||1m"`  最后一位为分时格式为“1m”，参数分别为：m -> 分钟; h -> 小时; d -> 天; w -> 周; M -> 月 。

```javascript
let kline = {
        exchange: this.name,
        symbol: info.symbol,
        contract_type: info.contract_type,
        data_type: MARKET_DATA.KLINE,
        metadata: {
            close: parseFloat(msg.data.PrzClose),      //收盘价
            high: parseFloat(msg.data.PrzHigh),        //最高价
            low: parseFloat(msg.data.PrzLow),        //最低价
            open: parseFloat(msg.data.PrzOpen),        //开盘价
            turnover: parseFloat(msg.data.Turnover),//成交额
            volume: parseFloat(msg.data.Volume),    //成交量
            timestamp: utils._util_convert_date_to_timestamp(msg.date),
            type: msg.data.Typ         //分时：1m、5h
        },
    timestamp: utils._util_get_human_readable_timestamp()
};
global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, kline);
```

- 请求方法  `_on_request_to_get_kline_via_rest()`

```javascript
function _on_request_to_get_kline_via_rest(symbol,type) {
     let kline_market_data_event = {
         exchange: this.name,
         contract_type: CONTRACT_TYPE,
         symbol: symbol,
         data_type: MARKET_DATA.KLINE,
         metadata:  {
             open,  //开盘价
             close, //收盘价
             high, //最高价
             low, //最低价
             volume, //成交量
             turnover, //成交额
             type  //kline类型  1m,5m, ...
         },
         timestamp: utils._util_get_human_readable_timestamp()
        };
     global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, kline_market_data_event);                
}
```

    kline note:
    range = "1min","5min","15min"……

##### MARKET_DATA.QUOTE

```javascript
function _on_request_to_get_quote(symbol) {
     let quote_market_data_event = {
         exchange: this.name,
         contract_type: CONTRACT_TYPE.SPOT,
         symbol: symbol,
         data_type: MARKET_DATA.QUOTE,
         update_type: ORDERBOOK_UPDATE_TYPE.QUOTE_STREAM,
         metadata: metadata,
         timestamp: utils._util_get_human_readable_timestamp()
        };
     global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, quote_market_data_event);                
}
```

    response.metadata.format
    
    spot:
    metadata = {
        [[side, price, count, size]]
    }

​    
​    future:
​    metadata = {
​        [[side, price, count, size in contracts, size in coins]]
​    }
​    
​    quote note:
​        count means how many seperate orders are at this price. if zero delete the price from the orderbook.
​        size means the updated size on this price for the orderbook,not delt size.
​        side is bid or ask

##### MARKET_DATA.INDEX

```javascript
function _on_request_to_get_index(symbol) {
     let index_market_data_event = {
         exchange: this.name,
         contract_type: CONTRACT_TYPE.SPOT,
         symbol: symbol,
         data_type: MARKET_DATA.INDEX,
         metadata: {
             index:price,
             timestamp: moment(timestamp).format("YYYYMMDDHHmmssSSS")
         },
         timestamp: utils._util_get_human_readable_timestamp()
         };
      global.EventHandler.emit(EVENT.ON_MARKET_DATA_ORIGIN, index_market_data_event);                
 }
```

##### MARKET_DATA.QUOTESTREAM

```javascript
response:
let response = {
    exchange: this.name,
    symbol: symbol,
    contract_type: CONTRACT_TYPE.SPOT,
    direction: direction,
    data_type: MARKET_DATA.QUOTESTREAM,
    event: REQUEST_ACTIONS.GET_QUOTE,
    metadata: metadata,
    timestamp: utils._util_get_human_readable_timestamp()
}
response.metadata format:
let metadata = {
    result: true,
    account_id: account_id,
    bids:  [[price,quantity]],
    asks:  [[pice,quantity]],
    quantity: quantity
}
```

##### MARKET_DATA.RATE

```javascript
response:
let response = {
    exchange: this.name,
    contract_type: contract_type,
    symbol: symbol,
    data_type: MARKET_DATA.RATE,
    metadata: metadata,
    timestamp: utils._util_get_human_readable_timestamp()
};
response.metadata format:
let metadata = {
    rate: parseFloat(pjdata.data[0].funding_rate),
    next_rate: parseFloat(next_rate),
    result: true,
    estimated_daily_rate: parseFloat(pjdata.data[0].funding_rate) * 2,
    timestamp: utils._util_get_human_readable_timestamp()
```

##### MARKET_DATA.LIQUIDATION

```javascript
response:
let response = {
    exchange: this.name,
    symbol: symbol,
    contract_type: contract_type,
    data_type: MARKET_DATA.LIQUIDATION,
    metadata: metadata,
    timestamp: utils._util_get_human_readable_timestamp()
};
response.metadata format:
let metadata = [
    {
        order_id: item["orderID"],
        quantity: parseFloat(item["size"]),
        price: parseFloat(item["price"]),
        created_time: moment(item["created_at"]).format("YYYYMMDDHHmmssSSS");
        direction: direction
    }]
```

##### MARKET_DATA.QUOTETICKER

```javascript
response:
let response = {
    exchange: this.name,
    contract_type: CONTRACT_TYPE.SPOT,
    symbol: symbol,
    data_type: MARKET_DATA.QUOTETICKER,
    metadata: [[symbol,quantity]],
    timestamp: utils._util_get_human_readable_timestamp()
};
```

##### MARKET_DATA.FUNDING

```javascript
let response = {
    exchange: this.name,
    data_type: MARKET_DATA.FUNDING,
    symbol: symbol,
    contract_type: CONTRACT_TYPE.SPOT,
    timestamp: moment().format("YYYYMMDDHHmmssSSS"),
    metadata: metadata
}
response.metadata format:
let metadata = {
    "bids": [[rate, amount]],
    "asks": [[rate, amount]]
}
```

##### MARKET_DATA.HOLDAMOUNT

```javascript
response:
let response = {
    exchange: this.name,
    contract_type: CONTRACT_TYPE,
    symbol: symbol,
    data_type: MARKET_DATA.HOLDAMOUNT,
    metadata: metadata,
    timestamp: utils._util_get_human_readable_timestamp()
};
response.metadata format:
let metadata = {
    holdamount: parseFloat(jdata["data"][0].openInterest),
    time: moment(jdata["data"][0].timestamp).format("YYYYMMDDHHmmssSSS")
}
```

### Private Data Formats:

## Trading

* Please emit trading request to strategy engine and subscribe to the response channel.  

##### send_order

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: ORDER_ACTIONS.SEND,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: contract_type,
        direction: "buy", 
        price: 5000.0,
        quantity: 1.0,
        order_type: "limit",
        post_only: false, //optional
        delay: 0, //optional
        account_id: this.account_id,
        lever_rate: undefined, //optional
        account_type: "margin" //optional
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

    send order request note:
    Currently order_type supports "limit", "market" and "fak".
    For limit and market orders we will use the default API function provided by the exchange.
    For fak orders, if exchange provides the fak function and the request does not include delay (or delay is specified to be zero), we will use the fak function provided by the exchange; otherwise we will use synthetic methods to create fak orders (by default send order first, cancel the order after the delay and then inspect the order, return the inspect order response).
    Post_only means the order will be rejected if it turns out to be taker, so the order will always apply the maker fee if done. Currently only Bitmex, Bitfinex and Binance support it. The send order response, however, will still return "result : true" even if the order gets rejected because it becomes taker. But the order status will automatically become "cancelled" if you inspect the order in that csae.

```javascript
function _send_order_via_rest(symbol, contract_type, direction, price, quantity, order_type, lever_rate, account_id, account_type, agentid = 0){
    try{
        //others code
        if(true){
            let cxl_resp ={
                exchange: this.name,
                symbol: symbol,
                contract_type: contract_type,
                event: ORDER_ACTIONS.SEND,
                metadata: {
                    account_id: this.account_id,
                    result: true,
                    order_id: "123456" //str
                },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return cxl_resp;        
        }else{
            let cxl_resp ={
                exchange: this.name,
                symbol: symbol,
                contract_type: contract_type,
                event: ORDER_ACTIONS.SEND,
                metadata: {
                    account_id: account_id,
                    result: false,
                    order_id: 0,
                    error_code: 999999,
                    error_code_msg: body["err-msg"]
                },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return cxl_resp;          
        }
    }catch (ex) {
         logger.error(ex.ex.toString());
         let cxl_resp = {
             exchange: this.name,
             symbol: symbol,
             contract_type: contract_type,
             event: ORDER_ACTIONS.SEND,
             metadata: {
                 account_id: account_id,
                 result: false,
                 order_id: 0,
                 error_code: 999999,
                 error_code_msg: ex.toString()
             },
             timestamp: utils._util_get_human_readable_timestamp()
         };
         return cxl_resp;
     }
}
```

##### cancel_order

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: ORDER_ACTIONS.CANCEL,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: contract_type,
        order_id: "123456",
        account_id: "act_test"
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _cancel_order_via_rest(symbol, contract_type, oId, account_id) {
    try{
        //other codes
        let body ={};
        if(true){
            body["result"] = true;
            body["status"] = ORDER_STATUS.CANCELLED;
            body.account_id = account_id;
            body.order_id = oId;
            let cxl_resp ={
                exchange: this.exchange,
                symbol: symbol,
                contract_type: contract_type,
                order_id:oId,//the order_id field is returned in cxl_resp and cxl_resp.metadata
                event: ORDER_ACTIONS.CANCEL,
                metadata:body,
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return cxl_resp;
        }else{
            throw new Error("msg")
        }
    } catch (ex) {
       logger.error(ex.toString());
       let cxl_resp = {
           exchange: this.name,
           symbol: symbol,
           contract_type: contract_type,
           order_id: oId,//the order_id field is returned in cxl_resp and cxl_resp.metadata
           event: ORDER_ACTIONS.CANCEL,
           metadata: {
               account_id: account_id,
               result: false,
               order_id: oId, //the order_id field is returned only in metadata
               error_code: 999999,
               error_code_msg: ex.toString()
           },
           timestamp: utils._util_get_human_readable_timestamp()
       };
       return cxl_resp;
    }
}
```

##### cancel_all_order

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: ORDER_ACTIONS.CANCEL_ALL,
    metadata: {
      exchange: "Bitfinex",
      symbol: "BTCUSD",
      contract_type: contract_type,
      account_id: "act_test"
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _cancel_all_order_via_rest(symbol, contract_type, account_id) {
    try{
        let cxl_resp ={
            exchange: this.exchange,
            symbol: symbol,
            contract_type: contract_type,
            event: ORDER_ACTIONS.CANCEL_ALL,
            metadata:{
              result: true,
              account_id: this.account_id,
              orders:[ //success list
                  {
                      order_id: "123456",
                      symbol:symbol,
                      contract_type:contract_type
                  }
              ],
              fail:[ //failed list
                  {
                      order_id:"45678",
                      symbol:symbol,
                      contract_type:contract_type
                  }
              ]
            },
            timestamp: utils._util_get_human_readable_timestamp()
            };
            return cxl_resp;
    }catch (ex) {
        logger.error(ex.toString());
        let cxl_resp = {
         exchange: this.name,
         symbol: symbol,
         contract_type: contract_type,
         event: ORDER_ACTIONS.CANCEL_ALL,
         metadata: {
             account_id: account_id,
             result: false,
             error_code: 999999,
             error_code_msg: ex.toString()
         },
         timestamp: utils._util_get_human_readable_timestamp()
        };
        return cxl_resp;
    }
}
```

##### inspect_order

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: ORDER_ACTIONS.INSPECT,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: "spot",
        order_id: "123456",
        account_id: "act_test"
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function inspect_order(symbol, contract_type, order_id, account_id) {
    try {
        if(true){
            let myevent ={
                exchange: this.exchange,
                symbol: symbol,
                contract_type: contract_type,
                event: ORDER_ACTIONS.INSPECT,
                metadata: {
                    result: true,
                    account_id: this.account_id,
                    order_id: "123456", //str
                    others: "some other items" //others optional
                },
                order_info: {
                    original_amount: 1, //float
                    avg_executed_price: 0.0, //float
                    filled: 0, //float
                    status: 'new',
                },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return myevent;
        }else{
            throw new Error(body.message);
        }      
    }catch (ex) {
         logger.error(ex.toString());
         let order_info = {
             original_amount: 0,
             filled: 0,
             avg_executed_price: 0,
             status: 'unknown'
         };
         let myevent = {
             exchange: this.name,
             symbol: symbol,
             contract_type: contract_type,
             event: ORDER_ACTIONS.INSPECT,
             metadata: {
                 account_id: account_id,
                 order_id: order_id,
                 result: false,
                 error_code: 999999,
                 error_code_msg: ex.toString()
             },
             timestamp: utils._util_get_human_readable_timestamp(),
             order_info: order_info
         };
         return myevent;
    }
}
```

    note:
    before today(2019-01-15) order_info.avg_executed_price may has three kind value:null,0.0,15.5(normal price)
    null means a order filled is 0,
    after today ,null will be abandoned,use 0.0 to replace it

##### active_orders

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_ORDERS,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: contract_type,
        account_id: "act_test"
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_get_active_order_via_rest(symbol,contract_type, account_id) {
    try{
        if(true){
            let openorder={
                exchange: this.exchange,
                symbol: symbol,
                contract_type: CONTRACT_TYPE.SPOT,
                event: REQUEST_ACTIONS.QUERY_ORDERS,
                metadata:{
                    account_id: this.account_id,
                    result: true,
                    orders:
                        [ { original_amount: 1,
                            avg_executed_price: 0.0, //optional for those exchanges that does not return this info.
                            filled: 0,
                            status: 'new',
                            direction: 'buy',
                            fee: 0, //optional
                            price: 5000,
                            order_type: 'limit', //optional
                            create_time: '20190114165048000',
                            order_id: '441545654000' } ] },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            global.EventHandler.emit("ON_X", openorder);
            return openorder;
        }else {
            throw new Error(body.message);
        }
    } catch (ex) {
         logger.error(ex.toString());
         let cxl_resp = {
             exchange: this.name,
             symbol: symbol,
             contract_type: CONTRACT_TYPE.SPOT,
             event: REQUEST_ACTIONS.QUERY_ORDERS,
             metadata: {
                 account_id: account_id,
                 result: false,
                 error_code: 999999,
                 error_code_msg: ex.toString()
             },
             timestamp: utils._util_get_human_readable_timestamp()
         };
         global.EventHandler.emit("ON_X", cxl_resp);
         return cxl_resp;
     }
}
```

##### history_orders

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_HISTORY_ORDERS,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: contract_type,
        account_id: "act_test",
        size: 200, //optional
        account_type: "margin",//optional
        start_date: "2018-01-01",//optional,
        end_date: "2018-12-01"
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_get_history_orders_via_rest(symbol, contract_type, account_id, size, account_type, start_date, end_date, options) {
    try{
        if(true){
            let myevent={
                exchange: this.name,
                symbol: symbol,
                contract_type: contract_type,
                event: REQUEST_ACTIONS.QUERY_HISTORY_ORDERS,
                metadata: {
                    result:true,
                    account_id: this.account_id,
                    orders:[
                        {
                            symbol: "BTCUSD", //str,upper case
                            order_id: "1745565685452", //str
                            status: ORDER_STATUS.PARTIALLY_FILLED, //str
                            original_amount : 21, //float
                            avg_executed_price: 7548, //float,optional
                            price: 7548, //float
                            filled: 10, //float
                            fee: 12, //float,optional
                            lever_rate: 10, //int,optional
                            direction: "sell", //str,low case
                            create_time : moment(timestamp).format("YYYYMMDDHHmmssSSS"), //str
                            order_type: "limit" //str,low case
                        },
                    ]
                },
                timestamp:  utils._util_get_human_readable_timestamp()
            };
            return myevent;
        }else{
            throw new Error(body.message);
        }
    }catch (ex) {
        let myevent = {
            exchange: this.name,
            symbol: symbol,
            contract_type: contract_type,
            event: REQUEST_ACTIONS.QUERY_HISTORY_ORDERS,
            metadata: {
                account_id: account_id,
                result: false,
                error_code: 999999,
                error_code_msg: ex.toString()
                },
            timestamp: utils._util_get_human_readable_timestamp()
         };
        return myevent;
    }
}
```

##### history_trades

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_HISTORY_TRADES,
    metadata: {
        exchange: "Bitfinex",
        symbol: "BTCUSD",
        contract_type: "spot",
        account_id: "act_test",
        size: 200, //optional
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_get_history_trades_via_rest(symbol, contract_type, account_id, size) {
    try{
        if(true){
            let myevent={
                exchange: this.name,
                symbol: symbol,
                contract_type: contract_type,
                event: REQUEST_ACTIONS.QUERY_HISTORY_TRADES,
                metadata: {
                    result:true,
                    account_id: this.account_id,
                    trades:[
                        {
                            order_id: "1745565685452", //str,if has, in has not , order_id === null
                            trade_id:"1235",
                            price: 7548, //float
                            filled: 10, //float
                            fee: 12, //float if has not , fee === null
                            direction: "sell", //str,low case
                            datetime : moment(timestamp).format("YYYYMMDDHHmmssSSS"), //str , indicted trade datetime
                        },
                    ]
                },
                timestamp:  utils._util_get_human_readable_timestamp()
            };
            return myevent;
        }else{
            throw new Error(body.message);
        }
    }catch (ex) {
        let myevent = {
            exchange: this.name,
            symbol: symbol,
            contract_type: contract_type,
            event: REQUEST_ACTIONS.QUERY_HISTORY_TRADES,
            metadata: {
                account_id: account_id,
                result: false,
                error_code: 999999,
                error_code_msg: ex.toString()
                },
            timestamp: utils._util_get_human_readable_timestamp()
         };
        return myevent;
    }
}
```

##### withdraw_history

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_HISTORY_TRADES,
    metadata: {
        exchange: "Bitfinex",
        currency: "BTC",
        account_id: "act_test",
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_query_withdrawals_history_via_rest(currency, account_id) {
    try{
        if(true){
            let my_event ={ 
                exchange: this.exchange,
                currency: currency,
                event: REQUEST_ACTIONS.QUERY_WITHDRAWALS,
                metadata:{ 
                    result: true,
                    account_id: account_id,
                    records:[
                        {
                         target_address: '0x4c74d3a18c947843700xxxxxxxxxxxxxxxxxx',//str
                         amount: 0.3975,//float
                         fee: 0,//float
                         create_time: moment(timestamp).format("YYYYMMDDHHmmssSSS"), //ms
                         status: 'succeed' 
                      }] 
                },
                timestamp: utils._util_get_human_readable_timestamp() 
                }
        }else{
            throw new Error(body.toString())
        }
    }catch (ex){
        logger.error(ex.toString())
        let myevent = {
        exchange: this.name,
        currency: currency,
        event: REQUEST_ACTIONS.QUERY_WITHDRAWALS,
        metadata: {
            result: false,
            error_code: 999999,
            error_code_msg: ex.toString()},
        timestamp: utils._util_get_human_readable_timestamp()};
        return myevent;
    }
}
```

## Position Data

* Please emit a query position/balance/margin request if only one time update is needed.  
* Please emit a position subscription poll position request if regular updates on position are needed. Then subscribe to the corresponding position data channel. (For OKex need to define subscribe_position_symbols in apiconfig first, for Bitmex need to define subscribe_position_symbols and symbolPositionMap in apiconfig first.)

##### spot_balancce

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_HISTORY_TRADES,
    metadata: {
        exchange: "Bitfinex",
        account_id: "act_test",
        currency: ["BTC","ETH"]
    }
};
intercom.emit('Demo_request', request, INTERCOM_SCOPE.TRADE);
```

    balance note:
    if currency input "" or [],api will return all currency balance;
    curreny only support "",[],["BTC","USD"...] these kind of format

```javascript
function _on_request_to_update_spot_balance_via_rest(currency,account_id) {
    let jdata = {};
  try{
      if(true){
          let spot_balance_update_event=
              {
                  exchange: this.name,
                  posInfoType: POSITION_INFO_TYPE.SPOT_BALANCE,
                  metadata:
                      {
                          account_id: this.account_id,
                          result: true,
                          ETH: {
                              available: 1.9680406133808,
                              reserved: 0.04,
                              shortable: 0,
                              total: 2.0080406133807998
                          },
                          //others
                   },
                  timestamp:  utils._util_get_human_readable_timestamp()
              };
          global.EventHandler.emit(EVENT.ON_POSITION, spot_balance_update_event);
          return spot_balance_update_event;
      }else{
        throw new Error(`${this.name}:fail to get balance from exchange`)
      }
  }catch (ex) {
    let spot_balance_update_event = {
        exchange: this.name,
        posInfoType: POSITION_INFO_TYPE.SPOT_BALANCE,
        metadata: {
           account_id: account_id,
           result: false,
           error_code: 999999,
           error_code_msg: ex.toString()
           },
        timestamp: utils._util_get_human_readable_timestamp()
    };
    return spot_balance_update_event;
       }

}
```

##### spot_position

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_POSITION,
    metadata: {
        exchange: "Deribit",
        account_id: "act_test",
        symbol: "BTCUSD" //must
    }
};
intercom.emit(strategy_name + '_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_update_spot_position_via_rest(symbol,account_id) {
    let jdata = {};
  try{
      if(true){
          let spot_position_update_event=
              {
                  exchange: this.name,
                  posInfoType: POSITION_INFO_TYPE.SPOT_POSITION,
                  metadata:
                      {
                          account_id: this.account_id,
                          result: true,
                          BTCUSD:
                              {
                                available: 2,
                                total: 2,
                                reserved: 0,
                                shortable: 0,
                                //some others 
                             },
                          BTC:
                              {
                                available: 2,
                                total: 2,
                                reserved: 0,
                                shortable: 0
                              }
                   },
                  timestamp:  utils._util_get_human_readable_timestamp()
              };
          global.EventHandler.emit(EVENT.ON_POSITION, spot_position_update_event);
          return spot_position_update_event;
      } else{
        jdata.account_id = account_id;
        jdata.result = false;
        jdata.error_code = 999999;
        jdata.error_code_msg = `${this.name}:fail to get position from exchange`;
        let event = {
            exchange: this.name,
            posInfoType: POSITION_INFO_TYPE.SPOT_POSITION,
            metadata: jdata,
            timestamp: utils._util_get_human_readable_timestamp()
        };
        return event;
      }
  }catch (ex) {
    let event = {
        exchange: this.name,
        contract_type: contract_type,
        posInfoType: "",
        metadata: {
           account_id: account_id,
           result: false,
           error_code: 999999,
           error_code_msg: ex.toString()
           },
        timestamp: utils._util_get_human_readable_timestamp()
    };
    return event;
  }
  }
```

##### spot_userinfo

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_MARGIN,
    metadata: {
        exchange: "Bitfinex",
        account_id: "act_test",
    }
};
intercom.emit(strategy_name + '_request', request, INTERCOM_SCOPE.TRADE);

function __on_request_to_update_spot_margin_via_rest(account_id) {
    let jdata = {};
  try{
      if(true){
          let spot_margin_update_event=
              {
                  exchange: this.name,
                  posInfoType: POSITION_INFO_TYPE.SPOT_USERINFO,
                  metadata:
                      {
                          account_id: this.account_id,
                          result: true,
                          reserved: 1,
                          toal : 5,
                          available: 4 ,
                          rights: 1, 
                          BTCUSD: 100,
                          ETHUSD: 101 // ≈ ritio_risk   
                   },
                  timestamp:  utils._util_get_human_readable_timestamp()
              };
          global.EventHandler.emit(EVENT.ON_POSITION, spot_margin_update_event);
          return spot_margin_update_event;
      }else{
        jdata.account_id = account_id;
        jdata.result = false;
        jdata.error_code = 999999;
        jdata.error_code_msg = `${this.name}:fail to get margin from exchange`;
        let event = {
            result: true,
            exchange: this.name,
            posInfoType: POSITION_INFO_TYPE.SPOT_USERINFO,
            metadata: jdata,
            timestamp: utils._util_get_human_readable_timestamp()
        };
        return event;
      }
  }catch (ex) {
    let event = {
        exchange: this.name,
        contract_type: contract_type,
        posInfoType: "",
        metadata: {
           account_id: account_id,
           result: false,
           error_code: 999999,
           error_code_msg: ex.toString()
           },
        timestamp: utils._util_get_human_readable_timestamp()
    };
    return event;
  }

}
```

##### future_position

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_POSITION,
    metadata: {
        exchange: "Deribit",
        account_id: "act_test",
        symbol: "BTCUSD" //must
    }
};
intercom.emit(strategy_name + '_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_update_future_position_via_rest(symbol,account_id) {
    let jdata = {};
  try{
      if(true){
          let fut_position_update_event=
              {
                  exchange: this.name,
                  posInfoType: POSITION_INFO_TYPE.FUTURE_POSITION,
                  metadata:
                      {
                          account_id: this.account_id,
                          result: true,
                          BTCUSD_this_week:
                              {
                                  buy_available: 2,
                                  buy_total: 2,
                                  buy_reserved: 0,
                                  buy_shortable: 0,
                                  buy_total_in_coin: 0,
                                  sell_available: 1,
                                  sell_total: 1,
                                  sell_reserved: 0,
                                  sell_shortable: 0 ,
                                  sell_total_in_coin: 0},
                          BTCUSD_next_week:
                              {
                                  buy_available: 0,
                                  buy_total: 0,
                                  buy_reserved: 0,
                                  buy_shortable: 0,
                                  buy_total_in_coin: 0,
                                  sell_available: 1,
                                  sell_total: 1,
                                  sell_reserved: 0,
                                  sell_shortable: 0,
                                  sell_total_in_coin: 0}
                   },
                  symbol: symbol,
                  timestamp:  utils._util_get_human_readable_timestamp()
              };
          global.EventHandler.emit(EVENT.ON_POSITION, fut_position_update_event);
          return fut_position_update_event;
      }else{
        jdata.account_id = account_id;
        jdata.result = false;
        jdata.error_code = 999999;
        jdata.error_code_msg = `${this.name}:fail to get position from exchange`;
        let event = {
            exchange: this.name,
            posInfoType: POSITION_INFO_TYPE.FUTURE_POSITION,
            metadata: jdata,
            symbol: symbol,
            timestamp: utils._util_get_human_readable_timestamp()
        };
        return event;
      }
  }catch (ex) {
    let event = {
        exchange: this.name,
        contract_type: contract_type,
        posInfoType: "",
        metadata: {
           account_id: account_id,
           result: false,
           error_code: 999999,
           error_code_msg: ex.toString()
           },
        timestamp: utils._util_get_human_readable_timestamp()
    };
    return event;
  }

}
```

    future position note:
    total in coin is calculated as amount / entry_price_avg
    there is a symbol in the root of response data

##### user_info /future_margin

```javascript
let request = {
    strategy: "Demo",
    ref_id: randomID(30),
    action: REQUEST_ACTIONS.QUERY_MARGIN,
    metadata: {
        exchange: "Deribit",
        account_id: "act_test",
    }
};
intercom.emit(strategy_name + '_request', request, INTERCOM_SCOPE.TRADE);
```

```javascript
function _on_request_to_update_future_margin_via_rest(account_id) {
    let jdata = {};
  try{
      if(true){
          let fut_margin_update_event=
              {
                  exchange: this.name,
                  posInfoType: POSITION_INFO_TYPE.FUTURE_USERINFO,
                  metadata:
                      {
                          account_id: this.account_id,
                          result: true,
                          margin_type:MARGIN_TYPE.SYMBOL,
                          BTCUSD_balance: 1, //float
                          BTCUSD_unprofit: 0, //float
                          BTCUSD_bond: 0, //float
                          BTCUSD_rights: 1, //float
                          BTC_balance: 1, //float
                          BTC_unprofit: 0, //float
                          BTC_bond: 0, //float
                          BTC_rights: 1, //float
                          ETHUSD_balance: 0,
                          ETHUSD_unprofit: 0,
                          ETHUSD_bond: 0,
                          ETHUSD_rights: 0,
                          ETH_balance: 0,
                          ETH_unprofit: 0,
                          ETH_bond: 0,
                          ETH_rights: 0 
                          //......返回所有currency      
                   },
                  timestamp:  utils._util_get_human_readable_timestamp()
              };
          global.EventHandler.emit(EVENT.ON_POSITION, fut_margin_update_event);
          return fut_margin_update_event;
      }else{
        jdata.account_id = account_id;
        jdata.result = false;
        jdata.error_code = 999999;
        jdata.error_code_msg = `${this.name}:fail to get margin from exchange`;
        let event = {
            exchange: this.name,
            posInfoType: POSITION_INFO_TYPE.FUTURE_USERINFO,
            metadata: jdata,
            timestamp: utils._util_get_human_readable_timestamp()
        };
        return event;
      }
  }catch (ex) {
    let event = {
        exchange: this.name,
        contract_type: contract_type,
        posInfoType: "",
        metadata: {
           account_id: account_id,
           result: false,
           error_code: 999999,
           error_code_msg: ex.toString()
           },
        timestamp: utils._util_get_human_readable_timestamp()
    };
    return event;
  }

}
```

    _on_request_to_update_future_margin_via_rest_margin_by_account(account_id)为按照全仓进行
    _on_request_to_update_future_margin_via_rest_margin_by_contract(account_id)为按照逐仓进行

```javascript
// 内部账户资金划转，指在交易所中，一个账户中的多个交易账户、钱包账户、杠杆账户、合约账户等之间的划转
function _on_request_to_transfer_different_account_asset_via_rest(symbol,account_id) {
  try{
        //需要检查account_id, symbol, currency, source, target是不是支持的
        //source、target所支持的转账账户类型：
        /**
         * *
         *   "child_account",
             "spot_account",
             "future_account",
             "c2c_account",
             "spot_margin_account",
             "wallet_account",
             "ett_account",
             "perp_account"
         * */

        let cxl_resp = {
            exchange: this.name,
            symbol: params.symbol,
            currency: params.currency,
            event: REQUEST_ACTIONS.TRANS_ASSET,
            metadata: {
                account_id: account_id,
                trans_id: transfer_id,
                result: true
            },
            timestamp: utils._util_get_human_readable_timestamp()
        };

        return cxl_resp;

  }catch (ex) {
    let myevent = {
        exchange: this.name,
        symbol: symbol,
        currency: currency,
        event: REQUEST_ACTIONS.TRANS_ASSET,
        metadata: {
            account_id: account_id,
            result: false,
            error_code: 999999,
            error_code_msg: ex.toString()
        },
        timestamp: utils._util_get_human_readable_timestamp()
    }；
    return myevent;
  }

}
```

    输入：
    symbol: 交易币对,
    currency：币种,
    account_id：账户id,
    source：从哪个账户转出,
    target：转到哪个账户,
    amount：数量

```javascript
    // 提币
    async _withdraw_via_rest(amount, account_id, options) {
        try {
            if (!this.keys[account_id]) {
                throw new InternalError({ code: ACCOUNT_ID_ERROR, message: `account_id [${account_id}] is error; service [${global.service}]` });
            }
            let currency = this.api_config.withdraw_currency_v2[options.currency];
            if (currency === undefined) {
                throw new InternalError({ code: CURRENCY_ERROR });
            }

            let myevent = {
                exchange: this.name,
                currency: currency,
                event: REQUEST_ACTIONS.WITHDRAW,
                metadata: {
                    result,
                    account_id,
                    address,
                    txHash
                },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return myevent;
        } catch (ex) {
            let cxl_resp = {
                exchange: this.name,
                currency: options.currency,
                event: REQUEST_ACTIONS.WITHDRAW,
                metadata: {
                    account_id: account_id,
                    result: false,
                    error_code: 999999,
                    error_code_msg: ex.toString()
                },
                timestamp: utils._util_get_human_readable_timestamp()
            };
            return cxl_resp;
        }
    }
```

    输入：
    account_id
    currency,
    address,
    amount,
    wallet: 钱包账户，非必输。bitfinex默认为exchange, 可输入值：exchange, margin, funding
