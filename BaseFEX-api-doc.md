# basefex-app-api
## 一、trades:  交易历史
[
  {
    "fee": 0.00006200177148,   ////!<手续费                             
    "symbol": "BTCUSD",     ////!<合约交易对                                
    "feeRate": 0.0007,       ////!<手续费费率                                
    "size": 1000,              ////!<资金费率数量                              
    "ts": 1562244089804,    ////!<时间戳                                  
    "notional": 0.08857395925,           ////!<价值                    
    "orderId": "5aef4041-ee43-4a60-0005-705a0f1edcb4",  ////!<订单id, 可能为null      
    "id": "5aef4041-f300-0000-0001-00000000001b",            
    "side": "SELL",    ///!<交易方向 SELL:卖出 ; BUY:买入；FUNDING:资金费用                 
    "price": 11290     ////!<资金费率价格      
    "order": {                                           
      "id": "5aef4041-ee43-4a60-0005-705a0f1edcb4",    ////!<订单ID      
      "type": "LIMIT",     ////!<订单类型                                  
      "size": 1000,          ////!<数量                                
      "price": 11290        ////!<价格                                 
    }, ////!< order为买入 or 卖出订单 可能为null      
  }      
]      

新增:      
1.national:价值      
     需要取绝对值      
     精度:scale:Int = {      
        ////!<SymbolLists为 "symbols”这个接口的返回数据，symbol为当前订单的合约种类，symbolModel为当前合约的配置      
       let symbolModel = SymbolLists[symbol]              
       ////!<获取价值单位      
      let balanceSymbol =   symbolModel.symbol == "BTCUSD" ? "BTC" : symbolModel.quoteCurrency   ////!<获取
   let nationalScale:Int      
      if  balanceSymbol == “BTC” {      
       nationalScale  = abs(national) - 0.0001 > 0 ?  4 : 6      
   }else {      
       nationalScale = 2      
   }      
    return  nationalScale      
}      

  2.price:价格      
 精度:priceScale:Int =  symbolModel.pricePrecision         
上述数据，其中 order与orderId 可能为null,  order = "<null>"; orderId = "<null>";      


## 二、 orders/opening 未成交订单      
[      
  {      
    "liquidateUserId": null,                                
    "ts": 1562125342068,        // timestamp      
    "size": 1,                 ////!<数量                             
    "notional": 0,         ////!<订单价值                                 
    "side": "BUY",        ////!<买入方向                                  
    "userId": "5aec525e-335d-4724-0005-20153b361f89",  ////!<用户id      
    "filledNotional": 0,     ////!<已成交价值                                 
    "isLiquidate": false,   ////!<是否爆仓订单                                 
    "reduceOnly": false,  ////!<是否只减仓                                   
    "type": "LIMIT",          ////!<订单类型                               
    "symbol": "BTCUSD",  ////!<数量                                   
    "seqNo": null,                                          
    "filled": 0,             ////!<已成交                               
    "meta": null,        ////!<可能为null, 也可能为json,下面说                                    
    "status": "UNTRIGGERED",     /////!<订单状态 未触发                           
    "avgPrice": 0,            ////!<平均成交价                               
    "price": 8000,           ////!<价格                                 
    "conditional": {    ////!<触发订单                                    
      "type": "REACH",    ////!<默认 reach                                   
      "price": 8000,     ////!<触发价格                                     
      "priceType": "MARKET_PRICE"         
      ////!<  触发类型:       
      MARKET_PRICE:市场价格；      
      MARK_PRICE:   标记价格；      
      INDEX_PRICE：指数价格                   
    },                                                
    "id": "5aed7b45-5d19-40f2-0005-ca49d01f33e3"         
    ////!<订单id， 1.取消订单时使用;   2.请求列表最后一个id用来做下次请求          
  }                                                     
]   


meta:可能为null, 也可能为json， 见下述orders.      
新增      
 订单详情Info, 下述type与reduceOnly同一级别:      
 "reduceOnly": false,  ////!<是否只减仓 ，为 true 显示 “只减仓”           
   type = FOK ////!<全部成交或取消      
   type = IOC ////!<立即成交或取消                           
   type = POST_ONLY ////!<被动      
   type = LIMIT  ////!< 限价      
   type = MARKET ////!<市价      

var titleArray = [“一直有效至取消”]      
if reduceOnly {      
   titleArray.add(“只减仓”)      
}      

switch type: {      
case FOK:  titleArray.add(“全部成交或取消”) break      
case IOC:  titleArray.add(“立即成交或取消”) break      
case POST_ONLY:  titleArray.add(“被动”) break      
default break      
}      

订单状态      
status:  后面为uiwording的多语言      
 NEW：新委托 order_table_status_new      
PARTIALLY_FILLED:  部分成交 order_table_status_partially_filled      
PARTIALLY_CANCELED: 部分取消 order_table_status_partially_cancel      
CANCELED:  取消 order_table_status_cancel      
REJECTED: 拒绝  order_table_status_reject      
FILLED:全部成交 order_table_status_filled      
UNTRIGGERED : 未触发  order_table_status_untriggered      
PENDING CANCEL: 待取消 order_filter_pending_cancel      
TRIGGERED:  已触发 order_filter_trigger_triggered      


## 三、 orders:历史委托      
[      
  {      
    "liquidateUserId": null,                                 
    "id": "5aed94fc-703e-42a2-0005-578d4c468767",           
    "ts": 1562132083136,                              // timestamp       
    "side": "BUY",        ////!<交易方向 ,BUY:买入 ;SELL:卖出                                 
    "userId": "5aec525e-335d-4724-0005-20153b361f89",       
    "filledNotional": 0,                                    
    "notional": 0.102512,     ////!<委托价值                             
    "status": "NEW",          ////!<订单状态                              
    "isLiquidate": false,     ////!<是否已强平                               
    "reduceOnly": false,   ///!<是否是只减仓                                 
    "type": "LIMIT",     ///!<订单类型                                   
    "symbol": "BTCUSD",     ////!<合约交易对                                 
    "seqNo": null,                                          
    "filled": 0,    ////!<已成交量                                        
    "conditional": null,            
 ////!<触发价格未设置则为null；设置触发价格则是dict    { price = “” ; priceType = “”; type = “” } 与上述一致                       
    "size": 1000,                                           
    "avgPrice": 0,   ////!<成交均价                                        
    "price": 9755,                                          
    "meta": {   ////!<可能有profit字段，为String                                            
      "bestPrice": 11311,                                   
      "markPrice": 11536.1075491,                           
      "bestPrices": {                                       
        "ask": 11311,                                       
        "bid": 11305.5                                      
      }      
    }      
  }      
]      

订单类型: ordertype      
1、 isLiquidate = true, ordertype = 爆仓订单      
2、 var trigger =  conditional.price.toFloat > 0 ?  “触发订单” : “”      
let profitLossDict = [“TAKE_PROFIT”:”止盈”,”STOP_LOSS”:”止损” ]      
let profit = profitLossDict[meta.profit]      
if profit == nil {       
   let typeSub =   type == “MARKET” ?  “市价” : “限价”      
    ordertype =  trigger + typeSub      
}else {      
    ordertype = profit      
}      

价格显示      
let dealPrice:string      
if type == “MARKET” {      
    dealPrice =  “市价”      
}else if type == “FORCE_LIQUIDATION” {      
   dealPrice  = “-” + symbolModel.quoteCurrency(结算价格单位)      
}else {      
   dealPrice = price +  symbolModel.quoteCurrency(结算价格单位)       ///!<这里price需要精度处理和科学计数法转化，有可能为科学计数数据      
}      


turnover24的结算单位      
如果是反向合约，取base currency      
如果是正向合约，取quote currency      

精度      
以usd、usdt结算，精度取2位      
btc结算  精度取4位      

24小时成交量，用的volume24这个字段      



## 四、 订单深度推送      

{      
    "to": 26075091, ////!<标记推送顺序      
    "bestPrices": {      
        "ask": 7899,      
        "bid": 7898.5      
    },      
    "lastPrice": 7900.5,       
    "bids": {      
        "7893.5": 9442,      
        "7892.5": 6994,      
         …..      
        "7897": 7678,      
        "7896.5": 6201      
    },      
    "asks": {      
        "7902": 9280,      
                  …..      
        "7904.5": 10685,      
        "7903": 6262      
    },      
    "from": 0 ////!<标记推送顺序      
}      

第一次推送的 from为0，全量推送，      
 
if from == 0 { ////!<全量数据      
  lastTo = to ////!<上一次推送的to      
}else if  from > lastTo + 1 {      
   数据有问题，需要重新订阅      
}else { ////!< 增量推送      
     lastTo = to      
     数据处理      
}  




## 五、 交易
post:  /orders
{    
"size": 200,    
"symbol": "BTCUSD",    
"type": "LIMIT",    
"side": "BUY",    
"price": 3750.5,    
"reduceOnly": False,    
"conditional": {    
    "type": "REACH",    
    "price": 3750.5,    
    "priceType": "MARKET_PRICE"    
}}                                                 

POST_ONLY: 被动
### 1、限价下单    
订单类型  type:      
   FOK ////!<全部成交或取消   
   IOC ////!<立即成交或取消   
   POST_ONLY ////!<被动，当选中 POST_ONLY 开启是，生效时间默认为"一直有效至取消"，不可更改      
   LIMIT ////!< 一直有效至取消  
 
symbol: BTCUSD ///!<合约种类     
size = 200     ///!<下单数量    
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖    
reduceOnly =  False  ////!< true开启"只减仓"; False：关闭"只减仓"    

### 2、市价下单    
symbol: BTCUSD ///!<合约种类     
size = 200     ///!<下单数量    
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖    
reduceOnly =  False  ////!< true开启"只减仓"; False：关闭"只减仓"    

### 3、触发下单      

////!<下述触发条件，可解释为：当市场价格达到3750.5触发
"conditional": {    
    "type": "REACH",    ////!<价格达到触发条件，该字段写死
    "price": 3750.5,      ////!<触发价格   
    "priceType": "MARKET_PRICE"  
    ////!<  触发类型:       
    MARKET_PRICE:市场价格；      
    MARK_PRICE:   标记价格；      
    INDEX_PRICE：指数价格   
}}   


#### 限价触发

订单type类型:
FOK ////!<全部成交或取消       
IOC ////!<立即成交或取消        
POST_ONLY ////!<被动，当选中 POST_ONLY 开启是，生效时间默认为"一直有效至取消"，不可更改           
LIMIT ////!<限价触发 一直有效至取消     

symbol: BTCUSD ///!<合约种类          
size = 200     ///!<下单数量         
price = 8800  ////!<限价交易价格
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖         
reduceOnly =  False  ////!< true开启"只减仓"; False：关闭"只减仓"         


#### 市价触发   

订单type类型:    
MARKET ////!<市价触发  

symbol: BTCUSD ///!<合约种类          
size = 200     ///!<下单数量   
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖         
price = ""  ///!< 可不传   
reduceOnly =  False  ////!< true开启"只减仓"; False：关闭"只减仓"         


### 4、平仓订单   

#### 限价平仓，属于限价下单的一种   

订单type类型:   
LIMIT ////!<限价触发 一直有效至取消     

symbol: BTCUSD ///!<合约种类          
size = 200     ///!<下单数量           
price = 8800  ////!<限价交易价格    
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖          
reduceOnly =  True  ////!< true开启"只减仓"   


#### 市价平仓   

订单type类型:   
MARKET  ////!<限价触发 一直有效至取消     

symbol: BTCUSD ///!<合约种类          
size = 200     ///!<下单数量         
price = ""  ////!<可不传   
side = BUY   ////!<交易方向， BUY : 买； SELL: 卖         
reduceOnly =  True  ////!< true开启"只减仓"   



### 5、止盈止损    




##  六、深度挂单

{    
  "to": 26075091, ////!<标记推送顺序    
  "bestPrices": {    
    "ask": 7899,    
    "bid": 7898.5    
  },    
  "lastPrice": 7900.5,     
  "bids": { ///!<  买单   
    "7893.5": 9442,    
    "7892.5": 6994,    
     …..    
    "7897": 7678,    
    "7896.5": 6201    
  },    
  "asks": {  ////!<卖单    
    "7902": 9280,    
    …..    
    "7904.5": 10685,    
    "7903": 6262    
  },    
  "from": 0 ////!<标记推送顺序    
}   


### 1.推送异常判断逻辑     

第一次推送的 from为0，全量推送     

if from == 0 { ////!<全量数据     
  lastTo = to ////!<上一次推送的to     
}else if  from > lastTo + 1 {     
   数据有问题，需要重新订阅     
}else { ////!< 增量推送     
     lastTo = to     
     数据处理     
}     



### 2.计算深度百分比(忽略价格因素）       

var sum:Int = 0 ////!<数量总和      
var sumArray:[Int] = [0] ////!<数据累加和组     
let count =  dataArrays.count ///!<数据源个数     
for index  in 0..<count {     
     let model =  dataArrays[index] ////!<模型化数据源     
     sum = sum + model.value     
     sumArray.append(sum)     
}     

var models:DepthModel = [ ]( ) ////!<显示模型     
for index in 0..<count {     
   let model =   dataArrays[index]     
   let value = sumArray[index]     
   let percent = value / sum ////!<百分比     
   model.percent =  percent     
   models.append(model)      
}     




