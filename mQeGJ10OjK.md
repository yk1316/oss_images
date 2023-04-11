# Project: ASKA支付系统

验签方式：
使用 http 协议的 head 部分进行传输。
lastUpdate: 2023-3-25 

## MENU
*支付接口*
- [ ] Pay01: 支付代金券订单
- [ ] Pay02: 支付购车订单
- [ ] Pay03: 退款
- [ ] Pay04: 查询代金券订单
- [ ] Pay05: 查询购车订单
- [ ] Pay06: 查询订单明细
- [ ] Callback01: 支付回调

# Interface Start

## Pay:支付接口

### Pay01: 支付礼品券订单
```
CODE: 01
NAME: 支付礼品券订单
URL: /askay/coupon
NOTE: 这个接口不会进行分账记录，更不会做分账操作. 这个接口的调用结果为ok是并不表示支付成功，仅表示调用成功
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"userId":"*string//用户的openid",
	"phoneNo":"*string//用户的手机号，便于系统内查账对账和操作",
	"orderId": "*string//购买代金券订单编号",
	"amount": "*float//金额",
	"couponId":"string//代金券的id，用于后续支付",
	"couponType":"string//代金券的类型，商品信息",
	"channel":"string//购买代金券的渠道编号,非必填",
	"callbackUrl":"*url//回调地址,新增参数"
}
RESPONSE:
{
	"code":"*string{ok,error,fault}//成功,失败(业务逻辑),故障(服务器内部问题)",
	"msg":"string//提示信息, code为error或fault时则为错误信息",
	"data":{
			"nonceStr":"*string//调起支付所需的参数",
			"package":"*string//prepay_id的字符串，以prepay_id=开头",
			"signType":"*string//内容一定为RSA",
			"timeStamp": "*string//9位整数的时间戳",
			"paySign":"*string//服务器端计算好的签名"
		}
}
```

### Pay02: 支付购车订单
```
CODE: 02
NAME: 支付购车订单
URL: /askay/order
NOTE: 
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"userId":"*string//用户的openid",
	"phoneNo":"*string//用户的手机号，便于系统内查账对账和操作",
	"orderId":"*string//购车订单编号",
	"orderTye":"*string//购车详情，如型号门店",
	"amount":"*float//应付金额",
	"couponId":"string//使用的折扣券,可以不传或为空"	,
	"retailId":"*string//门店id,必填，用于分账",
	"callbackUrl":"*url//回调地址"
}
RESPONSE:
{
	"code":"*string",
	"msg":"string//提示信息, code为error或fault时则为错误信息",
	"data":{
			"nonceStr":"*string//调起支付所需的参数",
			"package":"*string//prepay_id的字符串，以prepay_id=开头",
			"signType":"*string//内容一定为RSA",
			"timeStamp": "*string//9位整数的时间戳",
			"paySign":"*string//服务器端计算好的签名"
		}
}
```

### Pay03: 退款
```
CODE: 03
NAME: 退款
URL: /askay/refund
NOTE: 退款接口使用场景需要讨论，是否允许用户发起退款，还是必须财务人员通过才能发起退款？
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"paymentId":"string//订单编号，可以不区分折扣券还是购车，购车则会把折扣券抵扣退回成折扣券",
	"amount":"float//退款金额，必须要跟当时支付的金额一致，用于校验",
	"message":"string//退款原因"
}
RESPONSE:
{
	"code":"*string",
	"data":{

		},
	"message":"!string//错误消息，如登录是失败原因之类"
}
```

### Pay04: 查询折扣券的支付情况
```
CODE: 04
NAME: 查询折扣券的支付情况
URL: /askay/queryCoupon
NOTE: 查询购买折扣券的情况
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"userId":"*string//用户的openId",
	"phoneNo":"*string//用户的手机号"
}
RESPONSE:
{
	"code":"*retCode",
	"data":{[
			"paymentId":"string",
			"orderId":"string",
			"couponId":"string",
			"couponType":"string",
			"couponStatus":"string{待付款,可用,已用,已退}",
		]},
	"message":"!string//错误消息，如登录是失败原因之类"
}
```

### Pay05: 查询购车订单的支付情况
```
CODE: 05
NAME: 查询消费券的支付情况
URL: /askay/queryOrder
NOTE: 这里的接口定义只是一个示范，登录信息完全用明文传输在安全上有点弱
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"userId":"*string//用户的openId",
	"phoneNo":"*string//用户的手机号"
}
RESPONSE:
{
	"code":"*string",
	"data":{[
			"paymentId":"string//",
			"orderId":"string",
			"orderType":"string//",
			"couponId":"string//折扣券id，如果使用了的话",
			"orderStatus":"string{待付款,已付款,已退款,已关闭}",
		]},
	"message":"!string//错误消息，如登录是失败原因之类"
}
```

### Pay06: 查询支付详情
```
CODE: 06
NAME: 查询指定支付单的详情
URL: /askay/queryDetail
NOTE: 
FORMAT:JSON
METHOD:POST
REQUEST:
{
	"userId":"*string//用户的openId",
	"phoneNo":"*string//用户的手机号",
	“orderId”:"string"
}
RESPONSE:
{
	"code":"*string",
	"data":{[
			"paymentId":"string//",
			"orderId":"string",
			"orderType":"string//",
			"couponId":"string//折扣券id，如果使用了的话",
			"orderStatus":"string{待付款,已付款,已退款,已关闭}",
		]},
	"message":"!string//错误消息，如登录是失败原因之类"
}
```
### Callback01: 支付回调
```
CODE: 01
NAME: 支付回调
URL: 
NOTE: 这是服务器端直接调用发气端的接口，无需请求，且调用不成功时会反复调用
FORMAT:JSON
METHOD:POST
REQUEST:
{
}
RESPONSE:
{
	"code":"*string",
	"data":{
			"orderId":"*string//用户显示名",
			"paymentId":"*string"
		},
	"message":"!string//错误消息，如登录是失败原因之类"
}
```

TODO:
1. 同商户订单号，不同金额的下单时的逻辑处理：
	如果前一个订单已经付款，应返回错误提示，显示订单已支付
	如果前一个订单未付款，应该返回
2. 回调需要增加验证签名



CREATE TABLE `t_log_rawRR` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '如果可以，是不是每天重建这个表比较好呢？',
  `merchant` char(32) DEFAULT NULL,
  `order_no` char(32) DEFAULT NULL,
  `shop` char(32) DEFAULT NULL COMMENT '门店',
  `action` char(64) DEFAULT NULL,
  `remote_ip` char(16) NOT NULL COMMENT '用户侧ip',
  `raw_request` text NOT NULL,
  `request_time` int(11) NOT NULL,
  `raw_response` text,
  `response_time` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8 COMMENT='所有路由信息的原始记录'     

alter table t_log_rawRR add column request_entry char(32) NOT NULL after merchant;