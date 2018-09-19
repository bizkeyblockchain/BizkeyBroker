# Bizkey Broker API #


###### 公共参数及错误码说明


公共参数（必须提交的）
参数 | 说明
---|---
timestamp | 请求时间戳
signature | 本次请求的数据签名）
appid | 跟Bizkey申请的appid凭据
m | 接口方法（具体参看每个接口）

签名（signature参数）的说明：

    将全部GET参数按照参数名ASCII码从小到大排序（signature除外），
    所有键值对使用逗号拼接（即key1=value1,key2=value2…）拼接成字符串
    然后在末尾拼接appsecret，再进行MD5
    signature = md5('key1=value1,key2=value2,key3=value3…appsecret');


   
接口地址：

    https://trade-api.bizkey.io/index.php
   

返回示例

    {"status": true, "code":"00000" }
    
方法类型

    POST   
    
流程：
    
    商户：
        1.注册用户获得user_id
        2.提交资料注册商户
    消费者：
        1.注册用户获得user_id
        2.提交会员信息
        
    注：
    注册获得Bizkey的user_id后需要自行绑定自己的用户ID
    消费者注册及创建会员都是非必须项目。

其他事项：    
````
1、每天结算时间采取GMT+8时区时间。
2、不同国家汇率采取中国银行早上10点柜台更新汇率进行换算。
````


错误码

错误码 | 说明
---|---
10000 | 缺失字段(时间)
10001 | 超时(时间相差30秒以上)
10002 | 缺失字段(签名字段)
10003 | 签名不正确
10004 | 系统错误
10005 | 字段缺失(主要是appid)
10006 | APPID不存在
10007 | 该APPID已禁用
10008 | 该APP的加密密钥为空
10010 | 定段缺失(业务字段)
40001 | 用户不存在
30001 | 创建帐号失败.(内部错误,需要排查)
40002 | 开店失败(内部错误)
50001 | 店铺不存在
50002 | 支付时间不正确
50003 | 生成订单失败
50004 | 客户user_id不存在
60001 | 生成访问Token失败
---

#### 1. 创建用户ID接口 
参数 | 说明
  ---|---
m | reg
remark | [可选]用户备注


- 返回示例
```
返回参数：
    {
    	"status": true,
    	"code": 0,
    	"data": {
    		"user_id": "bzky_5b696cccb1b985b696cccb1be7",
    		"eth_wallet": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b"
    	}
    }
```

#### 2. 创建商户接口 

参数 | 必填 | 说明
  ---|---|---
m | Y | createStore
user_id | Y |  用户的User_id
store_name | Y  |  商店的名称
sellername |  Y |  真实姓名
mobile |  Y |  电话号码,未限制,20位以内
category_id | Y | 类目ID，请见类目表
country | Y | 所属国家(英文缩写，详见支持的国家表) CHN、USA、SGP
address | Y | 详细地址
acreage | N | 商店面积,必须为数字（单位：平方米）
lat | N | 维度
lng | N | 经度
idcard_sn | N | 身份证号
idcard_image_front | N | 身份证正面照（上传图片接口返回文件名）
idcard_image_back | N | 身份证背面照（上传图片接口返回文件名）
idcard_image_in_hand | N | 手持身份证照（上传图片接口返回文件名）
photo_shop_front | N | 门头照（上传图片接口返回文件名）
photo_shop_inside | N | 店内照（上传图片接口返回文件名）
bankcard_sn | N | 银行卡号
bankcard_image | N | 银行卡正面照（上传图片接口返回文件名）
license_sn | N | 营业执照号
license_image | N | 营业执照照片（上传图片接口返回文件名）

- 返回示例
```
    {
    	"status": "true",
    	"code": 0
    }
```

#### 3. 图片上传接口 

参数 | 必填 | 说明
  ---|---|---
  m |Y|uploadImg 	 
fileimage|Y |HTTP 表单的FILE类型 ，不参与加密运算

- 返回示例
```
    {
    	"status": true,
    	"code": 0,
    	"image_name": "2018083117002312323454.jpg"
    }
```


#### 4.创建会员接口

参数 | 必填 | 说明
  ---|---|---
m | Y | createMember
user_id | Y |  用户的User_id
real_name | Y  |  真实姓名
mobile | Y |  电话号码,未限制,20位以内
gender | N |  性别( 0未知；1男；2女 )
birthday | N | 出生日期（yyyy-mm-dd）

- 返回示例
```
    {
	"status": true,
	"code": 0
}
```



#### 5.上传订单接口

参数 | 必填 | 说明
  ---|---|---
m | Y |  neworder
seller_id | Y |  店主的user_id
buyer_id | N |  购买者的user_id
order_sn | Y |  订单号
order_amount | Y |  订单金额
new_user | N |  是否新用户,1是0否(默认)
out_sales | N |   是否外销 ,1是 0否(默认)
pay_time | Y |  支付时间（时间戳）
currency_code | Y |  使用的货币（USD、CNY）
payment_id | N | 支付方式
consignee | N | 收货人
phone | N | 收货人电话
goodslist | N | 订单商品列表（Json字符串）

goodslist格式示例及说明：
    
    数组中的商品对象必须包含4个属性：bar_code、goods_name、price、quantity

    [
        {
            "bar_code": "332457424641",
            "goods_name": "苏打饼干500克",
            "price": "15.5",
            "quantity": 2
        },
        {
            "bar_code": "323374553243",
            "goods_name": "康师傅绿茶",
            "price": "3.5",
            "quantity": 1
        }
    ]

- 返回示例
```
    {
	"status": true,
	"code": 0
}
```


#### 6.生成网页H5钱包访问的token

参数 | 必填 | 说明
  ---|---|---
m |Y |authToken
 user_id| Y | Bizkey的User_id

- 返回示例
```
    {
	"status": true,
	"code": 0,
	"data": {
		"token": "K$G3DyTDY5&%EPhmgUDc1O!^B5xLB=Za",
		"expire_time": 1534131626,
		"user_id": "bzky_5b5e8797dd0af5b5e8797dd0fc"
	}
}
```




# 附表：

 #### 货币代号

代号 | 币种
-- | --
CAD | 加元 
CNY | 人民币 
JPY | 日元 
KRW | 韩元 
MYR | 马来西亚林吉特 
SGD | 新加坡元 
THB | 泰铢 
USD | 美元 


 #### 支付方式

payment id | 支付方式
-- | --
1 | Wechat
2 | Alipay
3 | Paypal
4 | BankCard
5 | Cash
6 | BTC
7 | ETH
8 | BZKY
9 | Others

#### 国家代号

代号 | 国家
-- | --
CAN | 加拿大
CHN | 中国
JPN | 日本
KOR | 韩国
MAS | 马拉西亚
SGP | 新加坡
THA | 泰国
USA | 美国


#### 店铺类别

类目ID | 类目名
-- | --
1 | 餐饮美食
2 | 按摩SPA
3 | 超市百货
4 | 宠物服务
5 | 服饰鞋包
6 | 家政保洁
7 | 教育培训
8 | 美发美甲
9 | 美容美妆
10 | 母婴亲子
48 | 汽车服务
49 | 摄影冲印
50 | 数码家电
51 | 水电维修
52 | 水果生鲜
53 | 洗衣清洁
54 | 鲜花婚礼策划
55 | 小吃饮品
56 | 烟酒糖茶
57 | 医药健康
58 | 影音书刊
59 | 运动健身
60 | 珠宝饰品
61 | 休闲娱乐
62 | 快递跑腿
